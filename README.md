# nginx proxy for pdf view

## nginx conf

> 基于openresty

```code
        location /pdf {
		    proxy_redirect     off; 
            add_header Access-Control-Allow-Origin *;
            add_header Access-Control-Allow-Methods GET, POST, PUT, DELETE, OPTIONS;
            proxy_set_header Host $http_host;
            proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for; 
            client_body_buffer_size 10M;
            client_max_body_size 10G;
            set $agent "Mozilla/5.0 (iPad; U; CPU OS 4_3_3 like Mac OS X; en-us) AppleWebKit/533.17.9 (KHTML, like Gecko) Version/5.0.2 Mobile/8J2 Safari/6533.18.5";
            set_by_lua_block $oss_url { 
                local ossurl=  ngx.req.get_uri_args()["ossurl"];
                ngx.log(ngx.ERR, "error: ", ossurl)
                return ossurl
            }
            proxy_buffers 1024 4k;
            proxy_read_timeout 300;
            proxy_set_header User-Agent $agent;
            proxy_pass $oss_url;
        }
```

## 使用说明

* 请求格式

```code
http://localhost/pdf?ossurl=<oss-url>
```

* ossurl 说明

需要使用url编码，否则会存在url参数获取问题

```code
encodeURIComponent(ossurl)
```


## 直接集成web 使用

* 请求地址

http://localhost/pdf/web/viewer.html?file=<urlencode>

备注:urlencode  http://localhost/pdf?ossurl=<oss-url>

## 参考url说明

原始oss url

https://min.io/resources/docs/CPG-MinIO-implementation-guide.pdf

nginx pdf 地址:

上边的url编码拼接:
http://localhost/pdf?ossurl=https%3A%2F%2Fmin.io%2Fresources%2Fdocs%2FCPG-MinIO-implementation-guide.pdf

pdf 预览url

上边生成的url继续url编码同时拼接
http://localhost/doc/web/viewer.html?file=http%3A%2F%2Flocalhost%2Fpdf%3Fossurl%3Dhttps%253A%252F%252Fmin.io%252Fresources%252Fdocs%252FCPG-MinIO-implementation-guide.pdf