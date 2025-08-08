

[[OAuth2.0]]
[[OpenID Connect]]


# 1 开源系统

- https://gitee.com/dromara/sa-token
- https://www.oschina.net/project/tag/233/sso
- https://www.bilibili.com/read/cv7206185/
- https://gitee.com/mkk/oauth2-shiro
- https://gitee.com/cdk8s/tkey
- Sa-Token
- smart-sso
- tkey


# 2 协议

- SAML
- OAuth
- OIDC (OpenID Connect)
- CAS




# 3 SSO 和 OAuth 的区别

- 使用场景

```
- oauth 一次注册, 多出使用
- sso 一次登录, 多处同时登录
```

- 灵感

```
- 当用户在认证系统登录过后, 该用户拿到了凭证, 就相当于在多个系统同时登录了?
- 但是为什么各个服务方还需要拿凭证去换Token呢?
- Token是解决登录问题, 还是授权问题呢?
- 凭证是否只是服务方验证用户是否已经登录?
```

- spring security && spring security oauth2.0

```
一个安全框架
可以用来实现认证服务器, 管理共享session, 解决单点登录问题
```

- 讨论

```
oauth2.0 只是个授权规范
sso是个概念, 是个需求, oauth是实现方法(规范), 还有其他方法: saml, radius, kerberos
```


# 4 Cookie、Session、Token、JWT 之间的区别

它们都是为了解决记录用户状态的方法, 因为HTTP对于每次请求都不会记录是谁的请求(无状态), 这就好比顾客每次来光顾后, 老板都会把他忘记.

每次都不会记住谁是谁, 这样对于某些业务情况很不方便, 所以就有记录用户状态的各种技术的诞生.

- Cookie

用来记录用户信息, 每次请求浏览器都会帮助携带上Cookie, 这个机制是浏览器自动帮我们完成的

- Session

Cookie将用户信息保存到用户这边不安全, Session是将用户信息保存到服务端, 只向用户下发一个ID, 本质还是Cookie

- Token

由于Cookie机制是浏览器自动完成的, 如果发起请求用的不是浏览器怎么办呢? Token就是解决办法.

Token本质和Session一样, 但是没有Cookie机制, 需要自己维护. 并遵循Bearer规范, 需要将信息保存到Authorization头字段中, 而不是Cookie字段.(这里没有提到Token在客户端方是如何存储了, 在"讨论"里有说明)

- JWT

Session和Token都是将信息保存到服务端, 但是遇到分布式的情况, 共享会话信息是个麻烦事还影响性能, JWT和Cookie一样, 也是将用户信息保存在客户端, 使用JWT规范, 信息内容由三部分组成使得更安全些.

```
JWT的三个部分:
1. header 哈希算法
2. payload 用户信息
3. signature 签名

签名是经过服务端加密的, 所以其他客户端无法伪造JWT.
JWT一般保存在客户端的 localstorage
```


- 延申

localstorage, sessionstorage 是什么?

- 讨论

cookie是浏览器的存储机制，应该和localstorage、sessionStorage、indexedDB、webSQL这种对比. 强调cookie只是信息的载体, 并不是信息本身, 不要跟token和jwt混淆了. token和jwt可以用cookie存储和传递, 浏览器会自动处理, 但是会遇到跨区问题, 这个时候可以使用localstorage存储, 请求的时候取出来, 封装到Header中传递.


# 5 sso 实现细节

认证服务器一般会使用filter来验证请求是否携带token. 没有登录, 响应301重定向


# 6 演进

- 非单点登录
    - 去游乐园, 玩每个项目设施都需要买对应的票去玩, 太麻烦
- 单点登录
    - 去游乐园, 买一张通票, 这样一张票就能玩所有的项目设施(单点登录的雏形)

- 同域单点
    - 多个系统需要在同一个域名下, 当用户访问系统A时, 会将sessionid同步给同域名下的系统B, 用户就可以直接访问系统B了.
    - 系统A就充当了SSO服务器
- 同父域
    - http://www.xxxx.aaa.com和http://www.xxxx.bbb.com 的情况下需要设置cookie的domain为http://www.xxxx.com
- 跨域CAS
    - CAS 需要一个中心服务器, 用户通过中心服务器登录, 各个系统通过中心服务器验证用户身份


# 7 CAS和OAuth的区别


- CAS

CAS的单点登录时保障客户端的用户资源的安全，客户端要获取的最终信息是，这个用户到底有没有权限访问我（CAS客户端）的资源。

CAS的单点登录，资源都在客户端这边，不在CAS的服务器那一方。用户在给CAS服务端提供了用户名密码后，作为CAS客户端并不知道这件事。随便给客户端个ST，那么客户端是不能确定这个ST是用户伪造还是真的有效，所以要拿着这个ST去服务端再问一下，这个用户给我的是有效的ST还是无效的ST，是有效的我才能让这个用户访问。

- OAuth

OAuth2.0则是保障服务端的用户资源的安全，获取的最终信息是，我（OAuth2.0服务提供方）的用户的资源到底能不能让你（OAuth2.0的客户端）访问

所以在最安全的模式下，用户授权之后，服务端并不能直接返回token，通过重定向送给客户端，因为这个token有可能被黑客截获，如果黑客截获了这个token，那用户的资源也就暴露在这个黑客之下了。

于是聪明的服务端发送了一个认证code给客户端（通过重定向），客户端在后台，通过https的方式，用这个code，以及另一串客户端和服务端预先商量好的密码，才能获取到token和刷新token，这个过程是非常安全的。

如果黑客截获了code，他没有那串预先商量好的密码，他也是无法获取token的。这样oauth2就能保证请求资源这件事，是用户同意的，客户端也是被认可的，可以放心的把资源发给这个客户端了.




# 8 问题

- 跨域是什么?
    - 由浏览器的同源策略引起. 同源策略是什么?
    - 同源策略是否只在一个浏览器标签页下生效?
- redirect，cors 是什么?
- Cookie 为什么不能跨域?
    - cookie中有个domain属性, 当服务器下发给cookie时会带上域名信息
    - 浏览器在访问某域名的服务时, 会寻找该域名的cookie, 所以不能用其它域名的cookie访问本域名的服务
    - Cookie的作用域是什么?
- oauth2只能授权没有认证, 为什么很多实现sso的方案都是使用oauth2完成的, 那么认证的部分是如何做的呢?
    1. 授权码和隐式授权可以实现sso
    2. 授权服务器可以实现身份认证的功能
- SSO获取Token后, 每次访问资源是否都需要到授权服务器上验证token?
    1. 看token的有效性决定是否验证
    2. 如果使用JWT, 资源服务器可以自验证
    3. fresh token
- 为什么CAS只实现了认证, 而Oauth只实现了授权, 有人说Oauth没有认证, 只要拿着有效的凭证就能获取资源, 还不关心凭证的持有人是谁呢?


# 9 其它


- SSO本质上属于一种需求。
- CAS（中央认证服务）本质上是一个框架，用于实现SSO的需求，但是并不提供具体的解决方案，只是规定了CAS Server和CAS Client的关系，其中中央认证服务器必须单点部署，而其他可以分布部署。
- OAuth2是一个协议，用于解决CAS框架下，客户资源安全的问题。通过中央认证服务器提供的Token来保证用户资源不会被窃取。同样SAML也是一个协议。
- JWT是一种具体的技术实现方案，通过在请求头部置入一个加密后的多段式Json（一般至少是三段：header, payload, signature）来保证token的可靠性。
- 不跨域不存在单点登录问题；
- 非浏览器端不存在单点登录问题；
- 浏览器跨域解决单点登录的方法就只有：redirect，cors
- 什么session，什么令牌，这些都是登录问题，不是单点登录问题。
- SSO是一种广泛应用的身份验证方案，许多其他技术（如CAS、SAML、OAuth2）都实现了SSO的功能。
- 解决方案:
    - Keycloak
    - Auth0
    - Okta
    - OneLogin
    - Gluu
- CSRF 是什么?  跨域请求伪造
- CORS?  跨源资源共享
- 服务的无状态和有状态
- Basic Auth and Bearer Token 是什么?
    - Bearer Token: 服务端随机发给客户端一个token, 客户端每次请求需要携带这个token

- 服务端给客户端传递信息的方式

```
- Cookie
- URL重写: 信息附加在url中
- 自定义HTTP头
```



# 10 相关技术

- 多因素认证 (MFA)
- 集中式身份管理: LDAP, 对于SSO单一的登录机制更加完善
- 基于令牌的身份验证: JWT, 可以独立SSO实现, 也可以结合SSO



# 11 参考

- https://blog.csdn.net/u010786653/article/details/128737038
- https://juejin.cn/post/6945277725066133534
- https://www.cloudentify.com/archives/834


