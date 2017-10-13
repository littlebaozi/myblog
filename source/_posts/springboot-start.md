---
title: Spring Boot使用入门
date: 2017-10-13 09:22:25
tags:
- Spring Boot
categories:
- 框架
- JAVA
---

## 开发工具IDEA
1. 右键新建没有`java Class`,将src Mark as Sources
{% asset_img project_structure.png project structure 设置 %}

## 构建工具gradle
1. 用阿里云加速：在`C:\Users\uername\.gradle`目录下新建文件`init.gradle`，写入以下内容
```java
allprojects{
    repositories {
        def ALIYUN_REPOSITORY_URL = 'http://maven.aliyun.com/nexus/content/groups/public'
        def ALIYUN_JCENTER_URL = 'http://maven.aliyun.com/nexus/content/repositories/jcenter'
        all { ArtifactRepository repo ->
            if(repo instanceof MavenArtifactRepository){
                def url = repo.url.toString()
                if (url.startsWith('https://repo1.maven.org/maven2')) {
                    project.logger.lifecycle "Repository ${repo.url} replaced by $ALIYUN_REPOSITORY_URL."
                    remove repo
                }
                if (url.startsWith('https://jcenter.bintray.com/')) {
                    project.logger.lifecycle "Repository ${repo.url} replaced by $ALIYUN_JCENTER_URL."
                    remove repo
                }
            }
        }
        maven {
            url ALIYUN_REPOSITORY_URL
            url ALIYUN_JCENTER_URL
        }
    }
}
```

2. exception in phase class generation in source unit _BuildScript_ unsupported Target MODULE
开发环境：jdk 9, gradle 4.2。java环境退回到jdk 8
