# 加密传输(Encryption Transport)

加密传输并不是一种传输方式，而是允许您加密通过现有传输流动的数据。

### 技术细节(Technical details)

该传输通过[ECDH](https://en.wikipedia.org/wiki/Elliptic-curve\_Diffie%E2%80%93Hellman)和[HKDF](https://en.wikipedia.org/wiki/HKDF)-SHA256进行非对称密钥交换，安全地推导出一个共同的256位对称密钥，然后用于[AES](https://en.wikipedia.org/wiki/Advanced\_Encryption\_Standard)-[GCM](https://en.wikipedia.org/wiki/Galois/Counter\_Mode)，使用96位nonce和128位mac（类似于[TLS 1.3](https://en.wikipedia.org/wiki/Transport\_Layer\_Security#Cipher)中使用的方式）。AES-GCM在传输过程中保护数据，并在接收端通过mac（一种“校验和”）检查完整性。

握手过程完全通过不可靠的方式进行，加密是无序/可靠性不可知的，因此将适用于所有类型的传输。一旦握手完成，连接就完全安全。为了完全防止[中间人攻击](https://en.wikipedia.org/wiki/Man-in-the-middle\_attack)，至少一方需要验证公钥（通常是客户端验证服务器密钥）。

### 使用(Usage)

为了使用该传输，假设您已经设置了一个传输，只需将传输添加到“Inner”字段，并将EncryptionTransport分配给您的NetworkManager。

<figure><img src="../../.gitbook/assets/image (147).png" alt=""><figcaption><p>基本检查器设置</p></figcaption></figure>

### 验证服务器公钥(Validating Server Public Key)

如果不验证服务器公钥，初始握手容易受到中间人攻击的影响。

虽然在所有托管模式下可能无法验证服务器密钥（特别是玩家托管的游戏可能会很困难），但如果可能的话，仍建议将其添加到游戏中。

我们提供了3种验证模式：

#### 关闭(Off)

不进行任何验证，这是默认设置。适用于开发和无法进行验证的情况。

#### 列表(List)

公钥是从内置到构建中的受信任密钥列表中验证的。

这种模式适用于为玩家托管服务器的游戏，并且是最简单使用的，只需预先生成一个密钥文件加载到服务器上，服务器将自动将其添加到列表中。

<figure><img src="../../.gitbook/assets/image (149).png" alt=""><figcaption><p>检视器显示生成密钥对按钮</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (148).png" alt=""><figcaption><p>对话框自动将密钥添加到受信任列表</p></figcaption></figure>

#### 回调 (Callback)

在此模式下，当客户端连接到服务器时，将调用 `EncryptionTransport.onClientValidateServerPubKey` 回调，预期返回对于有效密钥的 `true`。

这允许最大的灵活性，并允许您通过在连接之前交换它们来验证公钥，例如从大厅服务或登录 API。

一旦客户端或服务器正在运行，可以从 `EncryptionTransport.EncryptionPublicKeyFingerprint` 检索指纹 (以及从 `EncryptionTransport.EncryptionPublicKey` 检索序列化的公钥本身)。

### 性能 (Performance)

目前，传输使用 BouncyCastle 进行所有加密操作，这对分配非常消耗资源。我们计划在未来的更新中解决这个问题，通过改进 BouncyCastle 本身并为大多数平台提供本机库来执行大部分工作，即 AES 加密/解密。 

**原文:**

# Public keys are validated from a list of trusted keys that are "baked in" to the build.

This mode is good for games that host servers for their players and the easiest to use, simply pre-generate a key file to load on the server which will automatically add it to the list.

<figure><img src="../../.gitbook/assets/image (149).png" alt=""><figcaption><p>Inspector showing Generate Key Pair Button</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (148).png" alt=""><figcaption><p>Dialogue to automatically add key to the trusted list</p></figcaption></figure>

#### Callback

In this mode, when a client connects to a server, it will call the `EncryptionTransport.onClientValidateServerPubKey` callback which is expected to return `true`for a valid key.

This allows for the most flexibility and allows you to validate public keys by exchanging them before connecting, for example from the lobby service, or a login api.

Once a client or server is running, the fingerprint can be retrieved from `EncryptionTransport.EncryptionPublicKeyFingerprint` (and the serialized public key itself from`EncryptionTransport.EncryptionPublicKey`).

### Performance

The transport currently uses BouncyCastle for all cryptography, which is very heavy on allocations. We are planning to address this in a future update through both, improving BouncyCastle itself and providing a native library for most platforms to do the bulk of the work, the AES en-/decryption.
