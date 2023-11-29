



### git 设置全局用户名以及邮箱

```shell
git config --global user.name "Your Name"

git config --global user.email "youremail@example.com"
```

### git 设置本地仓库用户名以及邮箱

```shell
git config user.name "Your Name"

git config user.email "youremail@example.com"
```

### git 设置代理

#### 两种情况：

**第一种情况自己有vpn**，网页可以打开github。说明命令行在拉取/推送代码时并没有使用vpn进行代理

**第二种情况没有vpn**，这时可以去某些网站上找一些[代理ip]+port

#### socks5代理

**配置socks5代理**

```shell
git config --global http.proxy socks5 127.0.0.1:7890
git config --global https.proxy socks5 127.0.0.1:7890
```



#### HTTP代理配置

**http代理**

```shell
git config --global http.proxy 127.0.0.1:7890
git config --global https.proxy 127.0.0.1:7890
```



#### 查看代理命令

```shell
git config --global --get http.proxy
git config --global --get https.proxy
```



#### 取消代理命令

```shell
git config --global --unset http.proxy
git config --global --unset https.proxy
```

