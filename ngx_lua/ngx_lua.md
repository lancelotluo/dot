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


 # <span id="lua_nginx">lua在Nginx中的执行<span>
 ## 执行流程
 1. 将ngx_http_lua_module注册为最后一个module.
 2. lua_module执行post_configuration回调时，调用ngx_http_lua_init.
 3. [**ngx_http_lua_init**](#ngx_http_lua_init)完成启动时的主要初始工作。


### access_by_lua指令的执行流程
access_by_lua指令由ngx_http_lua_access_handler_inline函数实现。主要流程如下：
1.

### <span id = "ngx_http_lua_init"> ngx_http_lua_init流程 </span>
