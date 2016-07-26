单点登录(SSO, single-sign-on)

CAS(Central Authentication Service)是SSO的一种实现。

需要单独部署CAS Server，对多个应用的身份认证均重定向到CAS Server进行验证。
需要定义新的用户认证、用户信息获取接口，用户接口是否需要独立为一个单独的服务？

http://steven-wiki.readthedocs.org/en/latest/security/sso/
ST在一段时间内失效.
CAS规定Service Ticket只能存活一定的时间，然后CAS Server会让它失效.