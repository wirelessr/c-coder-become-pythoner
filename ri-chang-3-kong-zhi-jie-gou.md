了解基本資料型態後接著一樣是C語言的日常，各種流程控制例如if-else、while、for，python與C語言在這方面其實概念差距並不大，但值得注意的是，python的for與C語言的for有一個顯著的差異，python的for並必須使用在iterable的對象身上，例如群集資料。下面先從if-else開始，在C語言中：

```c
if(a > b) {
    printf("%d", a);
} else if(a < b) {
    printf("%d", b);
} else {
    printf("%d %d", a, b);
}
```

這是很常見的if-else型式，在python中的表現方法是：

```py
if a > b:
    print(a)
elif a < b:
    print(b)
else
    print(a, b)
```

if-else還有一個常見的用法是賦值和comprehension，comprehension之前已經提過，這邊舉個賦值的例子：

```py
a_value = 20 if a > b else 10
```

while的用法在C語言和python差別也不大，只是python支援while-else的形式，以下是python：

```py
while a < b:
    print(a)
    a += 1
    if a == 5:
        break
else:
    print("terminated")
```

上述例子中若是迴圈正常的跑完，例如a=1且b=3，那麼else區塊會被執行，但若是被break，例如a=1且b=10，如此else就不會被執行。特別提一下，python沒有++或--，必須要用+=1和-=1。

for迴圈就比較特別，在python中的for迴圈是遍歷整個iterable的物件，例如列表，並且取值：

```py
a_list = [1, "hi", 1, , "hello", 2, "fib", 3, 5, 8, 13, 21]
for item in a_list:
    print(item)
    if item == 13:
        break
else:
    print("terminated")
```

這個例子中我們看到，即使是不同的資料型態，但依然可以被for迴圈取值，但務必小心，若是for迴圈的執行區塊對資料型態做了不預期的操作，例如對整數型態做revert\(\)，會導致丟出異常。

有時候，在程式邏輯中我們需要的不只是值，還需要索引，此時需要動點手腳，透過enumerate來取出index：

```py
for i, item in enumerate(a_list):
    print("item[%d] =" % i, item)
```

最後是C語言programmer很不習慣的例外捕捉，在C語言中要判斷一個函式的執行結果總是依靠函式的回傳值，C coder無法想像為什麼新一點的語言都有try-catch，為什麼需要？這邊一併解答這個問題，以下給一個C語言的範例，而且總是很常會見到的形式：

```c
int complex()
{
    int ret = foo();
    if(ret == -1)
    {
        printf("Error 1");
        return 1;
    }
    else if(ret == -2)
    {
        printf("Error 2");
        return 1;
    }
    else
    {
        printf("Success");
    }
    return 0;
}

void bar()
{
    if(!complex())
    {
        printf("foo");
    }
    else
    {
        printf("bar");
    }
    printf("teminated")
}
```

這個例子有點長，我稍微描述一下情境，有一個人姑且稱作bar\(\)想要透過complex\(\)做些事情，所以他必須要先理解，complex\(\)的正確是0錯誤是1，接著complex\(\)裡面呼叫foo\(\)，foo\(\)的錯誤更複雜，有-1、-2之類的，而complex\(\)忽略了-1和-2的差別，只告訴外面成功和失敗。若是牽扯到資源回收，例如開檔讀檔、malloc/free等，這問題就變得更加複雜，我們還忽略了有些API的成功是0而有些的成功是1，更遑論，要透過回傳值來確認成敗，那bar\(\)卻沒有回傳值。這林林總總的問題疊加起來，造成C語言的錯誤偵測非常困難，外層的人必須了解內層的實作，例如使用getaddrinfo要知道甚麼時間點要freeaddrinfo，當getaddrinfo != 0不需要freeaddrinfo而且代表失敗，這真的很麻煩，不看文件怎麼會知道？所以才需要例外處理，以下是python改寫過的例子：

```py
def complex():
    foo()

def bar():
    try:
        complex()
    except (ERROR1, ERROR2):
        print("bar")
    else:
        print("foo")
    finally:
        print("terminated")
```

無論foo\(\)今天丟出了甚麼，都會被最外層的使用者bar\(\)給接收，從而迴避了一層包一層的控制結構。這邊有幾個重點，except多個例外時一定要加小括號，不加是另外一種語法\(太複雜了，有興趣自己看書\)，會出現不預期的行為；另外，else和之前的while-else的行為類似，沒有例外被捕捉才會進入else區塊，若有例外則否；至於finally，無論有沒有例外都會進，else和finally都是optional。

