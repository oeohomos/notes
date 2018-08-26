========================================
Python Tips
========================================

紀錄一些 Python 上的小技巧，
善用後可以很方便，
不過有些已經用的很習慣了，
之後有想到任何新接觸 Python 的人會需要的再補上 :P



Python 的 bool 可以拿來做運算
========================================

.. code-block:: python

    >>> +True
    1
    >>> +False
    0
    >>> True + 1
    2



善用 any 和 all
========================================

* any 是只要傳入的 iterable 中間有任何的 bool 是 True 就回傳 True
* all 是傳入的 iterable 中間全部的 bool 必須是 True 才回傳 True



分 group
========================================

.. code-block:: python

    >>> x = range(9)
    >>> list(zip(*[iter(x)]*3))
    [(0, 1, 2), (3, 4, 5), (6, 7, 8)]


.. code-block:: python

    >>> x = range(9)
    >>> list(zip(*[iter(x), iter(x[1:])]*2))
    [(0, 1, 1, 2), (2, 3, 3, 4), (4, 5, 5, 6), (6, 7, 7, 8)]


* `Python zip( ) Fu <http://pavdmyt.com/python-zip-fu/>`_



套用自己 class 內的 decorator 到其他 method
===========================================

.. code-block:: python

    @staticmethod
    def decorator(function):
        pass

    @decorator.__func__
    def func(self):
        pass



更改遞迴深度限制
========================================

.. code-block:: python

    import sys
    print(sys.getrecursionlimit())  # 預設是 1000
    sys.setrecursionlimit(1000000)


雖然可以更改遞迴深度的限制，
但是改的太寬鬆可能會讓遞迴執行過深，
造成記憶體不夠。



一次存取多個 Index 或 Value
========================================

.. code-block:: python

    >>> from operator import itemgetter
    >>> a = [-2, 1, 5, 3, 8, 5, 6]
    >>> b = [1, 2, 5]
    >>> itemgetter(*b)(a)
    (1, 5, 5)
    >>> d = {'a': 1, 'b': 2, 'c': 3}
    >>> itemgetter('a', 'c')(d)
    (1, 3)



subprocess + ffplay
========================================

.. code-block:: python

    import subprocess

    cmd = ['ffplay', '-']
    ffplay = subprocess.Popen(cmd, stdin=subprocess.PIPE)

    with open("test.mp4", "rb") as f:
        ffplay.stdin.write(f.read())



簡單地週期性執行特定函式
========================================

.. code-block:: python

    import threading

    def run_periodically(func, interval=1):
      def _func():
        func()
        threading.Timer(interval, _func).start()  # new timer
      _func()

    # simple usage, print "42" every 5 seconds
    run_periodically(lambda: print(42), 5)



format_map
========================================

類似 ``str.format(**mapping)`` ，
但是 ``mapping`` 會直接使用而不會被複製，
可以和繼承 ``dict`` 的 class 搭配使用（可以客製化找不到 key 或是存取結果的處理）。



Others
========================================

* `Python name mangling: When in doubt, do what? <http://stackoverflow.com/questions/7456807/python-name-mangling-when-in-doubt-do-what>`_
