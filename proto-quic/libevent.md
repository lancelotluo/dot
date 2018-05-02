[toc]

# Runloop和libevent的关系
(gdb) bt
```
#0  0x00007ffff688a309 in (anonymous namespace)::MessagePumpLibevent::Run((anonymous namespace)::MessagePump::Delegate*) (this=0x7ffff2be9b20, delegate=0x7fffffffb670) at ../../base/message_loop/message_pump_libevent.cc:223
#1  0x00007ffff6883c8c in (anonymous namespace)::MessageLoop::Run(bool) (this=0x7fffffffb670, application_tasks_allowed=true)
    at ../../base/message_loop/message_loop.cc:342
#2  0x00007ffff693aded in (anonymous namespace)::RunLoop::Run() (this=0x7fffffffa8f8) at ../../base/run_loop.cc:130
#3  0x00007ffff693bb51 in (anonymous namespace)::RunLoop::RunUntilIdle() (this=0x7fffffffa8f8) at ../../base/run_loop.cc:143
#4  0x000000000022c369 in (anonymous namespace)::QuicClientMessageLooplNetworkHelper::RunEventLoop() (this=0x7ffff2c16aa0)
    at ../../net/tools/quic/quic_client_message_loop_network_helper.cc:113
#5  0x00000000002292fd in (anonymous namespace)::QuicClientBase::WaitForEvents() (this=0x7fffffffd178)
    at ../../net/tools/quic/quic_client_base.cc:168
#6  0x00000000002288e6 in (anonymous namespace)::QuicClientBase::Connect() (this=0x7fffffffd178)
    at ../../net/tools/quic/quic_client_base.cc:84
#7  0x000000000021fdfc in main(int, char**) (argc=5, argv=0x7fffffffdd18) at ../../net/tools/quic/quic_simple_client_bin.cc:287
```

# server如何绑定事件？
1. server在udp socket recv_from时会绑定事件
```
int UDPSocketPosix::RecvFrom
if (!base::MessageLoopForIO::current()->WatchFileDescriptor

```
