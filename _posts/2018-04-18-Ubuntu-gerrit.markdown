---
layout:     post
title:      "Ubuntu 配置 Gerrit 服务"
subtitle:   " \"Ubuntu Gerrit Setup\""
date:       2018-04-18 20:00:00
author:     "Aaron"
header-img: "img/post-bg-2015.jpg"
catalog: false
tags:
    - 运维生存指南
---



### Ubuntu 配置 Gerrit 服务

#### 1. 从 docker 镜像直接启动容器
```
mkdir ~/gerrit_volume
```
```
docker run -d -v ~/gerrit_volume:/var/gerrit/review_site -p 8080:8080 -p 29418:29418 -e AUTH_TYPE=HTTP openfrontier/gerrit
```

访问 http://your_host:8080， 应该能看到代理配置错误的页面

#### 2. 安装 nginx 服务器
```
sudo apt-get update
sudo apt-get install nginx
```

#### 3. 配置反向代理

创建代理配置文件
```
cd /etc/nginx/sites-available
sudo vim gerrit
```

```
server {
    listen *:8898;
    server_name gerrit.test.com;
    allow   all;
    deny    all;

    auth_basic "Welcomme to Gerrit Code Review Site!";
    auth_basic_user_file /home/aaron/gerrit/gerrit.password;

    location / {
        proxy_pass  http://127.0.0.1:8080;
        proxy_set_header X-Forwarded-For $remote_addr;
        proxy_set_header Host $host;
    }
}
```

```
sudo ln -s /etc/nginx/sites-available/gerrit /etc/nginx/sites-enable/gerrit
sudo service nginx reload
```

访问 http://your_host:8898，应该能看到http登陆验证弹框

#### 4. 配置 password
创建一个加密文件，也就是代理配置文件中的 auth_basic_user_file
```
htpasswd -bc /home/aaron/gerrit/gerrit.password gerrituser gerritpassword
```

访问 http://your_host:8898，就应该可以用 gerrituser/gerritpassword 登陆了





