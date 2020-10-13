---
title: 初探nginx
category:
  - 踩坑
tag:
  - 工具
  - Nginx
  - Linux
abbrlink: 20679
date: 2020-05-09 19:24:33
---

# 起因
接触前端学习一年出头，慢慢的也了解到了很多与前端紧密相关的一些非前端本职，但却很感兴趣的技术，比如docker啊、nginx啊、linux啊、cicd什么的；所以趁着疫情在家，买了服务器、域名，昨天备案也通过了；今天就准备先来试水一下，使用nginx部署一直托管在github的博客


# 相关知识笔记

### Nginx

>什么是nginx
- 一个支持HTTP和反向代理的开源的web服务器

>web服务器
- 基于HTTP协议，处理客户端请求，为客户端提供可供浏览内容的服务器
- 举例：Nginx

>应用服务器
- 用来运行那些可以访问、操作本机资源的应用程序的服务器
- 举例：Tomact

>两者区别
- 不同点：Web服务器只能处理静态资源，接受请求响应资源，结束；应用服务器也可以做请求处理，响应静态资源，但同时还可以处理动态内容，比如根据模板引擎生成页面等
- 相同点：2者都可以处理请求
- 以前开发中，会直接使用应用服务器处理用户请求，然后使用模板引擎动态生成页面给客户端；现在因为前后端分离的原因，以及为了减轻应用服务器压力的前提下；静态资源交给nginx这类web服务器来做，应用服务器只负责处理逻辑，提供数据

### Linux
>概念
- macOS和Windows之外的第三大操作系统，主要用于服务器等，而且开源

>版本
- Ubuntu、centos、debian等

>包安装工具
- 因为使用的Linux不同，所使用的的包管理工具也不同
- Debian/Ubuntu：apt、apt-cache、apt-get、dpkg
- centos：yum
- 备注1：apt是包管理平台，get负责包的安装更新删除管理；cache用于搜索
- 备注2：dpkg是Debian的包管理工具，Ubuntu基于Debian，所以也可以使用；但是两个的区别是dpkg安装会绕过并覆盖apt安装的包，切不关心模块依赖问题；不关心Ubuntu软件库软件；简单理解Ubuntu专用apt，Debian专用dpkg

>apt常用命令
- 安装包：apt-get install <包名>
- 移除包：apt-get remove <包名>
- 更新包列表：apt-get update 
- 更新系统、已安装包：apt-get upgrade
- 列出已安装包：dpkg -l
- 搜索某包：apt-cache search <包名>
- 查看包信息：apt-cache show <包名>

>其他命令
- wget：用来从指定的URL下载文件；类似迅雷
- 

# 基本使用
- 进入/etc/nginx/conf.d/目录，该目录存放自己的配置文件，nginx的主配置文件(/etc/nginx/nginx.conf)会自动加载该目录的配置文件
- 通过以下配置文件，就可以部署一个简单的静态项目
```shell
server{
    # 监听端口号，主配置文件默认监听了80
    listen 2333;
    # 制定ip地址或者域名
    server_name shuaxinjs.cn;
    # 配置对哪些url做出相应
    location / {
        # 配置响应文件的目录
        root /data;
        # 配置目录下的响应文件
        index index.html;
    }
}
```
>说明
- 当在浏览器输入的url匹配到location的url后，就会去内部定义的root或alias目录找要返回的文件
- 如果没有匹配到对应的规则，就会去默认的目录找404.html并响应；这是nginx自带的，一般在/usr/share/nginx/html
- 可以在/var/log/nginx下查看access和error日志文件，来查看访问状态
- root可以写在location也可以写在server中，如果内部有定义root，优先使用root的路径（精确匹配例外）

>alias和root的区别
- 假设此时域名为 [shuaxinjs.cn](shuaxinjs.cn)
```shell
# root
# 这时浏览器输入www.shuxinjs.cn/dist后，nginx会去/data/dist/目录寻找文件

location /dist{
    root /data/;
    index index.html;
}
# alias
# 这时浏览器输入www.shuxinjs.cn/dist2后，nginx会去/home/目录寻找文件
# alias指定目录时，结尾一定要加 /;root可有可无
location /dist2{
    alias /home/;
    index index.html;
}
```
# 常用命令
- 使用nginx命令管理
    ```shell
    nginx -s reload  # 向主进程发送信号，重新加载配置文件，热重启
    nginx -s reopen	 # 重启 Nginx
    nginx -s stop    # 快速关闭
    nginx -s quit    # 等待工作进程处理完成后关闭
    nginx -T         # 查看当前 Nginx 最终的配置
    nginx -t -c <配置路径>    # 检查配置是否有问题，如果已经在配置目录，则不需要-c
    ```
- 使用systemctl管理，这是Linux的系统应用管理工具
    ```shell
    systemctl start nginx    # 启动 Nginx
    systemctl stop nginx     # 停止 Nginx
    systemctl restart nginx  # 重启 Nginx
    systemctl reload nginx   # 重新加载 Nginx，用于修改配置后
    systemctl enable nginx   # 设置开机启动 Nginx
    systemctl disable nginx  # 关闭开机启动 Nginx
    systemctl status nginx   # 查看 Nginx 运行状态
    ```
# 配置

### location配置规则

>基本语法
- 这里不需要像js一样需要用//把规则包裹起来，直接写在启用正则的符号后即可
    ```shell
    location [=|~|~*|^~]/uri/{...}{
        # 具体配置
        # root/alias dist #响应文件目录
        # index file  #响应文件
    }
    ```

### 单独说明
>=
- 精确匹配
- 使用=的时候一定要在server中指定root，会无视location内部的路径
- 下例：当访问shuaxinjs.cn/的时候，nginx就会去/home目录寻找index.html；但是当仅在location指定的时候就会去/usr/nginx/share/html寻找，当然那个目录不存在我们的文件，就会返回404
    ```shell
    ...
    root /home;
    location = /login/ {
        index index.html;
    }
    ...
    ```

>^~
- 表示url以某个常规字符串开始的url，不区分大小写
- 下例：代理后端请求，凡是携带/apis/的url，都会进行去请求转发；即：shuaxinjs.cn/apis/getData会变成localhost:3000/getData
- 这里需要注意apis后面一定要跟/，不然apis会添加到url中
    ```shell
    location ^~ /apis/ {
        proxy_pass http://localhost:3000/;
    }
    ```

>~
- ***区分大小写*** 的正则匹配
- 下例：匹配所有的.(css|js|png|jpg|jepg)的资源文件;
- 正则说明：.为默认匹配除换行符以外的所有字符，\*：默认对前一个规则执行0-n次；\对.号做转意；（css|js|png|jpg|jepg）意思是.后的字符必须是这几种任意一种
    ```shell
    location ~.*\.(css|js|png|jpg|jepg){
        root /home/static/;
    }
    ```
>~*
- ***不区分大小写*** 的正则匹配

>!~ , !~*
- 区分大小写不匹配；不区分大小写不匹配

>/
- 通用匹配，任何请求都会匹配

### 匹配顺序
多个location配置的情况下匹配顺序为：
首先匹配= ；
其次是完整路径；
然后匹配^~；
再其次是按文件中顺序的正则匹配；
最后是交给 / 通用匹配；
当有匹配成功时候，停止匹配，按当前匹配规则处理请求。

# 常用配置
- 匹配静态资源
    ```shell
    location ~.*\.(css|js|png|jpg|jepg){
        root /home/static/;
    }
    ```
- 代理后端请求，解决跨域
    ```shell
     location ^~ /api/ {
        proxy_pass 后端地址;
    }
    ```

- 判断请求客户端为移动端还是pc，返回不同的页面
    ```shell
    server{
        listen 80;
        server_name 域名/ip;
        location / {
        root pc目录；
        root /data/pc;
        if ( $http_user_agent ~* '(Android|webOS|iPhone|iPod|BlackBerry)' ) {
                root 移动端目录;
        }
            index index.html;
        }
    }   
    ```
# 可用全局变量
```shell
$host ： 请求主机头字段，否则为服务器名称。
$http_user_agent ： 客户端agent信息
$http_cookie ： 客户端cookie信息
$request_method ： 客户端请求的动作，通常为GET或POST。
$content_length ： 请求头中的Content-length字段。
$content_type ： 请求头中的Content-Type字段。
$request_uri ： 包含请求参数的原始URI，不包含主机名，如：”/foo/bar.php?arg=baz”。
$uri ： 不包含请求参数的当前URI，$uri不包含主机名，如”/foo/bar.html”。
$document_uri ： 与$uri相同。
$query_string 
$args ： 这个变量等于请求行中的参数，同$query_string
$document_root ： 当前请求在root指令中指定的值。
$limit_rate ： 这个变量可以限制连接速率。
$request_body_file 
$request_filename ： 当前请求的文件路径，由root或alias指令与URI请求生成。
$remote_addr ： 客户端的IP地址。
$remote_port ： 客户端的端口。
$remote_user ： 已经经过Auth Basic Module验证的用户名。
$scheme ： HTTP方法（如http，https）。
$server_protocol ： 请求使用的协议，通常是HTTP/1.0或HTTP/1.1。
$server_addr ： 服务器地址，在完成一次系统调用后可以确定这个值。
$server_name ： 服务器名称。
$server_port ： 请求到达服务器的端口号。
```


