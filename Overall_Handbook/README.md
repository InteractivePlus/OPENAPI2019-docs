# 总册 \| Overall Handbook
此手册将详细讲解制作此项目的契机, 此项目的定义.
This handbook will go into detail of what motivates us to finish this project and what is the definition of this project.

## 定义 \| Definition
形随意动PDK全称形随意动公用开发套件, 专门用来处理对形随意动用户的登录请求和第三方应用授权请求.
形随意动PDK前端登录命名为BlueAirLive(新开发版本为2.0), 后端命名为OPENAPI(新开发版本为4.0)
InterActivePDK's full name is InterActive+ Public Development Kit, is designed specifically for dealing with the login of our user systems and auth request from 3rd party apps.
InterActivePDK's frontend login page is called BlueAirLive(New version is 2.0), backend is called OPENAPI(New version is 4.0)

## 项目目的 \| Motivations

1. 由于形随意动提供的旗下服务包括了多个有用户登录功能的网站, 统一模块让所有旗下服务的用户系统达成一致对提升用户体验非常重要.
2. 为了避免重复代码以及减少工作量, 形随意动旗下新产品在开发时一旦有需要储存用户信息 / 提供支付服务的, 使用此项目

---

1. Our services include a lot of websites that have user login systems. Making them unified under one module is extremely important for us to elevate the user experience.
2. In order to avoid repetitive code and reduce workload, whenever we make a new service that needs login / bill service, we will use this project.

## 数据交换流程 \| Data Exchange Process

1. 用户前往第三方登录页面, 选择BlueAirLive, 获取第三方登录的APPID
2. 用户提交登录请求(用户名, 密码, 被登陆的第三方APPID)
3. OPENAPI收到请求,验证用户登录凭据真实性,删除用户之前的token,配发新token到用户.
4. 用户发送token到第三方服务器, 第三方服务器询问OPENAPI关于Token真实性
5. 用户与第三方服务器交互.

---

1. User goes to 3rd party login page, choose BlueAirLive to login, and recieve the APPID from the 3rd party.
2. User submit login request(Username, Password, 3rd Party APPID)
3. OPENAPI recieves the request, checks the credentials from the user and delete previous tokens given to the user, and give the user a new token.
4. User send the token given by the OPENAPI to 3rd party server, 3rd party checks the credential from the user with the OPENAPI Server.
5. User interact with 3rd party server.