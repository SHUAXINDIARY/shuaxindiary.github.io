---
title: 初探Travis
category:
  - 技术笔记
tag:
  - 踩坑
  - Travis
  - Linux
  - CICD
abbrlink: 55766
date: 2020-05-12 16:41:41
---
# 初探cicd
> 什么是cicd？
- cicd是持续集成和持续交付的缩写，全称为continuous integration & continuous delivery

> cicd怎么做
- 通过一些工具，来进行

>这些工具做了哪些事
- 将我们平时手动去跑的测试、打包、部署，全部通过前期配置，然后在以后的开发中，自动去执行，解放双手
- 举个vue项目的例子：
    ```shell
    # 本地：对项目打包，（正规点还要跑一边测试）这些都没问题后，然后推送到远程仓库
    npm run build -> git push 
    # 服务器：进入对应的目录，然后拉去仓库的代码
    git pull
    ```
- 如果做好cicd的流程后，只需要git push代码就好，剩下的都会在集成工具里去根据配置文件，去自动执行，这个过程哪一步出现问题都会有对应的log文件
<!-- more -->

>目的
- 将繁琐的体力劳动变化自动化，把精力集中在项目核心逻辑开发中，从而间接影响项目的质量
# Travis
- 和github搭配比较好的cicd工具
- 同样的工具还有Gitlab、Jenkins等

# 工作流程
>ci：集成部分
- 用github登陆Travis，指定我们需要进行集成的仓库
- 在该仓库的项目中添加.travis.yml配置文件
- 在配置文件中，配置当我们git push后，需要travis去做的事

>cd：部署部分
- 这里使用ssh密钥免密登陆服务器的原理，来让travis可以免密登陆指定服务器
- 关于ssh登陆的流程和原理：在要登录服务器的机器生成一对秘钥，一公一私，公钥保存到目标服务器，私钥只能自己本地知道；然后当ssh连接目标服务器时，服务器会返回一段用公钥加密的字符串；客户端收到后，用私钥解密；然后在使用私钥加密该字符串，在发送给服务器，服务器使用公钥解密后，确认是自己之前传递的内容，确认成功进行连接
- 关于ssh：安全外科协议，使用非对称加密来进行身份验证

# 本地操作
>项目
- 创建项目，git初始化，添加.travis.yml文件
- git push到github
- 登陆travis，在设置页中，找到对应的仓库，并打开后面的按钮，如图
![travis](/img/jottings/travis.png)
    ```YAML
    # .travis.yml
    # 指定项目语言
    language: node_js
    # 指定版本
    node_js:
    - 10
    # 指定分支：这样只有该分支发生变动后，才会进行操作
    branches: 
    only:
    - serverBackup
    # 安装依赖：如果需要
    # install:
    # - npm install
    # 运行脚本
    script:
    - echo "test"
    ```
>说明
- travis的配置文件是YAML语法
- travis的执行阶段（生命周期）分为：（括号的为可选阶段）
- before_install -> install ->before_script -> script -> aftersuccess or afterfailure ->(before_deploy->deploy->after_deploy)->after_script
- 安装依赖前->安装依赖->执行脚本之前->执行脚本->脚本执行成功后 或者 执行失败后->（部署前->部署->部署后）->脚本执行完成后
- travis每一阶段执行成功后才继续下一步，如果失败立即停止


# 服务端操作
### Linux添加新用户/赋予root权限
- 这里不要换成root创建，用普通用户使用sudo命令添加新用户即可
```shell
# 添加用户
sudo adduser 用户名
# 按提示操作
# 给该用户赋予root权限
# 编辑该文件
vim /etc/sudoers
# 在改行注释下添加
# User privilege specification
用户名 ALL=(ALL:ALL) ALL
```

### 生成SSH秘钥
```shell
# 回到根目录
[~/home]:cd ~
[~]:ssh-keygen -t rsa #-t意思选择生成的加密算法，RSA为非对称加密
```

### 设置该文件权限为700
```shell
# -rw------- (600)      只有拥有者有读写权限。
# -rw-r--r-- (644)      只有拥有者有读写权限；而属组用户和其他用户只有读权限。
# -rwx------ (700)     只有拥有者有读、写、执行权限。
# -rwxr-xr-x (755)    拥有者有读、写、执行权限；而属组用户和其他用户只有读、执行权限。
# -rwx--x--x (711)    拥有者有读、写、执行权限；而属组用户和其他用户只有执行权限。
# -rw-rw-rw- (666)   所有用户都有文件读、写权限。
# -rwxrwxrwx (777)  所有用户都有读、写、执行权限。
# 通过chmod命令可以修改权限
# -----------
# 设置该目录为700
[~]:chmod 700 ~/.ssh/
# 设置该目录下文件为600
chmod 600 ~/.ssh/*
# 查看当前目录下文件信息 可以看到所有文件都属于travis
[~]:ls -al
# 添加生成公钥为受信列表
[~]:cd .ssh/
# 如果没有authorized_keys先创建
[~]:touch authorized_keys
[~/.ssh]:cat id_rsa.pub >> authorized_keys # cat打印文件内容
```

### 配置连接
```shell
# 创建一个配置文件
[~/.ssh]:vim config
# 内容如下
# 测试
[~/.ssh]:ssh test
# 查看是否有known_hosts 有即成功 之后再登陆时就不需要yes
[~/.ssh]:ls
```
```YAML
<!-- 文件内容 -->
Host test
HostName 99.99.99.99(你的服务器ip)
#登陆的用户名
User travis
IdentitiesOnly yes
#登陆使用的密钥
IdentityFile ~/.ssh/id_rsa
```

### 安装travis
travis是ruby写的，所以需要安装ruby环境

>安装rvm：ruby的版本管理工具，类似npx
```shell
# 切回root
# 安装公钥
[root@~]gpg2 --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
# 会提示安装gpg2 安装就好
# 安装rvm
[root@~]:curl -sSL https://get.rvm.io | bash -s stable
# 安装会提示需要添加用户到rvm分组
# 添加用户到新分组且不离开当前所在组
# usermod -G groupA---离开当前所在组，到目标组
[root@~]:usermod -a -G 分组名 待添加用户
# 执行重新执行刚修改的初始化文件
[root@~]:source /etc/profile.d/rvm.sh
# 查看rvm版本
[root@~]:rvm --version
# rvm 1.29.10 (latest) by Michal Papis, Piotr Kuczynski, Wayne E. Seguin [https://rvm.io]
```
>安装ruby和gem---ruby的包管理工具
```shell
[root@~]:rvm install ruby
# 安装完成后查看版本
[root@~]:ruby --version
[root@~]:gem -v
# 查看gem当前镜像
[root@~]:gem sources -l # https://rubygems.org/
# 切换为国内的镜像源
[root@~]:gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/
```
>安装travis命令行工具
```shell
[root@~]:gem install travis
# 切到travis 执行travis
[travis@~]:travis
# 提示yes
# 进入项目目录，开始添加秘钥到仓库
[travis@~/home/xxx]:travis login
# 输入github登陆名和密码
# 添加秘钥到仓库的配置文件中
[travis@~/home/xxx]:travis encrypt-file ~/.ssh/id_rsa  --add
# 打印配置文件看到多了几行
before_install:
- openssl aes-256-cbc -K $encrypted_ff98517d7fe4_key -iv $encrypted_ff98517d7fe4_iv
  -in id_rsa.enc -out ~\/.ssh/id_rsa -d
#   这里需要更改一下把out~后的\去掉，不然会报错这是traviscli的bug
```
>添加目标服务器
- 这里就在配置文件中添加一下目标服务器ip，和登陆后要进行的操作即可
```YAML
language: node_js
node_js:
- 10
branches: 
only:
- serverBackup
script:
- echo "test"
before_install:
- openssl aes-256-cbc -K $encrypted_5ec1b68e2d5b_key -iv $encrypted_5ec1b68e2d5b_iv
  -in id_rsa.enc -out ~/.ssh/id_rsa -d
addons:
ssh_known_hosts:
- ip地址或者域名
after_success:
- chmod 600 ~/.ssh/id_rsa
- ssh publish@shuaxinjs.cn -o StrictHostKeyChecking=no 'cd /home/publish/data/shuaxindiary.github.io && git pull'
```


# 参考链接
- [gem国内镜像](https://gems.ruby-china.com/)
- [阮一峰-cicd](http://www.ruanyifeng.com/blog/2017/12/travis_ci_tutorial.html)
- [travis自动化部署github项目到server](https://zhaosaisai.com/blog/2019/travis%E8%87%AA%E5%8A%A8%E5%8C%96%E9%83%A8%E7%BD%B2github%E9%A1%B9%E7%9B%AE%E5%88%B0server.html)

