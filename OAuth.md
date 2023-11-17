### 什么是OAuth2.0

OAuth 2.0 是一个行业的**标准授权协议**。OAuth 2.0 专注于简化客户端开发人员，同时为 Web 应用程序，桌面应用程序，手机和客厅设备提供特定的授权流程。

它的最终目的是为第三方应用颁发一个有时效性的令牌 token。使得第三方应用能够通过该令牌获取相关的资源。常见的场景就是：第三方登录。当你想要登录某个论坛，但没有账号，而这个论坛接入了如 QQ、Facebook 等登录功能，在你使用 QQ 登录的过程中就使用的 OAuth 2.0 协议。



### 协议中的角色

- resource owner 资源所有者，能够允许访问受保护的资源实体。

- resource server 资源服务器，托管受保护资源的服务器。

- client 客户端，简单的说就要请求授权的应用。

- authorization server 授权服务器，能够向客户端颁发令牌

- user-agent 用户代理，帮助资源所有者与客户端沟通的工具，一般为 web 浏览器，移动 APP 等。



假如我想要在 `coding.net` 这个网站上用 `github.com` 的账号登录。那么 coding 相对于 github 就是一个客户端。而我们用什么操作的呢？浏览器，这就是一个用户代理。当从 github 的授权服务器获得 token 后，coding 是需要请求 github 账号信息的，从哪请求？从 github 的资源服务器

![image](./images/oauth2-roles.jpg)

- (A) Client 请求 Resource Owner 的授权。授权请求可以直接向 Resource Owner 请求，也可以通过 Authorization Server 间接的进行。
- (B) Client 获得授权许可。
- (C) Client 向 Authorization Server 请求访问令牌。
- (D) Authorization Server 验证授权许可，如果有效则颁发访问令牌。
- (E) Client 通过访问令牌从 Resource Server 请求受保护资源。
- (F) Resource Server 验证访问令牌，有效则响应请求。它与认证服务器，可以是同一台服务器，也可以是不同的服务器。

OAuth的作用就是让"客户端"安全可控地获取"用户"的授权，与"服务商提供商"进行互动。上述就是coding.net可以安全可控的获取我在github.com上的授权。

不难看出，上面6个步骤之中，**B是关键，就是用户怎么样才能给client授权？Client获得授权许可后，就能向授权服务器获取令牌，而凭令牌获取资源（访问API）**。

### OAuth的思路

OAuth在"客户端"与"服务提供商"之间，设置了一个授权层（authorization layer）。“客户端"不能直接登录"服务提供商”，只能登录授权层，以此将用户与客户端区分开来。"客户端"登录授权层所用的令牌（token），**与用户的密码不同。用户可以在登录的时候，指定授权层令牌的权限范围和有效期。**

"客户端"登录授权层以后，"服务提供商"根据令牌的权限范围和有效期，向"客户端"开放用户储存的资料。



### 客户端的授权模式

客户端必须得到用户的**授权**（authorization grant）才能获取**令牌（access token）**

- 授权码模式（authorization code）

- 简化模式（implicit）隐式token

- 密码模式（resource owner password credentials）

- 客户端模式（client credentials）

#### 授权码模式

大致流程：

1. 资源拥有者（用户）需要登录客户端（APP），他选择了第三方登录。
2. 客户端（APP）重定向到第三方授权服务器。此时客户端携带了客户端标识（client_id），那么第三方就知道这是哪个客户端，资源拥有者确定（拒绝）授权后需要重定向到哪里。
3. 用户确认授权，客户端（APP）被重定向到注册时给定的 URI，并携带了第三方给定的 code。
4. 在重定向的过程中，客户端拿到 code 与 `client_id`、`client_secret` 去授权服务器请求令牌，如果成功，直接请求资源服务器获取资源，整个过程，用户代理是不会拿到令牌 token 的。
5. 客户端（APP）拿到令牌 token 后就可以向第三方的资源服务器请求资源了。
![image](./images/o)
![[images/20180521135707468.jpg]]

1. GET请求点击登录，重定向到github的授权端点

   >https://github.com/login/oauth/authorize?  response_type=code&  client_id=a5ce5a6c7e8c39567ca0&  redirect_uri=https://coding.net/api/oauth/github/callback&  scope=user:email
   >
   >
   >
   >| response_type | 必须，固定为 code，表示这是一个授权码请求。                  |
   >| ------------- | ------------------------------------------------------------ |
   >| client_id     | 必须，在 github 注册获得的客户端 ID。                        |
   >| redirect_uri  | 可选，通过客户端注册的重定向 URI（一般要求且与注册时一致）。 |
   >| scope         | 可选，请求资源范围，多个空格隔开。                           |
   >| state         | 可选（推荐），如果存在，原样返回给客户端。                   |
   >
   >返回值：
   >
   >```url
   >https://coding.net/api/oauth/github/callback?code=fb6a88dc09e843b33f
   >```

2. POST请求 获取令牌Token，当获取到授权码 code 后，客户端需要用它获取访问令牌：

> https://github.com/login/oauth/access_token?  client_id=a5ce5a6c7e8c39567ca0&  client_secret=xxxx&  grant_type=authorization_code&  code=fb6a88dc09e843b33f&  redirect_uri=https://coding.net/api/oauth/github/callback

> 出于安全考虑 client_id 和 client_secret 可以通过 HTTP Basic 认证：Authorization: Basic YTVjZTVhNmM3ZThjMzk1NjdjYTA6eHh4eA==

| grant_type    | 必须，固定为 authorization_code／refresh_token。             |
| ------------- | ------------------------------------------------------------ |
| code          | 必须，上一步获取到的授权码。                                 |
| redirect_uri  | 必须（如果请求/authorize接口有），完成授权后的回调地址，与注册时一致。 |
| client_id     | 必须，客户端标识。                                           |
| client_secret | 必须，客户端密钥。                                           |

返回值

```json
{
  "access_token":"a14afef0f66fcffce3e0fcd2e34f6ff4",
  "token_type":"bearer",
  "expires_in":3920,
  "refresh_token":"5d633d136b6d56a41829b73a424803ec"
}

access_token：表示访问令牌，必选项。

token_type：表示令牌类型，该值大小写不敏感，必选项，可以是bearer类型或mac类型。

expires_in：表示过期时间，单位为秒。如果省略该参数，必须其他方式设置过期时间。

refresh_token：表示更新令牌，用来获取下一次的访问令牌，可选项。

scope：表示权限范围，如果与客户端申请的范围一致，此项可省略。

HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Cache-Control: no-store
Pragma: no-cache

{
  "access_token":"2YotnFZFEjr1zCsicMWpAA",
  "token_type":"example",
  "expires_in":3600,
  "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",
  "example_parameter":"example_value"
}

从上面代码可以看到，相关参数使用JSON格式发送（Content-Type: application/json）。此外，HTTP头信息中明确指定不得缓存。

```

#### 简化模式

这种模式直接在浏览器中向认证服务器申请令牌，跳过申请授权码这个步骤，因此得名。**所有步骤都在浏览器中完成，令牌对访问者是可见的，且客户端不需要认证**

![img](./images/20180521140404957.jpg)

```
（A）客户端将用户导向认证服务器。

（B）用户决定是否给于客户端授权。

（C）假设用户给予授权，认证服务器将用户导向客户端指定的"重定向URI"，并在URI的Hash部分包含了访问令牌。

（D）浏览器向资源服务器发出请求，其中不包括上一步收到的Hash值。

（E）资源服务器返回一个网页，其中包含的代码可以获取Hash值中的令牌。

（F）浏览器执行上一步获得的脚本，提取出令牌。

（G）浏览器将令牌发给客户端。
```

1. 客户端发出的HTTP请求，包含以下参数

​	response_type：表示授权类型，此处的值固定为"token"，必选项。

​	client_id：表示客户端的ID，必选项。

​	redirect_uri：表示重定向的URI，可选项。

​	scope：表示权限范围，可选项。

​	state：表示客户端的当前状态，可以指定任意值，认证服务器会原封不动地返回这个值。

```
https://github.com/login/oauth/authorize?
  response_type=token&
  client_id=a5ce5a6c7e8c39567ca0&
  redirect_uri=https://coding.net/api/oauth/github/callback&
  scope=user:email
```

2. C步骤中，认证服务器回应客户端的URI，包含以下参数：

 	access_token：表示访问令牌，必选项。

​	token_type：表示令牌类型，该值大小写不敏感，必选项。

​	expires_in：表示过期时间，单位为秒。如果省略该参数，必须其他方式设置过期时间。

​	scope：表示权限范围，如果与客户端申请的范围一致，此项可省略。

​	state：如果客户端的请求中包含这个参数，认证服务器的回应也必须一模一样包含这个参数。

```
https://coding.net/api/oauth/github/callback#
  access_token=a14afef0f66fcffce3e0fcd2e34f6ff4&
  token_type=token&
  expires_in=3600
```

3. 用户代理提取令牌 token 并提交给 coding

4. coding 拿到 token 就可以获取用户信息了

> curl -H "Authorization: token a14afef0f66fcffce3e0fcd2e34f6ff4" https://api.github.com/user

#### 密码模式

密码模式（Resource Owner Password Credentials Grant）中，用户向客户端提供自己的用户名和密码。客户端使用这些信息，向"服务商提供商"索要授权。

在这种模式中，用户必须把自己的密码给客户端，但是客户端不得储存密码。这通常用在用户对客户端高度信任的情况下，比如客户端是操作系统的一部分，或者由一个著名公司出品。而认证服务器只有在其他授权模式无法执行的情况下，才能考虑使用这种模式。

下面是一个例子

```http
POST /token HTTP/1.1
Host: server.example.com
Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
Content-Type: application/x-www-form-urlencoded

grant_type=password&username=johndoe&password=A3ddj3w


grant_type：表示授权类型，此处的值固定为"password"，必选项。

username：表示用户名，必选项。

password：表示用户的密码，必选项。

scope：表示权限范围，可选项。

```

认证服务器向客户端发送访问令牌，下面是一个例子:

```http
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Cache-Control: no-store
Pragma: no-cache

{
  "access_token":"2YotnFZFEjr1zCsicMWpAA",
  "token_type":"example",
  "expires_in":3600,
  "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",
  "example_parameter":"example_value"
}
```



#### 客户端模式

客户端模式（Client Credentials Grant）指客户端以自己的名义，而不是以用户的名义，向"服务提供商"进行认证。严格地说，客户端模式并不属于OAuth框架所要解决的问题。在这种模式中，用户直接向客户端注册，客户端以自己的名义要求"服务提供商"提供服务，其实不存在授权问题。

这种模式只需要提供 `client_id` 和 `client_secret` 即可获取授权。一般用于后端 API 的相关操作

步骤：

```
（A）客户端向认证服务器进行身份认证，并要求一个访问令牌。

（B）认证服务器确认无误后，向客户端提供访问令牌。
```

客户端发出的HTTP请求，包含以下参数:

```http
POST /token HTTP/1.1
Host: server.example.com
Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials

granttype：表示授权类型，此处的值固定为"clientcredentials"，必选项。

scope：表示权限范围，可选项。
```

