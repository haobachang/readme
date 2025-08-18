# 0x01 漏洞解析
<font style="color:rgba(0, 0, 0, 0.85) !important;">SSRF（Server-Side Request Forgery，服务器端请求伪造）是一种由攻击者构造请求，迫使服务器端发起非预期网络请求的安全漏洞。</font>

**<font style="color:rgb(0, 0, 0) !important;">核心成因</font>**<font style="color:rgba(0, 0, 0, 0.85) !important;">：当应用程序需要从外部获取资源（如通过 URL 加载图片、获取第三方数据）时，若未对用户提供的 URL 进行严格验证，攻击者可构造恶意 URL，诱导服务器访问内部系统或其他敏感资源。</font>

**<font style="color:rgb(0, 0, 0) !important;">典型危害</font>**<font style="color:rgba(0, 0, 0, 0.85) !important;">：</font>

+ <font style="color:rgba(0, 0, 0, 0.85) !important;">探测内部网络结构和服务</font>
+ <font style="color:rgba(0, 0, 0, 0.85) !important;">访问 / 攻击内网服务（如数据库、Redis、后台管理系统）</font>
+ <font style="color:rgba(0, 0, 0, 0.85) !important;">读取服务器本地文件（结合 file 协议）</font>
+ <font style="color:rgba(0, 0, 0, 0.85) !important;">进行端口扫描</font>
+ <font style="color:rgba(0, 0, 0, 0.85) !important;">利用云服务元数据服务获取凭证（如 AWS、阿里云的 metadata 接口）</font>

乌云相关漏洞：

![](https://cdn.nlark.com/yuque/0/2025/png/8420228/1755436710575-7ffdc4fa-9895-42e8-aa31-25f0c1dd1d4f.png)  


# 0x02 漏洞环境
针对此漏洞，在[好靶场](http://www.loveli.com.cn:12531/)平台有搭建自创的同类型漏洞

靶场连接：[http://www.loveli.com.cn:12531/](http://www.loveli.com.cn:12531/)

![](https://cdn.nlark.com/yuque/0/2025/png/8420228/1755436974389-4f6214a3-a270-4c42-bdad-b9c55cd3b01c.png)

# 0x03 漏洞复现
访问目标靶场

浏览器访问之后进入旅游报团页面

![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1755245157463-a37b5824-35d1-4084-b966-fe154d2d357e.png)

然后点击立即报名

![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1755245192744-3c626d85-254b-4139-89f9-d3735af21745.png)

点击下载行程，发现下载了一个文件

![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1755245234077-08583acb-e171-40c5-9498-56d803bf4a28.png)

获取下载行程数据包

![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1755245271388-3214479d-5f6e-48ab-b214-32326253dd1a.png)

输入[http://www.baidu.com](http://www.baidu.com)获取到flag

![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1755245440548-e6d61a3a-0f67-4bff-8e6a-f29f78d217cd.png)

# 0x04 漏洞修复方法
**<font style="color:rgb(0, 0, 0) !important;">严格 URL 验证</font>**

    - <font style="color:rgba(0, 0, 0, 0.85) !important;">仅允许白名单内的域名 / IP</font>
    - <font style="color:rgba(0, 0, 0, 0.85) !important;">禁止访问内网 IP（如 192.168.0.0/16、10.0.0.0/8 等）</font>
    - <font style="color:rgba(0, 0, 0, 0.85) !important;">限制允许的协议（如仅允许 http/https，禁止 file、gopher 等危险协议）</font>

**<font style="color:rgb(0, 0, 0) !important;">请求限制</font>**

    - <font style="color:rgba(0, 0, 0, 0.85) !important;">限制请求端口（仅开放 80、443 等必要端口）</font>
    - <font style="color:rgba(0, 0, 0, 0.85) !important;">设置超时时间，防止 DoS 攻击</font>
    - <font style="color:rgba(0, 0, 0, 0.85) !important;">禁止跳转或严格限制跳转次数和目标</font>

**<font style="color:rgb(0, 0, 0) !important;">内部防护</font>**

    - <font style="color:rgba(0, 0, 0, 0.85) !important;">隔离敏感服务，不暴露在可被 SSRF 访问的网络</font>
    - <font style="color:rgba(0, 0, 0, 0.85) !important;">云服务关闭元数据服务访问或限制权限</font>





