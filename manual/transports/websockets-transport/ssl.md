# SSL(SSL)

{% hint style="info" %}
注意: WebGL 使用反向代理性能更好，通常比使用 cert.json 和 PFX 文件更容易设置和维护。

\
查看[反向代理](reverse-proxy/)页面获取说明。
{% endhint %}

如果您将 Webgl 构建托管在 HTTPS 域上，您将需要使用安全的 Web 套接字协议 (`wss://`)。WSS 需要在您的 Mirror 游戏服务器上设置 SSL 证书，用于基本场景，或者在您的网关服务器上用于“反向代理”场景。您还需要确保在传输选项中设置了“Client Use WSS”。

在接下来的部分中，我们将演示如何使用 [Let's Encrypt](https://letsencrypt.org/) 为域 `simpleweb.example.com` 获取 SSL 证书。

### 预设置(Pre-Setup)

* 您需要一个域名和编辑 DNS 记录的能力
  * 一个 DNS 记录应指向您的 Webgl 游戏客户端（通常托管在端口 443 上）
  * 一个 DNS 记录应指向您的 Mirror 游戏服务器
* 在云中设置虚拟机: [如何设置 Google 云服务器](https://mirror-networking.com/docs/Articles/Guides/DevServer/gcloud/index.html)

{% hint style="info" %}
注意: 您可能需要为 `certbot` 操作打开端口 80。
{% endhint %}

### 获取证书(Get Cert)

按照这里的指南进行操作:

[https://letsencrypt.org/getting-started/](https://letsencrypt.org/getting-started/) [https://certbot.eff.org/instructions](https://certbot.eff.org/instructions)

找到适用于您服务器版本的说明。以下是适用于 `Ubuntu 18.04 LTS (bionic)` 的链接

[https://certbot.eff.org/instructions?ws=nginx\&os=ubuntubionic](https://certbot.eff.org/instructions?ws=nginx\&os=ubuntubionic)

对于第 7 步的指令

```
sudo certbot certonly --standalone
```

填写详细信息后，您将获得如下结果

```
IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/simpleweb.example.com/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/simpleweb.example.com/privkey.pem
   Your cert will expire on 2021-01-07. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot
   again. To non-interactively renew *all* of your certificates, run
   "certbot renew"
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
```

`simpleweb.example.com` 应为您的域名

### 创建 cert.pfx(Create cert.pfx)

要创建一个 SimpleWebTransport 可以使用的 pfx 文件，请在 `/etc/letsencrypt/live/simpleweb.example.com/` 文件夹中运行以下命令

```
openssl pkcs12 -export -out cert.pfx -inkey privkey.pem -in cert.pem -certfile chain.pem
```

### 设置密码

您将被要求输入密码；您可以设置一个密码或留空。您可能需要成为超级用户才能执行此操作。

```
su

cd /etc/letsencrypt/live/simpleweb.example.com/
```

注意：目前 Unity 随附的 Mono 版本无法加载由 OpenSSL 版本 3 生成的 pfx 文件。您需要在上述 openssl 命令中添加 `-legacy` 命令行参数以生成兼容的 pfx 文件。

### 使用 cert.pfx

您可以将 cert.pfx 文件复制到服务器文件夹中，或者创建一个符号链接。

使用移动

```
mv /etc/letsencrypt/live/simpleweb.example.com/cert.pfx ~/path/to/server/cert.pfx
```

使用符号链接

```
ln -s /etc/letsencrypt/live/simpleweb.example.com/cert.pfx ~/path/to/server/cert.pfx
```

#### 创建 cert.json 文件

创建一个 `cert.json` 文件，SimpleWebTransport 可以读取

在 `~/path/to/server/` 文件夹中运行以下命令

如果在创建证书时将密码留空：

```
echo '{ "path":"./cert.pfx", "password": "" }' > cert.json
```

如果在创建证书时设置了密码 "yourPassword"：

```
echo '{ "path":"./cert.pfx", "password": "yourPassword" }' > cert.json
```

#### 运行您的服务器

在 `cert.json` 和 `cert.pfx` 位于游戏服务器文件夹中时

```
GameServerFolder
|- demo_server.x86_64
|- cert.json
|- cert.pfx
```

然后使服务器文件可执行

```
chmod +x demo_server.x86_64
```

要在活动终端中运行，请使用

```
./demo_server.x86_64
```

要在后台运行，请使用

```
./demo_server.x86_64 &
```

要使游戏服务器在您关闭 ssh 会话后继续运行，请使用

```
nohup ./demo_server.x86_64 &
```

> `nohup` 的意思是：可执行文件将在您关闭 ssh 会话后继续运行，`&` 符号的意思是：您的服务器将在后台运行

> 如果您创建了符号链接，可能需要使用 `sudo` 运行

#### 连接到您的游戏

通过将游戏客户端连接到游戏服务器，使用 Unity 编辑器作为游戏客户端或使用部署的 webgl 构建作为游戏客户端，测试一切是否正常工作。

在主机名字段中设置您的域名（例如 `simpleweb.example.com`），然后启动客户端。

#### 调试 SSL(Debugging SSL)

要检查您的 pfx 文件在 Unity 之外是否正常工作，您可以使用 `pfxTestServer.js` ([src](https://github.com/James-Frowen/SimpleWebTransport/blob/master/DebugScripts\~/node\~/pfxTestServer.js))。 (Debugging SSL)
