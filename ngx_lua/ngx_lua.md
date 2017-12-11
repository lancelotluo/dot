[TOC]

# [lua基础](#luabasic)
## [lua和C的交互](#lua_c)
# [lua在Nginx中的执行](#lua_nginx)

#
1. lua是如何在nginx中运行的
2. nginx中的lua执行顺序

[锚点](#anchor)

[12](#sss)

## lua和C的交互
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
### lua线程和协程
lua 不支持真正的多线程，主要有两个原因：
1. ANSI C 不支持，所以lua也不好移植。
2. 没有必要支持线程，原因是： 多线程为是低层语言设计的，出现BUG会导致Lua难以排查，甚至有可能出现安全问题。另外多线程的同步锁也会影响性能。

Lua的线程就是协程加API。A thread is the essence of a coroutine in Lua. We can think of a coroutine as a
thread plus a nice interface, or we can think of a thread as a coroutine with a
lower-level API
 # <span id="lua_nginx">lua在Nginx中的执行<span>
 ## 执行流程
 1. 将ngx_http_lua_module注册为最后一个module.
 2. lua_module执行post_configuration回调时，调用ngx_http_lua_init.
 3. [**ngx_http_lua_init**](#ngx_http_lua_init)完成启动时的主要初始工作。

### ngx_http_lua_init流程
1. 初始化几个 handlers，h = ngx_array_push(&cmcf->phases[NGX_HTTP_REWRITE_PHASE].handlers);  类似的还有*h = ngx_http_lua_access_handler；ngx_http_lua_log_handler
 *h = ngx_http_lua_rewrite_handler
2. 最重要的工作就是ngx_http_lua_init_vm,并将初始化的vm赋值给lmcf->lua 。重点介绍一下：

#### ngx_http_lua_init_vm的流程
1. L = ngx_http_lua_new_state(parent_vm, cycle, lmcf, log);
<!DOCTYPE svg [<!ENTITY nbsp "&#160;">]><svg width="1331pt" height="594pt" viewBox="0 0 1331 594" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
<g id="graph0" class="graph" transform="scale(1 1) rotate(0) translate(4 589.6313)">
<title>%0</title>
<polygon fill="#ffffff" stroke="transparent" points="-4,4 -4,-589.6313 1326.7324,-589.6313 1326.7324,4 -4,4"></polygon>
<!-- lmcf&#45;&gt;lua = ngx_http_lua_init_vm -->
<g id="node1" class="node">
<title>lmcf-&gt;lua = ngx_http_lua_init_vm</title>
<ellipse fill="none" stroke="#000000" cx="145.3096" cy="-567.6313" rx="145.1194" ry="18"></ellipse>
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
<ellipse fill="none" stroke="#000000" cx="629.3096" cy="-348.6313" rx="108.9835" ry="18"></ellipse>
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
<path fill="none" stroke="#000000" d="M592.0972,-331.5098C561.3589,-317.3669 517.5173,-297.1953 484.1693,-281.8517"></path>
<polygon fill="#000000" stroke="#000000" points="485.6089,-278.6614 475.0614,-277.6611 482.683,-285.0206 485.6089,-278.6614"></polygon>
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
<path fill="none" stroke="#000000" d="M675.3221,-332.2202C705.465,-318.1928 739.3096,-294.6829 739.3096,-259.8313 739.3096,-259.8313 739.3096,-259.8313 739.3096,-113.8313 739.3096,-62.4064 575.3015,-40.9573 488.6127,-33.2119"></path>
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

### <span id = "ngx_http_lua_init"> ngx_http_lua_init流程 </span>
