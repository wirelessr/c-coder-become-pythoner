# 群集資料 - 字串和字典

介紹完列表，接著回到最基本的字串。字串的使用方法基本上和列表是一模一樣的，因為說字串其實就是全部元素都是字元的列表，所以列表的操作完全適用在字串，但字串又多了許多有別於列表的特化功能，例如格式化。Python的字串與C語言不同，不需要有一個\0作為結尾，這個\0是C語言字串最大的問題，有太多衍伸的bug是依據\0來的，例如strncpy不會自己補\0，要手動補。

我個人很喜歡python的字串，他讓處理字串變得很容易也很舒服，還記得C語言的strtok嗎？大家捫心自問，好用嗎？

```c
cmd = strtok(cmdline, " ");
while ((token = strtok(NULL, " ")) != NULL) {
    /* ... */
}
```

第一次的strtok還知道是要拆cmdline，第二次是？更別提當今天在multi-core或multi-threading時要改用strtok\_r，這一切實在很不科學。在python中，只需要：

```py
tokens = cmdline.split()
```

就可以把所有的token放到一個叫做tokens的列表內，接著可以做自己想做的事。若是要組合字串，在C語言中需要不斷的strcat，還要小心有沒有buffer overflow，而python沒這煩惱：

```py
new_string = old_string + new_part # 支援加號
new_string = ",".join(tokens) # 一行搞定，把空格轉逗號
```

在C語言常見的格式化字串sprintf，在python裡也有很優秀的實作：

```py
a_string = "Hello %s, %d points" % ("world", 100)
```

百分比符號是字串格式化的運算子，上面展示了C語言很常見的用法。在python中還有另外一種群集資料型態叫做字典，類似C++的std::map：

```py
a_map = {}
a_map["name"] = "Adam"
a_map["phone"] = 978978978
a_map[100] = 789
```

字典對於C語言的programmer來說很陌生，可以把它想成是一個容器，初始化是大括號，每一個欄位都可以標上名字\(鍵\)，任何型態都可以，通常是字串或數字，而取值的方法則像是陣列用中括號的indexing，這也是很方便的一個資料型態，用字典提供一個字串格式化更方便的途徑：

```py
a_string = "Hello %(name)s, my phone number is %(phone)d" % a_map
```

字典還提供另外一種初始化的方式，透過冒號來區隔鍵和值，例如上面的初始化可以改成：

```py
a_map = {"name" : "Adam", "phone" : 978978978, 100 : 789}
```

使用字典取值時要注意，若鍵不存在於字典，會丟出KeyError的例外，可以先透過in來判斷要取的鍵是否存在，或者透過get來判斷值不是None，也可以透過get來給定初始值：

```py
a_value = a_map.get("address") # return None
a_value = a_map.get("address", "aklsdjaskljdklsajdljas")
```



