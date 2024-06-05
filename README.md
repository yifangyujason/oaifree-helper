# oaifree_helper
### 本项目基于始皇的new站服务。利用单个Worker&Pages优雅访问始皇镜像站，组建合租共享车队。包含直链登陆、前端登陆页、用户管理、token池管理、车队管理、用户注册、用量查询等等功能。全程无需服务器和域名，无需改代码。
## 首先，致敬始皇，致敬所有热佬，没有他们的项目和服务就没有这个项目。
### 主要功能
   - 原理是储存`refreshtoken`和`accesstoken`，并调用始皇的各项接口获取`sharetoken`一键直达始皇的`new.oaifree.com`镜像站
   - 用户使用唯一用户名登陆即可后台自动分配sharetoken，自带始皇的聊天隔离功能。包含简易的用户体系，储存各类用户，设置各类用户的限额和限制
   - 支持使用/?un=xxx的直链登陆，分享更省心。
   - 自带注册功能，分享激活码给朋友，不用总手动录入用户
   - 支持组建token池，可前端面板储存token，支持自动判断rt/at，自动解析json
   - 支持token自动刷新，若遇at过期，自动调用始皇接口刷新at
   - 包含多种选车模式，可手动/指定用户专车/顺序轮询/随机选车
   - 支持禁用失效车次
   - 自动检测官方服务状态，如遇官方故障自动禁止用户登陆，甩锅官方
   - 支持人机验证
   - 自带管理面板,包含`用户管理、token池管理、用量查询`，点击登录页Logo跳转
# Worker 部署
### 1. 配置Turnstile 站点
   - 注册/登陆你的cloudflare，右上角可设置语言为中文。
   - 左侧找到`Turnstile`，选择`添加站点`
   - `站点名称`随意，`域`为：`workers.dev`或你自己的域名
   - 创建，记录好`站点密钥`和`密钥`，备用
### 2. 部署 Cloudflare Worker：
   - 注册/登陆你的cloudflare，右上角可设置语言为中文。
   - 在左侧列表找到`Worker和Pages`
   - 选择`KV`，创建一个名为`oai_global_variables`的KV备用
   - 选择`概述`-`创建应用程序`-`Worker`
   - 为项目命名，随便想一个简单的名字，如`new`, `oaifree`之类的。并创建worker
   - 进入`worker`-`设置`-`变量`，在`KV 命名空间绑定`添加绑定KV，变量名`oai_global_variables`
   - `可选`在worker的`设置`-`触发器`-`添加自定义域`绑定自己的域名
   - 回到本GitHub项目，复制`_worker.js`中的全部内容
   - 在worker配置页面点击 `编辑代码`，清空原有内容粘贴后点右上角`部署`
   - 大功告成！
   - 访问`自定义的域名`，或点击`部署`-`查看版本`,在初始面板一键保存各项环境变量（`Admin`保存后后该页面自动禁用，若需更改请至KV中调整）
### 3. 环境变量
   - 以下是所有变量，无需手动填写，部署完项目后第一次进入可前端面板一键储存。
```
TurnstileKeys //turnsile的密钥
TurnstileSiteKey //站点密钥
Users //默认用户，以aaa,bbb,ccc形式填写，能访问所有车牌
VIPUsers //vip用户，即私车用户，无速率和时间限制
FreeUsers //限制用户，有速率和时间限制
Admin //管理员，用于管理面板的验证使用，且可看所有聊天记录
ForceAN //自动上车，若设置为1，用户名为xxx_n的私车用户用登陆强制进入n号车，忽略登陆所选车号
SetAN //无车牌模式下的默认车牌，如只有一辆车则填1。如多辆车则留空，不能填0。如果要开启随机或顺序轮询，填True，并用下面两个变量控制
PlusMode //plus号随机的轮询方式，Order或者Random
FreeMode //普号随机的轮询方式，Order/Random或Plus（使用plus号池和配置），如只有一辆车则填1。如多辆车则留空，不能填0
WebName //站点名称
WorkerURL //站点域名，无需https://。若未绑定自己的域名，一般为[worker名].[用户名].workers.dev
LogoURL //图片地址，需https://
CDKEY //注册可用的激活码，以aaa,bbb,ccc格式
AutoDeleteCDK //设置为1激活码只可用一次
FKDomain //把sharetoken当at用时，走的默认域名
Status //服务状态，若为非空，无视openai官方故障通告，始终允许登陆
//以下可不填，在管理面板添加更便捷
PlusAliveAccounts //plus号池存活序号，以1,2,3格式
FreeAliveAccounts //普号存活序号，以1,2,3格式
rt_1
rt_2
at_1//（若已有rt，at可不填）
at_2
……
```
# 日志
- 建立GitHub项目
