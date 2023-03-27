



购买域名后，解析一条`A`记录，比如`www`前缀，`www.${域名}`会解析成对应的`IP`地址，不包含端口，这时需要我们使用`nginx`的`SSL`来转发端口。

### 1. 流程

先说下配置和部署流程，我们是把我们的应用放到`80`端口，默认是`http`协议，使用`https`协议默认又是`443`端口，`80`端口不存在安全验证，而如果使用`https`协议转发到`http`协议，会出现页面无法显示问题，所以我们需要两个相同协议的端口，这里使用到`443`和`80`。

注册阿里云账号，这里免费获取 [跳转](https://www.aliyun.com/product/cas) ,注册完后进入SSL证书管理控制台 -> `SSL` 证书 -> 免费证书。

需要购买域名，因为证书是跟域名绑定的。

有了证书后，就可以下载`SSL`证书，选择`nginx`服务的证书格式 `pem/key`。

### 2. nginx 配置

配置`nginx.cnf`

`http`模块中添加`ssl`服务 ，一般通过`443`默认签证端口来转发到`http`端口。

所以你应用部署的端口不要设置为`https`的，比如`vue`使用`http`默认的`80`端口，让`443`转发，`ssl`负责签证和转发。

```conf
server {
        listen       443 ssl;
        # 可先通过 本地 ping 该域名
        server_name  ${域名};

        ssl_certificate      /usr/local/nginx/conf/cert/***.**.pem;
        ssl_certificate_key  /usr/local/nginx/conf/cert//***.**..key;

        ssl_session_cache    shared:SSL:1024m;
        ssl_session_timeout  10m;

        ssl_ciphers  HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers  on;

        #ssl_ciphers ECDHE-RSA-ASE128-GCM-SHA256:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

        location / {
            proxy_pass  http://${本机外网}:${端口};
        }
    }
```

### 3. nginx 部署

进入`nginx`解压包目录

```shell
bash ./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module
```

接着编译下源文件，使用`make`命令

覆盖前先备份好`nginx`执行文件

```shell
cp /usr/local/nginx/sbin/nginx /usr/local/nginx/sbin/nginx.bak
```

确保当前`nginx`不在运行，否则不允许覆盖

在`nginx`解压包目录中，覆盖原有`nginx`可执行文件

```shell
 cp ./objs/nginx /usr/local/nginx/sbin/
```

### 4. nginx 启动

出现502问题，首先看看协议对不对，转发端口是否开放，再查看`./logs/error.log`日志。