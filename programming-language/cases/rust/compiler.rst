========================================
Rust 編譯器
========================================


.. contents:: 目錄


編譯「編譯器」
========================================

.. code-block:: sh

    # 抓程式碼
    $ git clone https://github.com/rust-lang/rust/
    $ cd rust/

    # see "^opt" in configure
    #
    # compiler-docs     "build compiler documentation"
    # rustbuild         "use the rust and cargo based build system"
    # local-rust        "use an installed rustc rather than downloading a snapshot"
    # vendor            "enable usage of vendored Rust crates
    # clang             "prefer clang to gcc for building the runtime"
    #
    $ ./configure --enable-local-rust --enable-clang --enable-vendor --enable-rustbuild --enable-compiler-docs --enable-optimize --enable-debug

    # 上面 configure 完成後會產生 x.py

    # 查看 Help 訊息
    $ python x.py --help
    $ python x.py build --help

    # 編譯到 Stage 1：
    #
    # 在大多數的 Rust 編譯器開發情況下只需要編到 Stage 1 即可，
    # 不需要編到 Stage 2，
    # 藉此可以省去不少時間
    #
    # -i, --incremental   use incremental compilation
    # -j, --jobs JOBS     number of jobs to run in parallel
    #     --stage N       stage to build
    $ python x.py build -i -j6 --stage 1
    $ ./build/x86_64-unknown-linux-gnu/stage1/bin/rustc --version

    # 編譯到 Stage 2：
    $ python x.py build -i -j6 --stage 2
    $ ./build/x86_64-unknown-linux-gnu/stage2/bin/rustc --version

    # 只編譯 rustc：
    # 不編譯 std
    $ python x.py build src/rustc --stage 1

    # 產生文件：
    $ python x.py doc
    $ cd ./build/x86_64-unknown-linux-gnu/doc/
    $ python -m http.server


* `Rust internal compiler documentation <https://manishearth.github.io/rust-internals-docs/rustc/>`_



LLVM
========================================

* `rustc: Upgrade to LLVM 6 <https://github.com/rust-lang/rust/pull/47828>`_
    - 直接從 LLVM 4 切到 LLVM 6
    - 並沒有把先前 Rust 這邊的所有 patch 都加上去，因為很多都已經不確定是否需要了
    - 撿了少數對 Rust 重要的 patch 上去，來讓 Rust 的測試可以通過
    - 效能測試最高有 13% 改進
    - 切到 LLVM 6 後就可以讓 WebAssembly backend 使用 LLD



Parser
========================================

Rust 有用 ANTLR 做 parser 驗證：

* https://github.com/rust-lang/rust/tree/master/src/grammar
* https://github.com/antlr
* https://github.com/rust-lang/rust/blob/master/src/doc/grammar.md



編譯器效能追蹤
========================================

* `rustc performance data <http://perf.rust-lang.org/>`_
* `Some benchmarks for testing rustc performance <https://github.com/rust-lang-nursery/rustc-benchmarks>`_



平台 Porting 範例
========================================

新硬體
------------------------------

* `IBM s390x <https://github.com/rust-lang/rust/pull/36369>`_

修改內容：

1. 新增平台名稱到 ``configure`` 內的 ``$CFG_CPUTYPE``
2. 設定 Toolchain （例如 ``mk/cfg/s390x-unknown-linux-gnu.mk`` ）
3. 在各處設定對應的選擇（以 ``s390x`` 為例）
    * src/liballoc_jemalloc/lib.rs
        - 設定 ``MIN_ALIGN``
    * src/liballoc_system/lib.rs
        - 設定 ``MIN_ALIGN``
    * src/liblibc
        - [liblibc] src/lib.rs
        - [liblibc] src/unix/notbsd/linux/mod.rs
            + 設定 ``pthread_mutexattr_t``
            + 設定數個變數和函式
        - [liblibc] src/unix/notbsd/linux/s390x.rs
            + 設定許多 Type 的大小
            + 定義數個函式界面
    * src/libpanic_unwind/gcc.rs
        - 設定 ``UNWIND_DATA_REG``
    * src/librustc_back/target/s390x_unknown_linux_gnu.rs
        - LLVM target
        - target endian
        - data layout
        - ...
    * src/librustc_trans/abi.rs
        - 選擇使用 ``cabi_s390x`` 內的功能
    * src/librustc_trans/cabi_s390x.rs
        - 各 Type 的大小以及 Alignment 設定
    * src/librustc_trans/lib.rs
        - 加入 ``cabi_s390x.rs``
    * src/libstd/env.rs
        - 設定 ``ARCH``
    * src/libstd/os/linux/raw.rs
        - 將 libc 的資訊重新 expose 出去
    * src/libstd/os/raw.rs
        - 設定數個 Type 的大小
    * src/libstd/sys/unix/rand.rs
        - 設定 syscall 數值
        - 把能提供的功能打開
    * src/libunwind/libunwind.rs
        - 設定 ``unwinder_private_data_size``
    * 設定數個需要忽略的測試
        - src/test/compile-fail/asm-bad-clobber.rs
        - src/test/compile-fail/asm-in-bad-modifier.rs
        - src/test/compile-fail/asm-misplaced-option.rs
        - src/test/compile-fail/asm-out-assign-imm.rs
        - src/test/compile-fail/asm-out-no-modifier.rs
        - src/test/compile-fail/asm-out-read-uninit.rs
        - src/test/debuginfo/simd.rs
        - src/test/run-pass/union/union-basic.rs
    * 設定、實做數個需要開啟的測試
        - src/test/run-make/atomic-lock-free/Makefile
        - src/test/run-pass/conditional-compile-arch.rs
    * src/tools/compiletest/src/util.rs
        - 設定平台的對應


新作業系統
------------------------------

* `Haiku OS <https://github.com/rust-lang/rust/pull/36727>`_

修改內容：

1. 新增平台名稱到 ``configure`` 內的 ``$CFG_CPUTYPE``
2. 設定 Toolchain （例如 ``mk/cfg/i686-unknown-haiku.mk`` 、 ``mk/cfg/x86_64-unknown-haiku.mk`` 和 ``mk/main.mk`` ）
3. 設定 OS
    * src/etc/local_stage0.sh
4. 在各處設定對應的選擇
    * src/librustc_back/target/haiku_base.rs
        - Linker
        - rpath
        - ...
    * src/librustc_back/target/i686_unknown_haiku.rs
        - LLVM target
        - target endian
        - data layout
        - ...
    * src/librustc_back/target/x86_64_unknown_haiku.rs
        - LLVM target
        - target endian
        - data layout
        - ...
    *  src/librustc_back/target/mod.rs
        - 加入 ``haiku_base``
        - target 對應
    * src/librustc_data_structures/flock.rs
    * src/libstd/env.rs
        - 設定 ``FAMILY`` 、 ``OS`` 、 ``DLL_SUFFIX`` 、 ``EXE_SUFFIX`` 等
    * src/libstd/os/haiku/fs.rs
        - 設定對應的檔案系統 metadata
    * src/libstd/os/haiku/raw.rs
        - 設定對應的 Type 大小
    * src/libstd/os/mod.rs
        - 加入 ``haiku``
    * src/libstd/rtdeps.rs
        - 設定平台的 runtime dependencies 的 link attributes
    * src/libstd/sys/common/args.rs
        - 開啟 CLI 參數的 storage 的相關支援
    * src/libstd/sys/common/net.rs
        - 設定網路相關資訊
    * src/libstd/sys/unix/fd.rs
        - 設定 File Descriptor 相關資訊
    * src/libstd/sys/unix/fs.rs
        - 設定檔案系統相關資訊
    * src/libstd/sys/unix/os.rs
        - 設定作業系統相關資訊
    * src/libstd/sys/unix/thread.rs
        - 設定 Thread 相關資訊
    * src/libstd/sys/unix/mod.rs
        - 加入 ``os::haiku``
    * src/libsyntax/abi.rs
        - 加入 ``Haiku`` 的 ABI
    * src/libterm/terminfo/searcher.rs
        - 設定 terminfo 位址
    * src/libtest/lib.rs
        - 標示尚未實做的功能
    * src/libunwind/libunwind.rs
        - 設定 unwinding 方式
    * src/test/run-pass/fds-are-cloexec.rs
        - 設定需要忽略的測試
    * src/tools/compiletest/src/util.rs
        - 設定平台的對應


新平台 - NVPTX
------------------------------

* `PTX support <https://github.com/rust-lang/rust/commit/18d49288d546efaceab7c4c6d71a868b575bd00f>`_
* `PTX support, take 2 <https://github.com/rust-lang/rust/pull/38559>`_

1. 新增平台選擇（NVPTX）到 ``src/bootstrap/native.rs``
2. 新增平台 Intrinsics
    * src/etc/platform-intrinsics/nvptx/cuda.json
    * src/etc/platform-intrinsics/nvptx/info.json
    * src/etc/platform-intrinsics/nvptx/sreg.json
3. 新增平台選擇（nvptx）到 ``src/librustc_llvm/build.rs``
4. 新增平台在 LLVM 的 Calling Convension ID 和相關設定
    * src/librustc_llvm/ffi.rs
    * src/librustc_llvm/lib.rs
5. 新增 extern 內可使用的 Intrinsics 的 functions
    * src/librustc_platform_intrinsics/lib.rs
    * src/librustc_platform_intrinsics/nvptx.rs
6. 新增 ABI
    * src/librustc_trans/abi.rs
    * src/librustc_trans/cabi_nvptx.rs
    * src/librustc_trans/cabi_nvptx64.rs
    * src/librustc_trans/lib.rs
    * src/libsyntax/abi.rs
7. 新增相關的 Feature Gate
    * src/libsyntax/feature_gate.rs
    * ``extern "ptx-*" fn()``
8. 新增測試
    * src/test/ui/codemap_tests/unicode.stderr


相關範例：

* `How to: Run Rust code on your NVIDIA GPU <https://github.com/japaric/nvptx>`_
* `Experiments with CUDA and Rust <https://github.com/japaric/cuda>`_



參考
========================================

* `Helping with the Rust Errors <http://www.jonathanturner.org/2016/08/helping-out-with-rust-errors.html>`_
* `Error code list which need to be updated to new format #35233 <https://github.com/rust-lang/rust/issues/35233>`_
* `Rust Internals - New error format <https://internals.rust-lang.org/t/new-error-format/3438/>`_
* `Unstable Feature Gates <https://bot.tinaun.net/rust/featurelist/>`_

* `LLVM tutorial in Rust language <https://github.com/jauhien/iron-kaleidoscope>`_
