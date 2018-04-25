[toc]

# 数据加密级别
* EncryptionLevel::ENCRYPTION_NONE
* EncryptionLevel::ENCRYPTION_INITIAL
* EncryptionLevel::ENCRYPTION_FORWARD_SECURE

# 什么时候设置encrypter?
1. 发送CHlo前将encrypter设置为null，明文传输
2. 发送CHLO后，将encrypter设置为INITIAL。
3. 收到SHLO时，将encrypter设置为ENCRYPTION_FORWARD_SECURE


# encrypter是如何生成的？
QuicCryptoClientConfig::ProcessServerHello通过调用CryptoUtils::DeriveKeys生成
