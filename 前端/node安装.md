## Linux 安装node npm

### 下载Node

进入Node最新版下载 https://nodejs.org/en/download/current

### 安装Node
- 解压

   `tar -xvf node-v13.11.0-linux-x64.tar.xz`

- 添加环境变量

    通过修改.bashrc文件:
    vim ~/.bashrc

    在最后一行添上：export PATH=/usr/nodeJs/bin/:$PATH
    生效方法：（有以下两种）
    1.  关闭当前终端窗口，重新打开一个新终端窗口就能生效
    2.  输入“source ~/.bashrc”命令，立即生效

- 添加软链的方式

    添加node npm软链
    ln -s /www/node-v13.11.0-linux-x64/bin/node /usr/local/bin/node
    ln -s /www/node-v13.11.0-linux-x64/bin/npm /usr/local/bin/npm

### 测试
node -v
npm -v

### 加速npm

- 使用腾讯云镜像源

    `npm config set registry http://mirrors.cloud.tencent.com/npm/`

- 验证
    `npm config get registry`

