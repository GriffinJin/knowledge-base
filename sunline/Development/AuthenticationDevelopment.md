# 1 Dual-token Authentication

The backend generates two JWT tokens: an access_token and a refresh_token.

In the BDS system, servers will not transmit raw JWT tokens to the frontend.

# 2 Development Process

## Authentication-free APIs

* **rdplogincheck**

  Fetch login teller's branch id before login.

* **rdpssologinin**

  Login API.

* **rdploginouttemp**

  The BDS system supports teller logout functionality even when the teller has not logged in.

## Login API return dual-token

登录接口为 `/twsp/rdpssologinin`，所属模块 `euc-server.com-euc-uams-parent-com-euc-uams-aplt`

### 2.1.1 登录接口返回增加 access_token 字段

*需要注意，前后端原本已经已经实现一套 token 机制但是不够规范，比如后端返回的字段为 token，前端每次请求携带的请求头的名字也是 token 而不是 Authorization。如果还是使用原字段，则直接替换 JWT Token 的生成和校验逻辑即可。*

### 2.1.2 登录接口返回 refresh_token

登录接口返回的响应中需要通过 httponly cookie 的方式增加 refresh_token。

## Create Tables

### tuap_user_refresh_tokens



## 2.2 服务端退出登录接口清空登录状态

## 2.3 新增 refreshToken 接口

## 前端存储 access_token

前端原本通过平台登录钩子存储登录接口返回的信息。这里不修改平台代码，直接从响应报文中提取access_token存储在LocalStorage中。

```js
localStorage.setItem('access_token', output.access_token)
```

## 前端重发机制

发送请求的时候需要携带 LocalStorage 中存储的 access_token。

如果后端返回的状态码是 428 并且包含请求头 X-Token-Status 是 needs_refresh：

* 请求 /refreshToken 接口，refresh_token 存储在 httponly cookies中，所以不需要显示携带。

  - 获取到新 access_token 并携带新 access_token 请求原接口。

  - 返回状态码 427 并且包含请求头 X-Token-Status 是 needs_relogin，返回到登录页面，清空 LocalStorage 中存储的 access_token 以及 cookie 中存储的 refresh_token。

* 正常显示报错内容。







Refresh Token 开发中遇到的问题

1. Refresh Token 业内实现方案为放在http only cookie 中，方式 xss 攻击，aps 交易不便实现。

   解决方案：

   * 因为bds一般部署在内网，非常安全，所以直接通过返回体返回。

2. 前端存储登录状态的地方在 session storage 中，切换页签会丢失登录状态。



# Demonstration Cases

Token validity period

* access_token 2 min

* refresh_token 5 min

## CASE 1 BDS Generate Dual-token

Upon successful authentication of the login request, the backend will issue both an access token and a refresh token to the client-side application.

1. Clear the browser cache to avoid potential conflicts.

2. Log into the EPS system and launch the browser's developer console.

3. Authenticate with teller [xxxx]'s valid credentials.

   **Expected Outcome**: Navigate to the workspace.

4. Monitor the `/rdpssologin` API calls in the Network panel of browser's developer tools.

   **Expected Outcome**: The response payload includes an `access_token` and a `refresh_token`.

5. Inspect the Local Storage data in the Application panel of browser's developer tools.

   **Expected Outcome**: The Local Storage persistenly contains both the access_token and refresh_token.

## CASE 2 Carry Access-token Each Request

1. 在案例二的基础上（需要在登录成功之后的二分钟之内）；
2. 打开【xxxx】交易，进行查询操作；
3. 在调试工具的 network 网络下找到【xxx】交易对应的 【xxx】接口；请求头中包含 access_token；
4. 验证请求头中的 access_token 和 调试工具的application工具下localstorage中存储的access_token 是否一致；
5. 正常进行交易；

## CASE 3 BDS Server Valid Access-token

**案例四** 反例 后端在请求过程中验证 access_token

1. 在案例三的基础上；
2. 通过postman拼接【xxx】接口的请求参数；
3. 请求参数中删除或修改请求头中的 access_token;

接口返回401认证失败。

## CASE 4 Refresh Access-token

正例 在refresh有效期内获取新token

1. 在案例三的基础上（需要间隔2分钟以上）；
2. 记录当前localstorage中存储的access_token
3. 重复案例三种的步骤2；
4. 查看浏览器调试工具的网络工具，调用了/refresh-token接口；
5. /refresh-token接口返回新的access_token;
6. 查看浏览器调试工具的网络工具，调用的【xxx】交易的【xxx】接口；
7. 请求头中的 Authorization 的值 和 步骤5中返回的 access_token 一致，并且和步骤2记录的 access_token 不一致；

## CASE 5 Refresh-token expired

1. 在案例三的基础上（案例三完成5分钟后）；
2. 重复案例三的步骤2；
3. 查看浏览器调试工具中的网络工具，仅有 /refreshToken API；
4. /refreshToken API 返回 401 状态码。



