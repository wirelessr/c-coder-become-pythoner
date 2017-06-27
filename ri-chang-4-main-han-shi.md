# Main函式

C語言在寫任何功能時一定是從main開始，即使再怎麼不濟的功能main一定少不了，但在python就不同了，python保有script language的特性，所以不用main也可以執行，就是逐行執行，但為了能將開發的功能模組化，main還是不可或缺的，這邊先略過如何打包一個模組，專注在main該如何寫。

```py
import sys
import getopt

def main():
    # parse command line options
    try:
        opts, args = getopt.getopt(sys.argv[1:], "h", ["help"])
    except getopt.error, msg:
        print msg
        print "for help use --help"
        sys.exit(2)
    # process options
    for o, a in opts:
        if o in ("-h", "--help"):
            print __doc__
            sys.exit(0)
    # process arguments
    for arg in args:
        process(arg) # process() is defined elsewhere

if __name__ == "__main__":
    main()
```

import是python引用外部模組的關鍵字，與C語言的include類似，只是include只包含了file層級的引用，import包含了該模組的所有內容。以上是最簡單的寫法，當所寫功能變成模組為別人所引用時，import這個關鍵字會從該模組的第一行執行到最後一行，所以將所有的程式包裹在main這個函式可以減少別人的困擾，當外人import時**\_\_name\_\_**會是import的模組名，如此就不會執行到main函式。

但這樣還不夠，這邊sys.argv是從外部shell進入時輸入的參數，若是外部使用者直接使用main函式則無法取得sys.argv，因此建議修改prototype成：

```py
def main(argv=None):
    if argv is None:
        argv = sys.argv
    # etc., replacing sys.argv with argv in the getopt() call.
```

C語言的程式有一個小問題，當子函式從任何一個點呼叫了exit，那就會將整個函式結束掉，為了避免這個問題，盡量用例外來取代exit，但最外部的使用者可能還是會希望知道最後的執行結果，例如$?，因此透過例外處理來傳達錯誤，但依然透過exit來告知外部：

```py
import sys
import getopt

class Usage(Exception):
    def __init__(self, msg):
        self.msg = msg

def main(argv=None):
    if argv is None:
        argv = sys.argv
    try:
        try:
            opts, args = getopt.getopt(argv[1:], "h", ["help"])
        except getopt.error, msg:
             raise Usage(msg)
        # more code, unchanged
    except Usage, err:
        print >>sys.stderr, err.msg
        print >>sys.stderr, "for help use --help"
        return 2

if __name__ == "__main__":
    sys.exit(main())
```

在這篇稍微提了一些模組引用的概念，之後會更進一步介紹模組的相關知識，畢竟include也是C語言的日常。

