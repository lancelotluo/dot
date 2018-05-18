[toc]

# connection 何时建立？
实例化QuicConnection类的时候默认connetion建立。
会将connected_置为true。
比如

```
void QuicClientBase::StartConnect() 
...
session_ = CreateQuicClientSession(new QuicConnection
GetNextConnectionId(), server_address(), helper(), alarm_factory(),
writer,
/* owns_writer= */ false, Perspective::IS_CLIENT, supported_versions()));
...

```

调用void QuicConnection::TearDownLocalConnectionState(时销毁connection。


