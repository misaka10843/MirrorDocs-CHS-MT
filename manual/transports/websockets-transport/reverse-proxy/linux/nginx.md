# NGINX (原文: NGINX)

NGINX是一个快速且轻量级的Web服务器，也可以作为反向代理。它通常用于高流量的网站和应用程序。

在NGINX中添加一个反向代理就像定义一个`server`指令一样简单。在Debian / Ubuntu中，通常通过在`/etc/nginx/sites-enabled/`或`/etc/nginx/conf.d/`下创建一个新文件来完成这个操作（请双重检查文件`/etc/nginx/nginx.conf`中定义的NGINX `include`指令）。对于本文档，我们将假设NGINX已配置为包含来自`/etc/nginx/sites-enabled/`的文件。

在本文档中，我们将探讨为WebGL游戏客户端和Mirror游戏服务器编写NGINX配置。

---

值得注意的是，主要的`/etc/nginx/nginx.conf`文件将`/etc/nginx/sites-enabled/`中的文件包含到其`http`指令中。执行此操作的指令看起来类似于以下内容：

```nginx configuration
# ...
http {
    # ...
    include /etc/nginx/sites-enabled/*.conf;
    # ...
}
# ...

```

如果您试图遵循在线指南，有时内容作者会预期您编写一个新的`nginx.conf`文件，而其他内容作者则预期您在`sites-enabled`中编写新的`*.conf`文件。一个线索是，如果他们的代码示例显示`http`，这是用于TCP连接的指令，如果代码示例中有`http`，那么您不能在`sites-enabled`目录中不经修改地使用NGINX配置，因为`http`指令不能存在于`http`块中。

如果您想要设置一个NGINX服务器来反向代理UDP连接（例如，如果您正在使用KcpTransport），那么您需要添加一个`stream`指令。此外，如果您想要为`stream`指令设置类似于`sites-enabled`的机制，您可以按照以下方式更新`nginx.conf`文件：

```nginx configuration
# ...
http {
    # ...
}
stream {
    include /etc/nginx/streaming/*.conf;
}
# ...

```

目前，UDP连接将超出本文档的范围，因为本文档的存在是为了支持Simple Web Transport，但希望这可以作为一个很好的起点，如果您对NGINX UDP连接感兴趣。

### WebGL 游戏客户端 NGINX 配置（WebGL Game Client NGINX Configuration）

为了提供 WebGL 游戏客户端，我们必须记住，从 Unity 编辑器中，我们可以构建 Brotli（`.br`）压缩文件，GZIP（`.gz`）压缩文件，或者我们可以构建没有压缩的 WebGL 游戏客户端。为了在我们的 Web 服务器中提供 `.br` 或 `.gz` 压缩文件，我们必须返回正确的 `Content-Encoding` 头，其中包含 `br` 或 `gzip`，并且我们应该返回适当的 `Content-Type` 头给文件。

你可以参考 [Unity 的服务器配置文档](https://docs.unity3d.com/Manual/webgl-server-configuration-code-samples.html)，但我更喜欢这个配置。这个配置期望你将你的 webGL 游戏客户端构建放在 `/usr/share/nginx/html` 中。

注意：`server_name` 被设置为 `localhost` 而不是你的域名；这个 NGINX 服务器将在以后使用。

`/etc/nginx/sites-enabled/webgl-game-client.conf`
```nginx configuration
map $request_uri $gzip_encoding {
    ~*\.gz$  "gzip";
}
map $request_uri $br_encoding {
    ~*\.br$  "br";
}
map $request_uri $type_application_octet_stream {
    ~*\.data\.(gz|br)$  "application/octet-stream";
}
map $request_uri $type_text_javascript {
    ~*\.framework.js\.(gz|br)$  "text/javascript";
}
map $request_uri $type_application_wasm {
    ~*\.wasm\.(gz|br)$  "application/wasm";
}
server {
    # here we set the domain
    # server_name example.org;
    server_name localhost;
    
    http2 on;
    listen 8080;
    listen [::]:8080;

    root /usr/share/nginx/html;

    # Web Game Client
    location / {
        try_files $uri $uri/ /index.html =404;
    }

    location ~* \.(data|framework.js|wasm)\.(gz|br)?$ {
        gzip off;
        default_type '';

        add_header Content-Encoding $gzip_encoding always;
        add_header Content-Encoding $br_encoding always;
        add_header Content-Type $type_application_octet_stream always;
        add_header Content-Type $type_text_javascript always;
        add_header Content-Type $type_application_wasm always;
    }
}
```

（WebGL Game Client NGINX Configuration）

### Mirror 游戏服务器的 NGINX 配置（NGINX Configuration for a Mirror Game Server）

现在我们已经考虑了 WebGL 游戏客户端，我们可以专注于 Mirror 游戏服务器的适当 NGINX 配置。期望是你将生成一个 "Dedicated Server"，_目标平台_ 为 "Linux" 作为你的 Mirror 游戏服务器。启动 Mirror 游戏服务器应该就像执行你生成的 x86_64 二进制文件一样简单（例如 `./mirror-game-server.x86_64`，假设构建名称为 `mirror-game-server`），在上传所有游戏服务器文件后。期望是 Mirror 游戏服务器在与 NGINX 相同的服务器上运行；因此请求在本地被代理（`127.0.0.1`）。

Mirror 游戏服务器公开一个 HTTP 端点，以允许客户端 websocket 连接（`ws://` 协议）或安全 websocket 连接（`wss://`）使用。当你使用 Simple Web Transport 启动 Mirror 游戏服务器时，它将在 SWT 定义的端口上监听这些连接。

我们在 NGINX 配置中的目标是在不同的端口上公开一个端点，并将请求代理到 Mirror 游戏服务器的 HTTP 端点。

以下配置定义了一个反向代理，监听端口27778，代理到127.0.0.1:7778。

`/etc/nginx/sites-enabled/mirror-game-server-single.conf`

```nginx configuration
# helper map to support connection upgrading, only define this once
map $http_upgrade $connection_upgrade {
    default upgrade;
    ''      close;
}
# the actual reverse proxy server block
server {
    http2 on;
    # the server will listen on port 27778 for both ipv4 and ipv6 
    listen 27778;
    listen [::]:27778;
    
    server_name localhost;
    
    location / {
        proxy_pass 'http://127.0.0.1:7778';
        
        proxy_redirect off;
        # very long timeouts to make sure long-running connections aren't interrupted
        # you might be able to reduce these based on your use-case
        proxy_read_timeout 7d;
        proxy_send_timeout 7d;
        
        # The following headers are general settings, not directly used by SWT
        # Tell upstream the host
        proxy_set_header Host $host;
        # Tell upstream real ip & forwarded for header
        proxy_set_header X-Real-IP  $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        # tell upstream this was served via https
        proxy_set_header X-Forwarded-Proto https;
        
        proxy_buffer_size 2k;
        proxy_buffering off;
        
        # websocket support
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;

        # 10s connect timeout
        proxy_connect_timeout 10;
    }
}
```

### 反向代理用于 Mirror 游戏服务器和 WebGL 游戏客户端（启用 SSL & SSL 终止）

以下配置定义了一个反向代理，监听端口8443，用于提供 WebGL 游戏客户端并代理到 Mirror 游戏服务器。`upstream` 指令被用作 _web 游戏客户端_ 服务器和 _mirror 游戏服务器_ 服务器的别名。注意：`upstream` 指令可以用于在多个服务器之间负载均衡请求，如示例配置中的注释所示。

SSL 终止是一个概念，玩家通过 HTTPS 或 WSS 与配置为处理这些请求的服务器进行交互（意味着服务器配置了证书），然后服务器代理请求到其他服务器通过 HTTP 或 WS。尽管这种配置很常见，但为了实现"传输中的加密"，不应使用 SSL 终止。

`/etc/nginx/sites-enabled/reverse-proxy.conf`
```nginx configuration
# define an upstream server to proxy websocket connection requests to local (aka 127.0.0.1) game server(s)
upstream mirror_game_server {
    server 127.0.0.1:7778; # 7778 is the default port for Mirror Game Server's Simple Web Transport -- adjust accordingly
    # if you produce more Mirror Game Server builds with different SWT ports you may do something like...
    # server 127.0.0.1:7779;
    # server 127.0.0.1:7780;
    # server 127.0.0.1:7781;
}
# the webgl game client uses the server defined by web-game-client.conf; an alternative setup has this reverse proxy simple serve client files
upstream webgl_game_client {
    server 127.0.0.1:8080;
}
server {
    # here we set the domain (example.org)
    server_name example.org;
    ssl_certificate /etc/letsencrypt/live/example.org/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.org/privkey.pem;
    
    # the server will listen on port 8443 for both ipv4 and ipv6 
    http2 on;
    listen 8443 ssl;
    listen [::]:8443 ssl;
    
    # webgl game client served at /client (cannot be served at / as currently the / path is reserved for websocket connection)
    location ~* ^/client(/.*)$ {
        # remove /client from the request path before proxying to the web game client
        rewrite ^/client(/.*)$ $1 break;

        # SSL termination; proxy to http protocol (instead of https)
        proxy_pass http://webgl_game_client;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-Proto https;
        proxy_connect_timeout 10;
    }
    
    # mirror game server websocket proxy
    location / {
        # SSL termination; proxy to http protocol (instead of https)
        proxy_pass http://mirror_game_server;

        proxy_redirect off;

        # very long timeouts to make sure long-running connections aren't interrupted
        # you might be able to reduce these based on your use-case
        proxy_read_timeout 7d;
        proxy_send_timeout 7d;
        
        # The following headers are general settings, not directly used by SWT
        # Tell upstream the host
        proxy_set_header Host $host;
        # Tell upstream real ip & forwarded for header
        proxy_set_header X-Real-IP  $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";

        proxy_buffer_size 2k;
        proxy_buffering off;
        
        # 10s connect timeout
        proxy_connect_timeout 10;
    }
}
```
