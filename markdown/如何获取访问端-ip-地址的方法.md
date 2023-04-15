# 如何获取访问端 IP 地址的方法

如果使用 nginx 作为 web 代理服务器，则将添加 `proxy_set_header X-Real-IP $remote_addr;` 到站点的位置块。例如：

```conf
server {
    listen 443 ssl http2;
    server_name server_name; 

    client_max_body_size 1024m;

    location / {
        proxy_redirect off;
        proxy_set_header HOST $http_host;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-NginX-Proxy true;
        proxy_pass IP address;
    }
}
```

并且，如果采用 Express 作为后端开发框架，可以使用 `req.headers["x-real-ip"] || req.connection.remoteAddress` 获取 IP 地址。

此方法仅限简单使用，具体使用完善获取访问端 IP 地址可使用第三方中间件，如：`[express-ip](https://www.npmjs.com/package/express-ip)`、`[request-ip](https://www.npmjs.com/package/request-ip)` 等。
