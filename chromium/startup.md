[toc]

# 启动流程
main函数在
chrome/app/chrome_exe_main_aura.cc
执行到RunNamedProcessTypeMain函数时，代码如下:
```
  static const MainFunction kMainFunctions[] = {
#if !defined(CHROME_MULTIPLE_DLL_CHILD)
    { "",                            BrowserMain },
#endif
#if !defined(CHROME_MULTIPLE_DLL_BROWSER)
#if BUILDFLAG(ENABLE_PLUGINS)
    { switches::kPpapiPluginProcess, PpapiPluginMain },
    { switches::kPpapiBrokerProcess, PpapiBrokerMain },
#endif  // ENABLE_PLUGINS
    { switches::kUtilityProcess,     UtilityMain },
    { switches::kRendererProcess,    RendererMain },
    { switches::kGpuProcess,         GpuMain },
#endif  // !CHROME_MULTIPLE_DLL_BROWSER
  };

  RegisterMainThreadFactories();

  for (size_t i = 0; i < arraysize(kMainFunctions); ++i) {
    if (process_type == kMainFunctions[i].name) {
      if (delegate) {
        int exit_code = delegate->RunProcess(process_type,
            main_function_params);
#if defined(OS_ANDROID)
        // In Android's browser process, the negative exit code doesn't mean the
        // default behavior should be used as the UI message loop is managed by
        // the Java and the browser process's default behavior is always
        // overridden.
        if (process_type.empty())
          return exit_code;
#endif
        if (exit_code >= 0)
          return exit_code;
      }
      return kMainFunctions[i].function(main_function_params); 
    }

事实上这里主要执行了两个函数，一个是
delegate->RunProcess(process_type, main_function_params);调用的函数是
chrome/app/chrome_main_delegate.cc:997

另外就是kMainFunctions[i].function(main_function_params),调用的函数是
content/browser/browser_main.cc 中的int BrowserMain(const MainFunctionParams& parameters)函数。

```
线程都是int exit_code = main_runner->Initialize(parameters);这个函数初始化出来的.

### gdb纪录

#0  0x00007ffff33f0502 in (anonymous namespace)::RunNamedProcessTypeMain((anonymous namespace)::(anonymous namespace)::string const&, (anonymous namespace)::MainFunctionParams const&, (anonymous namespace)::ContentMainDelegate*) (process_type=..., main_function_params=..., delegate=0x7fffffffdb90) at ../../content/app/content_main_runner.cc:412
#1  0x00007ffff33f32a2 in (anonymous namespace)::ContentMainRunnerImpl::Run() (this=0x1a0ca5ce39b0)
    at ../../content/app/content_main_runner.cc:710
#2  0x00007ffff33ea70d in (anonymous namespace)::ContentServiceManagerMainDelegate::RunEmbedderProcess() (this=0x7fffffffdb20)
    at ../../content/app/content_service_manager_main_delegate.cc:51
#3  0x00007ffff7e90775 in (anonymous namespace)::Main((anonymous namespace)::MainParams const&) (params=...)
    at ../../services/service_manager/embedder/main.cc:456
#4  0x00007ffff33ef54f in (anonymous namespace)::ContentMain((anonymous namespace)::ContentMainParams const&) (params=...)
    at ../../content/app/content_main.cc:19
#5  0x0000555556c5423e in ChromeMain(int, char const**) (argc=6, argv=0x7fffffffdd48) at ../../chrome/app/chrome_main.cc:128
#6  0x0000555556c54152 in main(int, char const**) (argc=6, argv=0x7fffffffdd48) at ../../chrome/app/chrome_exe_main_aura.cc:17



