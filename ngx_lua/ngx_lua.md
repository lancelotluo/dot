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


### access_by_lua指令的执行流程
access_by_lua指令由ngx_http_lua_access_handler_inline函数实现。主要流程如下：
1.


### <span id = "ngx_http_lua_init"> ngx_http_lua_init流程 </span>
