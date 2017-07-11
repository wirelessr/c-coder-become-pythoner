# 正規表示式

Regular expression應該是寫shell script最好用的工具之一了，無論是要做分類或判斷pattern，都可以用正規表示式來達成。C語言沒有一個好用的正規表示式工具可以使用，所以碰到字串分類必須得要倚靠sscanf。先給一個簡單的例子，假設要分類的字串是：

```
/usr/sbin/sendmail - 0 errors, 4 warnings
```

我們希望將前面的絕對路徑和後面兩個數字給拔出來，在C語言透過sscanf是這麼做的：

```c
const char *line = "/usr/sbin/sendmail - 0 errors, 4 warnings";
int e, w;
char path[256];

sscanf(line, "%[^ ] - %d errors, %d warnings", path, &e, &w);
printf("path = %s , e = %d , w = %d\n", path, e, w);
```

透過sscanf可以簡單地達成這個目的。務必提醒一下，**%\[^ \]**這個寫法很重要，不能只單純的**%s**，因為空格也是字串的一部分，sscanf會不知道空格要停下來，那麼path就會變成**/usr/sbin/sendmail - 0 errors, 4 warnings**。

Python並沒有提供sscanf這樣的工具，但提供了更有威力的正規表示式，sscanf能夠做到的，正規表示式一定做得到，反之不成立。以下是python透過正規表示式的例子：

```py
import re
pat = '(\S+) - (\d+) errors, (\d+) warnings'
line = '/usr/sbin/sendmail - 0 errors, 4 warnings'
match = re.findall(pat, line)
print('path =', match[0][0], ', e =', match[0][1], ', w =', match[0][2])
```

上面例子中match會是一個列表，列表的每一個mamber裝了所有對應的tuple，長相是：

> \[\('/usr/sbin/sendmail', '0', '4'\)\]

因為列表只有一個成員，所以要先\[0\]，接著才是pattern對應的順序，這點請小心。python提供了許多正規表示式可以使用的功能，findall是其中一種，其他還有search和match，都滿常用到的。但講下去會離C語言越來越遠，所以點到為止。這邊整理一個表格，是sscanf對應的正規表示式寫法：

| sscanf Token | Regular Expression |
| :--- | :--- |
| %c | . |
| %5c | .{5} |
| %d | \[-+\]?\d+ |
| %e, %E, %f, %g | \[-+\]?\(\d+\(\.\d\*\)?\|\.\d+\)\(\[eE\]\[-+\]?\d+\)? |
| %i | \[-+\]?\(0\[xX\]\[\dA-Fa-f\]+\|0\[0-7\]\*\|\d+\) |
| %o | \[-+\]?\[0-7\]+ |
| %s | \S+ |
| %u | \d+ |
| %x, %X | \[-+\]?\(0\[xX\]\)?\[\dA-Fa-f\]+ |



透過正規表示式就可以完成所有sscanf的功能，但對C語言使用者來說，正規表示式不夠直覺，使用起來也卡卡的，這邊提供一個速解，但無法像正規表示式一般強大，不過對於一般日常也很夠用了。

```py
input = '1 3.0 false hello'
(a, b, c, d) = [t(s) for t,s in zip((int,float,bool,str),input.split())]
print (a, b, c, d)
```

如果只是要切割單純被空格分開的每一個item，那麼上面的做法更快也更單純，若是牽扯到的pattern更為複雜，依然可以搭配正規表示式：

```py
import re
input = '1:3.0 false,hello'
(a, b, c, d) = [t(s) for t,s in zip((int,float,bool,str),re.search('^(\d+):([\d.]+) (\w+),(\w+)$',input).groups())]
print (a, b, c, d)
```



