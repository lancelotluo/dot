[ toc ]

# 什么时候对数据加密？
1. void QuicPacketCreator::SerializePacket对数据加密
2.
＃ 什么时候解密？
1. QuicFramer::ProcessDataPacket, DecryptPayload

# 数据加密级别
总共有如下三类：
* EncryptionLevel::ENCRYPTION_NONE
* EncryptionLevel::ENCRYPTION_INITIAL
* EncryptionLevel::ENCRYPTION_FORWARD_SECURE

# 什么时候设置encrypter?
## encrypter初始化
**quic_framer构造函数时将decrypter_和encrypter_两个私有变量初始化为NullEncrypter。**

```
QuicFramer::QuicFramer(const ParsedQuicVersionVector& supported_versions,
                       QuicTime creation_time,
                       Perspective perspective)
    : visitor_(nullptr),
      error_(QUIC_NO_ERROR),
      largest_packet_number_(0),
      last_serialized_connection_id_(0),
      last_version_label_(0),
      version_(PROTOCOL_UNSUPPORTED, QUIC_VERSION_UNSUPPORTED),
      supported_versions_(supported_versions),
      decrypter_level_(ENCRYPTION_NONE),
      alternative_decrypter_level_(ENCRYPTION_NONE),
      alternative_decrypter_latch_(false),
      perspective_(perspective),
      validate_flags_(true),
      creation_time_(creation_time),
      last_timestamp_(QuicTime::Delta::Zero()),
      data_producer_(nullptr),
      use_incremental_ack_processing_(
          GetQuicReloadableFlag(quic_use_incremental_ack_processing2)) {
  if (use_incremental_ack_processing_) {
    QUIC_FLAG_COUNT(quic_reloadable_flag_quic_use_incremental_ack_processing2);
  }
  DCHECK(!supported_versions.empty());
  version_ = supported_versions_[0];
  decrypter_ = QuicMakeUnique<NullDecrypter>(perspective);
  encrypter_[ENCRYPTION_NONE] = QuicMakeUnique<NullEncrypter>(perspective);
}

```
## Server什么时候会再次赋值encrypter?
1. 处理完ClientHello时会设置Encrypter，参考FinishProcessingHandshakeMessageAfterProcessClientHello这个函数。
2. 使用INITIAL级别的密钥发送ServerHello
3. 使用forward_secure级别的密钥发送之后的消息
```
void QuicCryptoServerHandshaker::
    FinishProcessingHandshakeMessageAfterProcessClientHello(
        const ValidateClientHelloResultCallback::Result& result,
        QuicErrorCode error,
        const QuicString& error_details,
        std::unique_ptr<CryptoHandshakeMessage> reply,
        std::unique_ptr<DiversificationNonce> diversification_nonce,
        std::unique_ptr<ProofSource::Details> proof_source_details) {
          ...

   // Receiving a full CHLO implies the client is prepared to decrypt with
   // the new server write key.  We can start to encrypt with the new server
   // write key.
   //
   // NOTE: the SHLO will be encrypted with the new server write key.
   session()->connection()->SetEncrypter(
       ENCRYPTION_INITIAL,
       crypto_negotiated_params_->initial_crypters.encrypter.release());
   session()->connection()->SetDefaultEncryptionLevel(ENCRYPTION_INITIAL);
   // Set the decrypter immediately so that we no longer accept unencrypted
   // packets.
   session()->connection()->SetDecrypter(
       ENCRYPTION_INITIAL,
       crypto_negotiated_params_->initial_crypters.decrypter.release());
   session()->connection()->SetDiversificationNonce(*diversification_nonce);

   SendHandshakeMessage(*reply); //注意这个消息是ServerHello,使用了INITIAL级别的密钥加密

   //ServerHello 消息之后的内容都升级成了FORWARD_SECURE级别进行加密
   session()->connection()->SetEncrypter(
        ENCRYPTION_FORWARD_SECURE,
        crypto_negotiated_params_->forward_secure_crypters.encrypter.release());
    session()->connection()->SetDefaultEncryptionLevel(ENCRYPTION_FORWARD_SECURE);

    session()->connection()->SetAlternativeDecrypter(
        ENCRYPTION_FORWARD_SECURE,
        crypto_negotiated_params_->forward_secure_crypters.decrypter.release(),
        false /* don't latch */);

    encryption_established_ = true;
    handshake_confirmed_ = true;
    session()->OnCryptoHandshakeEvent(QuicSession::HANDSHAKE_CONFIRMED);
```

1. 发送CHlo前将encrypter设置为null，明文传输
2. 发送CHLO后，将encrypter设置为INITIAL。
3. 收到SHLO时，将encrypter设置为ENCRYPTION_FORWARD_SECURE

## Client

# encrypter是如何生成的？
QuicCryptoClientConfig::ProcessServerHello通过调用CryptoUtils::DeriveKeys生成
