# 全域變數

了解日常篇章後，基本上已經可以使用python寫出功能相對齊全的小程式了，但在C語言中還有一些很常使用的技巧，並沒有被提及到，例如全域變數。在C語言中為了創造出一個模組，經常使用全域變數並且透過static將其封裝，例如：

```c
static struct stat _state = {0};

void setState(int newState)
{
    _state.st = newState;
}

int getState(void)
{
    return _state.st;
}
```

如此一來外部使用者就可以透過`setState()`和`getState()`來操作\_state而不需要理解內部的型別，這是一種沒有物件機制下又想實作封裝的妥協。這在python中是可以辦到的：

```py
_state = None

def setState(newState):
    global _state
    _state.st = newState
    
def getState():
    global _state
    return _state.st
```

只要透過_global_就可以做到存取全域變數，附帶一提，若是在setState中沒有加入global \_state的話會丟出UnboundLocalError。但是，我不建議在python中還使用全域變數，原因在於python本身支援物件導向，已經可以做到封裝屬性和方法了，需要物件的人自己持有該擁有的物件，不需要分享給多餘的人使用，上面的例子應該改寫得更物件些；

```py
class state:
    def __init__(self):
        self.st = 0

    def setState(self, newState):
        self.st = newState

    def getState(self):
        return self.st
```

---

# 特殊字元

只要是程式語言，碰到字串的時候一定都會有個共通的問題，那就是特殊符號，凡是碰到特殊符號總是要使用跳脫字元，無論是C語言或python都是使用反斜線作為跳脫字元，例如：

```py
s = "hi, \"python\""
```

但python有一個獨特的地方，字串可以用單引號也可以用雙引號包，所以上面的那個字串如果不想跳脫，改成單引號在外即可：

```py
s = 'hi, "python"'
```

在C語言中，若是一個字串展開必須換行，就必須透過**\n**來換行，當有很多行出現在一個字串時，在C語言是這樣表示：

```c
char *s = "hi\npython\nthis is a book\n";
```

這種不太容易閱讀，尤其是普通n出現在字串內很容易和\n混淆。在python同樣可以用C語言的表示方式，但python額外提供了多行字串的機制，只需要用三個引號包住，就可以**隱式換行**，不需要透過\n表示。

```py
mulitple_line = """
hi,
"Hello",
this is a 'book'
"""
```

透過三個引號包住的多行字串還有一個好處，內部無論是單引號還是雙引號都不再需要跳脫，整體寫起來會變得清爽，請記得，在三個引號中間每按一次enter就會出現一個換行字元，因此上面multiple\_line內部是有四個換行字元的\(包含最開頭的空行\)。

若是字串中有反斜線，也必須使用反斜線來跳脫，寫起來就會變成**\\**，當反斜線很多例如以下例子：

```py
multiple = """
\\n是換行
\\t是跳格
\\r是回到行頭
"""
```

這樣看起來很醜，到底是真的要換行還是只是要跳脫反斜線，都需要仔細端詳，當一大堆出現在同一行時會更加雜亂，因此python多出一個機制，稱為修飾字串，它可以讓字串遇到這種情況變得美觀些，下面例子和上面是相同的輸出：

```py
multiple = r"""
\n是換行
\t是跳格
\r是回到行頭
"""
```

只需要在字串的前面加上一個r，就代表後面的字串是raw string，可以直接使用反斜線而無須跳脫。另外，python的預設編碼是unicode而不是ASCII，所以字串直接輸入中文是完全沒問題的。

