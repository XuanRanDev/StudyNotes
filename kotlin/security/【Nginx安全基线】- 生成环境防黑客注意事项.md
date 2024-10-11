# 【Nginx安全基线】- 生成环境防黑客注意事项

Nginx是一款高性能的HTTP和反向代理服务器。

### 1. Nginx后端服务指定的Header隐藏状态

为了防止攻击者通过HTTP头部信息识别服务器类型或版本，我们需要隐藏不必要的HTTP头部。具体步骤如下：
1. 打开`conf/nginx.conf`配置文件（或主配置文件中的include文件）。
2. 在`http`下配置`proxy_hide_header`项，添加或修改为：
   ```nginx
   proxy_hide_header X-Powered-By;
   proxy_hide_header Server;
   ```
   **目的**：通过隐藏敏感信息，减少被攻击的可能性，提升安全性。

### 2. Nginx的WEB访问日志记录状态

记录访问日志可以帮助分析访问情况和潜在的安全威胁。应为每个核心站点启用`access_log`指令，具体步骤如下：
1. 打开`conf/nginx.conf`配置文件（或主配置文件中的include文件）。
2. 在`http`下配置`access_log`项：
   ```nginx
   access_log logs/host.access.log main;
   ```
3. 确保在主配置文件及包含文件中删除`off`项或配置为适当值。
**目的**：通过记录访问日志，及时发现异常请求，分析攻击行为。

### 3. 确保NGINX配置文件权限为644

将配置文件的权限控制为644，以保护其不被恶意修改。具体操作：
```bash
chmod 644 <conf_path>/nginx.conf
```
**目的**：降低外部攻击者利用配置文件漏洞进行攻击的风险。

### 4. 检查Nginx进程启动账号

确保Nginx服务不以root身份运行，以降低安全风险。步骤如下：
1. 打开`conf/nginx.conf`配置文件。
2. 查看`user`配置项，确认其不是`root`。如果是，则修改为`nobody`或`nginx`用户。
3. 修改完后，重新启动Nginx服务。
**目的**：避免root用户的权限被滥用，降低被攻击的风险。

### 5. 任意文件读取

不当的配置可能导致目录穿越攻击。避免使用以下配置：
```nginx
location /path {
    alias /home/;
}
location /files {
    root /;
}
```
建议的安全配置如下：
```nginx
location /path/ {
    alias /home/;
}
location /files {
    root /path/;
}
```
**目的**：通过正确配置，防止目录穿越，确保文件访问安全。

### 6. PHP-fpm 远程代码执行漏洞

错误的配置可能导致远程代码执行漏洞，攻击者可以利用此漏洞来执行任意代码。避免以下配置：
```nginx
location ~ [^/]\.php(/|$) {
    fastcgi_split_path_info ^(.+?\.php)(/.*)$;
    fastcgi_param PATH_INFO $fastcgi_path_info;
    fastcgi_pass php_ip:9000;
    ...
}
```
**目的**：通过正确配置PHP处理，防止代码执行漏洞的发生。

### 7. Nginx/OpenResty在特殊配置下存在内存泄漏或目录穿越漏洞

不当的rewrite指令可能导致内存泄漏和目录穿越。建议升级至最新版本，并避免类似配置：
```nginx
location ~ /memleak {
    rewrite_by_lua_block {
        ngx.req.read_body();
        local args, err = ngx.req.get_post_args();
        ngx.req.set_uri(args["url"], true);
    }
}
location ~ /rewrite {
    rewrite ^.*$ $arg_x;
}
```
**目的**：通过升级和避免不当配置，减少内存泄漏和安全风险。

### 8. proxy_pass反向代理导致的SSRF

确保`proxy_pass`的参数不为用户控制，以防止SSRF攻击。应避免将内网IP暴露在`proxy_pass`中，配置应如下所示：
```nginx
location /proxy/ {
    proxy_pass http://backend_server;
}
```
**目的**：通过控制代理参数，减少SSRF攻击的风险。

