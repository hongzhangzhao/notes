

# 1 授权模式(授权流程)

## 1.1 授权码模式

- 客户端首先需要得到一个授权码, 才能继续申请Token.
- 通过授权码获取Token的过程, 是后端和认证服务器之间完成的, 前端无感知.

- 步骤:
    - 通过客户端跳转到授权地址(认证服务器地址), 并会携带一些参数过去: 客户端id, redirect uri 等; redirect uri 是用户同意后跳转的网页
    - 用户首先需要登录, 之后同意授权给客户端, 并携带授权码, 跳转到redirect uri地址
    - 客户端拿到授权码后, 在后端向认证服务器发起请求, 索取Token
        - 发起请求时携带的参数: 授权码, redirect uri, client id等
        - redirect uri 是获取Token后跳转的地址
    - 认证服务器收到请求后, 调用redirect uri并携带一段JSON数据(Token), 客户端获取Token成功

## 1.2 密码模式

- 直接把账号密码告诉客户端(第三方应用), 客户端通过账号密码申请Token
- 前提是对第三方应用高度信任

- 步骤:
    - 客户端携带用户的账号密码, 向认证服务器发起请求
    - 认证服务器直接响应Token

## 1.3 凭证式

- 适用于没有前端的命令行工具
- 第三方应用直接向认证服务器索取Token, 没有用户什么事

- 步骤:
    - 客户端向认证服务器发送请求
    - 认证服务器直接返回Token


# 2 使用Token

- 请求API都需要携带Token, Token放到HTTP请求头中的Authorization字段中

# 3 更新令牌

- 如果Token每次时效都要重新走遍认证流程, 太麻烦
- 授予Token时, 同时也会带有一个refresh token
- 只要在Token失效前, 使用refresh token调用认证服务器的API, 就会获取一个新的令牌

# 4 其它

- 客户端(第三方应用)备案?
    - 只是得到用户的授权, 但是认证服务器不认识客户端, 也不会发送Token
    - 客户端备案会得到: 客户端id和密钥
- 只有授权没有认证, 需要产品方自己实现. 可以通过OIDC实现认证.
- 为什么oauth只是授权机制, 但不是认证机制呢?
    1. 因为用户访问资源的时候, 资源服务器只关注用户的入场券是否有效, 但是并不知道是谁的入场券


# 5 灵感

认证就是登录, 验证用户并返回用户信息的过程. 那么授权就是oauth那套逻辑, 应用服务接入中心服务器也是一种授权?



# 6 Q&A

- **Oauth2是为了解决什么问题而诞生的?**

OAuth 2.0的诞生是为了解决用户在使用第三方应用时的安全性和隐私保护问题。传统的授权方式要求用户直接提供其用户名和密码给第三方应用，这样不仅存在密码泄露的风险，还可能让第三方应用获得过多的权限，导致用户数据的安全性受到威胁。

**安全性**：OAuth 2.0允许用户授权第三方应用访问其在服务提供者（如Google、Facebook等）上的数据，而无需直接分享用户名和密码。这种方式通过生成短期的访问令牌（token）来代替密码，从而降低了密码被泄露的风险.

**权限控制**：用户可以明确限制第三方应用访问的范围和有效期。例如，用户可以授权某个应用只访问其基本信息，而不允许访问其他敏感数据。这种灵活性使得用户对其数据的控制更加精细.

**简化用户体验**：OAuth 2.0使得用户在访问新应用时可以通过已有的账户（如社交媒体账户）进行快速登录，省去了繁琐的注册流程。这不仅提升了用户体验，也提高了应用的使用率。

OAuth 2.0的设计旨在通过引入一个**安全的授权层**，解决传统授权方式中的多个安全隐患，从而保护用户的隐私和数据安全。


- **为什么说OAuth2只有授权, 却没有认证?**

OAuth 2.0被称为一种授权机制，而非认证机制，主要是因为它的设计目标是允许用户授权第三方应用访问其资源，而不涉及对用户身份的验证。以下是OAuth 2.0与身份认证的关键区别：

授权与认证的定义:   
授权是指用户允许第三方应用访问其存储在资源服务器上的数据。OAuth 2.0通过发放访问令牌（access token）来实现这一点，令牌允许第三方应用在特定范围和时间内访问用户的资源，而无需直接共享用户的凭证（如用户名和密码);  
认证是验证用户身份的过程，通常通过用户名和密码来完成。认证确保用户是其所声称的身份。OAuth 2.0本身并不负责这一过程，虽然它通常与认证结合使用，但OAuth 2.0并不提供身份验证的功能.

OAuth 2.0的设计:   
OAuth 2.0的核心是授权，允许用户通过授权服务器向第三方应用颁发访问令牌。这个过程不需要再次确认用户身份，因此OAuth 2.0不具备认证的功能。用户的身份验证通常是在授权服务器的背景下进行，但OAuth 2.0的协议本身并不涉及身份验证的逻辑;  
为了实现身份认证，通常需要结合其他协议，如OpenID Connect（OIDC），它是在OAuth 2.0之上构建的，专门用于提供用户身份信息.

- **对令牌的验证难道不是对身份的验证码?**

在OAuth 2.0中，令牌的验证确实是一个重要的过程，但这并不等同于身份验证。

令牌验证与身份验证的区别:  
1 令牌的作用：  
在OAuth 2.0中，访问令牌（access token）是由授权服务器发放的，目的是授权第三方应用访问用户的资源。令牌包含了用户授权的范围和有效期，允许客户端在不直接使用用户凭证的情况下访问资源服务器。  
2 身份验证的定义：  
身份验证是确认用户身份的过程，通常涉及用户名和密码等凭证。OAuth 2.0本身并不提供身份验证的功能，它专注于授权。

令牌验证的过程：  
当第三方应用使用令牌请求资源时，资源服务器会验证该令牌的有效性。这一过程确认令牌是否有效、是否在有效期内、以及是否具有访问请求资源的权限。这一过程并不涉及对用户身份的确认。

为什么OAuth 2.0不包含身份验证:  
1 OAuth 2.0的设计初衷是为了解决授权问题，而不是身份验证。虽然在实践中，OAuth 2.0可以与身份验证机制结合使用（例如，使用OpenID Connect），但在其核心协议中，OAuth 2.0并不负责用户的身份确认。  
2 当使用OAuth 2.0时，用户的身份通常在授权服务器中进行验证，但这并不是OAuth 2.0协议的一部分。OAuth 2.0允许用户授权访问，而不需要再次确认用户的身份。

因此，虽然令牌的验证是确保授权有效的重要环节，但它并不等同于身份验证。OAuth 2.0的架构设计使得授权和身份验证成为两个独立的过程。


- **那么一个SSO单点登录系统, 单独实现了身份认证的功能和使用OAuth2.0来处理授权, 用户就不需要频繁登录多个系统即可访问资源, 那么对于各个资源服务来说, 只需要登录一次的用户是否就相当于第三方拿着令牌去访问它们?**

在一个实现了身份认证功能的单点登录（SSO）系统中，用户只需登录一次即可访问多个系统，这确实与OAuth 2.0的授权机制有相似之处。在这种情况下，用户在SSO系统中成功登录后，会获得一个令牌（或会话ID），这个令牌可以被视为用户在各个资源服务之间的“通行证”。

SSO与OAuth 2.0的关系  
1 **身份认证与授权**：  
SSO系统负责用户的身份认证，确保用户的身份信息是有效的。用户登录后，SSO会生成一个令牌，这个令牌表明用户已通过身份验证。  
2 **访问资源**：  
当用户尝试访问其他服务时，SSO会使用这个令牌来验证用户的身份，并授权用户访问相应的资源。这一过程类似于OAuth 2.0中的令牌使用，用户可以凭借令牌访问不同的资源，而无需再次输入凭证。  
3 **令牌的作用**：  
在SSO系统中，用户的令牌相当于OAuth 2.0中的访问令牌。用户可以在多个服务之间使用这个令牌进行访问，而不需要频繁登录。资源服务只需验证令牌的有效性，而不需要再次进行身份验证。

因此，在SSO系统中，用户登录后获得的令牌确实可以视为一种“授权令牌”，使得用户能够在多个资源服务之间无缝访问。这种机制提高了用户体验，减少了登录的频率，同时也简化了身份管理和安全控制。通过这种方式，用户在一次登录后，可以像使用OAuth 2.0中的令牌一样，方便地访问多个系统和资源。

- **那么用户拿到令牌后,是否在某种程度上就成为了一个第三方, 第三方拿着令牌去访问各个资源服务?**

在单点登录（SSO）系统中，用户在成功认证后获得的令牌确实可以在某种程度上被视为“第三方”凭证，允许用户访问多个资源服务。

用户与第三方的关系  
1 **用户作为资源拥有者**：  
在OAuth 2.0中，用户被称为资源拥有者（Resource Owner），他们拥有对某些资源的访问权。用户通过SSO系统进行身份认证后，获得一个令牌，这个令牌表明用户的身份已经被验证。  
2 **令牌的角色**:  
令牌在SSO系统中起到类似于OAuth 2.0中的访问令牌的作用。用户可以使用这个令牌作为凭证，向各个资源服务请求访问。这种情况下，用户在访问资源服务时，确实像是一个“第三方”，因为他们持有一个令牌来代表他们的身份和访问权限。
3 **资源服务的验证过程**：
当用户使用令牌访问资源服务时，资源服务会验证该令牌的有效性。这一过程确保了用户的身份和访问权限，但并不需要用户再次输入凭证。这与OAuth 2.0的工作方式相似，第三方应用使用令牌访问用户的资源。

因此，在SSO系统中，用户在获得令牌后，确实可以被视为在某种程度上成为了一个“第三方”，他们使用令牌访问各个资源服务。这种机制提高了用户体验，减少了频繁登录的需要，同时也简化了身份管理和安全控制。通过这种方式，用户能够在多个系统之间无缝地访问资源，类似于OAuth 2.0中第三方应用使用访问令牌的方式。


- **为了完善SSO, OAuth2负责授权, 身份认证有什么好的解决方案吗?**

1 OpenID Connect
2 自定义身份认证解决方案
3 多因素认证（MFA）
4 LDAP（轻量级目录访问协议）


- **OpenID Connect 是验证Token的同时还验证持有人的身份吗?**

OpenID Connect（OIDC）是一种基于OAuth 2.0的身份验证协议，旨在提供用户身份的验证，同时支持授权。OIDC引入了一个重要的概念，即ID Token，它是用于验证用户身份的令牌。

身份验证与ID Token: 在OpenID Connect中，ID Token是一个由身份提供者（Identity Provider）签发的JWT（JSON Web Token），其中包含用户的身份信息，例如用户的唯一标识符（sub）、电子邮件地址等。这使得应用程序能够在获取访问令牌（Access Token）的同时，验证用户的身份.

ID Token的作用:   
身份验证：ID Token专门用于验证用户的身份，确保用户是他们所声称的那个人。应用程序可以通过验证ID Token的签名和有效性来确认用户身份.  
信息获取：ID Token还可以携带用户的相关信息，应用程序可以利用这些信息进行个性化设置或用户管理.

因此，OpenID Connect不仅在获取令牌的过程中提供了身份验证，还通过ID Token对持有人的身份进行验证。这种机制使得用户在不同的应用程序之间可以实现单一登录（Single Sign-On），简化了用户的身份管理过程，同时提升了安全性.

- **那么OpenID Connect和Oauth的区别就是, Oauth不会验证令牌持有人的身份, 只要令牌有效就可以, 而OpenID Connect同时还需要验证持有人的身份吗?**

身份验证:  
OAuth 2.0主要用于授权,不提供用户身份验证。它只验证访问令牌的有效性,但不验证令牌持有人的身份.  
OpenID Connect在OAuth 2.0的基础上增加了身份认证层。它引入了ID Token的概念,在验证访问令牌的同时,还会验证ID Token中包含的用户身份信息,确保令牌持有人就是声称的那个用户.

返回信息:  
OAuth 2.0只返回访问令牌,用于授权第三方应用访问资源。  
OpenID Connect除了访问令牌,还返回ID Token,其中包含了经过验证的用户身份信息,如用户ID、电子邮件等.

使用场景:  
OAuth 2.0适用于授权场景,如允许第三方应用访问用户的社交网络、相册等。  
OpenID Connect适用于需要身份验证的场景,如单点登录。它可以让用户使用社交账号登录不同的应用,无需在每个应用上注册账号.

总之,OpenID Connect在OAuth 2.0的基础上增加了身份认证功能,在验证访问令牌的同时还会验证用户身份,提供了更完整的身份认证解决方案。

- **那么ID token是用来验证访问令牌是否是ID token中的用户提供的令牌吗?**

ID Token的主要作用是验证用户的身份，而不是直接验证访问令牌是否由ID Token中的用户提供。具体来说，ID Token是由身份提供者（Identity Provider）签发的JWT（JSON Web Token），其中包含了用户的身份信息和其他声明（claims）。

ID Token的验证:  
身份验证：ID Token包含用户的身份信息，如用户的唯一标识符（sub）、电子邮件等。应用程序在接收到ID Token后，可以通过验证其签名和声明来确认用户身份.  
与访问令牌的关系：虽然ID Token和访问令牌（Access Token）在某些情况下可能同时返回，但它们的用途不同。访问令牌用于授权访问受保护的资源，而ID Token用于身份验证。ID Token不用于授权目的.  
验证过程：在验证ID Token时，客户端需要检查：  
签名：确保ID Token未被篡改.  
受众（aud）：ID Token的受众声明应与应用的客户端ID匹配。  
时间戳：检查ID Token的有效期（如exp、nbf等）。  
Nonce：如果使用了nonce参数，需确保ID Token中的nonce与初始请求中的一致

因此，ID Token的主要功能是验证用户身份，而不是直接验证访问令牌的持有者。ID Token提供了用户身份的确认，而访问令牌则用于授权访问资源。






# 7 参考

- https://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html
- https://www.ruanyifeng.com/blog/2019/04/oauth_design.html
- https://www.ruanyifeng.com/blog/2019/04/oauth-grant-types.html
- https://www.ruanyifeng.com/blog/2019/04/github-oauth.html

