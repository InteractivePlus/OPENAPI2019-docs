# 前端开发手册 \| Front-End Handbook
此手册详细规范化 BlueAirLive 2.0 开发过程中的各种细枝末节.  
This handbook standardizes the development process of BlueAirLive2.0 in detail.  

## 简要概括 \| Brief Introduction
BlueAirLive2.0 将使用Bootstrap + jQuery + FontAwesome + CustomJS编写, 技术栈接近形随意动2019官网.  
一句口诀: 能用动态加载就用动态加载, 能概括成函数的就概括成函数, 公共的JS和部分数据放在一个特定的JS文件里面.  

---

BlueAirLive2.0 will utilize Bootstrap + jQuery + FontAwesome + CustomJS to write, its tech stack is similar to InterActive+ 2019 Website.  
One Statement: Use hot loading if you can, generalize repetitive codes into functions if you can, public JS and data need to be put into a specific JS file.  

## 与服务器交互逻辑 \| Logic Interacting with Servers
服务端将提供明确清晰的API定义以供前端调用, 前端将使用jQuery.ajax()进行交互.  
The server side will provide a clear API doc for front-end to use, and the front-end will utilize jQuery.ajax() to interact with the server.  

## 前端设计草图 \| Front-end Design Draft
暂无  
Unavailable Now  

## 页面内容命名规则 \| Content Naming Rules
### 网页文件命名规则
1. 任何文件后缀不得大写
2. 网页需要用语言/区域标识符作为文件夹分割(zh-CN,en-US,en,zh...), 文件夹大小写规则遵循 语言(小写)-区域(大写)的规则进行命名
3. 网页用小驼峰大小写命名(index.html,windySoHandsome.html)
4. 共用JS不允许多次存在(zh/abc.js,en/abc.js), 需要放在一个单独的文件夹中(js/abc.js).
### 共用JS函数命名规则
1. 函数使用小驼峰大小写.
2. 函数参数必须支持多语言选择, 且选择参数为最后一个参数. 此参数在调用时所填入的内容与网页的语言/区域标识符一致(zh-CN,en-US,...)

---

#### Web File Naming Rules
1. File extensions MUST NOT be put in upper case.
2. Website pages should be separated by Country / Language Identification Code (zh-CN, en-US, en, zh) as directories, the naming of those directories should follow Language(lower case)-Area(upper case).
3. Naming web pages using small camelcase.
4. Shared JS Files should not show up repeatedly(zh/abc.js,en/abc.js), they need to be put in a standalone directory other than the language directories.
### Shared JS Function Naming Rules
1. Function needs to be named using small camelcase.
2. Functions in shared JS files MUST support multi-language, and the language selection parameter should be the last parameter for the function. This parameter will have the same value as the language / area identification code of the web page when the function is called.

---

## 前端开发规范 \| Front-end standardization
### Cookie定义 \| Cookie Definition

#### Cookie术语定义 \| Cookie Manual Specific Word Definition
**更新 \| Update** - 用从服务端获取的数据替换Cookie储存的数据. \| Use the data from the server to replace the data stored in cookies.  
**刷新 \| renew** - 重置Cookie持续时间 \| reset the expiration date of the cookie  

#### 全局Cookie定义 \| Globally Stored Cookie Definition

|Cookie|类型(Type)|有效时间(available time)|详细信息(Description)|注解(Note)|属性(Property)|
|-|-|-|-|-|-|
|username|string|`$OPENAPISettings['TokenAvailableDuration']` = 3600*24*7|用户名(Username)|跟随Token更新(Renewed following token)|HTTPOnly|
|token|string|`$OPENAPISettings['TokenAvailableDuration']`|登录Token(Login Credential)|此Token可以在被检查时刷新, 是否刷新取决于服务端设置. It can be renewed when checking token, depends on `$OPENAPISettings['RenewAPPTokenWhenChecking']`|HTTPOnly|
|userInfo|string(json)|-1(临时性Cookie \| Temporary Cookie)|用户详细信息(User Detailed Info)|-|HTTPOnly|
|authedContents|string(json)|`$OPENAPISettings['TokenAvailableDuration']`|用户授权信息(User Auth Info)|每次登录和授权应用时得到更新(Updated everytime when logging in or authing apps), 且跟随token刷新+更新(and also renewed & updated when token is renewed), 另外每次授权应用后都在本地更新(Everytime user authed an app, it gets updated locally)|HTTPOnly|