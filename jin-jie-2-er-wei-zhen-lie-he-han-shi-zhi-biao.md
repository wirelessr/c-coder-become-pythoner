# 二維陣列

在C語言中，二維陣列的表示方式主要分為幾種：

1. 固定尺寸的二維陣列，int array1\[row\]\[col\];
2. 固定長度的二維陣列，int \*array2\[row\];
3. 完全彈性的二維陣列，int \*\*array3;

會有這麼多種是因為，在C語言中，陣列的大小是固定的，而二維陣列其實就是由數個一維陣列組成，因此為了要在執行時期產生對應的大小，就必須透過指標來動態allocate，以下是一個經典二維陣列的產生過程：

```c
int **arr = (int **)malloc(r * sizeof(int *));
for (i=0; i<r; i++)
    arr[i] = (int *)malloc(c * sizeof(int));
```

透過宣告一個指標的指標來存放一個一維指標陣列，接著在每個指標陣列中產生一個陣列。這是很過時的寫法，如果這個陣列的執行週期就在當下這個生命區間，根本不需要兩次malloc，直接宣告一個陣列包含兩個變數，例如下面這樣的code是可行的，只需要在陣列宣告的時候能夠正確讀到尺寸，就可以產生對應大小的陣列，此例中的r和c其實是外部餵進來的，編譯時期根本沒有值。

```c
void foo(int r, int c)
{
    int arr[r][c];
    /* ... */
}
```

在python中產生一個二維陣列的作法其實也是類似，透過一個列表包含數個列表，將整個二維的概念架構起來，差別只在於，python無型別也沒指標，所以不需要透過指標和malloc對應的型別來產生列表，另外，python的語法透過comprehension可以讓二維陣列寫起來很漂亮：

```py
arr = [[0 for x in range(cols_count)] for x in range(rows_count)]
```

這樣一行就完成了動態二維陣列的宣告，並且將其初始化為0。至於用法其實就是一般二維陣列的用法，與C語言不同的地方是，當超出了陣列的大小，會丟出IndexError，C語言會無法偵測的問題在python中無所遁形，可以避免很多corruption。

---

# 函式指標

在python中沒有指標的概念，應該說只有C語言與其延伸語言才擁有指標，指標是指向一個記憶體位址的表示方法，但在高階語言中並不會讓使用者直接操作記憶體，所以沒有指標的概念，但在物件導向語言中，每一個物件其實都有他對應到的實體\(instance\)，物件本身就是一種指標。

為什麼在C語言中會需要用到函式指標？有兩個比較主要的使用情境：

1. Callback function
2. 動態抽換

Callback function使用函式指標是因為，要將一件事將給別人來做，所以先將所要做的事情寫好\(function\)，接著把這個function交給別人\(pointer\)，透過這樣的方式就可以讓外人知道要做些甚麼。但在物件的世界，遊戲規則不是這樣的，只需要將所要做的事連同所要用到的資料包裹成一個物件，將整個物件交給對方，這樣就可以了。只是為了要讓沒有物件導向基礎的C coder了解這個概念很困難，因此以下示範如何使用函式指標，務必再提醒一次，物件導向的世界不是這樣的：

```py
def foo(x):
    print("foo: " + x)

def bar(x):
    print("bar: " + x)

f = foo
f("one")
f = bar
f("ten")
```

從以上例子我們可以看到，當宣告一個函式時，他就已經成為一個物件了，可以當作變數來使用，因此可以透過一個變數**f**去承接，當然也可以直接傳遞函式，要使用的時候其實就是一般函式的使用方式，如此就是C語言中的函式指標。

正因為每一個函式都是物件，所以python也有提供另外一種產生所謂函式指標的方式，稱為lambda，lambda是一個在modern language很常見的功能，lambda可以產生一個匿名函式的實體，若是函式並不複雜也不需要重複使用，那就可以使用lambda來節省時間，最常使用的例子是列表的排序：

```py
student_tuples = [
    ('john', 'A', 15),
    ('jane', 'B', 12),
    ('dave', 'B', 10),
]

student_tuples.sort(key=lambda student: student[2])   # sort by age
```

之前介紹過列表，也介紹過列表的排序，也就是sort\(\)，若是sort沒給參數，那就會按照python內部的比較方式來排序；有時候我們想要自訂排序的依據，以上述例子來說，這學生資料中有姓名、成績和年齡，我們希望使用年齡來排序，就可以指定key這個參數。key這個參數需要的是一個function，我們當然可以像這樣寫：

```py
def sk(x):
    return x[2]

student_tuples.sort(key=sk)   # sort by age
```

但我們發現，sk這個function一輩子就用這麼一次，以後再也沒人需要，而且甚至大家根本也不在乎他叫甚麼名字，今天要叫他sk、student\_key、student\_age之類的根本沒人在意，那何不省略定義和命名的過程，直接做一個臨時的就好，這就是lambda的精神。而lambda的語法是

> lambda arg1, arg2, ....: expression



