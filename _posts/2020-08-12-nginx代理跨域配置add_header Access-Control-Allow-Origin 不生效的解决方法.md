### nginx配置

```
location /api {
        proxy_pass http://b.com/;
        # 设置是否允许 cookie 传输
        add_header Access-Control-Allow-Credentials true;
        # 允许请求地址跨域 * 做为通配符
        add_header Access-Control-Allow-Origin *;
        # 允许跨域的请求方法
        add_header Access-Control-Allow-Methods 'GET, POST, PUT, DELETE, OPTIONS';
        # 请求头
        add_header Access-Control-Allow-Headers 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Authorization';

         if ($request_method = 'OPTIONS') {
                 return 204;
         }
}
```

当A服务中请求路径以 /api 打头时,nginx做了代理,全部转发到 api 服务上.

上面配置项 add_header Access-Control-Allow-Origin *;
说明转发的请求全部添加了请求头 Access-Control-Allow-Origin *
也就是说浏览器这边对于跨域请求不会拦截.

但是偶尔情况下,浏览器依然报错:

> XMLHttpRequest cannot load http://b.com/api/list. Response 
to preflight request doesn't pass access control check: No 'Access-
Control-Allow-Origin' header is present on the requested resource. 
Origin 'http://a.com/api/list' is therefore not allowed access. 
The response had HTTP status code 401.

而发现这个错误的时候,一般都是token过期以后,
检查api服务代码发现正常的请求返回 status:200 ,token过期的请求返回 status:401

**status:200** 的时候
跨域请求的 response header 显示:
> Access-Control-Allow-Origin: *  
> Content-Length: 3388  
> Content-Type: application/json; charset=utf-8  

**status:401** 的时候
跨域请求的 response header 显示:

> Content-Length: 3388  
> Content-Type: application/json; charset=utf-8  

明显丢失了Access-Control-Allow-Origin 项.

### 问题原因

原来nginx add_header 配置项只对 status=200时生效,
其他状态码时未生效,
这也就导致前端在token过期后请求api服务时,
由于api发现token过期,所以返回状态码 401,
当状态码是 200,201,204,206,301,302,303,304,307 时nginx会添加header，而 401、500，都没有添加header
由于 Access-Control-Allow-Origin * 选项未生效,导致返回的response header里面没有允许跨域请求的选项,
导致浏览器直接拒绝接收这个请求 (这是浏览器出于安全性方面考虑的)
因为这个请求不响应,导致前端项目一直loading状态

### 解决问题

nginx配置项
`add_header Access-Control-Allow-Origin *;`
添加一个参数 `always`
这个参数的作用,不论server返回什么样的状态码,`add_header Access-Control-Allow-Origin *` 都生效

```
location /api {
        proxy_pass http://b.com/;

        add_header Access-Control-Allow-Credentials true;
        add_header Access-Control-Allow-Origin * always;
        add_header Access-Control-Allow-Methods 'GET, POST, PUT, DELETE, OPTIONS';
        add_header Access-Control-Allow-Headers 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Authorization';

         if ($request_method = 'OPTIONS') {
                 return 204;
         }
}
```
