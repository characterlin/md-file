在node的世界里面，并不存在nginx或者apache，甚至tomcat这种东东。一个node，本身就用几行代码，就可以启动个server进程，监听个端口，为大家提供web服务。这和传统的网站代码的部署，是极为不一致的。


## pm2进程管理工具
1. 管理node进程
2.  性能监控，进程守护，负载均衡等


## 运行 pm2 start

``` shell
    pm2 start <js文件路径>.js
    pm2 start <json描述文件路径>.json
    pm2 start <python文件路径>.py --interpreter python
    pm2 start <sh文件路径>.sh --interpreter bash
    pm2 start ./node_modules/<某模块名称>/<模块主文件路径>.js
    pm2 start <某种方式> -- --param_name param_value
    pm2 start npm -- start
    pm2 start npm -- run <scriptname>
    pm2 start yarn -- start
    pm2 start yarn -- run <scriptname>
    pm2 start <某种方式> --watch
```


### 安装pm2
    npm install pm2 -g

### 启动
    cd 目标文件夹

    # --watch 加不加都可以 意思是检测到代码变化 自动重启

    pm2 start bin/www --watch


### 其它
    # 启动进程/应用 
    pm2 start bin/www
    
    # 重命名进程/应用 
    pm2 start app.js --name wb123、
    
    # 添加进程/应用 
    pm2 start bin/www
    
    # 结束进程/应用 
    pm2 stop www
    
    # 结束所有进程/应用 
    pm2 stop all
    
    # 删除进程/应用 pm2 
    delete www
    
    # 删除所有进程/应用 
    pm2 delete all
    
    # 列出所有进程/应用 
    pm2 list
    
    # 查看某个进程/应用具体情况
    pm2 describe www
    
    # 查看进程/应用的资源消耗情况
    pm2 monit
    
    # 查看pm2的日志 
    pm2 logs 序号/名称
    
    # 若要查看某个进程/应用的日志,使用 
    pm2 logs www
    
    # 重新启动进程/应用 
    pm2 restart www
    
    # 重新启动所有进程/应用 
    pm2 restart all