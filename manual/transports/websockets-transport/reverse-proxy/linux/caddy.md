# Caddy(服务器)

[按照[官方指南](https://caddyserver.com/docs/quick-starts/reverse-proxy)的说明，只需简单地定义一个 `Caddyfile` 文件：

```
example.com:7777

reverse_proxy:27777
```

或者通过以下方式运行 Caddy：

```
caddy reverse-proxy --from example.com:7777 --to :27777
```
