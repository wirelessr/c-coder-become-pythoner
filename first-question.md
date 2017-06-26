要如何讓C programmer快速入門python？首先就先從日常開始，C code的日常主要包含兩個：變數的宣告和function實作。因為是C coder，所以就不需要探討class之類的OOP。

在python中不需要_**宣告**_變數，直接定義就好，例如在C語言中：

```c
int a = 5;
```

但在python中，不需要int的前綴也**不需要分號的結尾**，直接：

```py
a = 5
```

當C語言要宣告字串並給予初始化時：

```c
char *a_string = "Hello world";
```

在python中只需要：

```py
a_string = "Hello world" # 單引號也可以
```

以上是變數的宣告，這時候還看不太出來python強在哪，只依稀感覺到方便而已，接下來看function的實作。醜話先說說在前頭，C的function很麻煩，非常麻煩，因為是強型別語言所以參數要要求型態，回傳值也要求型態；但python無型別，所以完全不需要前綴，而且python可以接受為參數初始化，所以可以使參數填和不填都沒差。

先給一個C語言的例子：

```c
int a_function(int i, char *s, double f)
{
    i = 5;
    f = 3.3;
    printf("%d %s %f\n", i, s, f);
    return 0;
}
```

再來看看python：

```py
def a_function(i, s = "python", f = 3.3):
    print(i, s, f)
    return 0
```

Python不需要花括號而是透過縮排來決定階層，在定義function前必須要有def而結尾要是冒號，若是參數需要初始值直接丟在參數列即可。呼叫的時候也有各種花式：

```py
a_function(10, "Hello", 5.5) # 最正常的用法
a_function(100) # 省略有初始值的參數
a_function(s="hi", i=10) # 可以直接指定參數，不用按照順序
```



