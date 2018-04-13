---
layout:     post
title:      "SonarQube Scanner for Jenkins 配置说明"
subtitle:   " \"SonarQube Scanner for Jenkins\""
date:       2018-04-13 14:00:00
author:     "Aaron"
header-img: "img/post-bg-2015.jpg"
catalog: false
tags:
    - 运维生存指南
---

## SonarQube Scanner for Jenkins 配置说明

#### 1. Jenkins 服务器安装 SonarQube Scanner 插件

#### 2. Jenkins 配置 SonarQube

Manage Jenkins -> Configure System -> SonarQube Servers 配置 Sonar 服务器相关信息

<img class="shadow" src="/img/in-post/sonarjenkins/jenkins-sonar-config.png" width="600">

#### 3. SonarQube 服务配置 Webhooks
配置 Sonar Webooks 的目的是当 Sonar 服务完成检测后，能够通过这个 hook 找到对应的 Jenkins 服务，发送检测结果消息通知 Jenkins 服务。

Administration -> Configuration -> Webhooks
URL=http://yourLogin:yourPassword@yourJenkinsInstanceServer/sonarqube-webhook/

**The trailing slash is mandatory!**

<img class="shadow" src="/img/in-post/sonarjenkins/sonar-webhooks.png" width="600">

#### 4. Jenkins file 增加 Sonar analysis/Qulity Gate stage
```
stage("build & SonarQube analysis") {
    withSonarQubeEnv('My SonarQube Server') {
        sh 'mvn clean package sonar:sonar'
    }
}

stage("Quality Gate"){
    timeout(time: 5, unit: 'MINUTES') {
        def qg = waitForQualityGate()
        if (qg.status != 'OK') {
            error "Pipeline aborted due to quality gate failure: ${qg.status}"
        }
    }
}
```