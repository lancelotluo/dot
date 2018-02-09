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

main_loop_->EarlyInitialization();void BrowserMainLoop::EarlyInitialization() 会创建几个线程
main_loop_->PostMainMessageLoopStart(); 也会创建几个线程
main_loop_->CreateStartupTasks();会创建多个线程，同时弹出浏览器界面，但不响应
exit_code = main_runner->Run()会创建几个线程，同时浏览器界面会弹到最前面
**浏览器会阻塞在当前函数content/browser/browser_main.cc:main_runner->Run()**

### 创建线程BrowserMainLoop::CreateThreads()
```
 // Start threads in the order they occur in the BrowserThread::ID enumeration,
  // except for BrowserThread::UI which is the main thread.
  //
  // Must be size_t so we can increment it.
  for (size_t thread_id = BrowserThread::UI + 1; 
       thread_id < BrowserThread::ID_COUNT;
       ++thread_id) {
    // If this thread ID is backed by a real thread, |thread_to_start| will be
    // set to the appropriate BrowserProcessSubThread*. And |options| can be
    // updated away from its default.
    ...
    会根据线程类型逐个创建

   switch (thread_id) {
      case BrowserThread::DB:
        TRACE_EVENT_BEGIN1("startup",
            "BrowserMainLoop::CreateThreads:start",
            "Thread", "BrowserThread::DB");
        non_ui_non_io_task_runner_traits = kUserVisibleTraits;
        break;
      case BrowserThread::FILE_USER_BLOCKING:
        TRACE_EVENT_BEGIN1("startup",
            "BrowserMainLoop::CreateThreads:start",
            "Thread", "BrowserThread::FILE_USER_BLOCKING");
        non_ui_non_io_task_runner_traits = kUserBlockingTraits;
        break;
      case BrowserThread::FILE:
        TRACE_EVENT_BEGIN1("startup",
            "BrowserMainLoop::CreateThreads:start",
            "Thread", "BrowserThread::FILE");
        non_ui_non_io_task_runner_traits = kUserVisibleTraits;
        break;
      ...
      重点关注IO线程
      case BrowserThread::IO:
        TRACE_EVENT_BEGIN1("startup",
            "BrowserMainLoop::CreateThreads:start",
            "Thread", "BrowserThread::IO");
        thread_to_start = &io_thread_;
        options = io_message_loop_options;
#if defined(OS_ANDROID) || defined(OS_CHROMEOS)
        // Up the priority of the |io_thread_| as some of its IPCs relate to
        // display tasks.
        options.priority = base::ThreadPriority::DISPLAY;
#endif
        break;
      case BrowserThread::UI:        // Falls through.
      case BrowserThread::ID_COUNT:  // Falls through.
        NOTREACHED();
        break;

    ......
    线程创建函数
       (*thread_to_start)
          .reset(message_loop ? new BrowserProcessSubThread(id, message_loop)
                              : new BrowserProcessSubThread(id));
```
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



