# ihoneyScan 扫描器
[![Python3](https://img.shields.io/badge/python-3.5-green.svg?style=plastic)](https://www.python.org/)

## 模块：
* 批量端口扫描
* 批量子域名验证(解析IP,状态码,web容器,web开发语言,网页TITLE)

<br>

写这个脚本的初衷是想模仿bugscan的一个端口指纹识别功能，看图:)：
![bugscan](images/bugscan.png)

基本的端口扫描和端口指纹识别(www,smtp,mongodb,...)、Banner识别(Http响应中的Server,X-Powered-By,...)，这些功能都挺有用的。

端口扫描部分自己也写好了，纯粹的telnet库实现，但还是感觉没有masscan加nmap好用
masscan可以扫全端口：sudo masscan 11.22.33.44 -p1-65535
nma指定开放端口识别Banner：sudo nmap -A --script=banner -p T:21,22,23,25,53,80,111,139,161,389,443 11.22.33.44

接着依葫芦画瓢实现了一个可以获取域名响应码、Banner(Server、X-Powered-By)、网页TITLE的脚本:


## 几种子域名收集方式：
```
1. https://github.com/lijiejie/subDomainsBrute
2. https://github.com/Chora10/FuzzDomain
3. 法师的Layer子域名挖掘机
```
将整理后的子域名存放在url.txt文件中。

## 使用方法：
```
$ python3.5 ihoneyScan.py -h
 _   _                            ____                  
(_) | |__   ___  _ __   ___ _   _/ ___|  ___ __ _ _ __  
| | | '_ \ / _ \| '_ \ / _ \ | | \___ \ / __/ _` | '_ \ 
| | | | | | (_) | | | |  __/ |_| |___) | (_| (_| | | | |
|_| |_| |_|\___/|_| |_|\___|\__, |____/ \___\__,_|_| |_|
                            |___/                       

----------------------------------------------------------------
usage: ihoneyScan.py [-h] [-f URL_FILE] [-t [MAX_THREADS]] [-m [MODULE]]
                     [--ip [IP]] [-d [DOMAIN]]

Batch of subdomain validation tool.

optional arguments:
  -h, --help        show this help message and exit
  -f URL_FILE       Set subdomain file
  -t [MAX_THREADS]  Set max threads
  -m [MODULE]       Set module, Choice: [subdomain, portscan]
  --ip [IP]         Example: 207.148.23.27
  -d [DOMAIN]       Example: www.ihoneysec.top
```
Example：
```
子域名验证：
$ python3.5 ihoneyScan.py -m subdomain -d www.ihoneysec.top
$ python3.5 ihoneyScan.py -m subdomain -f url.txt
$ python3.5 ihoneyScan.py -m subdomain -t 100 -f url.txt
```
```
端口扫描：
$ python3.5 ihoneyScan.py -m portscan --ip 207.148.23.27
$ python3.5 ihoneyScan.py -m portscan -f ip.txt
$ python3.5 ihoneyScan.py -m portscan -f ip.txt -t 100
```
## 输出主要分四部分：
![portscan](images/portscan.png)

端口扫描默认只扫描以下常见端口：
```
[21, 22, 23, 53, 80, 111, 139, 161, 389, 443, 445, 512, 513, 514,
 873, 1025, 1433, 1521, 3128, 3306, 3311, 3312, 3389, 5432, 5900,
 5984, 6082, 6379, 7001, 7002, 8000, 8080, 8081, 8090, 9000, 9090,
 8888, 9200, 9300, 10000, 11211, 27017, 27018, 50000, 50030, 50070]
```

![result](images/result.png)
* 解析IP
* 状态码：如果有多个验证码，代表访问该域名所经过的所有跳转后的状态码，比如 http://jingyan.ganji.com => [302, 200] 就经过了一次临时跳转才停止，使用$ curl -I http://jingyan.ganji.com -L也可以看到整个跳转过程：
* Banner信息就简单了，就是获取Response中的HTTP 头中对应的值。
* 网页TITLE。

```
$ curl -I http://jingyan.ganji.com -L
HTTP/1.1 302 Found
Server: Tengine
Date: Thu, 22 Feb 2018 17:42:21 GMT
Content-Type: text/html; charset=UTF-8
Connection: keep-alive
Cache-Control: private, must-revalidate
Location: http://jingyan.ganji.com/pc/shouye
expires: -1

HTTP/1.1 200 OK
Server: Tengine
Date: Thu, 22 Feb 2018 17:42:21 GMT
Content-Type: text/html; charset=UTF-8
Connection: keep-alive
Vary: Accept-Encoding
Cache-Control: private, must-revalidate
```

2018-02-26 21:53:56

## Change Log：
2018-02-26 增加解析IP、端口扫描、--ip/-d参数<br>
2018-02-25 增加识别TITLE<br>
2018-02-09 多线程改为异步<br>
2018-02-02 增加批量解析子域名状态码、WEB容器、WEB开发语言<br>
2018-02-02 project start<br>