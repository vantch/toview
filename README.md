## 安装与配置

### 在本地电脑运行网站

详细流程可参考课程：https://ninghao.net/course/6855

1. 安装 Docker Desktop（Mac 或 Windows 版）
下载地址：https://www.docker.com/products/docker-desktop

2. 启动 Docker Desktop，打开终端，macOS 用户可以使用系统自带的终端，Windows 用户可以下载完整版的 Cmder。  
进入到这个项目所在的目录，然后执行：
```
docker-compose up -d
```
3. 打开网站，访问：http://localhost:8080。
4. 登录网站，访问：http://localhost:8080/wp-login.php ，用户名：toview，密码：6H58v3hl9p2

### 在服务器运行网站

在服务器运行网站，需要安装 Docker 与 Docker Compose，还要配置一个 Nginx 的反向代理。Nginx 代理的配置模板，基于下面这个配置文件在你的服务器上创建一个 Nginx 服务。

服务器的具体配置流程参考：https://ninghao.net/video/6870

```
upstream app {
  server 127.0.0.1:8080;
}

server {
  listen        80;
  server_name   SERVER_NAME;
  return 301    https://$host$request_uri;
}

server {
  listen       443 ssl http2;
  server_name  SERVER_NAME;
  index        index.html;

  ssl_certificate           /etc/nginx/ssl/SERVER_NAME/SERVER_NAME_PUBLIC.pem;
  ssl_certificate_key       /etc/nginx/ssl/SERVER_NAME/SERVER_NAME_PRIVATE.key;
  ssl_session_timeout       5m;
  ssl_protocols             TLSv1 TLSv1.1 TLSv1.2;
  ssl_ciphers               AESGCM:ALL:!DH:!EXPORT:!RC4:+HIGH:!MEDIUM:!LOW:!aNULL:!eNULL;
  ssl_prefer_server_ciphers on;

  location / {
    proxy_set_header  X-Forwarded-Host $host;
    proxy_set_header  X-Forwarded-Proto $scheme;
    proxy_set_header  X-Real-IP  $remote_addr;
    proxy_set_header  X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header  Host $http_host;
    proxy_redirect    off;
    expires           off;
    sendfile          off;
    proxy_pass        http://app;
  }
}
```

## 参考资源

- 配置本地开发环境与服务器：https://ninghao.net/course/6855
- 安装主题与创建页面：https://ninghao.net/course/6914
- 展示作品，配置负载均衡：https://ninghao.net/course/6931
