# 行程\(Process\)

一般日常寫C的時候，常會透過system或popen去行使Shell指令，甚至讀回結果，這點在python中也是有可能會需要用到。若只是要下system執行一條指令，python的作法與C相同：

```py
import os
os.system('ls -al')
```

若是要讀取指令結果，C語言就得使用popen，在python中的subprocess這個模組內也有提供Popen這個API，功能強大但使用上較麻煩，若沒有甚麼特別需求，用check\_output就可以做到了：

```py
import subprocess
out = subprocess.check_output('ls -al | grep vim', shell=True)
```

如此一來，所有輸出結果就會被導入out這個字串變數，接著就是一般字串的操作。

有時候會想要把一些工作丟出去，讓子行程去跑，可以透過Process這個API去產生一個子行程並且控制他的開始和結束：

```py
from multiprocessing import Process
import os

def info(title):
    print(title)
    print('module name:', __name__)
    print('parent process:', os.getppid())
    print('process id:', os.getpid())

def f(name):
    info('function f')
    print('hello', name)

if __name__ == '__main__':
    info('main line')
    p = Process(target=f, args=('bob',))
    p.start()
    p.join()
```

以上例子，我們可以注意到，Process是可以提供參數\(args\)的，但他預設是多參數型式，如果參數只有一個還是得要用逗號。而子行程就可以透過start來啟動或是join來等待，與pthread的操作類似。

---

# 執行緒

在python中要使用執行緒，最簡單的做法就是透過threading.Tread，類似產生行程的方式產生一個執行緒：

```py
import threading, time

def foo(args):
    time.sleep(10)
    print(args)
    
t1 = threading.Thread(target=foo, args=(1,2,3), name="FOO")
t1.start()
t1.join()
```

整個操作的過程與行程基本上是一樣的，也有共通的操作方法。但python的thread提供一個很方便的功能Queue，先直接給一個範例：

```py
def worker():
    while True:
        item = q.get()
        if item is None:
            break
        do_work(item)
        q.task_done()

q = queue.Queue()
threads = []
for i in range(num_worker_threads):
    t = threading.Thread(target=worker)
    t.start()
    threads.append(t)

for item in source():
    q.put(item)

# block until all tasks are done
q.join()

# stop workers
for i in range(num_worker_threads):
    q.put(None)
for t in threads:
    t.join()
```

上面的例子在做的事情很單純，是典型的producer-consumer架構，也就是有個人一直把東西往queue裡丟，而其他人一直從queue裡面拿東西出來做事。從C語言的角度想這件事，一定會覺得需要有一個同步機制，無論是mutex或是semaphore，但在python範例中，這個同步機制消失了，因為queue本身隱含了這個同步機制。

另外，atomic是一個很重要的概念，在python中有一個萬惡的GIL\(Global Interpreter Lock\)，所以所有的執行緒同時間只會有一條在跑，某種程度來說保證了atomic；這也代表著，如果想要將工作加速，執行緒是不可靠的，必須使用行程。那甚麼樣的情況適合使用執行緒、甚麼時候該使用行程，這裡給初學者一個結論：

> 如果需要等待I/O，例如socket、file、dev等，使用執行緒；若是單純想要將一個很大的工作細拆，例如取1-100,000之間所有的質數，那麼用行程。將1-50,000和50,001-100,000拆成兩個行程，理論上就是兩倍速。



