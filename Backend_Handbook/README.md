# 后端开发手册 \| Back-End Handbook
此手册详细规范化 OPENAPI 4.0 开发过程中的细枝末节.  
This handbook standardizes the development process of OPENAPI 4.0 in detail.

## 简要概括 \| Brief Introduction
OPENAPI 4.0 使用全新版本的 BoostPHP2.0 作为核心框架, 主要程序结构为OPENAPI 4.0类模块 + 外部API包装  
OPENAPI 4.0 utilizes brand new version of BoostPHP2.0 as its core framework. Its basic structure is a OPENAPI 4.0 class with a outside wrapper for giving API functionalities.  

## 数据库结构定义 \| DB Structure Definition
*数据表命名采用大驼峰命名法, 数据字段名采用小驼峰命名法*  
*Naming tables using big camelcase, naming fields using small camelcase*  

1. 数据库表定义  

|表名|表介绍|
|-|-|
|Users|用户信息储存的表, 用来储存用户名密码, 第三方登录信息, 邮箱等内容|
|UserGroups|用户组储存表|
|Tokens|临时储存用户Token的表, 包括Token内容, Token持续时间等信息|
|APPTokens|临时储存APPID访问用户信息的表, 包括Token内容, Token持续时间等信息|
|VerificationCodes|储存验证码的表|
|Log|日志表|
|UserAuth|用户授权应用列表存放表|
|Apps|APPID存放表, 用来储存APPID以及密码, 认证信息, 权限信息等内容|

2. 表内字段定义  

Users表  

|字段名|类型|解释|算法|注释|
|-|-|-|-|-|
|userName|VARCHAR(30)|用户名(Username)|Original|-|
|userDisplayName|VARCHAR(30)|用户展示名|Original|-|
|password|CHAR(32)|密码|md5(SHA256(Original, Salt))|-|
|email|VARCHAR(50)|邮箱|Original|-|
|settings|TEXT|用户设置|Original JSON|-|
|thirdAuth|TEXT|第三方登录|Original JSON|-|
|emailVerified|TINYINT(1)|邮箱是否验证过|Original|值为1(true)或0(false)|
|emailVerifyCode|CHAR(32)|邮箱验证码|md5(SHA256(userName + time(), Salt))|用户验证完毕后删除|
|userPermission|TEXT|用户权限|Original JSON|-|
|userGroup|VARCHAR(30)|用户组|Original|-|
|regTime|INT|用户注册时间|time()|-|


UserGroups表  

|字段名|类型|解释|算法|注释|
|-|-|-|-|-|
|groupName|VARCHAR(30)|组名|Original|-|
|groupDisplayName|VARCHAR(30)|组展示名|Original|-|
|groupPermission|TEXT|组权限|Original JSON|-|


Tokens 表  

|字段名|类型|解释|算法|注释|
|-|-|-|-|-|
|token|CHAR(32)|用户分配到的token|md5(SHA256(time(), Salt))|-|
|startTime|INT|token分配时间|time()|-|
|relatedUser|VARCHAR(30)|用户名(Username)|Original|-|
|tokenIP|VARCHAR(40)|用户登录时的IP|Original|Ipv4/Ipv6|


APPTokens 表

|字段名|类型|解释|算法|注释|
|-|-|-|-|-|
|token|CHAR(32)|APPID分配到的token|md5(SHA256(time(), Salt))|-|
|startTime|INT|token分配时间|time()|-|
|relatedUser|VARCHAR(30)|用户名(Username)|Original|-|
|relatedAPP|VARCHAR(30)|APPID|Original|-|
|tokenIP|VARCHAR(40)|用户登录时的IP|Original|Ipv4/Ipv6|


VerificationCodes 表  

|字段名|类型|解释|算法|注释|
|-|-|-|-|-|
|actionType|INT|此验证码用来做什么?|Original|-|
|veriCode|CHAR(32)|验证码|md5(userName+time()+Salt)|-|
|issueTime|INT|此验证码被发出的日期|time()|-|
|userName|VARCHAR(30)|用户名(Username)|Original|-|

*对于VerificationCodes表, 每一行数据都会在他们过期后或被使用后被删除.*  


Log表  

|字段名|类型|解释|算法|注释|
|-|-|-|-|-|
|logTime|INT|日志记录时间|time()|-|
|logContent|TEXT|日志内容|Original|-|
|logLevel|INT|日志等级|Original|1(Normal)-5(Severe)|


UserAuth表  

|字段名|类型|解释|算法|注释|
|-|-|-|-|-|
|userName|VARCHAR(30)|用户名(Username)|Original|-|
|authContent|TEXT|用户授权详情|Original JSON|-|


Apps表  

|字段名|类型|解释|算法|注释|
|-|-|-|-|-|
|appID|VARCHAR(30)|应用使用的APPID|Original|-|
|appDisplayName|VARCHAR(30)|应用展示名|Original|-|
|appPass|CHAR(32)|APPID对应的密码|md5(SHA256(Original, Salt))|-|
|appPermission|TEXT|应用可以调用的权限|Original JSON|-|
|adminUser|VARCHAR(30)|注册APPID的用户|Original|-|
|manageUsers|TEXT|可以管理此APPID的用户|Original JSON|\["userName1","userName2"\]|
|pendingUsers|TEXT|等待接受管理此APPID权限的用户|Original JSON|\["userName1","userName2"\]|
|appjumpBackPage|TINYTEXT|登录BlueAirLive账户后跳转回的位置|https://www.example.com/OPENAPILoginCallBack.php|


---


1. Data Table Definition  

|Table Name|Table Introduction|
|-|-|
|Users|Table where user informations get stored, used to store username, password, 3rd party login information, user email, etc.|
|UserGroups|Table for storing user groups infos|
|Tokens|Table for storing temporary user tokens, including token content, token duration, etc.|
|APPTokens|Table for storing temporary appid tokens, including token content, token duration, etc.|
|VerificationCodes|Table for storing verification codes|
|Log|Log Table|
|UserAuth|Table for storing user auth infos for different apps|
|Apps|Table for storing APPIDs and their passwords, permissions, etc.|

1. In-table Fields definitions  

Users Table  

|Field|Data Type|Explanations|Algorithms|Notes|
|-|-|-|-|-|
|userName|VARCHAR(30)|username|Original|-|
|userDisplayName|VARCHAR(30)|User Display Name|Original|-|
|password|CHAR(32)|password|md5(SHA256(Original, Salt))|-|
|email|VARCHAR(50)|user email|Original|-|
|settings|TEXT|user settings|Original JSON|-|
|thirdAuth|TEXT|3rd party apps login infos|Original JSON|-|
|emailVerified|TINYINT(1)|has user's mail been varified?|Original|Value is either 1(true) or 0(false)|
|emailVerifyCode|CHAR(32)|Verification Code for verifying email|md5(SHA256(userName + time(), Salt))|Deleted after verified the email|
|userPermission|TEXT|User Permissions|Original JSON|-|
|userGroup|VARCHAR(30)|User Group|Original|-|
|regTime|INT|User Register Time|time()|-|


UserGroups Table  

|Field|Data Type|Explanations|Algorithms|Notes|
|-|-|-|-|-|
|groupName|VARCHAR(30)|GroupID|Original|-|
|groupDisplayName|VARCHAR(30)|Group Display Name|Original|-|
|groupPermission|TEXT|Group Permissions|Original JSON|-|


Tokens Table  

|Field|Data Type|Explanations|Algorithms|Notes|
|-|-|-|-|-|
|token|CHAR(32)|The token user gets|md5(SHA256(time(), Salt))|-|
|startTime|INT|The time token was given out|time()|-|
|relatedUser|VARCHAR(30)|The user that has this token|Original|-|
|tokenIP|VARCHAR(40)|The IP of the user when Logged in|Original|Ipv4/Ipv6|


APPTokens Table

|Field|Data Type|Explanations|Algorithms|Notes|
|-|-|-|-|-|
|token|CHAR(32)|The token given to APPID|md5(SHA256(time(), Salt))|-|
|startTime|INT|token distribution time|time()|-|
|relatedUser|VARCHAR(30)|The user that is related to this token|Original|-|
|relatedAPP|VARCHAR(30)|The APPID that owns this token|Original|-|
|tokenIP|VARCHAR(40)|The IP of the user when logged in|Original|Ipv4/Ipv6|


VerificationCodes Table  

|Field|Data Type|Explanations|Algorithms|Notes|
|-|-|-|-|-|
|actionType|INT|What is this verification code used for?|Original|-|
|veriCode|CHAR(32)|The code itself|md5(userName+time()+Salt)|-|
|issueTime|INT|Time the code has been issued|time()|-|
|userName|VARCHAR(30)|User owning this code|Original|-|

*for the verificationcodes table, every row get deleted immediately after they expire or they are used for verification*  


Log Table  

|Field|Data Type|Explanations|Algorithms|Notes|
|-|-|-|-|-|
|logTime|INT|Time when logged|time()|-|
|logContent|TEXT|Log content|Original|-|
|logLevel|INT|Log Level|Original|1(Normal)-5(Severe)|


UserAuth Table  

|Field|Data Type|Explanations|Algorithms|Notes|
|-|-|-|-|-|
|userName|VARCHAR(30)|User Name|Original|-|
|authContent|TEXT|User Auth Content|Original JSON|-|


Apps Table  

|Field|Data Type|Explanations|Algorithms|Notes|
|-|-|-|-|-|
|appID|VARCHAR(30)|APPID used by APP|Original|-|
|appDisplayName|VARCHAR(30)|APP Display Name|Original|-|
|appPass|CHAR(32)|APPID's password|md5(SHA256(Original, Salt))|-|
|appPermission|TEXT|Permissions App Can Use|Original JSON|-|
|adminUser|VARCHAR(30)|Users that register for this APPID|Original|-|
|manageUsers|TEXT|Users that can manage this APPID|Original JSON|\["userName1","userName2"\]|
|pendingUsers|TEXT|Users that have been invited to manage this APPID but is still pending to accept|Original JSON|\["userName1","userName2"\]|
|appjumpBackPage|TINYTEXT|The URL after user successfully logs into BlueAirLive|https://www.example.com/OPENAPILoginCallBack.php|


## 用户设置JSON定义 \| User Setting Definition

```json
{
    "subscribeToMail":"true/false",
    "perferredLanguage":"zh-CN/en",
}
```

## 第三方登录JSON定义 \| Thrid Party Login JSON Definition

```json
{
    "QQ":{
        "qqNumber":"XXXXXX"
    },
    "WeChat":{
        "weChatID":"XXXXXX"
    }
}
```

## 用户授权JSON定义 \| User Auth JSON Definition

```json
{
    "APPID":{
        "accessInfo": "true/false",
        "sendEmailToMe": "true/false"
    }
}
```

## 用户/组权限JSON定义 \| User/Group Permission JSON Definition

```json
{
    "EditUsers": "true/false",
    "ViewLogs": "true/false",
    "ManageUserGroups": "true/false",
    "ChangeUserPermissions": "true/false",
}
```

## 验证码类型定义 \| Verification Code Definition

|代码(Code)|定义|Definition|Need Token|
|-|-|-|-|
|1|更改密码|Change Password|FALSE|
|2|更改邮箱|Change Mail|TRUE|
|3|删除账号|Delete Account|TRUE|
|4|删除APPID|Delete APPID|TRUE|

## APP权限JSON定义 \| APP Permission JSON Definition

```json
{
    "accessInfo":"true/false",
    "sendEmailToUsers": "true/false"
}
```

## API定义 \| API Declaration
**登录 \| Login**  
URL: /API/V040/userAPI/login.php
方法(Method): POST
参数(Parameters):

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|password|string|密码(Password)|-|
|language|string|语言(Language)|zh-CN/en/zh|

返回类型 \| Return Type:JSON
返回值 \| Return Values:

|键值(Key)|键值类型(Key Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功登录(is the operation successful?)|-|
|errorInfo\\errCode|int|错误代码(Error Code)|-|
|errorInfo\\errDescription|string|错误详情(Error Description)|-|
|token|string|分配的token(token distributed by OPENAPI)|-|

返回值例子 \| Return Value Example:

```json
{
    "succeed": true,
    "errorInfo":{
        "errCode": 0,
        "errDescription": "No error"
    },
    "token": "XXXXXXXXXXXXX"
}
```

**注册 \| Register**  
URL: /API/V040/userAPI/register.php
方法(Method): POST
参数(Parameters):

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|password|string|密码(Password)|-|
|email|string|邮箱(Email)|-|
|settings|string|设置(Settings)|JSON格式,可以只包含部分键值或设为空(In the format of JSON, can only include partial keys or set to empty)|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功注册(Is registration successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|-|
|errorInfo\errDescription|string|错误详情(Error Description)|-|

返回值例子 \| Return Value Example:

```json
{
    "succeed": true,
    "errorInfo":{
        "errCode": 0,
        "errDescription": "No error"
    }
}
```

**重发邮件认证邮件 \| Resend Email Verification**  
URL: /API/V040/userAPI/resendRegVerification.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|password|string|密码(Password)|-|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:  

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功(Is operation successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|-|
|errorInfo\errDescription|string|错误详情(Error Description)|-|

返回值例子 \| Return Value Example:  

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    }
}
```

**验证Token \| Check Token**  
URL: /API/V040/userAPI/tokenVerification.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|token|string|Token|-|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:  

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|Token是否合法(Is token legal?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|-|
|errorInfo\errDescription|string|错误详情(Error Description)|-|

返回值例子 \| Return Value Example:  

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    }
}
```

**更改设置 \| Change Settings**  
URL: /API/V040/userAPI/changeSetting.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|token|string|用户登陆后获取的Token|-|
|newSettings|string|新设置JSON, 可以只包含部分键值|-|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功(Is Operation Successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|-|
|errorInfo\errDescription|string|错误详情(Error Description)|-|

返回值例子 \| Return Value Examples:

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    }
}
```

**更改昵称 \| Change Display Name**  
URL: /API/V040/userAPI/changeDisplayName.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|token|string|用户登陆后获取的Token(Token gotten by user after logging in)|-|
|changingUserName|string|被更改的用户名(Changed Username)|-|
|newDisplayName|string|新昵称(New Display Name)|-|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功(Is Operation Successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|-|
|errorInfo\errDescription|string|错误详情(Error Description)|-|

返回值例子 \| Return Value Examples:

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    }
}
```
**授权应用 \| Authorize APPS**  
URL: /API/V040/userAPI/authAPPs.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|token|string|用户登陆后获取的Token(Token after logging in)|-|
|authingUsername|string|给予授权的用户名(Username that is given out auth)|-|
|authingAPPID|string|被授权的APPID(APP that is getting authed)|-|
|permissions|string|权限(Permission JSONS)|为空则删除授权(If empty then delete auth for the APP)|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:  

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功(Is Operation Successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|-|
|errorInfo\errDescription|string|错误详情(Error Description)|-|

返回值例子 \| Return Value Examples:  

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    }
}
```

**更改邮箱 \| Change Email**  
URL: /API/V040/userAPI/changeMail.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|token|string|用户登陆后获取的Token(Token got by the user after logging in)|-|
|changingUserName|string|被更改邮箱的用户(User that get his mail changed)|-|
|newMail|string|新邮箱(new Email Addr)|-|
|veriCode|string|更改邮箱所需的, 发到老邮箱的验证码(Verification sent to the old email)|如果不是本人更改本人账号, 则是管理员, 无需veriCode(If it is not the user itself changing email, then veriCode is not needed)|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:  

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功(Is Operation Successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|-|
|errorInfo\errDescription|string|错误详情(Error Description)|-|

返回值例子 \| Return Value Examples:  

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    }
}
```

**管理员重置密码 \| Admin Resetting Password of one user**  
URL: /API/V040/userAPI/adminResetPassword.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|token|string|用户登陆后获取的Token(Token got by user after logging in)|-|
|changingUserName|string|被更改密码的用户(User getting his/her password changed)|-|
|newPassword|string|新密码(New Password)|-|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功(Is Operation Successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|-|
|errorInfo\errDescription|string|错误详情(Error Description)|-|

返回值例子 \| Return Value Examples:

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    }
}
```


**更改密码 \| Password Change**  
URL: /API/V040/userAPI/changePassword.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|veriCode|string|更改密码所需的, 发到邮箱的验证码(Verification code send to user's email)|-|
|newPassword|string|新密码(New Password)|-|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:  

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功(Is Operation Successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|-|
|errorInfo\errDescription|string|错误详情(Error Description)|-|

返回值例子 \| Return Value Examples:  

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    }
}
```

**发送验证码 \| Send Verification Code**  
URL: /API/V040/userAPI/sendVerificationCode.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|token|string|用户Token(Token got by user after logging in)|有时需要有时不需要,见验证码类型定义(it is needed depended on different vericode types)|
|action|int|验证码用途(Type of Vericode)|见验证码类型定义(See VeriCode Type Definition)|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:  

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功(Is Operation Successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|-|
|errorInfo\errDescription|string|错误详情(Error Description)|-|

返回值例子 \| Return Value Examples:  

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    }
}
```

**列出用户信息 \| View User Info**  
URL: /API/V040/userAPI/viewUserInfo.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|token|string|用户Token(Token got by the user after logging in)|-|
|viewingUserName|string|被查看用户名(Username get viewed)|-|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:  

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功(Is Operation Successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|见验证码类型定义|
|errorInfo\errDescription|string|错误详情(Error Description)|-|
|userInfo|object|用户详细信息(User detailed info)|-|

返回值例子 \| Return Value Examples:  

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    },
    "userInfo":{
        "userDisplayName": "XXX",
        "email": "XXX",
        "settings": "XXX",
        "thridAuth": "XXX",
        "userGroup": "XXX"
    }
}
```

*第三方应用看不见您的信息*  

**删除用户 \| Delete User**  
URL: /API/V040/userAPI/deleteUser.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|token|string|用户Token(Token got by the user after logging in)|-|
|deletingUserName|string|被删除用户名(Username)|-|
|verificationCode|string|删除用户验证码(Verification Code send to user)|如果删除的是别人, 说明是管理员, 不需要验证码(If deletingUsername is different from userName, than no veriCode is needed.|-|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:  

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功(Is Operation Successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|见验证码类型定义|
|errorInfo\errDescription|string|错误详情(Error Description)|-|

返回值例子 \| Return Value Examples:  

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    }
}
```

**修改用户权限 \| Change User Permission**  
URL: /API/V040/userAPI/changeUserPermission.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|token|string|用户Token(Token got by the user after logging in)|-|
|changingUserName|string|被修改用户名(Username got edited)|-|
|newPermission|string|新权限JSON(New Permission JSON)|详见权限定义(See Permission Definition)|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:  

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功(Is Operation Successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|-|
|errorInfo\errDescription|string|错误详情(Error Description)|-|

返回值例子 \| Return Value Examples:  

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    }
}
```

**修改用户组 \| Change User Group**  
URL: /API/V040/userAPI/changeUserGroup.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|token|string|用户Token(Token got by the user after logging in)|-|
|changingUserName|string|被修改用户名(Username got changed)|-|
|newGroup|string|新组名(New Group Name)|-|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:  

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功(Is Operation Successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|见验证码类型定义|
|errorInfo\errDescription|string|错误详情(Error Description)|-|

返回值例子 \| Return Value Examples:  

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    }
}
```

**列出所有用户 \| List All Users**  
URL: /API/V040/userAPI/listUsers.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|token|string|用户Token(Token got by the user after logging in)|-|
|findingUser|string|查找用户名(Username that is searching)|为空时列出全部用户(if empty, list all users)|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功(Is Operation Successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|-|
|errorInfo\errDescription|string|错误详情(Error Description)|-|
|users|array|所有符合条件的用户(all users that matches the findingUser)|此可以为null,意味着没有users符合搜索条件(null means no users match)|

返回值例子 \| Return Value Examples:

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    },
    "users":[
        "admin",
        "toiletcommander"
    ]
}
```

**创建组 \| Create Group**  
URL: /API/V040/groupAPI/createGroup.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|token|string|用户Token(Token got by the user after logging in)|-|
|groupName|string|新组ID(new Group ID)|-|
|groupDisplayName|string|新组展示名(new Group Display Name)|-|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:  

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功(Is Operation Successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|-|
|errorInfo\errDescription|string|错误详情(Error Description)|-|

返回值例子 \| Return Value Examples:  

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    }
}
```

**编辑组权限 \| Edit Group Permission**  
URL: /API/V040/groupAPI/changeGroupPermission.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|token|string|用户Token(Token got by the user after logging in)|-|
|groupName|string|组ID(Group ID)|-|
|newPermission|string|新组权限(New Permission for the Group)|见组权限定义|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:  

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功(Is Operation Successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|-|
|errorInfo\errDescription|string|错误详情(Error Description)|-|

返回值例子 \| Return Value Examples:  

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    }
}
```

**删除组 \| Delete Group**  
URL: /API/V040/groupAPI/deleteGroup.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|token|string|用户Token(Token got by the user after logging in)|-|
|groupName|string|组ID(Group ID)|-|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:  

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功(Is Operation Successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|-|
|errorInfo\errDescription|string|错误详情(Error Description)|-|

返回值例子 \| Return Value Examples:  

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    }
}
```

**创建APPID \| Create APPID**  
URL: /API/V040/PDKAPI/createAPPID.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|token|string|用户Token(Token got by the user after logging in)|-|
|appID|string|APPID|-|
|appPass|string|APPID密钥(APPID's Password)|-|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:  

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功(Is Operation Successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|-|
|errorInfo\errDescription|string|错误详情(Error Description)|-|

返回值例子 \| Return Value Examples:  

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    }
}
```

**修改APPID权限 \| Change APPID Permission**  
URL: /API/V040/PDKAPI/changeAPPPermission.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|token|string|用户Token(Token got by the user after logging in)|-|
|appID|string|APPID|-|
|newPermission|string|新权限JSON(New User Permission JSON)|-|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:  

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功(Is Operation Successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|-|
|errorInfo\errDescription|string|错误详情(Error Description)|-|

返回值例子 \| Return Value Examples:  

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    }
}
```

**查看APPID信息 \| View APPID Info**  
URL: /API/V040/PDKAPI/viewAPPIDInfo.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|token|string|用户Token(Token got by the user after logging in)|-|
|appID|string|APPID|-|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:  

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功(Is Operation Successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|-|
|errorInfo\errDescription|string|错误详情(Error Description)|-|
|appIDInfo|Object|APPID详细信息|-|

返回值例子 \| Return Value Examples:  

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    },
    "appIDInfo":{
        "adminUser":"XXX",
        "appDisplayName": "XXX",
        "appPermission": {
            "accessInfo":"true/false",
            "sendEmailToUsers": "true/false"
        },
        "managerUsers": [
            "XXX",
            "XXX",
        ],
        "pendingUsers": [
            "XXX",
            "XXX",
        ]
    }
}
```

**转移APPID所有权 \| Change APPID Ownership**  
URL: /API/V040/PDKAPI/changeAPPIDOwnership.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|token|string|用户Token(Token got by the user after logging in)|-|
|appID|string|APPID|-|
|newOwner|string|APPID新所有者的username(new Owner's Username of the APPID)|newOwner必须是managerUsers中的一员|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:  

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功(Is Operation Successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|-|
|errorInfo\errDescription|string|错误详情(Error Description)|-|

返回值例子 \| Return Value Examples:  

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    }
}
```

**邀请管理APPID \| Invite to Manage APPID**  
URL: /API/V040/PDKAPI/addManager.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|token|string|用户Token(Token got by the user after logging in)|-|
|appID|string|APPID|-|
|newManager|string|APPID新添加的管理员的username(New Manager's Username)|newManager不能是adminUser, manageUsers或pendingUsers的一员(newManager cannot be a member of adminUser, manageUsers or pendingUsers)|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:  

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功(Is Operation Successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|-|
|errorInfo\errDescription|string|错误详情(Error Description)|-|

返回值例子 \| Return Value Examples:  

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    }
}
```

**接受管理APPID \| Accept Managing APPID**  
URL: /API/V040/PDKAPI/acceptManager.php  
方法(Method): POST  
参数(Parameters):   

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|token|string|用户Token(Token got by the user after logging in)|-|
|appID|string|APPID|-|
|acceptingUserName|string|被添加的userName(username getting Added)|-|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:  

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功(Is Operation Successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|-|
|errorInfo\errDescription|string|错误详情(Error Description)|-|

返回值例子 \| Return Value Examples:  

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    }
}
```

**删除管理员/退出APPID/不接受管理 \| Delete Manager / Exit APPID Management / Not Accepting Management**  
URL: /API/V040/PDKAPI/leaveAPPID.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|token|string|用户Token(Token got by the user after logging in)|-|
|appID|string|APPID|-|
|deletingUser|string|被删除的UserName(username get deleted)|deletingUser必须是manageUsers或pendingUsers的一员(deletingUser must be a member of manageUsers or pendingUsers)|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:  

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功(Is Operation Successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|-|
|errorInfo\errDescription|string|错误详情(Error Description)|-|

返回值例子 \| Return Value Examples:  

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    }
}
```

**更改APPID展示名 \| Change APPID Display Name**  
URL: /API/V040/PDKAPI/changeAPPDisplayName.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|token|string|用户Token(Token got by the user after logging in)|-|
|appID|string|APPID|-|
|newDisplayName|string|新展示名称(New Display Name)|-|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:  

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功(Is Operation Successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|-|
|errorInfo\errDescription|string|错误详情(Error Description)|-|

返回值例子 \| Return Value Examples:  

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    }
}
```

**更改APPID回调地址 \| Change APPID's Callback URL**  
URL: /API/V040/PDKAPI/changeAPPLoginCallBack.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|token|string|用户Token(Token got by the user after logging in)|-|
|appID|string|APPID|-|
|newCallBack|string|新回调地址(New Callback URL)|-|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:  

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功(Is Operation Successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|-|
|errorInfo\errDescription|string|错误详情(Error Description)|-|

返回值例子 \| Return Value Examples:  

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    }
}
```

**删除APPID \| Delete APPID**  
URL: /API/V040/PDKAPI/deleteAPPID.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|token|string|用户Token(Token got by the user after logging in)|-|
|appID|string|APPID|-|
|veriCode|string|验证码(Verification Code)|-|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:  

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功(Is Operation Successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|-|
|errorInfo\errDescription|string|错误详情(Error Description)|-|

返回值例子 \| Return Value Examples:  

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    }
}
```

**列出APPID \| List APPID**  
URL: /API/V040/PDKAPI/listAPPID.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|token|string|用户Token(Token got by the user after logging in)|-|
|searchUser|string|查找用户名(Username getting searched)|可以为空, 则列出所有(If empty then list all)|
|searchAPPID|string|查找APPID(APPID getting searched)|可以为空, 则列出所有(If empty then list all)|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:  

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功(Is Operation Successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|-|
|errorInfo\errDescription|string|错误详情(Error Description)|-|
|appIDs|详细列表(List of APPIDS)|-|

返回值例子 \| Return Value Examples:  

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    },
    "appIDs":[
        "XXXX",
        "XXXX",
        "XXXX",
        "XXXX"
    ]
}
```

## 公用PDK API定义 \| Public PDK API Declaration
**第三方登录(用于BlueAirLive调用) \| 3rd Party Login(For BlueAirLive to call)**  
URL: /API/V040/PDK/login.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|password|string|密码(Password)|-|
|appID|string|APPID|-|
|appCustomData|string|第三方给的自定数据, 回调时自动附上(CustomData given to callback URL after successful login)|-|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:  

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功(Is Operation Successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|见验证码类型定义|
|errorInfo\errDescription|string|错误详情(Error Description)|-|
|callBackURL|string|成功登陆后回调URL(URL redirected after successful login)|-|

返回值例子 \| Return Value Examples:  

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    },
    "callBackURL": "https://www.example.com/PDKLoginCB.php?token=XXX"
}
```

**第三方Token验证(用于第三方调用) \| 3rd Party Token Verification(For 3rd party to call)**  
URL: /API/V040/PDK/checkToken.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|token|string|分配的Token(Token distributed to 3rd party)|-|
|appID|string|APPID|-|
|appPass|string|APPID对应的密码(APPID's Password)|-|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:  

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|Token是否合法(Is token legal?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|-|
|errorInfo\errDescription|string|错误详情(Error Description)|-|

返回值例子 \| Return Value Examples:  

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    }
}
```

**第三方用户信息调取(用于第三方调用) \| Third party user info read(For 3rd party to call)**  
URL: /API/V040/PDK/getUserInfo.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|token|string|分配的Token(Token got by 3rd party)|-|
|appID|string|APPID|-|
|appPass|string|APPID对应的密钥(APPID's Password)|-|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|

返回值 \| Return Values:  

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功(Is Operation Successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|=|
|errorInfo\errDescription|string|错误详情(Error Description)|-|
|userInfo|string|错误详情(Error Description)|-|

返回值例子 \| Return Value Examples:  

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    },
    "userInfo":{
        "userDisplayName": "XXX"
    }
}
```

**第三方邮件发送(用于第三方调用) \| 3rd Party Email Service(For 3rd party to call)**  
URL: /API/V040/PDK/sendMail.php  
方法(Method): POST  
参数(Parameters):  

|参数名(Parameter)|参数类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|userName|string|用户名(Username)|-|
|token|string|分配的Token(Token got by 3rd party)|-|
|appID|string|APPID|-|
|appPass|string|APPID对应的密钥(APPID's Password)|-|
|language|string|语言(Language)|"zh-CN"/"en"/"zh"|
|mailTitle|string|邮件主题(Email Title)|-|
|mailBody|string|邮件内容(Email Body)|-|

返回值 \| Return Values:  

|键值(Key)|键值类型(Type)|简介(Introduction)|注解(Note)|
|-|-|-|-|
|succeed|bool|是否成功(Is Operation Successful?)|-|
|errorInfo\errCode|int|错误代码(Error Code)|-|
|errorInfo\errDescription|string|错误详情(Error Description)|-|

返回值例子 \| Return Value Examples:  

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    },
}
```

## API错误代码定义 \| API Error Code Definition

|错误代码(ErrCode)|错误详情(Error Description)|Error Description|
|-|-|-|
|0|无错误|No error|
|1|凭据错误|Credential is not valid|
|2|用户不存在|Non-existence user|
|3|用户已存在|Existence user|
|4|格式不正确|Format Error|
|5|权限错误|Permission Error|
|6|操作过于频繁|Too frequent operation|
|500|内部错误|Internal Error|

## PHP命名规则 \| PHP Naming Rules
1. 对于OpenAPI 4.0类, 所有函数需要被namespace OPENAPI40包裹
2. 函数命名小驼峰, 类命名大驼峰

---

1. For the OpenAPI 4.0 Class, all functions needs to be bounded by OPENAPI40 namespace
2. Use small camelcase for function naming, big camelcase for class naming.
