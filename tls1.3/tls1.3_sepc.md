# change
和TLS1.2的变化
1. Session resumption with and without server-side state as well as
      the PSK-based ciphersuites of earlier TLS versions have been
      replaced by a single new PSK exchange
2. All handshake messages after the ServerHello are now encrypted.
      The newly introduced EncryptedExtension message allows various
      extensions previously sent in clear in the ServerHello to also
      enjoy confidentiality protection from active attackers.
