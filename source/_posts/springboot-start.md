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
*. 右键新建没有`java Class`,将src Mark as Sources
{% asset_img project_structure.png project structure 设置 %}

*. 代码提示不区分大小写
{% asset_img code_completion.png project 代码提示 设置 %}

## 构建工具
### 1. gradle
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

### 2. maven
#### 配置
* 设置repository目录
1. 环境变量增加`M2_REPO`，值为`D:/Dev/apache-maven-3.5.0/repository`，同时把`%M2_REPO%`添加到path中
2. 打开`maven\conf\settings.xml`，找到`localRepository`，根据示例添加以下代码：
```xml
<localRepository>D:/Dev/apache-maven-3.5.0/repository</localRepository>
```
3. 将settings.xml文件复制一份到`D:/Dev/apache-maven-3.5.0/repository`中
4. IDEA的设置`build, execution,deployment -> build tools -> maven`，将`user setting file`、`local repository`定位到自定义目录

### maven生命周期
* clean，移除所有上一次构建生成的文件
* validate，验证工程是否正确，所有需要的资源是否可用
* compile，编译项目的源代码
* test，使用已编译的测试代码，测试已编译的源代码
* package，已发布的格式，如jar，将已编译的源代码打包
* verify，运行任何检查，验证包是否有效且达到质量标准
* install，把包安装在本地的repository中，可以被其他工程作为依赖来使用
* site，生成项目的站点文档
* deploy，在整合或者发布环境下执行，将最终版本的包拷贝到远程的repository，使得其他的开发者或者工程可以共享

### 3. 数据库
#### 1. 引入依赖
1. 在`pom.xml`中增加`spring-boot-starter-jdbc`
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
```
2. 引入mysql连接类和连接池
```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>

<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.0.29</version>
</dependency>
```

#### 2. 配置相关文件
在`application.properties`文件配置mysql的驱动类，数据库地址，数据库账号、密码信息
```xml
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/test
spring.datasource.username=root
spring.datasource.password=123456
```