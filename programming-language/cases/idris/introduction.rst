========================================
Idris
========================================


.. contents:: 目錄


基本介紹
========================================

Idris 是一個研究用的純 Functional 程式語言，
具有 Dependent Type 的特色，
Type System 類似於 Agda，
和 Agda 相比，Idris 更著重於打造通用的程式語言，
而不是只著重在 Theorem Proving。

Idris 語言本身主要是使用 Haskell 做開發，
而程式碼可以在編譯後轉換成其他程式語言，
例如 C 和 JavaScript。



安裝
========================================

.. code-block:: sh

    $ sudo pacman -S idris



範例
========================================

Hello World
------------------------------

``hello.idr`` :

.. code-block:: idris

    module Main

    main : IO ()
    main = putStrLn "Hello, World!"


.. code-block:: sh

    $ idris hello.idr -o hello
    $ ./hello
    Hello, World!
    $ ls -l hello
    -rwxr-xr-x 1 user user 85320 Dec  3 11:00 hello
    $ file hello
    hello: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=08cf680adb63917f28e607caae2ccbd74cc9d53e, not stripped
    $ ldd hello
            linux-vdso.so.1 (0x00007ffc122c8000)
            libgmp.so.10 => /usr/lib/libgmp.so.10 (0x00007f5e76961000)
            libpthread.so.0 => /usr/lib/libpthread.so.0 (0x00007f5e76744000)
            libm.so.6 => /usr/lib/libm.so.6 (0x00007f5e76440000)
            libc.so.6 => /usr/lib/libc.so.6 (0x00007f5e760a2000)
            /lib64/ld-linux-x86-64.so.2 (0x00007f5e76bf4000)
    $ strip hello
    $ ls -l hello
    -rwxr-xr-x 1 user user 64232 Dec  3 11:04 hello-stripped



範例
========================================

畢氏定理（Pythagorean theorem）
-------------------------------

.. code-block:: idris

    module Main

    pythag : Int -> List (Int, Int, Int)
    pythag max = [(x, y, z) | z <- [1..max], y <- [1..z], x <- [1..y],
                              x * x + y * y == z * z]

    main : IO ()
    main = print (pythag 50)

.. code-block:: sh

    $ idris example.idr -o example
    $ ./example
    [(3, (4, 5)), (6, (8, 10)), (5, (12, 13)), (9, (12, 15)), (8, (15, 17)), (12, (16, 20)), (15, (20, 25)), (7, (24, 25)), (10, (24, 26)), (20, (21, 29)), (18, (24, 30)), (16, (30, 34)), (21, (28, 35)), (12, (35, 37)), (15, (36, 39)), (24, (32, 40)), (9, (40, 41)), (27, (36, 45)), (30, (40, 50)), (14, (48, 50))]



參考
========================================

* `Idris - A Language with Dependent Types <http://www.idris-lang.org/>`_
* `Documentation for the Idris Language <http://docs.idris-lang.org/en/latest/index.html>`_
* `Wikipedia - Idris (programming language) <https://en.wikipedia.org/wiki/Idris_(programming_language)>`_
