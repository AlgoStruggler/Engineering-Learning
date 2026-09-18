# 第三课：HTTP 与浏览器

## 一、HTTP

**HTTP = 浏览器和服务器之间约定好的“请求-响应”协议。**

### 1. 为什么需要 HTTP

- 浏览器要拿网页、图片、数据。
- 服务器要告诉浏览器“给你什么”“成功还是失败”。
- 双方需要一个共同语言。
- 这个语言就是 HTTP。

### 2. 请求的结构

一个 HTTP 请求包含：

| 部分           | 说明         | 例子                              |
| :------------- | :----------- | :-------------------------------- |
| 方法 `Method`  | 要做什么     | `GET` 、`POST` 、`PUT` 、`DELETE` |
| 路径 `Path`    | 操作哪个资源 | `/api/users/1`                    |
| 版本 `Version` | `HTTP` 版本  | `HTTP/1.1` 、`HTTP/2`             |
| `Header`       | 附加信息     | `Content-Type` 、`Authorization`  |
| `Body`         | 请求体       | `JSON` 、表单                     |

例如：

```http
POST /api/login HTTP/1.1
Host: example.com
Content-Type: application/json
Authorization: Bearer xxx

{"username": "alice", "password": "123"}
```

### 3. 响应的结构

| 部分     | 说明       | 例子                     |
| :------- | :--------- | :----------------------- |
| 版本     | HTTP 版本  | HTTP/1.1                 |
| 状态码   | 结果       | 200、404、500            |
| 状态文本 | 状态码说明 | OK、Not Found            |
| Header   | 附加信息   | Content-Type、Set-Cookie |
| Body     | 响应体     | HTML、JSON               |

例如：

```http
POST /api/login HTTP/1.1
Host: example.com
Content-Type: application/json
Authorization: Bearer xxx

{"username": "alice", "password": "123"}
```

### 4. HTTP 方法

| 方法     | 作用     | 幂等 | 安全 |
| :------- | :------- | :--- | :--- |
| `GET`    | 获取资源 | 是   | 是   |
| `POST`   | 创建资源 | 否   | 否   |
| `PUT`    | 全量更新 | 是   | 否   |
| `PATCH`  | 部分更新 | 否   | 否   |
| `DELETE` | 删除资源 | 是   | 否   |

- **幂等**：执行一次和执行多次，结果一样。
- **安全**：不改变服务器状态。

### 5. 状态码

| 范围  | 含义       | 常见                                              |
| :---- | :--------- | :------------------------------------------------ |
| `1xx` | 信息       | `101` `Switching Protocols`                       |
| `2xx` | 成功       | `200` `OK` 、`201` `Created` 、`204` `No Content` |
| `3xx` | 重定向     | `301` 永久、`302` 临时、`304` `Not Modified`      |
| `4xx` | 客户端错误 | `400` 、`401` 、`403` 、`404` 、`429`             |
| `5xx` | 服务器错误 | `500` 、`502` 、`503`                             |

必须记住：

- `200` 成功
- `201` 创建成功
- `301` 永久重定向
- `302` 临时重定向
- `304` 缓存有效
- `400` 请求参数错
- `401` 未登录
- `403` 已登录但无权限
- `404` 资源不存在
- `429` 请求太频繁
- `500` 服务器内部错误
- `502` 网关错误
- `503` 服务不可用

### 6. Header

常见请求 `Header` ：

| Header          | 作用                  |
| :-------------- | :-------------------- |
| `Host`          | 目标主机              |
| `User-Agent`    | 客户端信息            |
| `Accept`        | 接受什么类型          |
| `Content-Type`  | 请求体类型            |
| `Authorization` | 认证信息              |
| `Cookie`        | 携带 `Cookie`         |
| `Origin`        | 请求来源（`CORS` 用） |

常见响应 `Header` ：

| Header                        | 作用            |
| :---------------------------- | :-------------- |
| `Content-Type`                | 响应体类型      |
| `Content-Length`              | 响应体长度      |
| `Set-Cookie`                  | 设置 `Cookie`   |
| `Cache-Control`               | 缓存策略        |
| `Access-Control-Allow-Origin` | `CORS` 允许来源 |
| `Location`                    | 重定向目标      |

### 7. Cookie

- 服务器通过 `Set-Cookie` 让浏览器存一小段数据。
- 浏览器之后每次请求同一域名，自动带上 `Cookie`。
- 用于登录态、偏好设置、追踪。

| 属性                  | 作用                                      |
| :-------------------- | :---------------------------------------- |
| `HttpOnly`            | `JS` 不能读，防 `XSS`                     |
| `Secure`              | 只在 `HTTPS` 下发送                       |
| `SameSite`            | 防 `CSRF` ，值：`Strict` 、`Lax` 、`None` |
| `Max-Age` / `Expires` | 过期时间                                  |
| `Domain` / `Path`     | 作用范围                                  |

### 8. 同源策略

**同源 = 协议 + 域名 + 端口 完全相同。**

- `https://a.com` 和 `https://a.com:443` → 同源（`443` 是默认端口）
- `http://a.com` 和 `https://a.com` → 不同源（协议不同）
- `https://a.com` 和 `https://b.a.com` → 不同源（域名不同）
- `https://a.com` 和 `https://a.com:8080` → 不同源（端口不同）

同源策略限制：

- `JS` 不能读另一个源的 `DOM` 。
- `JS` 不能读另一个源的 `Cookie` 、`LocalStorage` 。
- `JS` 不能随意发跨源请求（除非 `CORS` 允许）。

### 9. CORS

**`CORS` = 服务器告诉浏览器，允许哪些源来访问。**

浏览器发跨源请求时：

1. 简单请求：直接发，服务器返回 `Access-Control-Allow-Origin`。
2. 复杂请求：先发 `OPTIONS` 预检请求，服务器允许后才发真实请求。

关键响应头：

| Header                             | 作用                |
| :--------------------------------- | :------------------ |
| `Access-Control-Allow-Origin`      | 允许的源            |
| `Access-Control-Allow-Methods`     | 允许的方法          |
| `Access-Control-Allow-Headers`     | 允许的请求头        |
| `Access-Control-Allow-Credentials` | 是否允许带 `Cookie` |
| `Access-Control-Max-Age`           | 预检缓存时间        |

常见 CORS 错误：

- `No 'Access-Control-Allow-Origin' header`：服务器没配。
- `Credentials` + `*`：带了 Cookie 就不能用 `*`，必须指定源。

## 二、浏览器

### 1. 从输入 URL 到页面显示

1. **`URL` 解析**：拆出协议、域名、路径。
2. **`DNS` 查询**：域名 → `IP` 。
3. **`TCP` 连接**：三次握手。
4. **`TLS` 握手**（`HTTPS`）：证书、密钥。
5. **发 `HTTP` 请求**。
6. **服务器响应**。
7. **浏览器解析 `HTML`**：构建 `DOM` 。
8. **解析 `CSS`** ：构建 `CSSOM` 。
9. **合成渲染树**：`DOM` + `CSSOM` 。
10. **布局**：计算每个元素位置大小。
11. **绘制**：画到屏幕。
12. **执行 `JS`**：可能修改 `DOM`、发新请求。

### 2. 关键概念

| 概念          | 说明                          |
| :------------ | :---------------------------- |
| `DOM`         | `HTML` 解析后的树             |
| `CSSOM`       | `CSS` 解析后的树              |
| `Render Tree` | `DOM` + `CSSOM`               |
| `Layout`      | 计算位置大小                  |
| `Paint`       | 绘制像素                      |
| `Reflow`      | 重新布局                      |
| `Repaint`     | 重新绘制                      |
| 阻塞渲染      | `CSS` 阻塞渲染，`JS` 阻塞解析 |

### 3. DevTools 的 Network 面板

你会用它看：

- 请求方法、`URL`、状态码
- 请求头、响应头
- 请求体、响应体
- 耗时：`DNS` 、`TCP` 、`TLS` 、`TTFB` 、下载
- 过滤：`XHR` 、` JS` 、`CSS` 、`Img`
- 模拟慢网
- 查看 `CORS` 错误

这是前端调试最重要的工具。

## 三、真实场景

### 场景 1：为什么登录后，刷新页面还是登录状态

1. 登录请求返回 `Set-Cookie: session=abc; HttpOnly`。
2. 浏览器存下 Cookie。
3. 之后每次请求同域，自动带 `Cookie: session=abc`。
4. 服务器读 `Cookie` ，识别用户。

### 场景 2：为什么前端调后端报 CORS 错

1. 前端在 `http://localhost:3000`。
2. 后端在 `http://localhost:8000`。
3. 端口不同 → 不同源。
4. 浏览器发跨源请求，后端没返回 `Access-Control-Allow-Origin`。
5. 浏览器拦截响应，控制台报 CORS 错。

解决：

- 后端加 CORS 头。
- 或前端用代理（Next.js 的 rewrites、Vite 的 proxy）。

### 场景 3：为什么 401 和 403 不一样

- `401`：你没登录，去登录。
- `403`：你登录了，但没权限，别试了。

## 四、小练习

### 1. 用 DevTools 分析一个真实请求

**需求：**

- 打开任意网站，按 F12。
- 切到 Network 面板。
- 刷新页面。
- 找一个 XHR/Fetch 请求。
- 打开任意网站，按 F12。
- 切到 Network 面板。
- 刷新页面。
- 找一个 XHR/Fetch 请求。

**步骤：**

随便打开一个网站，建议用**本身就靠 XHR/Fetch 加载数据的站点** （比如 `github`），

通过 `F12` 打开 `DevTools` ，切到 `Network` 面板。

刷新页面，表格会出现很多请求。

在 `Network` 面板上方有一排过滤器：`All / Fetch/XHR / JS / CSS / Img ...`

点 **`Fetch/XHR`**，只留下 `AJAX` 类请求。

点任意一条，右侧会出现详情面板，重点看三块：

| 区域                     | 看什么                                                       |
| :----------------------- | :----------------------------------------------------------- |
| **`Headers`**            | 请求方法、`URL` 、状态码、`Request Headers` 、`Response Headers` |
| **`Payload`**            | 请求体（`POST` 时才有）、`Query String` 参数                 |
| **`Response / Preview`** | 响应体内容                                                   |

**General（概览）**

```text
Request URL: https://api.github.com/users/github
Request Method: GET
Status Code: 200 OK
Remote Address: 140.82.xx.xx:443
```

**Request Headers（请求头，浏览器发给服务器的）**

```text
:authority: api.github.com
:method: GET
:path: /users/github
:scheme: https
accept: application/json
user-agent: Mozilla/5.0 ...
accept-encoding: gzip, deflate, br
```

**Response Headers（响应头，服务器回给浏览器的）**

```text
content-type: application/json; charset=utf-8
cache-control: public, max-age=60
etag: "xxxxx"
x-ratelimit-limit: 60
x-ratelimit-remaining: 59
```

**Response Body（响应体，JSON 数据）**

```text
{
  "login": "github",
  "id": 9919,
  "name": "GitHub",
  "public_repos": 500,
  ...
}
```

### 2. curl 请求

**需求**：

- 用 `curl` 发 GET、POST。
- 用 `-i` 看响应头。
- 用 `-X` 指定方法。
- 用 `-d` 发数据。
- 用 `-H` 加 Header。
- 能看到完整响应头和响应体。
- 能解释 `Content-Type` 的作用

**步骤：**

用 `GET` 请求 + 看响应头：

```bash
curl -i https://api.github.com/users/github 		# 不指定请求方式，默认就是 GET 请求
curl -i -X GET https://api.github.com/users/github  # 等价于显示指定方式
```

`-i` 表示 **include headers**，会把响应头和响应体一起打印。会看到：

```text
HTTP/2 200
date: Fri, 18 Sep 2026 01:25:24 GMT
content-type: application/json; charset=utf-8
cache-control: public, max-age=60, s-maxage=60
vary: Accept,Accept-Encoding, Accept, X-Requested-With
etag: W/"776d056f643ca8bcc9199a95dc7d0d88e15595f9e55d50129f256b2d2ba61aec"
last-modified: Wed, 06 May 2026 23:12:05 GMT
x-github-media-type: github.v3; format=json
x-github-api-version-selected: 2022-11-28
access-control-expose-headers: ETag, Link, Location, Retry-After, X-GitHub-OTP, X-RateLimit-Limit, X-RateLimit-Remaining, X-RateLimit-Used, X-RateLimit-Resource, X-RateLimit-Reset, X-OAuth-Scopes, X-Accepted-OAuth-Scopes, X-Poll-Interval, X-GitHub-Media-Type, X-GitHub-SSO, X-GitHub-Request-Id, Deprecation, Sunset, Warning
access-control-allow-origin: *
strict-transport-security: max-age=31536000; includeSubdomains; preload
x-frame-options: deny
x-content-type-options: nosniff
x-xss-protection: 0
referrer-policy: origin-when-cross-origin, strict-origin-when-cross-origin
content-security-policy: default-src 'none'
server: github.com
accept-ranges: bytes
x-ratelimit-limit: 60
x-ratelimit-remaining: 53
x-ratelimit-used: 7
x-ratelimit-resource: core
x-ratelimit-reset: 1789695500
content-length: 1382
x-github-request-id: EA90:131820:3BF50BD:3E7C935:6AAC9303
x-github-edge-region: southeastasia

{
  "login": "github",
  "id": 9919,
  "node_id": "MDEyOk9yZ2FuaXphdGlvbjk5MTk=",
  "avatar_url": "https://avatars.githubusercontent.com/u/9919?v=4",
  "gravatar_id": "",
  "url": "https://api.github.com/users/github",
  "html_url": "https://github.com/github",
  "followers_url": "https://api.github.com/users/github/followers",
  "following_url": "https://api.github.com/users/github/following{/other_user}",
  "gists_url": "https://api.github.com/users/github/gists{/gist_id}",
  "starred_url": "https://api.github.com/users/github/starred{/owner}{/repo}",
  "subscriptions_url": "https://api.github.com/users/github/subscriptions",
  "organizations_url": "https://api.github.com/users/github/orgs",
  "repos_url": "https://api.github.com/users/github/repos",
  "events_url": "https://api.github.com/users/github/events{/privacy}",
  "received_events_url": "https://api.github.com/users/github/received_events",
  "type": "Organization",
  "user_view_type": "public",
  "site_admin": false,
  "name": "GitHub",
  "company": null,
  "blog": "https://github.com/about",
  "location": "United States of America",
  "email": null,
  "hireable": null,
  "bio": "How people build software.",
  "twitter_username": null,
  "public_repos": 562,
  "public_gists": 0,
  "followers": 86355,
  "following": 0,
  "created_at": "2008-05-11T04:37:31Z",
  "updated_at": "2026-05-06T23:12:05Z"
}
```

上面是响应头，下面 `{...}` 是响应体。

用 `POST` 请求 + 发 `JSON` 数据：

```bash
curl -X POST \
> -H "Content-Type: application/json"\
> -d '{"a": 1}' \
> https://httpbin.org/post
```

参数解释：

| 参数                                  | 作用                                  |
| :------------------------------------ | :------------------------------------ |
| `-X POST`                             | 指定请求方法为 POST                   |
| `-H "Content-Type: application/json"` | 加一个请求头，告诉服务器我发的是 JSON |
| `-d '{"a":1}'`                        | 请求体数据                            |
| 最后的 URL                            | 目标地址                              |

[httpbin.org](https://httpbin.org/) 会把你的请求原样回显，所以能看到：

```json
curl: (3) URL rejected: Malformed input to a URL function
{
  "args": {},
  "data": "",
  "files": {},
  "form": {},
  "headers": {
    "Accept": "*/*",
    "Content-Type": "application/json-d",
    "Host": "httpbin.org",
    "User-Agent": "curl/8.5.0",
    "X-Amzn-Trace-Id": "Root=1-6aac9668-38dac4b971fc4e7b0baf764c"
  },
  "json": null,
  "origin": "223.245.19.6",
  "url": "https://httpbin.org/post"
}
```

`Content-Type` 的作用：

`Content-Type` 告诉服务器：**我发过来的数据是什么格式**。

- `application/json` → 服务器按 JSON 解析
- `application/x-www-form-urlencoded` → 表单格式 `a=1&b=2`
- `multipart/form-data` → 文件上传
- `text/plain` → 纯文本

**如果不写**，curl 默认用 `application/x-www-form-urlencoded`，服务器可能解析不了你的 JSON，就会拿到错误或空数据。

### 3. CORS 错误

打开任意网站（比如 `https://www.baidu.com`），用 `F12` 进入 `DevTools` ，切换到 `Console` 。

```js
fetch('https://example.com').then(r => r.text()).then(console.log)
```

注意此时会弹出一个 `Warning` ，

```text
Warning: Don’t paste code into the DevTools Console that you don’t understand or haven’t reviewed yourself. This could allow attackers to steal your identity or take control of your computer. Type "allow pasting" below and press Enter to allow pasting.
```

此时需要你手动输入：

```text
allow pasting
```

才可以粘贴。

```text
Access to fetch at 'https://example.com/' from origin 'https://www.baidu.com'
has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header
is present on the requested resource.
```

**同源策略（`Same-Origin Policy`）**：浏览器规定，页面只能自由访问**同源**（协议 + 域名 + 端口都相同）的资源。

- 你的页面来源：`https://www.baidu.com`
- 请求目标：`https://example.com`
- 两者**不同源** → 浏览器发出的是**跨域请求**

浏览器会先检查：目标服务器返回的响应头里，有没有 `Access-Control-Allow-Origin` 允许你的来源？

`example.com` 没有返回这个头 → 浏览器**拦截响应**，把结果藏起来不交给 `JS` → `Console` 报 `CORS` 错误。

> ⚠️意：请求其实**发出去了**，服务器也**响应了**，是**浏览器**在收到响应后把它拦下的。这就是 `CORS` 的本质。
