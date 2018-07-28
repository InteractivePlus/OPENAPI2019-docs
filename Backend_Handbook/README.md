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
|VerificationCodes|储存验证码的表|
|Log|日志表|
|UserAuth|用户授权应用列表存放表|
|Apps|APPID存放表, 用来储存APPID以及密码, 认证信息, 权限信息等内容|

2. 表内字段定义  

Users表  

|字段名|类型|解释|算法|注释|
|-|-|-|-|-|
|userName|VARCHAR(30)|用户名|Original|-|
|userDisplayName|VARCHAR(30)|用户展示名|Original|-|
|passWord|CHAR(32)|密码|md5(SHA256(Original, Salt))|-|
|email|VARCHAR(50)|邮箱|Original|-|
|settings|TEXT|用户设置|Original JSON|-|
|thirdAuth|TEXT|第三方登录|Original JSON|-|
|emailVerified|TINYINT(1)|邮箱是否验证过|Original|值为1(true)或0(false)|
|emailVerifyCode|CHAR(32)|邮箱验证码|md5(SHA256(userName + time(), Salt))|用户验证完毕后删除|
|userPermission|TEXT|用户权限|Original JSON|-|
|userGroup|VARCHAR(30)|用户组|Original|-|


UserGroups表  

|字段名|类型|解释|算法|注释|
|-|-|-|-|-|
|groupName|VARCHAR(30)|组名|Original|-|
|groupDisplayName|VARCHAR(30)|组展示名|Original|-|
|groupPermission|TEXT|组权限|Original JSON|-|


Tokens表  

|字段名|类型|解释|算法|注释|
|-|-|-|-|-|
|token|CHAR(32)|用户分配到的token|md5(SHA256(time(), Salt))|-|
|startTime|INT|token分配时间|time()|-|
|relatedUser|VARCHAR(30)|用户名|Original|-|
|tokenIP|VARCHAR(40)|用户登录时的IP|Original|Ipv4/Ipv6|


VerificationCodes 表  

|字段名|类型|解释|算法|注释|
|-|-|-|-|-|
|actionType|INT|此验证码用来做什么?|Original|-|
|veriCode|CHAR(32)|验证码|md5(userName+time()+Salt)|-|
|issueTime|INT|此验证码被发出的日期|time()|-|
|userName|VARCHAR(30)|用户名|Original|-|

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
|userName|VARCHAR(30)|用户名|Original|-|
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


---


1. Data Table Definition  

|Table Name|Table Introduction|
|-|-|
|Users|Table where user informations get stored, used to store username, password, 3rd party login information, user email, etc.|
|UserGroups|Table for storing user groups infos|
|Tokens|Table for storing temporary user tokens, including token content, token duration, etc.|
|VerificationCodes|Table for storing verification codes|
|Log|Log Table|
|UserAuth|Table for storing user auth infos for different apps|
|Apps|Table for storing APPIDs and their passwords, permissions, etc.|

2. In-table Fields definitions  

Users Table  

|Field|Data Type|Explanations|Algorithms|Notes|
|-|-|-|-|-|
|userName|VARCHAR(30)|username|Original|-|
|userDisplayName|VARCHAR(30)|User Display Name|Original|-|
|passWord|CHAR(32)|password|md5(SHA256(Original, Salt))|-|
|email|VARCHAR(50)|user email|Original|-|
|settings|TEXT|user settings|Original JSON|-|
|thirdAuth|TEXT|3rd party apps login infos|Original JSON|-|
|emailVerified|TINYINT(1)|has user's mail been varified?|Original|Value is either 1(true) or 0(false)|
|emailVerifyCode|CHAR(32)|Verification Code for verifying email|md5(SHA256(userName + time(), Salt))|Deleted after verified the email|
|userPermission|TEXT|User Permissions|Original JSON|-|
|userGroup|VARCHAR(30)|User Group|Original|-|


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

|代码(Code)|定义|Definition|
|-|-|-|
|1|更改密码|Change Password|
|2|更改邮箱|Change Mail|
|3|删除账号|Delete Account|
|4|更改用户名|Change userName|

## APP权限JSON定义 \| APP Permission JSON Definition

```json
{
    "accessInfo":"true/false",
    "sendEmailToUsers": "true/false"
}
```

## API定义 \| API Declaration
1. 登录API
网址: /Apis/V040/Login.php
方法: Post
参数:

|参数名|参数类型|简介|注解|
|-|-|-|-|
|userName|string|用户名|-|
|passWord|string|密码|-|
|language|string|语言|zh-CN/en/zh|

返回类型:JSON
返回值:

|键值|键值类型|简介|注解|
|-|-|-|-|
|succeed|bool|是否成功登录|-|
|errorInfo\\errCode|int|错误代码|-|
|errorInfo\\errDescription|string|错误详情|-|
|token|string|分配的token|-|

返回值例子:

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

2. 注册API
网址: /Apis/V040/Register.php
方法: Post
参数:

|参数名|参数类型|简介|注解|
|-|-|-|-|
|userName|string|用户名|-|
|passWord|string|密码|-|
|email|string|邮箱|-|
|settings|string|设置|JSON格式,可以只包含部分键值或设为空|
|language|string|语言|"zh-CN"/"en"/"zh"|

返回值:

|键值|键值类型|简介|注解|
|-|-|-|-|
|succeed|bool|是否成功注册|-|
|errorInfo\errCode|int|错误代码|-|
|errorInfo\errDescription|string|错误详情|-|

返回值例子:

```json
{
    "succeed": true,
    "errorInfo":{
        "errCode": 0,
        "errDescription": "No error"
    }
}
```

3. 验证Token API
网址: /Apis/V040/TokenVerification.php
方法: POST
参数:

|参数名|参数类型|简介|注解|
|-|-|-|-|
|userName|string|用户名|-|
|token|string|Token|-|
|language|string|语言|"zh-CN"/"en"/"zh"|

返回值:

|键值|键值类型|简介|注解|
|-|-|-|-|
|succeed|bool|token是否合法|-|
|errorInfo\errCode|int|错误代码|-|
|errorInfo\errDescription|string|错误详情|-|

返回值例子:

```json
{
    "succeed": true,
    "errorInfo": {
        "errCode": 0,
        "errDescription": "No error"
    }
}
```

## API错误代码定义 \| API Error Code Definition

|错误代码(ErrCode)|错误详情|Error Description|
|-|-|-|
|0|无错误|No error|
|1|凭据错误|Credential is not valid|
|2|用户不存在|Non-existence user|
|3|用户已存在|Existence user|
|4|格式不正确|Format Error|
|5|权限错误|Permission Error|
|500|内部错误|Internal Error|

## PHP命名规则 \| PHP Naming Rules
1. 对于OpenAPI 4.0类, 所有函数需要被namespace OPENAPI40包裹
2. 函数命名小驼峰, 类命名大驼峰

---

1. For the OpenAPI 4.0 Class, all functions needs to be bounded by OPENAPI40 namespace
2. Use small camelcase for function naming, big camelcase for class naming.
