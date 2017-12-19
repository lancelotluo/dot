# 索引
- [lua基础](#luabasic)
- [lua和C的交互](#luac)
- [lua在Nginx中的执行](#lua_nginx)
- [ngx_lua如何实现ngx.say和ngx.sleep](#ngx_say)
- [KONG (API GW)的实现](#kong)
- [ngx lua的局限](#ngx_lua_drawback)
# <span id = "luabasic"> lua基础 </span>
## luaJIT 和lua
openresty目前使用luaJIT 编译lua。
luajit是一个即时编译器，相比lua标准解释器性能要好很多。原因是：
1. luajit使用自定义的字节码格式，解析快。
2. luajit使用汇编重写了解释器。
3. 使用了直接分发。

mike pall本人的回答可以参考：
http://article.gmane.org/gmane.comp.lang.lua.general/58908
从lua_pcall的实现看一下区别：
### lua_pcall标准实现
```

```
### lua_pacll luaJIT实现


## lua线程和协程
lua 不支持真正的多线程，主要有[如下原因](https://www.quora.com/What-makes-LuaJIT-faster-than-Lua)：
1. ANSI C 不支持，所以lua也不好移植。
2. 没有必要支持线程，原因是： 多线程为是低层语言设计的，出现BUG会导致Lua难以排查，甚至有可能出现安全问题。另外多线程的同步锁也会影响性能。Lua的线程就是协程加API。A thread is the essence of a coroutine in Lua. We can think of a coroutine as a
thread plus a nice interface, or we can think of a thread as a coroutine with a
lower-level API
## lua函数用法
- **lua_pcall**  
lua_pcall(L, narg, nres, 0),第二个参数是调用函数的参数个数，第三个参数是返回结果的个数，第四个参数是错误处理的函数。
The second and third arguments to lua_pcall are the number of arguments
you are passing and the number of results you want. The fourth argument
indicates an error-handling function;
```
* call a function 'f' defined in Lua */
double f (lua_State *L, double x, double y) {
int isnum;
double z;
/* push functions and arguments */
lua_getglobal(L, "f"); /* function to be called */
lua_pushnumber(L, x);
/* push 1st argument */
lua_pushnumber(L, y);
/* push 2nd argument */
/* do the call (2 arguments, 1 result) */
if (lua_pcall(L, 2, 1, 0) != LUA_OK)
error(L, "error running function 'f': %s",
lua_tostring(L, -1));
}
/* retrieve result */
z = lua_tonumberx(L, -1, &isnum);
if (!isnum)
error(L, "function 'f' must return a number");
lua_pop(L, 1); /* pop returned value */
return z;
```

# <span id = "luac"> lua和C的交互 </span>
###示例程序

```
#include <stdio.h>
#include <string.h>
#include "lua.h"
#include "lauxlib.h"
#include "lualib.h"
int main (void) {
  char buff[256];
  int error;
  lua_State *L = luaL_newstate();
  /* opens Lua */
  luaL_openlibs(L);
  /* opens the standard libraries */
  while (fgets(buff, sizeof(buff), stdin) != NULL) {
      error = luaL_loadstring(L, buff) || lua_pcall(L, 0, 0, 0);
      if (error) {
          fprintf(stderr, "%s\n", lua_tostring(L, -1));
          lua_pop(L, 1); /* pop error message from the stack */
      }
  }

  lua_close(L);
  return 0;
}
```
有几点说明：
1. The header file lauxlib.h defines the functions provided by the auxiliary
library (auxlib). All its definitions start with luaL_
2. The Lua library defines no global variables at all. It keeps all its state in
the dynamic structure lua_State ; all functions inside Lua receive a pointer to
this structure as an argument. This implementation makes Lua reentrant and
ready to be used in multithreaded code.
3. As its name implies, the luaL_newstate function creates a new Lua state.
When luaL_newstate creates a fresh state, its environment contains no prede-
fined functions, not even print . To keep Lua small, all standard libraries are
provided as separate packages, so that you do not have to use them if you do
not need to. The header file lualib.h defines functions to open the libraries.
Function luaL_openlibs opens all standard libraries
4. luaL_loadstring编译代码，然后将函数压入栈。
5. lua_pcall在保护模式下调用函数。
### stack的重要性
lua和C是通过无处不在的 virtual stack实现的。无论是C向Lua传递参数，还是

# <span id="lua_nginx">lua在Nginx中的执行<span>
 ## 总体执行流程
 1. 将ngx_http_lua_module注册为最后一个module.
 2. lua_module执行post_configuration回调时，调用ngx_http_lua_init.
 3. [**ngx_http_lua_init**](#ngx_http_lua_init)完成启动时的主要初始工作。这里面也会注册一些常用的变量和函数，包括控制指令。可以参考[ngx_lua如何ngx.say和ngx.sleep](#ngx.say)?
 4. 之后的一些指令主要是通过各个模块完成，比如access_by_lua, content_by_lua等。

### ngx_http_lua_init流程
1. 初始化几个 handlers，h = ngx_array_push(&cmcf->phases[NGX_HTTP_REWRITE_PHASE].handlers);  类似的还有*h = ngx_http_lua_access_handler；ngx_http_lua_log_handler
 *h = ngx_http_lua_rewrite_handler
2. 最重要的工作就是ngx_http_lua_init_vm,并将初始化的vm赋值给lmcf->lua 。重点介绍一下：

#### ngx_http_lua_init_vm的流程

<!DOCTYPE svg [<!ENTITY nbsp "&#160;">]><svg width="1331pt" height="594pt" viewBox="0 0 1331 594" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
<g id="graph0" class="graph" transform="scale(1 1) rotate(0) translate(4 589.6313)">
<title>%0</title>
<polygon fill="#ffffff" stroke="transparent" points="-4,4 -4,-589.6313 1326.7324,-589.6313 1326.7324,4 -4,4"></polygon>
<!-- lmcf&#45;&gt;lua = ngx_http_lua_init_vm -->
<g id="node1" class="node">
<title>lmcf-&gt;lua = ngx_http_lua_init_vm</title>
<ellipse fill="#00ff00" stroke="#000000" cx="145.3096" cy="-567.6313" rx="145.1194" ry="18"></ellipse>
<text text-anchor="middle" x="145.3096" y="-563.4313" font-family="Times,serif" font-size="14.00" fill="#000000">lmcf-&gt;lua = ngx_http_lua_init_vm</text>
</g>
<!-- L = ngx_http_lua_new_state(parent_vm, cycle, lmcf, log); -->
<g id="node2" class="node">
<title>L = ngx_http_lua_new_state(parent_vm, cycle, lmcf, log);</title>
<ellipse fill="none" stroke="#000000" cx="707.3096" cy="-494.6313" rx="236.8442" ry="18"></ellipse>
<text text-anchor="middle" x="707.3096" y="-490.4313" font-family="Times,serif" font-size="14.00" fill="#000000">L = ngx_http_lua_new_state(parent_vm, cycle, lmcf, log);</text>
</g>
<!-- lmcf&#45;&gt;lua = ngx_http_lua_init_vm&#45;&gt;L = ngx_http_lua_new_state(parent_vm, cycle, lmcf, log); -->
<g id="edge1" class="edge">
<title>lmcf-&gt;lua = ngx_http_lua_init_vm-&gt;L = ngx_http_lua_new_state(parent_vm, cycle, lmcf, log);</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M245.7839,-554.5803C338.442,-542.5447 476.5141,-524.61 577.4946,-511.4934"></path>
<polygon fill="#000000" stroke="#000000" points="577.9901,-514.9585 587.456,-510.1995 577.0884,-508.0168 577.9901,-514.9585"></polygon>
</g>
<!-- L = luaL_newstate(); -->
<g id="node3" class="node">
<title>L = luaL_newstate();</title>
<ellipse fill="none" stroke="#000000" cx="913.3096" cy="-421.6313" rx="92.7659" ry="18"></ellipse>
<text text-anchor="middle" x="913.3096" y="-417.4313" font-family="Times,serif" font-size="14.00" fill="#000000">L = luaL_newstate();</text>
</g>
<!-- L = ngx_http_lua_new_state(parent_vm, cycle, lmcf, log);&#45;&gt;L = luaL_newstate(); -->
<g id="edge2" class="edge">
<title>L = ngx_http_lua_new_state(parent_vm, cycle, lmcf, log);-&gt;L = luaL_newstate();</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M757.1764,-476.96C787.9312,-466.0615 827.3892,-452.0788 858.9433,-440.897"></path>
<polygon fill="#000000" stroke="#000000" points="860.2072,-444.1625 868.4638,-437.5232 857.869,-437.5645 860.2072,-444.1625"></polygon>
</g>
<!-- cln&#45;&gt;handler = ngx_http_lua_cleanup_vm -->
<g id="node4" class="node">
<title>cln-&gt;handler = ngx_http_lua_cleanup_vm</title>
<ellipse fill="none" stroke="#000000" cx="629.3096" cy="-421.6313" rx="173.9389" ry="18"></ellipse>
<text text-anchor="middle" x="629.3096" y="-417.4313" font-family="Times,serif" font-size="14.00" fill="#000000">cln-&gt;handler = ngx_http_lua_cleanup_vm</text>
</g>
<!-- L = ngx_http_lua_new_state(parent_vm, cycle, lmcf, log);&#45;&gt;cln&#45;&gt;handler = ngx_http_lua_cleanup_vm -->
<g id="edge3" class="edge">
<title>L = ngx_http_lua_new_state(parent_vm, cycle, lmcf, log);-&gt;cln-&gt;handler = ngx_http_lua_cleanup_vm</title>
<path fill="none" stroke="#000000" d="M688.0287,-476.5863C678.3723,-467.549 666.5272,-456.4632 656.0111,-446.6212"></path>
<polygon fill="#000000" stroke="#000000" points="658.3318,-443.9994 648.639,-439.7216 653.5486,-449.1103 658.3318,-443.9994"></polygon>
</g>
<!-- luaL_openlibs(L); -->
<g id="node10" class="node">
<title>luaL_openlibs(L);</title>
<ellipse fill="none" stroke="#000000" cx="913.3096" cy="-348.6313" rx="81.0994" ry="18"></ellipse>
<text text-anchor="middle" x="913.3096" y="-344.4313" font-family="Times,serif" font-size="14.00" fill="#000000">luaL_openlibs(L);</text>
</g>
<!-- L = luaL_newstate();&#45;&gt;luaL_openlibs(L); -->
<g id="edge11" class="edge">
<title>L = luaL_newstate();-&gt;luaL_openlibs(L);</title>
<path fill="none" stroke="#000000" d="M913.3096,-403.5863C913.3096,-395.5141 913.3096,-385.8077 913.3096,-376.813"></path>
<polygon fill="#000000" stroke="#000000" points="916.8097,-376.7216 913.3096,-366.7216 909.8097,-376.7217 916.8097,-376.7216"></polygon>
</g>
<!-- if (lmcf&#45;&gt;preload_hooks) -->
<g id="node5" class="node">
<title>if (lmcf-&gt;preload_hooks)</title>
<polygon fill="none" stroke="#000000" points="629.3096,-366.6313 475.0343,-348.6313 629.3096,-330.6313 783.5848,-348.6313 629.3096,-366.6313"></polygon>
<text text-anchor="middle" x="629.3096" y="-344.4313" font-family="Times,serif" font-size="14.00" fill="#000000">if (lmcf-&gt;preload_hooks)</text>
</g>
<!-- cln&#45;&gt;handler = ngx_http_lua_cleanup_vm&#45;&gt;if (lmcf&#45;&gt;preload_hooks) -->
<g id="edge4" class="edge">
<title>cln-&gt;handler = ngx_http_lua_cleanup_vm-&gt;if (lmcf-&gt;preload_hooks)</title>
<path fill="none" stroke="#000000" d="M629.3096,-403.5863C629.3096,-395.5141 629.3096,-385.8077 629.3096,-376.813"></path>
<polygon fill="#000000" stroke="#000000" points="632.8097,-376.7216 629.3096,-366.7216 625.8097,-376.7217 632.8097,-376.7216"></polygon>
</g>
<!-- ngx_http_lua_probe_register_preload_package(L, hook[i].package); -->
<g id="node6" class="node">
<title>ngx_http_lua_probe_register_preload_package(L, hook[i].package);</title>
<ellipse fill="none" stroke="#000000" cx="436.3096" cy="-259.8313" rx="275.3234" ry="18"></ellipse>
<text text-anchor="middle" x="436.3096" y="-255.6313" font-family="Times,serif" font-size="14.00" fill="#000000">ngx_http_lua_probe_register_preload_package(L, hook[i].package);</text>
</g>
<!-- if (lmcf&#45;&gt;preload_hooks)&#45;&gt;ngx_http_lua_probe_register_preload_package(L, hook[i].package); -->
<g id="edge5" class="edge">
<title>if (lmcf-&gt;preload_hooks)-&gt;ngx_http_lua_probe_register_preload_package(L, hook[i].package);</title>
<path fill="none" stroke="#000000" d="M597.8712,-334.1664C567.0829,-320.0006 519.7554,-298.225 484.269,-281.8976"></path>
<polygon fill="#000000" stroke="#000000" points="485.6071,-278.6606 475.0596,-277.6603 482.6812,-285.0198 485.6071,-278.6606"></polygon>
<text text-anchor="middle" x="554.3629" y="-300.0313" font-family="Times,serif" font-size="14.00" fill="#000000">Y</text>
</g>
<!-- return L -->
<g id="node9" class="node">
<title>return L</title>
<ellipse fill="none" stroke="#000000" cx="436.3096" cy="-29.4156" rx="42.3144" ry="18"></ellipse>
<text text-anchor="middle" x="436.3096" y="-25.2156" font-family="Times,serif" font-size="14.00" fill="#000000">return L</text>
</g>
<!-- if (lmcf&#45;&gt;preload_hooks)&#45;&gt;return L -->
<g id="edge9" class="edge">
<title>if (lmcf-&gt;preload_hooks)-&gt;return L</title>
<path fill="none" stroke="#000000" d="M668.7114,-335.1771C700.2747,-321.6076 739.3096,-297.2599 739.3096,-259.8313 739.3096,-259.8313 739.3096,-259.8313 739.3096,-113.8313 739.3096,-62.4064 575.3015,-40.9573 488.6127,-33.2119"></path>
<polygon fill="#000000" stroke="#000000" points="488.7043,-29.7069 478.4404,-32.3342 488.1025,-36.681 488.7043,-29.7069"></polygon>
<text text-anchor="middle" x="744.3629" y="-182.6313" font-family="Times,serif" font-size="14.00" fill="#000000">N</text>
</g>
<!-- lua_pushcfunction(L, hook[i].loader); -->
<g id="node7" class="node">
<title>lua_pushcfunction(L, hook[i].loader);</title>
<ellipse fill="none" stroke="#000000" cx="436.3096" cy="-186.8313" rx="157.3716" ry="18"></ellipse>
<text text-anchor="middle" x="436.3096" y="-182.6313" font-family="Times,serif" font-size="14.00" fill="#000000">lua_pushcfunction(L, hook[i].loader);</text>
</g>
<!-- ngx_http_lua_probe_register_preload_package(L, hook[i].package);&#45;&gt;lua_pushcfunction(L, hook[i].loader); -->
<g id="edge6" class="edge">
<title>ngx_http_lua_probe_register_preload_package(L, hook[i].package);-&gt;lua_pushcfunction(L, hook[i].loader);</title>
<path fill="none" stroke="#000000" d="M436.3096,-241.7863C436.3096,-233.7141 436.3096,-224.0077 436.3096,-215.013"></path>
<polygon fill="#000000" stroke="#000000" points="439.8097,-214.9216 436.3096,-204.9216 432.8097,-214.9217 439.8097,-214.9216"></polygon>
</g>
<!-- lua_setfield(L, &#45;2, (char *) hook[i].package); -->
<g id="node8" class="node">
<title>lua_setfield(L, -2, (char *) hook[i].package);</title>
<ellipse fill="none" stroke="#000000" cx="436.3096" cy="-113.8313" rx="183.9539" ry="18"></ellipse>
<text text-anchor="middle" x="436.3096" y="-109.6313" font-family="Times,serif" font-size="14.00" fill="#000000">lua_setfield(L, -2, (char *) hook[i].package);</text>
</g>
<!-- lua_pushcfunction(L, hook[i].loader);&#45;&gt;lua_setfield(L, &#45;2, (char *) hook[i].package); -->
<g id="edge7" class="edge">
<title>lua_pushcfunction(L, hook[i].loader);-&gt;lua_setfield(L, -2, (char *) hook[i].package);</title>
<path fill="none" stroke="#000000" d="M436.3096,-168.7863C436.3096,-160.7141 436.3096,-151.0077 436.3096,-142.013"></path>
<polygon fill="#000000" stroke="#000000" points="439.8097,-141.9216 436.3096,-131.9216 432.8097,-141.9217 439.8097,-141.9216"></polygon>
</g>
<!-- lua_setfield(L, &#45;2, (char *) hook[i].package);&#45;&gt;return L -->
<g id="edge8" class="edge">
<title>lua_setfield(L, -2, (char *) hook[i].package);-&gt;return L</title>
<path fill="none" stroke="#000000" d="M436.3096,-95.5143C436.3096,-84.563 436.3096,-70.4121 436.3096,-58.0151"></path>
<polygon fill="#000000" stroke="#000000" points="439.8097,-57.6968 436.3096,-47.6968 432.8097,-57.6969 439.8097,-57.6968"></polygon>
</g>
<!-- return L&#45;&gt;lmcf&#45;&gt;lua = ngx_http_lua_init_vm -->
<g id="edge10" class="edge">
<title>return L-&gt;lmcf-&gt;lua = ngx_http_lua_init_vm</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M394.1787,-32.3342C310.9226,-39.2631 133.3096,-60.3159 133.3096,-113.8313 133.3096,-494.6313 133.3096,-494.6313 133.3096,-494.6313 133.3096,-509.529 135.7731,-525.9131 138.4677,-539.3781"></path>
<polygon fill="#000000" stroke="#000000" points="135.1128,-540.4255 140.6357,-549.4669 141.9566,-538.9548 135.1128,-540.4255"></polygon>
</g>
<!-- lua_getglobal(L, &#39;package&#39;); -->
<g id="node11" class="node">
<title>lua_getglobal(L, 'package');</title>
<ellipse fill="none" stroke="#000000" cx="913.3096" cy="-259.8313" rx="121.5104" ry="18"></ellipse>
<text text-anchor="middle" x="913.3096" y="-255.6313" font-family="Times,serif" font-size="14.00" fill="#000000">lua_getglobal(L, 'package');</text>
</g>
<!-- luaL_openlibs(L);&#45;&gt;lua_getglobal(L, &#39;package&#39;); -->
<g id="edge12" class="edge">
<title>luaL_openlibs(L);-&gt;lua_getglobal(L, 'package');</title>
<path fill="none" stroke="#000000" d="M913.3096,-330.2319C913.3096,-318.1262 913.3096,-302.0389 913.3096,-288.2986"></path>
<polygon fill="#000000" stroke="#000000" points="916.8097,-287.9033 913.3096,-277.9033 909.8097,-287.9033 916.8097,-287.9033"></polygon>
</g>
<!-- lua_pop(L, 1); -->
<g id="node12" class="node">
<title>lua_pop(L, 1);</title>
<ellipse fill="none" stroke="#000000" cx="913.3096" cy="-186.8313" rx="66.6383" ry="18"></ellipse>
<text text-anchor="middle" x="913.3096" y="-182.6313" font-family="Times,serif" font-size="14.00" fill="#000000">lua_pop(L, 1);</text>
</g>
<!-- lua_getglobal(L, &#39;package&#39;);&#45;&gt;lua_pop(L, 1); -->
<g id="edge13" class="edge">
<title>lua_getglobal(L, 'package');-&gt;lua_pop(L, 1);</title>
<path fill="none" stroke="#000000" d="M913.3096,-241.7863C913.3096,-233.7141 913.3096,-224.0077 913.3096,-215.013"></path>
<polygon fill="#000000" stroke="#000000" points="916.8097,-214.9216 913.3096,-204.9216 909.8097,-214.9217 916.8097,-214.9216"></polygon>
</g>
<!-- ngx_http_lua_init_registry(L, log); -->
<g id="node13" class="node">
<title>ngx_http_lua_init_registry(L, log);</title>
<ellipse fill="none" stroke="#000000" cx="913.3096" cy="-113.8313" rx="146.433" ry="18"></ellipse>
<text text-anchor="middle" x="913.3096" y="-109.6313" font-family="Times,serif" font-size="14.00" fill="#000000">ngx_http_lua_init_registry(L, log);</text>
</g>
<!-- lua_pop(L, 1);&#45;&gt;ngx_http_lua_init_registry(L, log); -->
<g id="edge14" class="edge">
<title>lua_pop(L, 1);-&gt;ngx_http_lua_init_registry(L, log);</title>
<path fill="none" stroke="#000000" d="M913.3096,-168.7863C913.3096,-160.7141 913.3096,-151.0077 913.3096,-142.013"></path>
<polygon fill="#000000" stroke="#000000" points="916.8097,-141.9216 913.3096,-131.9216 909.8097,-141.9217 916.8097,-141.9216"></polygon>
</g>
<!-- ngx_http_lua_init_globals(L, cycle, lmcf, log);
 &#160;&#160;&#160;这个函数非常重要，注册了大部分的lua和nginx交互的函数 -->
<g id="node14" class="node">
<title>ngx_http_lua_init_globals(L, cycle, lmcf, log);
 &nbsp;&nbsp;&nbsp;这个函数非常重要，注册了大部分的lua和nginx交互的函数</title>
<ellipse fill="none" stroke="#000000" cx="1037.3096" cy="-29.4156" rx="285.3456" ry="29.3315"></ellipse>
<text text-anchor="middle" x="1037.3096" y="-33.6156" font-family="Times,serif" font-size="14.00" fill="#ff0000">ngx_http_lua_init_globals(L, cycle, lmcf, log);</text>
<text text-anchor="middle" x="1037.3096" y="-16.8156" font-family="Times,serif" font-size="14.00" fill="#ff0000"> &nbsp;&nbsp;&nbsp;这个函数非常重要，注册了大部分的lua和nginx交互的函数</text>
</g>
<!-- ngx_http_lua_init_registry(L, log);&#45;&gt;ngx_http_lua_init_globals(L, cycle, lmcf, log);
 &#160;&#160;&#160;这个函数非常重要，注册了大部分的lua和nginx交互的函数 -->
<g id="edge15" class="edge">
<title>ngx_http_lua_init_registry(L, log);-&gt;ngx_http_lua_init_globals(L, cycle, lmcf, log);
 &nbsp;&nbsp;&nbsp;这个函数非常重要，注册了大部分的lua和nginx交互的函数</title>
<path fill="none" stroke="#000000" d="M939.6068,-95.9289C953.0432,-86.7818 969.8732,-75.3244 985.7604,-64.5088"></path>
<polygon fill="#000000" stroke="#000000" points="988.1655,-67.1056 994.4622,-58.5849 984.2262,-61.3192 988.1655,-67.1056"></polygon>
</g>
<!-- ngx_http_lua_init_globals(L, cycle, lmcf, log);
 &#160;&#160;&#160;这个函数非常重要，注册了大部分的lua和nginx交互的函数&#45;&gt;L = ngx_http_lua_new_state(parent_vm, cycle, lmcf, log); -->
<g id="edge16" class="edge">
<title>ngx_http_lua_init_globals(L, cycle, lmcf, log);
 &nbsp;&nbsp;&nbsp;这个函数非常重要，注册了大部分的lua和nginx交互的函数-&gt;L = ngx_http_lua_new_state(parent_vm, cycle, lmcf, log);</title>
<path fill="none" stroke="#000000" d="M1065.2333,-59.0377C1076.6864,-74.2264 1087.3096,-93.7319 1087.3096,-113.8313 1087.3096,-421.6313 1087.3096,-421.6313 1087.3096,-421.6313 1087.3096,-445.9343 965.8916,-466.0872 860.3216,-479.0165"></path>
<polygon fill="#000000" stroke="#000000" points="859.6668,-475.5701 850.1592,-480.245 860.507,-482.5195 859.6668,-475.5701"></polygon>
<text text-anchor="middle" x="1110.0491" y="-255.6313" font-family="Times,serif" font-size="14.00" fill="#000000">return L</text>
</g>
</g>
</svg>

其中有一个函数非常重要，ngx_http_lua_init_globals,它的过程如下：

<!DOCTYPE svg [<!ENTITY nbsp "&#160;">]><svg width="664pt" height="1122pt" viewBox="0 0 664 1122" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
<g id="graph0" class="graph" transform="scale(1 1) rotate(0) translate(4 1118.2917)">
<title>%0</title>
<polygon fill="#ffffff" stroke="transparent" points="-4,4 -4,-1118.2917 660.0784,-1118.2917 660.0784,4 -4,4"></polygon>
<!-- ngx_http_lua_init_globals(lua_State *L, ngx_cycle_t *cycle) -->
<g id="node1" class="node">
<title>ngx_http_lua_init_globals(lua_State *L, ngx_cycle_t *cycle)</title>
<ellipse fill="#00ff00" stroke="#000000" cx="408.495" cy="-1096.2917" rx="247.6668" ry="18"></ellipse>
<text text-anchor="middle" x="408.495" y="-1092.0917" font-family="Times,serif" font-size="14.00" fill="#000000">ngx_http_lua_init_globals(lua_State *L, ngx_cycle_t *cycle)</text>
</g>
<!-- lua_pushlightuserdata(L, cycle) -->
<g id="node2" class="node">
<title>lua_pushlightuserdata(L, cycle)</title>
<ellipse fill="none" stroke="#000000" cx="325.495" cy="-1023.2917" rx="133.1277" ry="18"></ellipse>
<text text-anchor="middle" x="325.495" y="-1019.0917" font-family="Times,serif" font-size="14.00" fill="#000000">lua_pushlightuserdata(L, cycle)</text>
</g>
<!-- ngx_http_lua_init_globals(lua_State *L, ngx_cycle_t *cycle)&#45;&gt;lua_pushlightuserdata(L, cycle) -->
<g id="edge1" class="edge">
<title>ngx_http_lua_init_globals(lua_State *L, ngx_cycle_t *cycle)-&gt;lua_pushlightuserdata(L, cycle)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M387.9782,-1078.2467C377.525,-1069.053 364.6616,-1057.7394 353.3287,-1047.7719"></path>
<polygon fill="#000000" stroke="#000000" points="355.5963,-1045.1052 345.7759,-1041.129 350.9733,-1050.3614 355.5963,-1045.1052"></polygon>
</g>
<!-- lua_setglobal(L, &quot;__ngx_cycle&quot;) -->
<g id="node3" class="node">
<title>lua_setglobal(L, "__ngx_cycle")</title>
<ellipse fill="none" stroke="#000000" cx="316.495" cy="-950.2917" rx="136.8161" ry="18"></ellipse>
<text text-anchor="middle" x="316.495" y="-946.0917" font-family="Times,serif" font-size="14.00" fill="#000000">lua_setglobal(L, "__ngx_cycle")</text>
</g>
<!-- lua_pushlightuserdata(L, cycle)&#45;&gt;lua_setglobal(L, &quot;__ngx_cycle&quot;) -->
<g id="edge2" class="edge">
<title>lua_pushlightuserdata(L, cycle)-&gt;lua_setglobal(L, "__ngx_cycle")</title>
<path fill="none" stroke="#000000" d="M323.2703,-1005.2467C322.2751,-997.1745 321.0784,-987.468 319.9695,-978.4734"></path>
<polygon fill="#000000" stroke="#000000" points="323.4227,-977.8786 318.7253,-968.382 316.4753,-978.7352 323.4227,-977.8786"></polygon>
</g>
<!-- ngx_http_lua_inject_ndk_api(L) -->
<g id="node4" class="node">
<title>ngx_http_lua_inject_ndk_api(L)</title>
<ellipse fill="none" stroke="#000000" cx="322.495" cy="-877.2917" rx="136.6072" ry="18"></ellipse>
<text text-anchor="middle" x="322.495" y="-873.0917" font-family="Times,serif" font-size="14.00" fill="#000000">ngx_http_lua_inject_ndk_api(L)</text>
</g>
<!-- lua_setglobal(L, &quot;__ngx_cycle&quot;)&#45;&gt;ngx_http_lua_inject_ndk_api(L) -->
<g id="edge3" class="edge">
<title>lua_setglobal(L, "__ngx_cycle")-&gt;ngx_http_lua_inject_ndk_api(L)</title>
<path fill="none" stroke="#000000" d="M317.9782,-932.2467C318.6416,-924.1745 319.4394,-914.468 320.1787,-905.4734"></path>
<polygon fill="#000000" stroke="#000000" points="323.6771,-905.6351 321.0081,-895.382 316.7007,-905.0617 323.6771,-905.6351"></polygon>
</g>
<!-- ngx_http_lua_inject_ngx_api(L, lmcf, log) -->
<g id="node5" class="node">
<title>ngx_http_lua_inject_ngx_api(L, lmcf, log)</title>
<ellipse fill="none" stroke="#000000" cx="405.495" cy="-804.2917" rx="176.4973" ry="18"></ellipse>
<text text-anchor="middle" x="405.495" y="-800.0917" font-family="Times,serif" font-size="14.00" fill="#000000">ngx_http_lua_inject_ngx_api(L, lmcf, log)</text>
</g>
<!-- ngx_http_lua_inject_ndk_api(L)&#45;&gt;ngx_http_lua_inject_ngx_api(L, lmcf, log) -->
<g id="edge4" class="edge">
<title>ngx_http_lua_inject_ndk_api(L)-&gt;ngx_http_lua_inject_ngx_api(L, lmcf, log)</title>
<path fill="none" stroke="#000000" d="M343.0119,-859.2467C353.387,-850.1216 366.1366,-838.9081 377.4073,-828.9953"></path>
<polygon fill="#000000" stroke="#000000" points="379.7291,-831.6144 384.9265,-822.382 375.1061,-826.3582 379.7291,-831.6144"></polygon>
</g>
<!-- ngx_http_lua_inject_ngx_api(L, lmcf, log)&#45;&gt;ngx_http_lua_init_globals(lua_State *L, ngx_cycle_t *cycle) -->
<g id="edge5" class="edge">
<title>ngx_http_lua_inject_ngx_api(L, lmcf, log)-&gt;ngx_http_lua_init_globals(lua_State *L, ngx_cycle_t *cycle)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M447.0029,-821.962C468.1656,-834.0263 489.495,-852.3678 489.495,-877.2917 489.495,-1023.2917 489.495,-1023.2917 489.495,-1023.2917 489.495,-1044.2782 474.3053,-1060.778 457.0815,-1072.7415"></path>
<polygon fill="#000000" stroke="#000000" points="455.0362,-1069.8955 448.5204,-1078.2498 458.8239,-1075.7823 455.0362,-1069.8955"></polygon>
<text text-anchor="middle" x="501.9403" y="-946.0917" font-family="Times,serif" font-size="14.00" fill="#000000">void</text>
</g>
<!-- lua_createtable(L, 0 /* narr */, 116 /* nrec */) -->
<g id="node6" class="node">
<title>lua_createtable(L, 0 /* narr */, 116 /* nrec */)</title>
<ellipse fill="none" stroke="#000000" cx="290.495" cy="-731.2917" rx="187.4474" ry="18"></ellipse>
<text text-anchor="middle" x="290.495" y="-727.0917" font-family="Times,serif" font-size="14.00" fill="#000000">lua_createtable(L, 0 /* narr */, 116 /* nrec */)</text>
</g>
<!-- ngx_http_lua_inject_ngx_api(L, lmcf, log)&#45;&gt;lua_createtable(L, 0 /* narr */, 116 /* nrec */) -->
<g id="edge6" class="edge">
<title>ngx_http_lua_inject_ngx_api(L, lmcf, log)-&gt;lua_createtable(L, 0 /* narr */, 116 /* nrec */)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M377.3629,-786.4339C362.3266,-776.8891 343.6279,-765.0195 327.4346,-754.7403"></path>
<polygon fill="#000000" stroke="#000000" points="329.0538,-751.6225 318.7353,-749.2181 325.3023,-757.5323 329.0538,-751.6225"></polygon>
</g>
<!-- lua_pushcfunction(L, ngx_http_lua_get_raw_phase_context) -->
<g id="node7" class="node">
<title>lua_pushcfunction(L, ngx_http_lua_get_raw_phase_context)</title>
<ellipse fill="none" stroke="#000000" cx="246.495" cy="-658.2917" rx="246.4901" ry="18"></ellipse>
<text text-anchor="middle" x="246.495" y="-654.0917" font-family="Times,serif" font-size="14.00" fill="#000000">lua_pushcfunction(L, ngx_http_lua_get_raw_phase_context)</text>
</g>
<!-- lua_createtable(L, 0 /* narr */, 116 /* nrec */)&#45;&gt;lua_pushcfunction(L, ngx_http_lua_get_raw_phase_context) -->
<g id="edge7" class="edge">
<title>lua_createtable(L, 0 /* narr */, 116 /* nrec */)-&gt;lua_pushcfunction(L, ngx_http_lua_get_raw_phase_context)</title>
<path fill="none" stroke="#000000" d="M279.6186,-713.2467C274.4887,-704.7358 268.2639,-694.4082 262.6014,-685.0136"></path>
<polygon fill="#000000" stroke="#000000" points="265.5587,-683.1398 257.3988,-676.382 259.5635,-686.7534 265.5587,-683.1398"></polygon>
</g>
<!-- lua_setfield(L, &#45;2, &quot;_phase_ctx&quot;) -->
<g id="node8" class="node">
<title>lua_setfield(L, -2, "_phase_ctx")</title>
<ellipse fill="none" stroke="#000000" cx="251.495" cy="-585.2917" rx="137.3725" ry="18"></ellipse>
<text text-anchor="middle" x="251.495" y="-581.0917" font-family="Times,serif" font-size="14.00" fill="#000000">lua_setfield(L, -2, "_phase_ctx")</text>
</g>
<!-- lua_pushcfunction(L, ngx_http_lua_get_raw_phase_context)&#45;&gt;lua_setfield(L, &#45;2, &quot;_phase_ctx&quot;) -->
<g id="edge8" class="edge">
<title>lua_pushcfunction(L, ngx_http_lua_get_raw_phase_context)-&gt;lua_setfield(L, -2, "_phase_ctx")</title>
<path fill="none" stroke="#000000" d="M247.731,-640.2467C248.2839,-632.1745 248.9487,-622.468 249.5648,-613.4734"></path>
<polygon fill="#000000" stroke="#000000" points="253.0644,-613.5978 250.256,-603.382 246.0807,-613.1194 253.0644,-613.5978"></polygon>
</g>
<!-- ngx_http_lua_inject_arg_api(L) -->
<g id="node9" class="node">
<title>ngx_http_lua_inject_arg_api(L)</title>
<ellipse fill="none" stroke="#000000" cx="254.495" cy="-496.4917" rx="134.2964" ry="18"></ellipse>
<text text-anchor="middle" x="254.495" y="-492.2917" font-family="Times,serif" font-size="14.00" fill="#000000">ngx_http_lua_inject_arg_api(L)</text>
</g>
<!-- lua_setfield(L, &#45;2, &quot;_phase_ctx&quot;)&#45;&gt;ngx_http_lua_inject_arg_api(L) -->
<g id="edge9" class="edge">
<title>lua_setfield(L, -2, "_phase_ctx")-&gt;ngx_http_lua_inject_arg_api(L)</title>
<path fill="none" stroke="#000000" d="M252.1166,-566.8922C252.5256,-554.7865 253.0691,-538.6993 253.5333,-524.959"></path>
<polygon fill="#000000" stroke="#000000" points="257.0448,-524.6762 253.8845,-514.5637 250.0487,-524.4398 257.0448,-524.6762"></polygon>
</g>
<!-- ngx_http_lua_inject_http_consts(L);
 &#160;&#160;&#160;ngx_http_lua_inject_core_consts(L);
 &#160;&#160;&#160;...
 &#160;&#160;&#160;ngx_http_lua_inject_req_api(log, L);
 &#160;&#160;&#160;...
 &#160;&#160;&#160;ngx_http_lua_inject_misc_api(L); -->
<g id="node10" class="node">
<title>ngx_http_lua_inject_http_consts(L);
 &nbsp;&nbsp;&nbsp;ngx_http_lua_inject_core_consts(L);
 &nbsp;&nbsp;&nbsp;...
 &nbsp;&nbsp;&nbsp;ngx_http_lua_inject_req_api(log, L);
 &nbsp;&nbsp;&nbsp;...
 &nbsp;&nbsp;&nbsp;ngx_http_lua_inject_misc_api(L);</title>
<ellipse fill="none" stroke="#000000" cx="254.495" cy="-364.5584" rx="168.0494" ry="76.8665"></ellipse>
<text text-anchor="middle" x="254.495" y="-402.3584" font-family="Times,serif" font-size="14.00" fill="#000000">ngx_http_lua_inject_http_consts(L);</text>
<text text-anchor="middle" x="254.495" y="-385.5584" font-family="Times,serif" font-size="14.00" fill="#000000"> &nbsp;&nbsp;&nbsp;ngx_http_lua_inject_core_consts(L);</text>
<text text-anchor="middle" x="254.495" y="-368.7584" font-family="Times,serif" font-size="14.00" fill="#000000"> &nbsp;&nbsp;&nbsp;...</text>
<text text-anchor="middle" x="254.495" y="-351.9584" font-family="Times,serif" font-size="14.00" fill="#000000"> &nbsp;&nbsp;&nbsp;ngx_http_lua_inject_req_api(log, L);</text>
<text text-anchor="middle" x="254.495" y="-335.1584" font-family="Times,serif" font-size="14.00" fill="#000000"> &nbsp;&nbsp;&nbsp;...</text>
<text text-anchor="middle" x="254.495" y="-318.3584" font-family="Times,serif" font-size="14.00" fill="#000000"> &nbsp;&nbsp;&nbsp;ngx_http_lua_inject_misc_api(L);</text>
</g>
<!-- ngx_http_lua_inject_arg_api(L)&#45;&gt;ngx_http_lua_inject_http_consts(L);
 &#160;&#160;&#160;ngx_http_lua_inject_core_consts(L);
 &#160;&#160;&#160;...
 &#160;&#160;&#160;ngx_http_lua_inject_req_api(log, L);
 &#160;&#160;&#160;...
 &#160;&#160;&#160;ngx_http_lua_inject_misc_api(L); -->
<g id="edge10" class="edge">
<title>ngx_http_lua_inject_arg_api(L)-&gt;ngx_http_lua_inject_http_consts(L);
 &nbsp;&nbsp;&nbsp;ngx_http_lua_inject_core_consts(L);
 &nbsp;&nbsp;&nbsp;...
 &nbsp;&nbsp;&nbsp;ngx_http_lua_inject_req_api(log, L);
 &nbsp;&nbsp;&nbsp;...
 &nbsp;&nbsp;&nbsp;ngx_http_lua_inject_misc_api(L);</title>
<path fill="none" stroke="#000000" d="M254.495,-478.4235C254.495,-470.9605 254.495,-461.7228 254.495,-451.762"></path>
<polygon fill="#000000" stroke="#000000" points="257.9951,-451.599 254.495,-441.5991 250.9951,-451.5991 257.9951,-451.599"></polygon>
</g>
<!-- lua_getglobal(L, &quot;package&quot;); /* ngx package */
 &#160;&#160;&#160;lua_getfield(L, &#45;1, &quot;loaded&quot;); /* ngx package loaded */
 &#160;&#160;&#160;lua_pushvalue(L, &#45;3); /* ngx package loaded ngx */
 &#160;&#160;&#160;lua_setfield(L, &#45;2, &quot;ngx&quot;); /* ngx package loaded */
 &#160;&#160;&#160;lua_pop(L, 2);
 &#160;&#160;&#160;...
 &#160;&#160;&#160;lua_setglobal(L, &quot;ngx&quot;) -->
<g id="node11" class="node">
<title>lua_getglobal(L, "package"); /* ngx package */
 &nbsp;&nbsp;&nbsp;lua_getfield(L, -1, "loaded"); /* ngx package loaded */
 &nbsp;&nbsp;&nbsp;lua_pushvalue(L, -3); /* ngx package loaded ngx */
 &nbsp;&nbsp;&nbsp;lua_setfield(L, -2, "ngx"); /* ngx package loaded */
 &nbsp;&nbsp;&nbsp;lua_pop(L, 2);
 &nbsp;&nbsp;&nbsp;...
 &nbsp;&nbsp;&nbsp;lua_setglobal(L, "ngx")</title>
<ellipse fill="none" stroke="#000000" cx="254.495" cy="-161.8126" rx="237.8719" ry="88.6256"></ellipse>
<text text-anchor="middle" x="254.495" y="-208.0126" font-family="Times,serif" font-size="14.00" fill="#000000">lua_getglobal(L, "package"); /* ngx package */</text>
<text text-anchor="middle" x="254.495" y="-191.2126" font-family="Times,serif" font-size="14.00" fill="#000000"> &nbsp;&nbsp;&nbsp;lua_getfield(L, -1, "loaded"); /* ngx package loaded */</text>
<text text-anchor="middle" x="254.495" y="-174.4126" font-family="Times,serif" font-size="14.00" fill="#000000"> &nbsp;&nbsp;&nbsp;lua_pushvalue(L, -3); /* ngx package loaded ngx */</text>
<text text-anchor="middle" x="254.495" y="-157.6126" font-family="Times,serif" font-size="14.00" fill="#000000"> &nbsp;&nbsp;&nbsp;lua_setfield(L, -2, "ngx"); /* ngx package loaded */</text>
<text text-anchor="middle" x="254.495" y="-140.8126" font-family="Times,serif" font-size="14.00" fill="#000000"> &nbsp;&nbsp;&nbsp;lua_pop(L, 2);</text>
<text text-anchor="middle" x="254.495" y="-124.0126" font-family="Times,serif" font-size="14.00" fill="#000000"> &nbsp;&nbsp;&nbsp;...</text>
<text text-anchor="middle" x="254.495" y="-107.2126" font-family="Times,serif" font-size="14.00" fill="#000000"> &nbsp;&nbsp;&nbsp;lua_setglobal(L, "ngx")</text>
</g>
<!-- ngx_http_lua_inject_http_consts(L);
 &#160;&#160;&#160;ngx_http_lua_inject_core_consts(L);
 &#160;&#160;&#160;...
 &#160;&#160;&#160;ngx_http_lua_inject_req_api(log, L);
 &#160;&#160;&#160;...
 &#160;&#160;&#160;ngx_http_lua_inject_misc_api(L);&#45;&gt;lua_getglobal(L, &quot;package&quot;); /* ngx package */
 &#160;&#160;&#160;lua_getfield(L, &#45;1, &quot;loaded&quot;); /* ngx package loaded */
 &#160;&#160;&#160;lua_pushvalue(L, &#45;3); /* ngx package loaded ngx */
 &#160;&#160;&#160;lua_setfield(L, &#45;2, &quot;ngx&quot;); /* ngx package loaded */
 &#160;&#160;&#160;lua_pop(L, 2);
 &#160;&#160;&#160;...
 &#160;&#160;&#160;lua_setglobal(L, &quot;ngx&quot;) -->
<g id="edge11" class="edge">
<title>ngx_http_lua_inject_http_consts(L);
 &nbsp;&nbsp;&nbsp;ngx_http_lua_inject_core_consts(L);
 &nbsp;&nbsp;&nbsp;...
 &nbsp;&nbsp;&nbsp;ngx_http_lua_inject_req_api(log, L);
 &nbsp;&nbsp;&nbsp;...
 &nbsp;&nbsp;&nbsp;ngx_http_lua_inject_misc_api(L);-&gt;lua_getglobal(L, "package"); /* ngx package */
 &nbsp;&nbsp;&nbsp;lua_getfield(L, -1, "loaded"); /* ngx package loaded */
 &nbsp;&nbsp;&nbsp;lua_pushvalue(L, -3); /* ngx package loaded ngx */
 &nbsp;&nbsp;&nbsp;lua_setfield(L, -2, "ngx"); /* ngx package loaded */
 &nbsp;&nbsp;&nbsp;lua_pop(L, 2);
 &nbsp;&nbsp;&nbsp;...
 &nbsp;&nbsp;&nbsp;lua_setglobal(L, "ngx")</title>
<path fill="none" stroke="#000000" d="M254.495,-287.6198C254.495,-278.911 254.495,-269.9463 254.495,-261.0043"></path>
<polygon fill="#000000" stroke="#000000" points="257.9951,-260.9329 254.495,-250.9329 250.9951,-260.933 257.9951,-260.9329"></polygon>
</g>
<!-- ngx_http_lua_inject_coroutine_api(log, L) -->
<g id="node12" class="node">
<title>ngx_http_lua_inject_coroutine_api(log, L)</title>
<ellipse fill="none" stroke="#000000" cx="387.495" cy="-18" rx="175.9137" ry="18"></ellipse>
<text text-anchor="middle" x="387.495" y="-13.8" font-family="Times,serif" font-size="14.00" fill="#000000">ngx_http_lua_inject_coroutine_api(log, L)</text>
</g>
<!-- lua_getglobal(L, &quot;package&quot;); /* ngx package */
 &#160;&#160;&#160;lua_getfield(L, &#45;1, &quot;loaded&quot;); /* ngx package loaded */
 &#160;&#160;&#160;lua_pushvalue(L, &#45;3); /* ngx package loaded ngx */
 &#160;&#160;&#160;lua_setfield(L, &#45;2, &quot;ngx&quot;); /* ngx package loaded */
 &#160;&#160;&#160;lua_pop(L, 2);
 &#160;&#160;&#160;...
 &#160;&#160;&#160;lua_setglobal(L, &quot;ngx&quot;)&#45;&gt;ngx_http_lua_inject_coroutine_api(log, L) -->
<g id="edge12" class="edge">
<title>lua_getglobal(L, "package"); /* ngx package */
 &nbsp;&nbsp;&nbsp;lua_getfield(L, -1, "loaded"); /* ngx package loaded */
 &nbsp;&nbsp;&nbsp;lua_pushvalue(L, -3); /* ngx package loaded ngx */
 &nbsp;&nbsp;&nbsp;lua_setfield(L, -2, "ngx"); /* ngx package loaded */
 &nbsp;&nbsp;&nbsp;lua_pop(L, 2);
 &nbsp;&nbsp;&nbsp;...
 &nbsp;&nbsp;&nbsp;lua_setglobal(L, "ngx")-&gt;ngx_http_lua_inject_coroutine_api(log, L)</title>
<path fill="none" stroke="#000000" d="M332.2465,-77.7401C343.7293,-65.3237 354.7512,-53.4058 363.8885,-43.5257"></path>
<polygon fill="#000000" stroke="#000000" points="366.565,-45.7864 370.7852,-36.0683 361.4258,-41.0336 366.565,-45.7864"></polygon>
</g>
<!-- ngx_http_lua_inject_coroutine_api(log, L)&#45;&gt;ngx_http_lua_inject_ngx_api(L, lmcf, log) -->
<g id="edge13" class="edge">
<title>ngx_http_lua_inject_coroutine_api(log, L)-&gt;ngx_http_lua_inject_ngx_api(L, lmcf, log)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M447.4779,-35.0051C467.3489,-43.428 487.8499,-55.6352 501.495,-73 526.4353,-104.7388 520.495,-121.4472 520.495,-161.8126 520.495,-731.2917 520.495,-731.2917 520.495,-731.2917 520.495,-755.5056 502.0977,-772.0281 480.0198,-783.1382"></path>
<polygon fill="#000000" stroke="#000000" points="478.3366,-780.0601 470.7232,-787.4281 481.2696,-786.4161 478.3366,-780.0601"></polygon>
<text text-anchor="middle" x="532.9403" y="-536.6917" font-family="Times,serif" font-size="14.00" fill="#000000">void</text>
</g>
</g>
</svg>


### access_by_lua指令的执行流程
access_by_lua指令由ngx_http_lua_access_handler_inline函数实现。主要流程如下：
1. 调用ngx_http_lua_cache_loadbuffer加载代码。
2. 调用ngx_http_lua_access_by_chunk(L, r)执行。
3. ngx_http_lua_access_by_chunk(L, r)首先创建一个coroutine，这样就不会阻塞主进程。
4. 然后调用ngx_http_lua_run_thread执行。
5. 最后根据执行结果进行善后并返回。

详细的调用逻辑可以参考下图：

<svg width="1674pt" height="1177pt" viewBox="0 0 1674 1177" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
<g id="graph0" class="graph" transform="scale(1 1) rotate(0) translate(4 1172.6)">
<title>%0</title>
<polygon fill="#ffffff" stroke="transparent" points="-4,4 -4,-1172.6 1670.3055,-1172.6 1670.3055,4 -4,4"></polygon>
<g id="clust1" class="cluster">
<title>cluster_ngx_http_lua_access_by_chunk</title>
<polygon fill="none" stroke="#000000" points="24.166,-8 24.166,-1160.6 1272.166,-1160.6 1272.166,-8 24.166,-8"></polygon>
</g>
<!-- 入口函数ngx_http_lua_access_handler_inline -->
<g id="node1" class="node">
<title>入口函数ngx_http_lua_access_handler_inline</title>
<ellipse fill="#00ff00" stroke="#000000" cx="1473.166" cy="-1061.6" rx="193.279" ry="18"></ellipse>
<text text-anchor="middle" x="1473.166" y="-1057.4" font-family="Times,serif" font-size="14.00" fill="#000000">入口函数ngx_http_lua_access_handler_inline</text>
</g>
<!-- L = ngx_http_lua_get_lua_vm(r, NULL) -->
<g id="node2" class="node">
<title>L = ngx_http_lua_get_lua_vm(r, NULL)</title>
<ellipse fill="none" stroke="#000000" cx="1461.166" cy="-988.6" rx="168.6274" ry="18"></ellipse>
<text text-anchor="middle" x="1461.166" y="-984.4" font-family="Times,serif" font-size="14.00" fill="#000000">L = ngx_http_lua_get_lua_vm(r, NULL)</text>
</g>
<!-- 入口函数ngx_http_lua_access_handler_inline&#45;&gt;L = ngx_http_lua_get_lua_vm(r, NULL) -->
<g id="edge1" class="edge">
<title>入口函数ngx_http_lua_access_handler_inline-&gt;L = ngx_http_lua_get_lua_vm(r, NULL)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M1470.1997,-1043.5551C1468.8728,-1035.4828 1467.2772,-1025.7764 1465.7987,-1016.7817"></path>
<polygon fill="#000000" stroke="#000000" points="1469.2156,-1015.9902 1464.1398,-1006.6904 1462.3083,-1017.1257 1469.2156,-1015.9902"></polygon>
</g>
<!-- rc = ngx_http_lua_cache_loadbuffer -->
<g id="node3" class="node">
<title>rc = ngx_http_lua_cache_loadbuffer</title>
<ellipse fill="none" stroke="#000000" cx="1446.166" cy="-915.6" rx="151.7505" ry="18"></ellipse>
<text text-anchor="middle" x="1446.166" y="-911.4" font-family="Times,serif" font-size="14.00" fill="#000000">rc = ngx_http_lua_cache_loadbuffer</text>
</g>
<!-- L = ngx_http_lua_get_lua_vm(r, NULL)&#45;&gt;rc = ngx_http_lua_cache_loadbuffer -->
<g id="edge2" class="edge">
<title>L = ngx_http_lua_get_lua_vm(r, NULL)-&gt;rc = ngx_http_lua_cache_loadbuffer</title>
<path fill="none" stroke="#000000" d="M1457.4582,-970.5551C1455.7815,-962.3951 1453.7616,-952.5652 1451.8966,-943.4887"></path>
<polygon fill="#000000" stroke="#000000" points="1455.3244,-942.7812 1449.8832,-933.6904 1448.4677,-944.1902 1455.3244,-942.7812"></polygon>
</g>
<!-- ngx_http_lua_access_by_chunk(L, r) -->
<g id="node4" class="node">
<title>ngx_http_lua_access_by_chunk(L, r)</title>
<ellipse fill="none" stroke="#000000" cx="670.166" cy="-1134.6" rx="154.5509" ry="18"></ellipse>
<text text-anchor="middle" x="670.166" y="-1130.4" font-family="Times,serif" font-size="14.00" fill="#000000">ngx_http_lua_access_by_chunk(L, r)</text>
</g>
<!-- rc = ngx_http_lua_cache_loadbuffer&#45;&gt;ngx_http_lua_access_by_chunk(L, r) -->
<g id="edge3" class="edge">
<title>rc = ngx_http_lua_cache_loadbuffer-&gt;ngx_http_lua_access_by_chunk(L, r)</title>
<path fill="none" stroke="#000000" d="M1378.9528,-931.7435C1348.4988,-940.6722 1312.769,-953.4804 1283.166,-970.6 1208.7518,-1013.6344 1217.3425,-1065.1257 1138.166,-1098.6 1109.6197,-1110.6688 949.7303,-1121.1068 824.6821,-1127.5902"></path>
<polygon fill="#000000" stroke="#000000" points="824.4099,-1124.0995 814.6027,-1128.1082 824.7692,-1131.0902 824.4099,-1124.0995"></polygon>
</g>
<!-- ngx_http_lua_access_by_chunk(L, r)&#45;&gt;入口函数ngx_http_lua_access_handler_inline -->
<g id="edge4" class="edge">
<title>ngx_http_lua_access_by_chunk(L, r)-&gt;入口函数ngx_http_lua_access_handler_inline</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M815.4524,-1128.3057C926.7097,-1122.7118 1083.8901,-1113.1384 1221.166,-1098.6 1269.7126,-1093.4586 1323.254,-1085.8705 1368.4536,-1078.9022"></path>
<polygon fill="#000000" stroke="#000000" points="1369.2155,-1082.3259 1378.5601,-1077.3331 1368.1415,-1075.4088 1369.2155,-1082.3259"></polygon>
</g>
<!-- co = ngx_http_lua_new_thread(r, L, &amp;co_ref) -->
<g id="node6" class="node">
<title>co = ngx_http_lua_new_thread(r, L, &amp;co_ref)</title>
<ellipse fill="none" stroke="#ff0000" cx="670.166" cy="-988.6" rx="188.7729" ry="18"></ellipse>
<text text-anchor="middle" x="670.166" y="-984.4" font-family="Times,serif" font-size="14.00" fill="#000000">co = ngx_http_lua_new_thread(r, L, &amp;co_ref)</text>
</g>
<!-- ngx_http_lua_access_by_chunk(L, r)&#45;&gt;co = ngx_http_lua_new_thread(r, L, &amp;co_ref) -->
<g id="edge5" class="edge">
<title>ngx_http_lua_access_by_chunk(L, r)-&gt;co = ngx_http_lua_new_thread(r, L, &amp;co_ref)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M670.166,-1116.4042C670.166,-1091.4868 670.166,-1046.4938 670.166,-1017.0877"></path>
<polygon fill="#000000" stroke="#000000" points="673.6661,-1017.0251 670.166,-1007.0251 666.6661,-1017.0251 673.6661,-1017.0251"></polygon>
</g>
<!-- rc -->
<g id="node5" class="node">
<title>rc</title>
<polygon fill="none" stroke="#000000" points="670.166,-385 643.166,-367 670.166,-349 697.166,-367 670.166,-385"></polygon>
<text text-anchor="middle" x="670.166" y="-362.8" font-family="Times,serif" font-size="14.00" fill="#000000">rc</text>
</g>
<!-- rc == NGX_ERROR || rc &gt; NGX_OK -->
<g id="node11" class="node">
<title>rc == NGX_ERROR || rc &gt; NGX_OK</title>
<ellipse fill="none" stroke="#000000" cx="379.166" cy="-256" rx="158.1834" ry="18"></ellipse>
<text text-anchor="middle" x="379.166" y="-251.8" font-family="Times,serif" font-size="14.00" fill="#000000">rc == NGX_ERROR || rc &gt; NGX_OK</text>
</g>
<!-- rc&#45;&gt;rc == NGX_ERROR || rc &gt; NGX_OK -->
<g id="edge11" class="edge">
<title>rc-&gt;rc == NGX_ERROR || rc &gt; NGX_OK</title>
<path fill="none" stroke="#000000" d="M652.8341,-360.3889C610.6171,-344.2854 501.0193,-302.4801 434.0269,-276.9263"></path>
<polygon fill="#000000" stroke="#000000" points="435.0558,-273.5729 424.4651,-273.279 432.561,-280.1132 435.0558,-273.5729"></polygon>
</g>
<!-- rc == NGX_AGAIN -->
<g id="node12" class="node">
<title>rc == NGX_AGAIN</title>
<ellipse fill="none" stroke="#000000" cx="1004.166" cy="-256" rx="91.2007" ry="18"></ellipse>
<text text-anchor="middle" x="1004.166" y="-251.8" font-family="Times,serif" font-size="14.00" fill="#000000">rc == NGX_AGAIN</text>
</g>
<!-- rc&#45;&gt;rc == NGX_AGAIN -->
<g id="edge13" class="edge">
<title>rc-&gt;rc == NGX_AGAIN</title>
<path fill="none" stroke="#000000" d="M681.0106,-355.7338C689.5622,-347.4719 702.1769,-336.5884 715.166,-330 752.4986,-311.0641 857.9118,-286.5373 930.544,-271.0301"></path>
<polygon fill="#000000" stroke="#000000" points="931.5093,-274.4033 940.5647,-268.9033 930.0559,-267.5558 931.5093,-274.4033"></polygon>
</g>
<!-- rc == NGX_DONE -->
<g id="node15" class="node">
<title>rc == NGX_DONE</title>
<ellipse fill="none" stroke="#000000" cx="642.166" cy="-256" rx="86.6276" ry="18"></ellipse>
<text text-anchor="middle" x="642.166" y="-251.8" font-family="Times,serif" font-size="14.00" fill="#000000">rc == NGX_DONE</text>
</g>
<!-- rc&#45;&gt;rc == NGX_DONE -->
<g id="edge16" class="edge">
<title>rc-&gt;rc == NGX_DONE</title>
<path fill="none" stroke="#000000" d="M666.2728,-351.5659C661.8822,-334.1604 654.6519,-305.4974 649.2529,-284.0943"></path>
<polygon fill="#000000" stroke="#000000" points="652.6097,-283.0918 646.7701,-274.2517 645.8224,-284.804 652.6097,-283.0918"></polygon>
</g>
<!-- rc == NGX_OK -->
<g id="node17" class="node">
<title>rc == NGX_OK</title>
<ellipse fill="none" stroke="#000000" cx="821.166" cy="-256" rx="73.8857" ry="18"></ellipse>
<text text-anchor="middle" x="821.166" y="-251.8" font-family="Times,serif" font-size="14.00" fill="#000000">rc == NGX_OK</text>
</g>
<!-- rc&#45;&gt;rc == NGX_OK -->
<g id="edge20" class="edge">
<title>rc-&gt;rc == NGX_OK</title>
<path fill="none" stroke="#000000" d="M683.0402,-357.5362C706.413,-340.3549 756.4538,-303.5699 789.4484,-279.3156"></path>
<polygon fill="#000000" stroke="#000000" points="791.8699,-281.8795 797.8542,-273.1365 787.7239,-276.2394 791.8699,-281.8795"></polygon>
</g>
<!-- lua_xmove(L, co, 1) -->
<g id="node8" class="node">
<title>lua_xmove(L, co, 1)</title>
<ellipse fill="none" stroke="#000000" cx="670.166" cy="-842.6" rx="89.7556" ry="18"></ellipse>
<text text-anchor="middle" x="670.166" y="-838.4" font-family="Times,serif" font-size="14.00" fill="#000000">lua_xmove(L, co, 1)</text>
</g>
<!-- co = ngx_http_lua_new_thread(r, L, &amp;co_ref)&#45;&gt;lua_xmove(L, co, 1) -->
<g id="edge6" class="edge">
<title>co = ngx_http_lua_new_thread(r, L, &amp;co_ref)-&gt;lua_xmove(L, co, 1)</title>
<path fill="none" stroke="#000000" d="M670.166,-970.4042C670.166,-945.4868 670.166,-900.4938 670.166,-871.0877"></path>
<polygon fill="#000000" stroke="#000000" points="673.6661,-871.0251 670.166,-861.0251 666.6661,-871.0251 673.6661,-871.0251"></polygon>
</g>
<!-- rc = ngx_http_lua_run_thread(L, r, ctx, 0) -->
<g id="node7" class="node">
<title>rc = ngx_http_lua_run_thread(L, r, ctx, 0)</title>
<ellipse fill="none" stroke="#ff0000" cx="670.166" cy="-478" rx="173.1833" ry="18"></ellipse>
<text text-anchor="middle" x="670.166" y="-473.8" font-family="Times,serif" font-size="14.00" fill="#000000">rc = ngx_http_lua_run_thread(L, r, ctx, 0)</text>
</g>
<!-- rc = ngx_http_lua_run_thread(L, r, ctx, 0)&#45;&gt;rc -->
<g id="edge10" class="edge">
<title>rc = ngx_http_lua_run_thread(L, r, ctx, 0)-&gt;rc</title>
<path fill="none" stroke="#000000" d="M670.166,-459.6706C670.166,-442.2373 670.166,-415.7482 670.166,-395.5489"></path>
<polygon fill="#000000" stroke="#000000" points="673.6661,-395.3566 670.166,-385.3566 666.6661,-395.3567 673.6661,-395.3566"></polygon>
</g>
<!-- ngx_http_lua_get_globals_table(co) -->
<g id="node9" class="node">
<title>ngx_http_lua_get_globals_table(co)</title>
<ellipse fill="none" stroke="#000000" cx="670.166" cy="-731.6" rx="150.4907" ry="18"></ellipse>
<text text-anchor="middle" x="670.166" y="-727.4" font-family="Times,serif" font-size="14.00" fill="#000000">ngx_http_lua_get_globals_table(co)</text>
</g>
<!-- lua_xmove(L, co, 1)&#45;&gt;ngx_http_lua_get_globals_table(co) -->
<g id="edge7" class="edge">
<title>lua_xmove(L, co, 1)-&gt;ngx_http_lua_get_globals_table(co)</title>
<path fill="none" stroke="#000000" d="M670.166,-824.2706C670.166,-806.8373 670.166,-780.3482 670.166,-760.1489"></path>
<polygon fill="#000000" stroke="#000000" points="673.6661,-759.9566 670.166,-749.9566 666.6661,-759.9567 673.6661,-759.9566"></polygon>
</g>
<!-- ngx_http_lua_set_req(co, r) -->
<g id="node10" class="node">
<title>ngx_http_lua_set_req(co, r)</title>
<ellipse fill="none" stroke="#000000" cx="670.166" cy="-604.8" rx="118.1062" ry="18"></ellipse>
<text text-anchor="middle" x="670.166" y="-600.6" font-family="Times,serif" font-size="14.00" fill="#000000">ngx_http_lua_set_req(co, r)</text>
</g>
<!-- ngx_http_lua_get_globals_table(co)&#45;&gt;ngx_http_lua_set_req(co, r) -->
<g id="edge8" class="edge">
<title>ngx_http_lua_get_globals_table(co)-&gt;ngx_http_lua_set_req(co, r)</title>
<path fill="none" stroke="#000000" d="M670.166,-713.4327C670.166,-692.4352 670.166,-657.6261 670.166,-633.079"></path>
<polygon fill="#000000" stroke="#000000" points="673.6661,-632.9336 670.166,-622.9336 666.6661,-632.9337 673.6661,-632.9336"></polygon>
</g>
<!-- ngx_http_lua_set_req(co, r)&#45;&gt;rc = ngx_http_lua_run_thread(L, r, ctx, 0) -->
<g id="edge9" class="edge">
<title>ngx_http_lua_set_req(co, r)-&gt;rc = ngx_http_lua_run_thread(L, r, ctx, 0)</title>
<path fill="none" stroke="#000000" d="M670.166,-586.6327C670.166,-565.6352 670.166,-530.8261 670.166,-506.279"></path>
<polygon fill="#000000" stroke="#000000" points="673.6661,-506.1336 670.166,-496.1336 666.6661,-506.1337 673.6661,-506.1336"></polygon>
</g>
<!-- rc == NGX_ERROR || rc &gt; NGX_OK&#45;&gt;ngx_http_lua_access_by_chunk(L, r) -->
<g id="edge12" class="edge">
<title>rc == NGX_ERROR || rc &gt; NGX_OK-&gt;ngx_http_lua_access_by_chunk(L, r)</title>
<path fill="none" stroke="#000000" d="M401.5854,-274.1768C411.1573,-284.0313 420.166,-297.1246 420.166,-311.5 420.166,-1061.6 420.166,-1061.6 420.166,-1061.6 420.166,-1091.0533 484.9127,-1109.4346 548.5059,-1120.3852"></path>
<polygon fill="#000000" stroke="#000000" points="548.2535,-1123.8912 558.6923,-1122.0794 549.4021,-1116.9861 548.2535,-1123.8912"></polygon>
<text text-anchor="middle" x="425.6036" y="-664" font-family="Times,serif" font-size="14.00" fill="#000000">rc</text>
</g>
<!--  rc = ngx_http_lua_run_posted_threads(c, L, r, ctx, nreqs) -->
<g id="node13" class="node">
<title> rc = ngx_http_lua_run_posted_threads(c, L, r, ctx, nreqs)</title>
<ellipse fill="none" stroke="#000000" cx="1009.166" cy="-145" rx="234.5041" ry="18"></ellipse>
<text text-anchor="middle" x="1009.166" y="-140.8" font-family="Times,serif" font-size="14.00" fill="#000000"> rc = ngx_http_lua_run_posted_threads(c, L, r, ctx, nreqs)</text>
</g>
<!-- rc == NGX_AGAIN&#45;&gt; rc = ngx_http_lua_run_posted_threads(c, L, r, ctx, nreqs) -->
<g id="edge14" class="edge">
<title>rc == NGX_AGAIN-&gt; rc = ngx_http_lua_run_posted_threads(c, L, r, ctx, nreqs)</title>
<path fill="none" stroke="#000000" d="M1004.9917,-237.6706C1005.777,-220.2373 1006.9702,-193.7482 1007.8801,-173.5489"></path>
<polygon fill="#000000" stroke="#000000" points="1011.3855,-173.504 1008.3392,-163.3566 1004.3926,-173.189 1011.3855,-173.504"></polygon>
</g>
<!-- rc = ngx_http_lua_run_posted_threads(c, L, r, ctx, nreqs) -->
<g id="node14" class="node">
<title>rc = ngx_http_lua_run_posted_threads(c, L, r, ctx, nreqs)</title>
<ellipse fill="none" stroke="#000000" cx="561.166" cy="-34" rx="232.1757" ry="18"></ellipse>
<text text-anchor="middle" x="561.166" y="-29.8" font-family="Times,serif" font-size="14.00" fill="#000000">rc = ngx_http_lua_run_posted_threads(c, L, r, ctx, nreqs)</text>
</g>
<!-- rc = ngx_http_lua_run_posted_threads(c, L, r, ctx, nreqs)&#45;&gt;ngx_http_lua_access_by_chunk(L, r) -->
<g id="edge15" class="edge">
<title>rc = ngx_http_lua_run_posted_threads(c, L, r, ctx, nreqs)-&gt;ngx_http_lua_access_by_chunk(L, r)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M737.5123,-45.7617C931.2472,-60.5178 1219.5991,-88.6202 1253.166,-127 1361.702,-251.0983 1133.166,-313.1351 1133.166,-478 1133.166,-1061.6 1133.166,-1061.6 1133.166,-1061.6 1133.166,-1094.1492 952.3155,-1114.5271 816.6721,-1125.2336"></path>
<polygon fill="#000000" stroke="#000000" points="816.2754,-1121.7538 806.5771,-1126.0192 816.8186,-1128.7327 816.2754,-1121.7538"></polygon>
<text text-anchor="middle" x="1198.5495" y="-537.2" font-family="Times,serif" font-size="14.00" fill="#000000">NGX_DECLINED || rc</text>
</g>
<!-- rc = ngx_http_lua_run_posted_threads(c, L, r, ctx, nreqs)&#45;&gt;ngx_http_lua_access_by_chunk(L, r) -->
<g id="edge19" class="edge">
<title>rc = ngx_http_lua_run_posted_threads(c, L, r, ctx, nreqs)-&gt;ngx_http_lua_access_by_chunk(L, r)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M365.1332,-43.6447C236.354,-54.5615 84.3477,-77.7663 43.166,-127 -56.9427,-246.6826 50.166,-321.969 50.166,-478 50.166,-1061.6 50.166,-1061.6 50.166,-1061.6 50.166,-1107.695 323.9591,-1124.7638 507.6572,-1131.0284"></path>
<polygon fill="#000000" stroke="#000000" points="507.5918,-1134.5281 517.7029,-1131.3633 507.8251,-1127.532 507.5918,-1134.5281"></polygon>
<text text-anchor="middle" x="115.5495" y="-537.2" font-family="Times,serif" font-size="14.00" fill="#000000">NGX_DECLINED || rc</text>
</g>
<!-- ngx_http_lua_finalize_request(r, NGX_DONE) -->
<g id="node16" class="node">
<title>ngx_http_lua_finalize_request(r, NGX_DONE)</title>
<ellipse fill="none" stroke="#000000" cx="561.166" cy="-145" rx="195.5533" ry="18"></ellipse>
<text text-anchor="middle" x="561.166" y="-140.8" font-family="Times,serif" font-size="14.00" fill="#000000">ngx_http_lua_finalize_request(r, NGX_DONE)</text>
</g>
<!-- rc == NGX_DONE&#45;&gt;ngx_http_lua_finalize_request(r, NGX_DONE) -->
<g id="edge17" class="edge">
<title>rc == NGX_DONE-&gt;ngx_http_lua_finalize_request(r, NGX_DONE)</title>
<path fill="none" stroke="#000000" d="M629.152,-238.1659C616.0042,-220.1486 595.539,-192.1037 580.4952,-171.4881"></path>
<polygon fill="#000000" stroke="#000000" points="583.1389,-169.1733 574.4169,-163.1586 577.4843,-173.2997 583.1389,-169.1733"></polygon>
</g>
<!-- ngx_http_lua_finalize_request(r, NGX_DONE)&#45;&gt;rc = ngx_http_lua_run_posted_threads(c, L, r, ctx, nreqs) -->
<g id="edge18" class="edge">
<title>ngx_http_lua_finalize_request(r, NGX_DONE)-&gt;rc = ngx_http_lua_run_posted_threads(c, L, r, ctx, nreqs)</title>
<path fill="none" stroke="#000000" d="M561.166,-126.6706C561.166,-109.2373 561.166,-82.7482 561.166,-62.5489"></path>
<polygon fill="#000000" stroke="#000000" points="564.6661,-62.3566 561.166,-52.3566 557.6661,-62.3567 564.6661,-62.3566"></polygon>
</g>
<!-- rc = ngx_http_lua_send_chain_link -->
<g id="node18" class="node">
<title>rc = ngx_http_lua_send_chain_link</title>
<ellipse fill="none" stroke="#000000" cx="200.166" cy="-145" rx="147.8011" ry="18"></ellipse>
<text text-anchor="middle" x="200.166" y="-140.8" font-family="Times,serif" font-size="14.00" fill="#000000">rc = ngx_http_lua_send_chain_link</text>
</g>
<!-- rc == NGX_OK&#45;&gt;rc = ngx_http_lua_send_chain_link -->
<g id="edge21" class="edge">
<title>rc == NGX_OK-&gt;rc = ngx_http_lua_send_chain_link</title>
<path fill="none" stroke="#000000" d="M766.3189,-243.762C756.9406,-241.7679 747.2838,-239.7759 738.166,-238 585.9147,-208.3454 408.4781,-178.6022 300.2652,-161.0007"></path>
<polygon fill="#000000" stroke="#000000" points="300.7675,-157.5365 290.3357,-159.3881 299.6453,-164.446 300.7675,-157.5365"></polygon>
</g>
<!-- rc = ngx_http_lua_send_chain_link&#45;&gt;ngx_http_lua_access_by_chunk(L, r) -->
<g id="edge22" class="edge">
<title>rc = ngx_http_lua_send_chain_link-&gt;ngx_http_lua_access_by_chunk(L, r)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M200.666,-163.4985C200.916,-174.2897 201.166,-188.1647 201.166,-200.5 201.166,-1061.6 201.166,-1061.6 201.166,-1061.6 201.166,-1094.6362 385.199,-1114.9285 522.7033,-1125.4796"></path>
<polygon fill="#000000" stroke="#000000" points="522.6999,-1128.9893 532.9354,-1126.2535 523.2278,-1122.0092 522.6999,-1128.9893"></polygon>
</g>
</g>
</svg>


### <span id = "ngx_say"> ngx_lua如何实现ngx.say和ngx.sleep </span>
先看一段示例配置：
```    server {
        listen    80;

        location /test2 {
            content_by_lua_block {
                ngx.say("hello")
                ngx.flush() -- 显式的向客户端刷新响应输出
                ngx.sleep(3)
                ngx.say("the world")
            }
        }
```
ngx.say 由ngx_http_lua_inject_output_api通过lua_pushcfunction注册，由gx_http_lua_ngx_echo(lua_State *L, unsigned newline)负责实现。
它的逻辑简述如下：

<svg width="1098pt" height="332pt" viewBox="0 0 1098 332" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
<g id="graph0" class="graph" transform="scale(1 1) rotate(0) translate(4 328)">
<title>%0</title>
<polygon fill="#ffffff" stroke="transparent" points="-4,4 -4,-328 1093.7197,-328 1093.7197,4 -4,4"></polygon>
<!-- r = ngx_http_lua_get_req(L) 获取请求 -->
<g id="node1" class="node">
<title>r = ngx_http_lua_get_req(L) 获取请求</title>
<ellipse fill="none" stroke="#000000" cx="544.8599" cy="-306" rx="167.018" ry="18"></ellipse>
<text text-anchor="middle" x="544.8599" y="-301.8" font-family="Times,serif" font-size="14.00" fill="#000000">r = ngx_http_lua_get_req(L) 获取请求</text>
</g>
<!-- nargs = lua_gettop(L)获取参数总数 -->
<g id="node2" class="node">
<title>nargs = lua_gettop(L)获取参数总数</title>
<ellipse fill="none" stroke="#000000" cx="544.8599" cy="-234" rx="145.7817" ry="18"></ellipse>
<text text-anchor="middle" x="544.8599" y="-229.8" font-family="Times,serif" font-size="14.00" fill="#000000">nargs = lua_gettop(L)获取参数总数</text>
</g>
<!-- r = ngx_http_lua_get_req(L) 获取请求&#45;&gt;nargs = lua_gettop(L)获取参数总数 -->
<g id="edge1" class="edge">
<title>r = ngx_http_lua_get_req(L) 获取请求-&gt;nargs = lua_gettop(L)获取参数总数</title>
<path fill="none" stroke="#000000" d="M544.8599,-287.8314C544.8599,-280.131 544.8599,-270.9743 544.8599,-262.4166"></path>
<polygon fill="#000000" stroke="#000000" points="548.36,-262.4132 544.8599,-252.4133 541.36,-262.4133 548.36,-262.4132"></polygon>
</g>
<!-- for (i = 1; i &lt;= nargs; i++)对参数进行遍历，类型检查和拼接 -->
<g id="node3" class="node">
<title>for (i = 1; i &lt;= nargs; i++)对参数进行遍历，类型检查和拼接</title>
<ellipse fill="none" stroke="#000000" cx="544.8599" cy="-162" rx="266.4737" ry="18"></ellipse>
<text text-anchor="middle" x="544.8599" y="-157.8" font-family="Times,serif" font-size="14.00" fill="#000000">for (i = 1; i &lt;= nargs; i++)对参数进行遍历，类型检查和拼接</text>
</g>
<!-- nargs = lua_gettop(L)获取参数总数&#45;&gt;for (i = 1; i &lt;= nargs; i++)对参数进行遍历，类型检查和拼接 -->
<g id="edge2" class="edge">
<title>nargs = lua_gettop(L)获取参数总数-&gt;for (i = 1; i &lt;= nargs; i++)对参数进行遍历，类型检查和拼接</title>
<path fill="none" stroke="#000000" d="M544.8599,-215.8314C544.8599,-208.131 544.8599,-198.9743 544.8599,-190.4166"></path>
<polygon fill="#000000" stroke="#000000" points="548.36,-190.4132 544.8599,-180.4133 541.36,-190.4133 548.36,-190.4132"></polygon>
</g>
<!-- rc = ngx_http_lua_send_chain_link(r, ctx, cl)内容发送 -->
<g id="node4" class="node">
<title>rc = ngx_http_lua_send_chain_link(r, ctx, cl)内容发送</title>
<ellipse fill="none" stroke="#000000" cx="544.8599" cy="-90" rx="224.1513" ry="18"></ellipse>
<text text-anchor="middle" x="544.8599" y="-85.8" font-family="Times,serif" font-size="14.00" fill="#000000">rc = ngx_http_lua_send_chain_link(r, ctx, cl)内容发送</text>
</g>
<!-- for (i = 1; i &lt;= nargs; i++)对参数进行遍历，类型检查和拼接&#45;&gt;rc = ngx_http_lua_send_chain_link(r, ctx, cl)内容发送 -->
<g id="edge3" class="edge">
<title>for (i = 1; i &lt;= nargs; i++)对参数进行遍历，类型检查和拼接-&gt;rc = ngx_http_lua_send_chain_link(r, ctx, cl)内容发送</title>
<path fill="none" stroke="#000000" d="M544.8599,-143.8314C544.8599,-136.131 544.8599,-126.9743 544.8599,-118.4166"></path>
<polygon fill="#000000" stroke="#000000" points="548.36,-118.4132 544.8599,-108.4133 541.36,-118.4133 548.36,-118.4132"></polygon>
</g>
<!-- 这个函数就是nginx发送内容的逻辑类似，会根据条件调用ngx_http_lua_send_special,ngx_http_lua_output_filter等发送响应 -->
<g id="node5" class="node">
<title>这个函数就是nginx发送内容的逻辑类似，会根据条件调用ngx_http_lua_send_special,ngx_http_lua_output_filter等发送响应</title>
<ellipse fill="none" stroke="#000000" cx="544.8599" cy="-18" rx="544.7197" ry="18"></ellipse>
<text text-anchor="middle" x="544.8599" y="-13.8" font-family="Times,serif" font-size="14.00" fill="#000000">这个函数就是nginx发送内容的逻辑类似，会根据条件调用ngx_http_lua_send_special,ngx_http_lua_output_filter等发送响应</text>
</g>
<!-- rc = ngx_http_lua_send_chain_link(r, ctx, cl)内容发送&#45;&gt;这个函数就是nginx发送内容的逻辑类似，会根据条件调用ngx_http_lua_send_special,ngx_http_lua_output_filter等发送响应 -->
<g id="edge4" class="edge">
<title>rc = ngx_http_lua_send_chain_link(r, ctx, cl)内容发送-&gt;这个函数就是nginx发送内容的逻辑类似，会根据条件调用ngx_http_lua_send_special,ngx_http_lua_output_filter等发送响应</title>
<path fill="none" stroke="#000000" d="M544.8599,-71.8314C544.8599,-64.131 544.8599,-54.9743 544.8599,-46.4166"></path>
<polygon fill="#000000" stroke="#000000" points="548.36,-46.4132 544.8599,-36.4133 541.36,-46.4133 548.36,-46.4132"></polygon>
</g>
</g>
</svg>

<span id=kong > #KONG(API GW)的实现</span>
## KONG的参考配置
```
# Comments begin with the letters #! for special comment, do not delete it.

upstream kong_upstream {
    server 0.0.0.1;
    balancer_by_lua_block {
       kong.balancer()
    }
    keepalive 19;
}

server {

    listen 80;
    listen 443 ssl;
    server_name apigw.qq.com;
    ssl_certificate /usr/local/l7/l7_nginx/cert/default.pem;
    ssl_certificate_key /usr/local/l7/l7_nginx/cert/default.key;

    ssl_certificate_by_lua_block {
        kong.ssl_certificate()
    }

    location / {

        set $upstream_host               '';
        set $upstream_upgrade            '';
        set $upstream_connection         '';
        set $upstream_scheme             '';
        set $upstream_uri                '';
        set $upstream_x_forwarded_for    '';
        set $upstream_x_forwarded_proto  '';
        set $upstream_x_forwarded_host   '';
        set $upstream_x_forwarded_port   '';

        access_by_lua_block {
           kong.access()
        }

        header_filter_by_lua_block {
           kong.header_filter()
        }

        body_filter_by_lua_block {
            kong.body_filter()
        }

        log_by_lua_block {
           kong.log()
        }

        proxy_http_version 1.1;
        proxy_set_header   Host               $upstream_host;
        proxy_set_header   X-Real-IP          $remote_addr;
        proxy_pass_header  Server;
        proxy_pass         $upstream_scheme://kong_upstream$upstream_uri;
    }
}
```
### kong的启动
kong安装完后，bin/目录下有一个kon的lua执行脚本。
```
#!/usr/bin/env resty

require "luarocks.loader"

package.path = "./?.lua;./?/init.lua;" .. package.path

require("kong.cmd.init")(arg)

```
执行./bin/kong start，会调用 kong/cmd/init.lua,该脚本会接着调用
kong/cmd/start.lua。  
start.lua接着调用kong/cmd/utils/nginx_signals.lua。
这个脚本会寻找openresty路径，并启动nginx。
```
cmd = fmt("%s -p %s -c %s", nginx_bin, kong_conf.prefix, "nginx.conf")
```
### kong如何实现路由
说一下核心的逻辑。
路由查找都是放在内存中实现lrucache。key是request的方法，URL 和HOST。
```
local cache_key = fmt("%s:%s:%s", req_method, req_uri, req_host)
do  
   local match_t = cache:get(cache_key)
   if match_t then
     return match_t
   end
 end
 ```

如果没有查找到，会更新相应的cache。
```
local match_t    = {
  api            = matched_api.api,
  upstream_url_t = upstream_url_t,
  upstream_uri   = upstream_uri,
  upstream_host  = upstream_host,
  matches        = {
    uri_captures = matches.uri_captures,
    uri          = matches.uri,
    host         = matches.host,
    method       = matches.method,
  }
}

cache:set(cache_key, match_t)

```
代码逻辑如下：
<!DOCTYPE svg [<!ENTITY nbsp "&#160;">]><svg width="1841pt" height="913pt" viewBox="0 0 1841 913" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
<g id="graph0" class="graph" transform="scale(1 1) rotate(0) translate(4 908.8214)">
<title>%0</title>
<polygon fill="#ffffff" stroke="transparent" points="-4,4 -4,-908.8214 1837,-908.8214 1837,4 -4,4"></polygon>
<g id="clust1" class="cluster">
<title>cluster_access_before</title>
<polygon fill="none" stroke="#000000" points="286,-318.8 286,-839.8214 730,-839.8214 730,-318.8 286,-318.8"></polygon>
</g>
<g id="clust2" class="cluster">
<title>cluster_find_api</title>
<polygon fill="none" stroke="#000000" points="8,-8 8,-297.8 570,-297.8 570,-8 8,-8"></polygon>
</g>
<g id="clust3" class="cluster">
<title>cluster_access_after</title>
<polygon fill="none" stroke="#000000" points="889,-318.8 889,-608.6 1825,-608.6 1825,-318.8 889,-318.8"></polygon>
</g>
<!-- kong.access -->
<g id="node1" class="node">
<title>kong.access</title>
<ellipse fill="#00ff00" stroke="#000000" cx="996" cy="-886.8214" rx="57.3455" ry="18"></ellipse>
<text text-anchor="middle" x="996" y="-882.6214" font-family="Times,serif" font-size="14.00" fill="#000000">kong.access</text>
</g>
<!-- core.access.before(ctx)
 &#160;&#160;&#160;定义在kong/core/handler.lua -->
<g id="node2" class="node">
<title>core.access.before(ctx)
 &nbsp;&nbsp;&nbsp;定义在kong/core/handler.lua</title>
<ellipse fill="none" stroke="#000000" cx="585" cy="-802.4057" rx="137.4992" ry="29.3315"></ellipse>
<text text-anchor="middle" x="585" y="-806.6057" font-family="Times,serif" font-size="14.00" fill="#000000">core.access.before(ctx)</text>
<text text-anchor="middle" x="585" y="-789.8057" font-family="Times,serif" font-size="14.00" fill="#000000"> &nbsp;&nbsp;&nbsp;定义在kong/core/handler.lua</text>
</g>
<!-- kong.access&#45;&gt;core.access.before(ctx)
 &#160;&#160;&#160;定义在kong/core/handler.lua -->
<g id="edge1" class="edge">
<title>kong.access-&gt;core.access.before(ctx)
 &nbsp;&nbsp;&nbsp;定义在kong/core/handler.lua</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M947.6445,-876.8896C885.7586,-864.1788 776.7099,-841.7812 694.3079,-824.8566"></path>
<polygon fill="#000000" stroke="#000000" points="694.8956,-821.4043 684.3959,-822.8207 693.4872,-828.2611 694.8956,-821.4043"></polygon>
</g>
<!-- for plugin, plugin_conf in plugins_iterator(singletons.loaded_plugins, true) do
 &#160;&#160;&#160;&#160;&#160;&#160;&#160;plugin.handler:access(plugin_conf)
 &#160;&#160;&#160;遍历执行各个插件 -->
<g id="node3" class="node">
<title>for plugin, plugin_conf in plugins_iterator(singletons.loaded_plugins, true) do
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;plugin.handler:access(plugin_conf)
 &nbsp;&nbsp;&nbsp;遍历执行各个插件</title>
<ellipse fill="none" stroke="#000000" cx="1059" cy="-678.895" rx="321.3438" ry="41.0911"></ellipse>
<text text-anchor="middle" x="1059" y="-691.495" font-family="Times,serif" font-size="14.00" fill="#000000">for plugin, plugin_conf in plugins_iterator(singletons.loaded_plugins, true) do</text>
<text text-anchor="middle" x="1059" y="-674.695" font-family="Times,serif" font-size="14.00" fill="#000000"> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;plugin.handler:access(plugin_conf)</text>
<text text-anchor="middle" x="1059" y="-657.895" font-family="Times,serif" font-size="14.00" fill="#000000"> &nbsp;&nbsp;&nbsp;遍历执行各个插件</text>
</g>
<!-- core.access.before(ctx)
 &#160;&#160;&#160;定义在kong/core/handler.lua&#45;&gt;for plugin, plugin_conf in plugins_iterator(singletons.loaded_plugins, true) do
 &#160;&#160;&#160;&#160;&#160;&#160;&#160;plugin.handler:access(plugin_conf)
 &#160;&#160;&#160;遍历执行各个插件 -->
<g id="edge2" class="edge">
<title>core.access.before(ctx)
 &nbsp;&nbsp;&nbsp;定义在kong/core/handler.lua-&gt;for plugin, plugin_conf in plugins_iterator(singletons.loaded_plugins, true) do
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;plugin.handler:access(plugin_conf)
 &nbsp;&nbsp;&nbsp;遍历执行各个插件</title>
<path fill="none" stroke="#000000" d="M672.4913,-779.608C738.0341,-762.5295 829.3616,-738.7322 906.7847,-718.5579"></path>
<polygon fill="#000000" stroke="#000000" points="907.8451,-721.8986 916.6394,-715.9901 906.08,-715.1248 907.8451,-721.8986"></polygon>
</g>
<!-- build_router(singletons.dao, version) -->
<g id="node5" class="node">
<title>build_router(singletons.dao, version)</title>
<ellipse fill="none" stroke="#000000" cx="554" cy="-582.6" rx="153.9591" ry="18"></ellipse>
<text text-anchor="middle" x="554" y="-578.4" font-family="Times,serif" font-size="14.00" fill="#000000">build_router(singletons.dao, version)</text>
</g>
<!-- core.access.before(ctx)
 &#160;&#160;&#160;定义在kong/core/handler.lua&#45;&gt;build_router(singletons.dao, version) -->
<g id="edge5" class="edge">
<title>core.access.before(ctx)
 &nbsp;&nbsp;&nbsp;定义在kong/core/handler.lua-&gt;build_router(singletons.dao, version)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M580.8192,-772.7617C574.8139,-730.1812 563.8243,-652.2589 557.9498,-610.6064"></path>
<polygon fill="#000000" stroke="#000000" points="561.401,-610.0139 556.5387,-600.6007 554.4696,-610.9916 561.401,-610.0139"></polygon>
</g>
<!-- core.access.after(ctx) -->
<g id="node4" class="node">
<title>core.access.after(ctx)</title>
<ellipse fill="none" stroke="#000000" cx="1351" cy="-582.6" rx="93.1098" ry="18"></ellipse>
<text text-anchor="middle" x="1351" y="-578.4" font-family="Times,serif" font-size="14.00" fill="#000000">core.access.after(ctx)</text>
</g>
<!-- for plugin, plugin_conf in plugins_iterator(singletons.loaded_plugins, true) do
 &#160;&#160;&#160;&#160;&#160;&#160;&#160;plugin.handler:access(plugin_conf)
 &#160;&#160;&#160;遍历执行各个插件&#45;&gt;core.access.after(ctx) -->
<g id="edge3" class="edge">
<title>for plugin, plugin_conf in plugins_iterator(singletons.loaded_plugins, true) do
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;plugin.handler:access(plugin_conf)
 &nbsp;&nbsp;&nbsp;遍历执行各个插件-&gt;core.access.after(ctx)</title>
<path fill="none" stroke="#000000" d="M1176.0851,-640.283C1216.8088,-626.8533 1260.659,-612.3924 1294.2673,-601.3092"></path>
<polygon fill="#000000" stroke="#000000" points="1295.507,-604.5858 1303.9078,-598.13 1293.3147,-597.938 1295.507,-604.5858"></polygon>
</g>
<!-- core.access.after(ctx)&#45;&gt;kong.access -->
<g id="edge4" class="edge">
<title>core.access.after(ctx)-&gt;kong.access</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M1367.4729,-600.4304C1375.666,-610.5278 1384.7154,-623.8611 1389,-637.6 1399.9282,-672.6422 1409.266,-689.585 1389,-720.1901 1315.7449,-830.8178 1152.7318,-868.2085 1061.3387,-880.6917"></path>
<polygon fill="#000000" stroke="#000000" points="1060.8336,-877.2279 1051.3709,-881.9931 1061.7399,-884.169 1060.8336,-877.2279"></polygon>
<text text-anchor="middle" x="1389.7132" y="-742.3901" font-family="Times,serif" font-size="14.00" fill="#000000">return</text>
</g>
<!-- balancer.execute(ctx.balancer_address) -->
<g id="node11" class="node">
<title>balancer.execute(ctx.balancer_address)</title>
<ellipse fill="none" stroke="#000000" cx="1219" cy="-471.6" rx="162.5409" ry="18"></ellipse>
<text text-anchor="middle" x="1219" y="-467.4" font-family="Times,serif" font-size="14.00" fill="#000000">balancer.execute(ctx.balancer_address)</text>
</g>
<!-- core.access.after(ctx)&#45;&gt;balancer.execute(ctx.balancer_address) -->
<g id="edge13" class="edge">
<title>core.access.after(ctx)-&gt;balancer.execute(ctx.balancer_address)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M1330.0842,-565.0117C1307.9125,-546.3673 1272.738,-516.7888 1247.9195,-495.9187"></path>
<polygon fill="#000000" stroke="#000000" points="1250.1613,-493.2308 1240.255,-489.4736 1245.6561,-498.5884 1250.1613,-493.2308"></polygon>
</g>
<!-- local match_t = router.exec(ngx) -->
<g id="node6" class="node">
<title>local match_t = router.exec(ngx)</title>
<ellipse fill="none" stroke="#000000" cx="545" cy="-471.6" rx="137.8575" ry="18"></ellipse>
<text text-anchor="middle" x="545" y="-467.4" font-family="Times,serif" font-size="14.00" fill="#000000">local match_t = router.exec(ngx)</text>
</g>
<!-- build_router(singletons.dao, version)&#45;&gt;local match_t = router.exec(ngx) -->
<g id="edge6" class="edge">
<title>build_router(singletons.dao, version)-&gt;local match_t = router.exec(ngx)</title>
<path fill="none" stroke="#000000" d="M552.5138,-564.2706C551.1003,-546.8373 548.9526,-520.3482 547.3148,-500.1489"></path>
<polygon fill="#000000" stroke="#000000" points="550.7852,-499.641 546.4884,-489.9566 543.8081,-500.2068 550.7852,-499.641"></polygon>
</g>
<!-- local match_t = find_api(method, uri, req_host, ngx) -->
<g id="node7" class="node">
<title>local match_t = find_api(method, uri, req_host, ngx)</title>
<ellipse fill="none" stroke="#000000" cx="508" cy="-344.8" rx="214.2732" ry="18"></ellipse>
<text text-anchor="middle" x="508" y="-340.6" font-family="Times,serif" font-size="14.00" fill="#000000">local match_t = find_api(method, uri, req_host, ngx)</text>
</g>
<!-- local match_t = router.exec(ngx)&#45;&gt;local match_t = find_api(method, uri, req_host, ngx) -->
<g id="edge7" class="edge">
<title>local match_t = router.exec(ngx)-&gt;local match_t = find_api(method, uri, req_host, ngx)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M536.8537,-453.5514C532.3049,-442.9518 526.8049,-429.2018 523,-416.6 518.7305,-402.4596 515.2506,-386.3929 512.7212,-373.0485"></path>
<polygon fill="#000000" stroke="#000000" points="516.1304,-372.2325 510.8996,-363.0189 509.243,-373.4835 516.1304,-372.2325"></polygon>
</g>
<!-- local match_t = find_api(method, uri, req_host, ngx)&#45;&gt;local match_t = router.exec(ngx) -->
<g id="edge8" class="edge">
<title>local match_t = find_api(method, uri, req_host, ngx)-&gt;local match_t = router.exec(ngx)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M514.2819,-363.0575C516.1788,-368.7245 518.2272,-375.004 520,-380.8 526.3964,-401.7124 532.9332,-425.5583 537.7177,-443.564"></path>
<polygon fill="#000000" stroke="#000000" points="534.3836,-444.6473 540.3179,-453.4242 541.1522,-442.8623 534.3836,-444.6473"></polygon>
<text text-anchor="middle" x="572.0137" y="-404" font-family="Times,serif" font-size="14.00" fill="#000000">return match_t</text>
</g>
<!-- local cache_key = fmt(&quot;%s:%s:%s&quot;, req_method, req_uri, req_host) -->
<g id="node9" class="node">
<title>local cache_key = fmt("%s:%s:%s", req_method, req_uri, req_host)</title>
<ellipse fill="none" stroke="#000000" cx="289" cy="-271.8" rx="273.4511" ry="18"></ellipse>
<text text-anchor="middle" x="289" y="-267.6" font-family="Times,serif" font-size="14.00" fill="#000000">local cache_key = fmt("%s:%s:%s", req_method, req_uri, req_host)</text>
</g>
<!-- local match_t = find_api(method, uri, req_host, ngx)&#45;&gt;local cache_key = fmt(&quot;%s:%s:%s&quot;, req_method, req_uri, req_host) -->
<g id="edge9" class="edge">
<title>local match_t = find_api(method, uri, req_host, ngx)-&gt;local cache_key = fmt("%s:%s:%s", req_method, req_uri, req_host)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M455.544,-327.3147C424.3323,-316.9108 384.5417,-303.6472 351.6814,-292.6938"></path>
<polygon fill="#000000" stroke="#000000" points="352.7614,-289.3645 342.1677,-289.5226 350.5477,-296.0053 352.7614,-289.3645"></polygon>
</g>
<!-- if match_t -->
<g id="node8" class="node">
<title>if match_t</title>
<polygon fill="none" stroke="#000000" points="462,-52 390.78,-34 462,-16 533.22,-34 462,-52"></polygon>
<text text-anchor="middle" x="462" y="-29.8" font-family="Times,serif" font-size="14.00" fill="#000000">if match_t</text>
</g>
<!-- if match_t&#45;&gt;local match_t = find_api(method, uri, req_host, ngx) -->
<g id="edge12" class="edge">
<title>if match_t-&gt;local match_t = find_api(method, uri, req_host, ngx)</title>
<path fill="none" stroke="#000000" d="M499.367,-42.567C537.1479,-52.3426 590,-69.578 590,-89.5 590,-271.8 590,-271.8 590,-271.8 590,-292.9299 574.6227,-309.4489 557.1863,-321.3881"></path>
<polygon fill="#000000" stroke="#000000" points="555.0921,-318.5716 548.5195,-326.8814 558.8396,-324.484 555.0921,-318.5716"></polygon>
<text text-anchor="middle" x="631.0137" y="-185.2" font-family="Times,serif" font-size="14.00" fill="#000000">return match_t</text>
</g>
<!-- local match_t = cache:get(cache_key) -->
<g id="node10" class="node">
<title>local match_t = cache:get(cache_key)</title>
<ellipse fill="none" stroke="#000000" cx="376" cy="-145" rx="157.4779" ry="18"></ellipse>
<text text-anchor="middle" x="376" y="-140.8" font-family="Times,serif" font-size="14.00" fill="#000000">local match_t = cache:get(cache_key)</text>
</g>
<!-- local cache_key = fmt(&quot;%s:%s:%s&quot;, req_method, req_uri, req_host)&#45;&gt;local match_t = cache:get(cache_key) -->
<g id="edge10" class="edge">
<title>local cache_key = fmt("%s:%s:%s", req_method, req_uri, req_host)-&gt;local match_t = cache:get(cache_key)</title>
<path fill="none" stroke="#000000" d="M301.465,-253.6327C316.1947,-232.1644 340.8308,-196.2582 357.7193,-171.6436"></path>
<polygon fill="#000000" stroke="#000000" points="360.7866,-173.3596 363.5581,-163.1336 355.0145,-169.3992 360.7866,-173.3596"></polygon>
</g>
<!-- local match_t = cache:get(cache_key)&#45;&gt;if match_t -->
<g id="edge11" class="edge">
<title>local match_t = cache:get(cache_key)-&gt;if match_t</title>
<path fill="none" stroke="#000000" d="M390.2012,-126.6706C404.9608,-107.6204 428.0983,-77.7568 444.1002,-57.1033"></path>
<polygon fill="#000000" stroke="#000000" points="446.9094,-59.192 450.2673,-49.1434 441.3759,-54.9048 446.9094,-59.192"></polygon>
</g>
<!-- balancer完成了后端RS &#160;IP 的查找包括失败重试等逻辑，之后再完善一些后端信息，记录查找时间就返回 -->
<g id="node12" class="node">
<title>balancer完成了后端RS &nbsp;IP 的查找包括失败重试等逻辑，之后再完善一些后端信息，记录查找时间就返回</title>
<ellipse fill="none" stroke="#000000" cx="1357" cy="-344.8" rx="460.256" ry="18"></ellipse>
<text text-anchor="middle" x="1357" y="-340.6" font-family="Times,serif" font-size="14.00" fill="#000000">balancer完成了后端RS &nbsp;IP 的查找包括失败重试等逻辑，之后再完善一些后端信息，记录查找时间就返回</text>
</g>
<!-- balancer.execute(ctx.balancer_address)&#45;&gt;balancer完成了后端RS &#160;IP 的查找包括失败重试等逻辑，之后再完善一些后端信息，记录查找时间就返回 -->
<g id="edge14" class="edge">
<title>balancer.execute(ctx.balancer_address)-&gt;balancer完成了后端RS &nbsp;IP 的查找包括失败重试等逻辑，之后再完善一些后端信息，记录查找时间就返回</title>
<path fill="none" stroke="#000000" d="M1238.4793,-453.7016C1262.2821,-431.8307 1302.7022,-394.691 1329.6239,-369.9542"></path>
<polygon fill="#000000" stroke="#000000" points="1332.1774,-372.3612 1337.1729,-363.0179 1327.4412,-367.2067 1332.1774,-372.3612"></polygon>
</g>
<!-- balancer完成了后端RS &#160;IP 的查找包括失败重试等逻辑，之后再完善一些后端信息，记录查找时间就返回&#45;&gt;core.access.after(ctx) -->
<g id="edge15" class="edge">
<title>balancer完成了后端RS &nbsp;IP 的查找包括失败重试等逻辑，之后再完善一些后端信息，记录查找时间就返回-&gt;core.access.after(ctx)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M1378.2267,-362.8321C1389.4783,-374.4584 1401,-390.6487 1401,-408.2 1401,-527.1 1401,-527.1 1401,-527.1 1401,-539.1571 1394.4058,-549.7764 1386.0068,-558.4122"></path>
<polygon fill="#000000" stroke="#000000" points="1383.6282,-555.8447 1378.6324,-565.1878 1388.3642,-560.9993 1383.6282,-555.8447"></polygon>
</g>
</g>
</svg>


# <span id = ngx_lua_drawback> ngx lua的局限<span>
## 1. 功能局限：  
 lua只是嵌入到nginx的11个阶段中的部分阶段（REWRITE ACCESS CONTENT LOG阶段）：  
- 无法支持server级别的动态加载
- 非上诉4个阶段的功能无法通过lua实现，比如公有云为了实现虚拟化，通过对socket进行setsockopt、 ssl协议版本的配置等功能
- 不支持spdy ,quic协议
## 2. 开发成本大
- nginx个性化配置的动态加载（包括新增修改nginx中的lua配置）：每个新增配置需要通过lua实现，无法直接复用nginx现有模块
- 和stgw当前实现及nginx现有配置相比，改动很大，运营系统升级成本高
## 3. 维护成本高
- OpenResty 对lua支持更加完善，但对于同步nginx原生代码至少存在2个月的滞后
- 需要同时维护nginx和OpenResty代码，维护成本变高
