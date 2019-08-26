---
date: 2015-09-29T23:08:25

title: oauth2.0原理

---

oauth2.0的大致原理

三个角色： 用户（u）  服务商（s）   web（c）

1. 跳转至s的登录授权页面（Request User Url）  提示用户登录并显示授权信息 生成oauth code给c（应该是通过callback url返回）

2.C 向S 请求access_token（Request access url ）

3.C通过Access Token向S请求OpenID（Request info url）

4.C用code、token 向S请求用户信息

如图：

<a href="http://www.sfhy20.com/wp-content/uploads/2015/09/oauth2.0_developer_pic.jpg"><img class="alignnone  wp-image-269" src="http://www.sfhy20.com/wp-content/uploads/2015/09/oauth2.0_developer_pic-300x221.jpg" alt="oauth2.0_developer_pic" width="657" height="484" /></a>

From： http://kb.cnblogs.com/page/189153/

&nbsp;

&nbsp;
