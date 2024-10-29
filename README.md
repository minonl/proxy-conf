# Proxy Conf

学习自新树蜂使用的反向代理配置。

## 配置方法

需要一个专用域名如 `hub.some.com`、nginx 服务器和 cloudflare
可以先行替换 `hub-nginx.conf` 和 `hub-workers.js` 中的域名

1. 将域名 `hub.some.com` 添加到 cloudflare，需要将 DNS 伺服器地址转移到 cloudflare
  - 通常 https 默认开启 flexible 模式，不需要配置；如转发到 vercel 可能需要改为 full 模式
  - 如果域名上还有别的记录，可以 dns 记录不开启 proxy 模式指向原服务
2. 为域名设置 A 记录指向 nginx 服务器 IP 地址，开启 cloudflare proxy
3. 通过 `hub-nginx.conf` 配置 nginx 服务器
  - docker 上建议使用 openresty，已包含了必须的 lua 插件
4. 在 cloudflare 配置 Worker Route，新增 `hub.some.com/*`
5. 新建 cloudflare Worker，将脚本内容替换 `hub-workers.js` 的内容并部署
6. 尝试访问 `hub.some.com`, `hub.some.com/v2`，如果正常返回结果那么大功告成

## 使用

通常修改 docker 的镜像配置即可，如 linux 中 `/etc/docker/daemon.json`，如：
```
{
    "registry-mirrors": {
        "https://hub.some.com"
    }
}
```

docker-compose 通常还需要替换 image 地址，显示指定镜像服务的地址，如：
```
services:
  mssql:
    # image: nginx:latest
    image: hub.some.com/nginx:latest
...
```

## License

[WTFPL](http://www.wtfpl.net/)
