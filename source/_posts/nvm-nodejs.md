---
title: Windows下安装nvm
date: 2017-08-30 19:02:55
tags: node.js
categories: 工具
---

现在做前端开发已经离不开node环境了。有些工具框架之类的又node的版本要求，所以切换node版本的工具是极好的。目前有两个工具，一个是tj大神的[n](https://github.com/tj/n)，另一个是[nvm](https://github.com/creationix/nvm)。工具上总有二选一这种艰难选择的情况。然后我选择了nvm，不要问我为什么，因为我瞎选的ㄟ( ▔, ▔ )ㄏ 。

<!-- more -->

我的开发环境是windows，所以下面来说说windows下的安装步骤：
### 1. 先卸载已经安装的node
最好是先别装node，保持干净环境。如果装了请把全局的npm的包卸载删除。这是因为用了nvm后，全局安装会安装到nvm下的当前版本node的`node_modules`目录，然而之前全局的npm包命令还能用有干扰，用npm-check工具无法更新，一直会检测原来的`node_modules`目录。

### 2. 安装nvm
windows下有专门的nvm安装包，前去[下载](https://github.com/coreybutler/nvm-windows/releases)。
下载完成后下一步下一步就行了，安装目录可以改一下，我安装在了`D:/dev/nvm`下。
安装完了，打开cmd，输入nvm，出现下面的东西是安装成功了。

{% asset_img nvm.jpg nvm %}
 
### 3. 安装node
如果觉得下载太慢，可以使用国内的镜像源。
在nvm安装目录下面，打开settings.txt。修改`node_mirror`、`npm_mirror`成淘宝的镜像
```
node_mirror: http://npm.taobao.org/mirrors/node/ 
npm_mirror: https://npm.taobao.org/mirrors/npm/
```

{% asset_img setting.jpg settings.txt %}

接下来就可以愉快的用命令安装了。
```bash
nvm list available   # 查看线上的node版本
nvm list             # 查看本地已安装的node版本
nvm install 8.4.0    # 安装想要node版本，默认安装的是64位
nvm use 8.4.0        # 安装完成后或者切换版本需要use将环境切换到想要的版本
nvm uninstall 8.4.0  # 卸载node
```

### 4. 设置全局npm
全局安装的npm包会安装在当前node版本的npm目录下面，切换node版本也会切换npm版本，上一个版本全局安装的包会无法使用。所以设置有一个全局npm比较好。
```bash
npm config set prefix "D:\dev\nvm\npm-global"
```
运行上面的命令后，会在你的用户名文件夹下面生成`.npmrc`文件，里面是：
```
prefix=D:\dev\nvm\npm-global
```
然后，`npm install npm -g`，npm就安装在了`D:\dev\nvm\npm-global`下面了。

* 最重要的还要设置全局的npm环境变量，把当前npm的执行目录改成刚才下载的npm。
新增环境变量`NPM_HOME`，值为`D:\dev\nvm\npm-global`，在path中添加`%NPM_HOME%`。一定要添加在 %NVM_SYMLINK%之前。

### 4. 安装其他好用的工具
`cnpm`淘宝的镜像源
`nrm`npm镜像源切换管理工具
`npm-check`npm包的更新检测安装工具
```bash
npm install -g cnpm nrm npm-check --registry=http://r.cnpmjs.org
```

