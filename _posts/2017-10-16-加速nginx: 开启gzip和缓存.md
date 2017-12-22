nginx 是一个高性能的 Web 服务器，之前也写过一些关于 nginx 的文章。为了提高博客的响应速度，可以从设置 nginx 的 gzip 和缓存这2方面入手。为字体开启 gzip 和缓存能大大减少带宽的消耗。

### 开启gzip

#### 配置
```
# 开启gzip
gzip on;
# 启用gzip压缩的最小文件，小于设置值的文件将不会压缩
gzip_min_length 1k;
# gzip 压缩级别，1-10，数字越大压缩的越好，也越占用CPU时间，后面会有详细说明
gzip_comp_level 2;
# 进行压缩的文件类型。javascript有多种形式。其中的值可以在 mime.types 文件中找到。
gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
# 是否在http header中添加Vary: Accept-Encoding，建议开启
gzip_vary on;
# 禁用IE 6 gzip
gzip_disable "MSIE [1-6]\.";
```
关于具体的参数说明可以参考 [nginx 的文档](https://nginx.org/en/docs/http/ngx_http_gzip_static_module.html)。

#### 相关学习文章

- [加速nginx: 开启gzip和缓存](https://www.darrenfang.com/2015/01/setting-up-http-cache-and-gzip-with-nginx/)
- [nginx静态资源缓存策略配置](http://blog.csdn.net/yu12377/article/details/77875045)
- [Nginx开启Gzip压缩大幅提高页面加载速度](https://www.cnblogs.com/mitang/p/4477220.html)
