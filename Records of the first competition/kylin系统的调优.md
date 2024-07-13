<a name="C3eKe"></a>
#  背景：
基于信创平台的调优的学习
<a name="we1ek"></a>
# ptyhon的自动化运维学习：

<a name="JOAlS"></a>
## 1,一些基础概念的解释：

<a name="W8Q5d"></a>
### **1.1，DNS，全称为域名系统（Domain Name System）**
**是互联网上的一项核心服务，它的主要功能是将人类易于记忆的域名转换为计算机能够识别的IP地址**。这一过程通常被称为域名解析。以下是关于DNS的一些关键信息：

- **工作原理**：当用户在浏览器中输入一个网址，如163.com，DNS会先检查本地缓存是否有这个域名对应的IP地址。如果没有，它会向配置的DNS服务器查询，服务器会返回相应的IP地址，然后用户的设备就能够通过这个IP地址访问目标网站。
- **分布式数据库**：DNS是一个分布式的数据库系统，它允许多个DNS服务器分布在世界各地，这些服务器相互协作，存储和提供域名与IP地址之间的映射信息。
- **服务器类型**：存在不同类型的DNS服务器，包括权威名称服务器（负责特定域名的解析信息）、递归解析服务器（负责响应客户端的解析请求并返回结果）和根服务器（最高级别的DNS服务器，知道所有顶级域的权威服务器地址）。
- **安全性问题**：DNS系统可能会遇到安全威胁，如域名劫持和DDoS攻击等。因此，为了提高安全性，出现了一些安全协议，如DNSSEC（域名系统安全扩展）。

综上所述，DNS作为互联网基础设施的重要组成部分，它通过将域名映射到IP地址，使得人们可以更加方便地访问网络资源，而不需要记住复杂的数字序列。

<a name="z10Zl"></a>
## 2,python的一些模块：
<a name="FMK3c"></a>
### 2.1,dnspython模块(关于DNS处理)
DNS处理模块dnspython是一个**Python实现的DNS工具包，它提供了丰富的功能来处理DNS相关的任务**。具体来说：

1. **查询功能**：支持几乎所有的记录类型，可以用于执行DNS查询，比如查找域名对应的IP地址等。<br />2. **区域传输**：允许用户进行区域传输，即获取一个DNS区域的完整副本，这通常用于备份或迁移DNS数据。<br />3. **动态更新**：支持动态更新ZONE信息，使得可以实时修改DNS记录。<br />4. **安全特性**：支持TSIG（事务签名）认证消息和EDNS0（扩展DNS），这些是提高DNS查询安全性的重要技术。<br />5. **兼容性**：dnspython起源于Nominum，它的开发是为了方便DNS软件的测试，现在已经成为一个稳定的工具包，版本1.16.0是目前的稳定版本。<br />6. **安装方式**：可以通过pip命令进行安装，例如使用`pip install dnspython`来安装这个模块。<br />7. **应用场景**：dnspython可以代替一些命令行工具如dig、nslookup等，用于自动化运维中的DNS服务监控以及解析结果的分析。

综上所述，dnspython是一个功能强大的Python DNS工具包，它可以帮助开发者和系统管理员在Python环境中轻松地进行DNS相关的操作和管理工作。

<a name="Es0rQ"></a>
#### 域名查询
dnspython提供了一个DNS解析器类——resolver，使用它的query方法来实现域名的查询功能。query方法的定义如下：

```
query（self， qname， rdtype=1， rdclass=1， tcp=False， source=None， raise_on_no_answer=True， source_port=0）
```
qname参数为查询的域名。rdtype参数用来指定RR资源的类型，常用的有以下几种：<br />·A记录，将主机名转换成IP地址；<br />·MX记录，邮件交换记录，定义邮件服务器的域名；<br />·CNAME记录，指别名记录，实现域名间的映射；<br />·NS记录，标记区域的域名服务器及授权子域；<br />·PTR记录，反向解析，与A记录相反，将IP转换成主机名；<br />·SOA记录，SOA标记，一个起始授权区的定义。<br />rdclass参数用于指定网络类型，可选的值有IN、CH与HS，其中IN为默认，使用最广泛。tcp参数用于指定查询是否启用TCP协议，默认为False（不启用）。source与source_port参数作为指定查询源地址与端口，默认值为查询设备IP地址和0。raise_on_no_answer参数用于指定当查询无应答时是否触发异常，默认为True。
<a name="TVkb0"></a>
## 3,Python监控Linux系统
[https://www.yuque.com/molic/wl1o6y/gf0gfn314utr2035](https://www.yuque.com/molic/wl1o6y/gf0gfn314utr2035)
<a name="lzpCU"></a>
### 3.1   dstat的使用(多功能系统资源统计工具)
[https://www.cnblogs.com/shoufeng/p/9739805.html#13--%E6%A3%80%E6%B5%8B%E7%95%8C%E9%9D%A2%E5%90%84%E5%8F%82%E6%95%B0%E7%9A%84%E5%90%AB%E4%B9%89](https://www.cnblogs.com/shoufeng/p/9739805.html#13--%E6%A3%80%E6%B5%8B%E7%95%8C%E9%9D%A2%E5%90%84%E5%8F%82%E6%95%B0%E7%9A%84%E5%90%AB%E4%B9%89)
<a name="tUeep"></a>
### 将其结果的监控信息保存到CSV文件中：
```
dstat -a --output dstat_utput.csv
```

<a name="DZBYs"></a>
## 3.2   glances的使用(交互式监控工具)
[https://cloud.tencent.com/developer/article/1697737](https://cloud.tencent.com/developer/article/1697737)
<a name="ZJV7C"></a>
### 1，支持简化其他数据导入到其他服务器之中

3.3   /proc目录(伪文件系统)

