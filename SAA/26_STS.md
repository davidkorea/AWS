# Security Token Service
使用AWS Security Token Service (STS)服务，你可以创建和控制对你的AWS资源访问的安全凭证。

这种临时的凭证的工作方式和长期存在于AWS账户中的IAM用户的工作方式类似，但会存在以下的区别：

- STS服务产生的凭证是临时的，它的有效期可以是几分钟到几小时，一旦过了这个时效时间，你的凭证就会失去作用，无法再访问相应的资源
- IAM会长期保存在AWS账户中，而临时凭证只有在需要的时候才动态生成

STS的临时凭证可以由以下几种方式产生：
- 企业联合身份验证（Federation）
  - 使用了基于**Security Assertion Markup Language (SAML)** 的标准
  - 可以使用微软Active Directory的用户来获取临时权限，不需要创建IAM用户
  - 支持**单点登录（Single Sign On, SSO）**
- Web联合身份验证（Federation with Mobile Apps）
  - 使用已知的第三方身份供应商（Amazon, Facebook, Google或其他OpenID提供商）来登录
- 跨账户访问
  - 让一个账号内的用户访问同一个组织（Organization）内其他账号的AWS资源
  
如下图所示，是用户使用STS服务访问AWS资源的完整过程。

- 用户在应用程序内输入账号密码，应用程序将其发送给Identity Provider (Identity Broker)
- Identity Provider (IdP)将用户名和密码发送到企业的LDAP目录进行验证
- 验证成功后IdP发送一个SAML认证响应给应用程序
- 应用程序使用AssumeRoleWithSAMLRequest API发送SMAL请求给STS
- STS返回临时安全凭证给应用程序，其中包括了AccessKeyId, SecretAccessKey, SessionToken和时限（1到36小时）
- 应用程序使用临时凭证访问S3存储桶

![](https://cdnstatic.iteablue.com/iteablue-production-data/wp-content/uploads/2018/08/saml-based-federation.diagram.png)

知识点
- LDAP和AWS STS之间的通信需要通过Identity Broker (IdP)，而IdP一般需要自己开发
- IdP总是先跟LDAP认证，审核用户名密码，然后再和STS通信
- 应用程序最后会使用临时访问权限访问AWS的资源

另外，STS和微软AD域集成的时候，可以做到用户使用自己企业LDAP目录的AD账号密码来登录AWS管理控制台。其中的Identity Broker位置变成了ADFS (Active Directory Federation Services)。
