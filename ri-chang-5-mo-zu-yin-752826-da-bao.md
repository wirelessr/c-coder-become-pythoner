# 模組引用&打包

在C語言中，無論是需要function的signature或者要知道變數的型態，都必須透過\#include來引用定義的header file，在header file中記載了可能用到的型別和函式；在python中，這樣的過程使用的是import這個關鍵字。之前我們已經看過最基本的import了，但其實import可以再更限定範圍，提供使用上的便利，但要避免namespace重疊。

```py
import os
print(os.path.basename(filename))

import os.path as path
print(path.basename(filename))

from os import path
print(path.basename(filename))

from os.path import basename
print(basename(filename))

from os.path import * # 千萬不要這樣做，namespace會一蹋糊塗
print(basename(filename))
```

這樣介紹實在沒甚麼意思，其實python內部包含了無數動人模組，透過import可以讓生活無限便利，例如要寫一個TCP server，在C語言需要：

```c
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
```

這樣才能搞定socket、bind、listen、accept還有inet的型別sockaddr\_in，接著就是一系列的初始化sockaddr和顯式的使用底層網路API，才真正能夠做到一個TCP server，但在python，要做一個TCP server基本上只要幾行就可以搞定：

```py
import socketserver

class MyTCPHandler(socketserver.BaseRequestHandler):

    def handle(self):
        self.data = self.request.recv(1024).strip()
        print("{} wrote:".format(self.client_address[0]))
        print(self.data)
        self.request.sendall(self.data.upper())

if __name__ == "__main__":
    HOST, PORT = "localhost", 9999

    with socketserver.TCPServer((HOST, PORT), MyTCPHandler) as server:
        server.serve_forever()
```

Python中有很多內建的模組可供使用，但若是需要使用別人編寫的模組又該如何？最簡單的作法是pip，pip是一個python的套件管理機制，可以透過pip安裝許多第三方的套件，例如有人嫌棄python原生的json，想使用第三方的json套件，就可以透過pip：

> pip install simplejson

在使用上與上述的import並無差別，依然是：

```py
import simplejson as json
```

這樣就可以把原生的json套件用第三方的simplejson取代。

很多時候，我們都會需要自己編寫套件給別人使用，在C語言的使用情境下是提供一系列header file，讓外部的人可以看懂，並且提供shared library或static library給別人連結。在python中要做到這樣的事只需要將寫好的py檔打包成一個資料夾，內部放入一個空的\_\_init\_\_.py，然後放置在python安裝套件的目錄下即可。以下舉個例子，我撰寫了一個圖形模組\(Graphics\)，內部包含了處理bmp和jpeg兩種格式的API，因此我打包成：

```
Graphics/
├── Bmp.py
├── __init__.py
└── Jpeg.py
```

這樣就可以正常使用Graphics：

```py
import Graphics.Bmp
image = Graphics.Bmp.load("oxox.bmp")

import Graphics.Jpeg as Jpeg
image = Jpeg.load("oxox.jpeg")
```

如何得知本機的套件安裝目錄在哪？所有的搜尋路徑被放在sys.path中，並且搜尋的先後順序就取決於列表的先後順序，因此若是重名，前面的會被優先使用：

```py
import sys
print(sys.path)
```



