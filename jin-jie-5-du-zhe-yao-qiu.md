有人提問題給我，如果內容複雜我就會放在這篇。

# CGI

Python是可以做CGI，事實上越來越多CGI採用python來實作，因為比較好寫而且功能豐富，這邊只稍微介紹一點，至於CGI的原理和如何安裝不是本篇的重點。CGI是由web server呼叫的一個tool，web server讀取CGI的standard out作為給user的page。CGI最常使用的功能就是擷取user輸入的url，然後解析內部提供的各種參數，在python中有現成的API可以直接使用，假設url如下形式：

> ```
> www.foo.com/bar?key1=value1&key2=value2
> ```

```py
import cgi, cgitb
form = cgi.FieldStorage() 
site_key1 = form.getvalue('key1')
site_key2 = form.getvalue('key2')
```

這樣就可以輕鬆解析url了。

---

# Resolve IP Address

將domain name轉成IP是寫網路應用程式很常碰到的問題，python提供與C語言類似的API來做：

1. gethostbyname - IPv4 only
2. getaddrinfo - dual stack

這兩個API應該似曾相似，其實和C語言的API名稱一模一樣，但用法比C語言單純。

```py
import socket
addr = socket.gethostbyname('www.google.com')
```

這樣就可以拿到google的IP地址，但只有支援IPv4，若是需要IPv6那就必須使用getaddrinfo，用法與gethostbyname一樣，但參數和回傳的值多了很多。API的原型是：

> socket.getaddrinfo\(host, port, family=0, type=0, proto=0, flags=0\)

```py
import socket
addr_list = socket.getaddrinfo("example.org", 80, proto=socket.IPPROTO_TCP)

for (family, type, proto, canonname, sockaddr) in addr_list:
    if family == AddressFamily.AF_INET6:
        (address, port, flow_info, scope_id) = sockaddr
    elif family == AddressFamily.AF_INET:
        (address, port) = sockaddr
```

上面的例子已經把所有回傳值給拆開了，flow\_info和scope\_id是IPv6獨有的屬性，有興趣可以去查IPv6的spec。

---

# 環境變數

在python中，所有的環境變數都被裝在一個map裡，所以要設定或者讀取只需要對map做操作即可，例如：

```py
import os
os.environ['HOME']
```

其實就和C語言的

```c
getenv("HOME")
```

是等價的。若是platfrom支援unsetenv的操作，那麼就可以透過map的pop\(\)把想要移出的環境變數給踢掉。

