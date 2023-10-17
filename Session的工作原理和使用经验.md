## Session的工作原理和使用经验

### 前言

#### 什么是Session？

字面义就是会话，由于HTTP是无状态协议，为了保持浏览器与服务器之间的联系，才有了Session。Session就是用于在服务器端保存用户状态的协议。通常保存用户登录状态。

### 工作原理

#### Session是如何实现的？

session通常是保存再内存中，当然也可以保存在文件，数据库等等。

客户端与服务端通过SessionId关联，

SessionId通常以Cookie的形式存储在客户端。

每次HTTP请求，SessionId都会随着Cookie被传递到服务器端，这行就可以通过SessionId取到对应的信息，来判断这个请求来自于哪个客户端/用户。

#### 如果没有Cookie的话Session还能用吗

Cookie可以说是Session技术中至关重要的一环。如果客户端禁用了Cookie，那么Seesion就无法正常工作。

是不是没有Cookie就一定无法工作？
答案是，不一定，通常情况下，SessionId均由Cookie负责保存，但是客户端跟服务器端通过HTTP交互，除了Cookie可以携带信息之外，URL也可以。不过对用户不友好，所以基本上没有互联网项目会采用这种方案。

Session是一种协议，是保持用户状态的协议。
也就是说，只要能通过SessionId来识别客户端，并能将客户端对应的用户状态保存在服务器端，都可以认为是Session的实现。不论Session是保存在服务器内存，还是数据库，还是memcached、redis。

### 使用建议/经验

#### 建议&经验

- Session中保存的数据的大小要考虑到存储上线不论是内存还是数据库
- Session中不要存储不可恢复的内容
- 依赖Session的关键业务一定要确保客户端开启了Cookie
- 注意Session的过期时间
- 在负载均衡的情况下，由于存在Web服务器内存中的Session无法共享，通常需要重写Session的实现。

#### 常见的Session丢失的问题

- Session内容的丢失都是有原因的，通常都是由于Web服务器的重启造成的，比如IIS、Tomcat的重启