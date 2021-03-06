========================================
文章閱讀（Articles）
========================================


.. contents:: 目錄


Lossless compression with Brotli in Rust for a bit of Pied Piper on the backend
===============================================================================

:來源: Dropbox
:作者: Daniel Horn and Mehant
:URL: https://blogs.dropbox.com/tech/2016/06/lossless-compression-with-brotli/

Dropbox 希望他們的壓縮、解壓縮演算法可以有三個性質：

1. 安全
2. 可預期
3. 快速

先前 Dropbox 使用的是 C 語言實作的 Brotli 壓縮，
只達到第三個條件「快」，
於是 Dropbox 選用 Rust 這個注重記憶體安全性的語言來實作 Brotli，
再搭配上 Linux kernel 內的 SECCOMP 功能來增加安全性。
剛實作完的 Rust 版 Brotli 只達到原本 C 版本效能的七成多，
在經過些許調整後已經達到原本效能的八成。

（背景知識填補）
`Zopfli <https://github.com/google/zopfli>`_ 是一個壓縮 Library，
目標是高壓縮率（所以壓縮時間可能會比較久），
支援了 DEFLATE、gzip、zlib，
在官方的說明裡就寫著「perform very good, but slow, deflate or zlib compression」。
`Brotli <https://github.com/google/brotli>`_ 也是壓縮 Library，
使用的是和 gzip 以及 Zopfli 一樣的 LZ77 壓縮演算法，
跟 Zopfli 比起來有更多 trade off，
可以有更好的壓縮品質又有更快的壓縮速率。


目前（2016）Zopfli 有以下優勢：

* Zopfli 被視為目前壓縮率最有效的 DEFLATE，被認為是目前 DEFLATE 演算法的工藝水平
* `ZopfliPNG <https://github.com/google/zopfli/blob/master/README.zopflipng>`_ 利用 Zopfli 來壓縮 PNG，被視為目前壓縮效果最好的 PNG optimizer。


相關資源：

* [2015] `Introducing Brotli: a new compression algorithm for the internet <https://google-opensource.blogspot.tw/2015/09/introducing-brotli-new-compression.html>`_
* `Quora - What is Brotli? How is it different from Zopfli? <https://www.quora.com/What-is-Brotli-How-is-it-different-from-Zopfli/answer/Cody-Ray-Hoeft>`_
* `Comparison of Brotli, Deflate, Zopfli, LZMA, LZHAM and Bzip2 Compression Algorithms <https://www.gstatic.com/b/brotlidocs/brotli-2015-09-22.pdf>`_



ripgrep is faster than {grep, ag, git grep, ucg, pt, sift}
==========================================================

:作者: Andrew Gallant
:URL: http://blog.burntsushi.net/ripgrep/
:HackerNews: https://news.ycombinator.com/item?id=12564442

稍微介紹 Regex Engine 的差異、ripgrep 有哪些特色、有哪些地方可以加速字串的尋找，
最後做一些效能測試



Incremental Compilation
==========================================================

:作者: Michael Woerister
:URL: https://blog.rust-lang.org/2016/09/08/incremental.html
:HackerNews: https://news.ycombinator.com/item?id=12457942

介紹 Rust 編譯器正在開發的 Incremental Compilation 功能



Pretty State Machine Patterns in Rust
========================================

:作者: Andrew Hobden
:URL: https://hoverbear.org/2016/10/12/rust-state-machine-pattern/



Thoughts on DX: GNOME and Rust
========================================

:作者: Alberto Ruiz
:URL: https://siliconislandblog.wordpress.com/2016/10/31/thoughts-on-dx-gnome-and-rust/

相關 repo：

* `GNOME/librsvg - branch rustification <https://github.com/GNOME/librsvg/tree/rustification/rust>`_


後續相關發展：

* `Rust and GNOME meeting notes <https://internals.rust-lang.org/t/rust-and-gnome-meeting-notes/4339>`_
* `Rust and GObject <https://blogs.gnome.org/despinosa/2016/11/01/rust-and-gobject/>`_
* `Rust and Vala <https://blogs.gnome.org/despinosa/2016/11/01/rust-and-vala/>`_
* `Exposing Rust objects to C code <https://people.gnome.org/~federico/news-2016-11.html#14>`_
* `Debugging Rust code inside a C library <https://people.gnome.org/~federico/news-2016-11.html#16>`_



Fixing Python Performance with Rust
========================================

:作者: Armin Ronacher
:URL: https://blog.sentry.io/2016/10/19/fixing-python-performance-with-rust.html



Refactoring std for ultimate portability - libs - Rust Internals
================================================================

:作者: Brian Anderson
:URL: https://internals.rust-lang.org/t/refactoring-std-for-ultimate-portability/4301



2016 Rust Commercial User Survey Results
========================================

:作者: Jonathan Turner
:URL: https://internals.rust-lang.org/t/2016-rust-commercial-user-survey-results/4317



Paradigms of Rust for the Go developer
========================================

:作者: Ralph Caraveo
:URL: https://medium.com/@deckarep/paradigms-of-rust-for-the-go-developer-210f67cd6a29

講解在 Go 發生的 data race 問題，
而在 Rust 中會怎樣被編譯器避免，
最後建立出沒有 data race 的程式碼。



sccache, Mozilla's distributed compiler cache, now written in Rust
==================================================================

:作者: Ted Mielczarek
:URL: https://blog.mozilla.org/ted/2016/11/21/sccache-mozillas-distributed-compiler-cache-now-written-in-rust/



WebVR coming to Servo: Architecture and latency optimizations
==================================================================

:作者: Imanol Fernandez
:URL: https://blog.mozvr.com/webvr-servo-architecture-and-latency-optimizations/

講述 Servo 中支援 WebVR 的架構，
以及以 Rust 寫的 WebVR Library 的設計。


相關資源：

* `WebVR - Bringing Virtual Reality to the Web <https://webvr.info/>`_
* `Is WebVR Ready? <https://iswebvrready.org/>`_


ripgrep code review
===========================================

:作者: Michele Bertasi
:URL: http://blog.mbrt.it/2016-12-01-ripgrep-code-review/

針對 ripgrep 的程式碼做研究，
分析裡面的架構和運作流程。



Rust futures at a glance
===========================================

:作者: Zimon Dai
:URL: https://daiheitan.github.io/blog/2016/12/07/Rust-futures-at-a-glance/

介紹 Rust 的 Async IO 以及 Futures 的概念和使用。


相關資源：

* `Tokio <https://github.com/tokio-rs>`_
* `RFC 1522 - conservative impl trait <https://github.com/rust-lang/rfcs/blob/master/text/1522-conservative-impl-trait.md>`_
    - Abstract return types allow a function to hide a concrete return type behind a trait interface similar to trait objects, while still generating the same statically dispatched code as with concrete types.
    - ``#![feature(conservative_impl_trait)]``
    - 合法的操作
        + https://github.com/rust-lang/rust/tree/master/src/test/run-pass/impl-trait
    - 不合法的操作
        + https://github.com/rust-lang/rust/tree/master/src/test/compile-fail/impl-trait
* `RFC 0019 - Opt-in builtin traits (OIBITs) <https://github.com/rust-lang/rfcs/blob/master/text/0019-opt-in-builtin-traits.md#default-and-negative-impls>`_



Optimizing Rc memory usage in Rust
========================================

:作者: Robert Grosse
:URL: https://medium.com/@robertgrosse/optimizing-rc-memory-usage-in-rust-6652de9e119e4


作者想要降低他程式所使用的記憶體，
並以他程式中常使用的 Arc 著手觀察。

這是他研究的程式碼：

.. code-block:: rust

    enum LongStr {
        Leaf(Vec<u8>),
        Concat(RcStr, RcStr),
    }
    type RcStr = Arc<LongStr>;


一開始猜測他沒用到的 Arc 中的 weak reference 會佔用額外的記憶體，
但是仔細實驗後發現在沒使用到的狀況下會被編譯器直接優化掉。

後來覺得 ``Concat`` 用的記憶體太多了，
發現是同 enum 內 ``Leaf`` 的 ``Vec`` 造成的，
``Vec`` 除了資料以外還存了現在長度、目前容量，
所以花費了額外的記憶體，
連帶造成 ``Concat`` 的記憶體也降不下來，
這兩個欄位對作者的狀況來說是不需要的，
所以可以用更單純的方式代替，
於是程式碼就變成這樣：

.. code-block:: rust

    enum LongStr {
        Leaf(Box<[u8]>),
        Concat(RcStr, RcStr),
    }
    type RcStr = Arc<LongStr>;


作者觀察型別大小的方式： ``cargo rustc --release -- -Z print-type-sizes``

另外也有嘗試使用系統的 memoyr allocator，
但是發現沒有比 ``jemalloc`` 來的好。



What Are Sum, Product, and Pi Types?
========================================

:作者: Manish Goregaokar
:URL: https://manishearth.github.io/blog/2017/03/04/what-are-sum-product-and-pi-types/

因為最近有 Rust RFC 在討論 Pi Type，
因此作者寫了一篇文章介紹 Sum Type、Product Type、Pi Type，
講解名稱的來源和使用起來的樣子。

基本上就是以集合的角度去看而命名的，
Sum Type 就是數個集合組合成的大集合，
Product Type 就是數個集合進行 Cartesian Product 的結果，
而 Pi Type 則是數個集合進行 Infinite Product 的結果。

Product Type：

.. code-block:: rust

    // { (x, y) : x ∈ bool, y ∈ u8 }
    // Foo = bool × u8
    struct Foo {
        x: bool,
        y: u8,
    }


Sum Type：

.. code-block:: rust

    // Foo = bool + u8
    enum Foo {
        Bool(bool),
        Integer(u8),
    }

    // Bar = bool_1 + bool_2 + u8
    enum Bar {
        Bool1(bool),
        Bool2(bool),
        Integer(u8).
    }


Pi Type：

.. code-block:: rust

    // Not valid Rust sytax, yet

    //               ∞
    // make_array =  ∏  (fn() → Array<bool, x>)
    //              x=0
    fn make_array<const x: u8>() -> Array<bool, x> {
       // ...
    }



Rust on the ARM Cortex M3
========================================

:URL: http://www.acrawford.com/2017/03/09/rust-on-the-cortex-m3.html

作者介紹如何在官方沒有支援的平台上編譯簡單的 Rust 程式，
從環境建立（Cargo、rustup、xargo）到寫 Linker Script。
