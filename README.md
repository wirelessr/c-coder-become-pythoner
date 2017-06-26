Python是軟體世界的趨勢，有越來越多應用透過python來完成，無論是數據科學、web service或自動控制等，都可以在python中取得不錯的成果。雖然python在效能上為人詬病，但因為其高度彈性，可以快速整合C語言等其餘套件來提升效能，因此效能已不經足以成為抗拒python的主要原因，從大數據和機器學習都可以使用python就可以知道，效能已經不是python的天井。

我本來是個使用C語言的程式設計師，近來完全投入在python中，因為使用python能夠讓人專注在解決問題，而不是解決語言，諸如memory leak或corruption，這些在C語言中惱人的問題，在python中都不復見。另外，C語言中也缺少很多方便的工具，例如：無型別的容器。

```py
container = [1, 2, "hello", attrs, 5.5]
```

更有甚者，一個function可以回傳兩個以上的值，這件事讓人頗為興奮，若是C語言要回傳多值，必須是將pointer丟入function中，該在function內還是外allocate，又該在哪free，都是日常很常見的問題，但python不需要擔心這個。

```py
host, port = get_remote(addr)
```

類似這樣的function，看起來不是簡單又漂亮嗎？

其他還有字串或列表的切片等很日常的功能，寫成C code會變成落落長的流水帳，但在python中卻可以很優雅。

```py
a_piece_of_str = orig[3:7]
```

當我想取字串的最後一個字，或list的最後一個elements，C該怎麼寫？一個for迴圈跑到最後\(\0或NULL\)，然後取前一個，python呢？

```py
last_one = orig[-1]
```

基於以上種種，還有很多特性，例如lambda或comprehension這些又更強大的功能我就不說了，我逐漸開始捨棄過時的C語言，轉投入python的懷抱。

> Beautiful is better than ugly.

我很建議每個要學習python的都先看看python之禪，只要安裝好python然後：

```py
import this
```



