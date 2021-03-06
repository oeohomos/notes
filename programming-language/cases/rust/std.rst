========================================
Rust's std
========================================


.. contents:: 目錄


liballoc
========================================

OOM (Out Of Memory)
------------------------------

Rust 有針對 OOM 供可以客製化的界面，
相關的界面在 ``src/liballoc/oom.rs`` ，
裡面提供的東西很簡單：

* oom 函式
* set_oom_handler 函式

裡面有提供一個靜態變數指向實際上的 OOM Handler，
這個變數可以被 ``set_oom_handler`` 函式更動，
設定的 OOM Handler 會被 ``oom`` 函式取出並呼叫。

這是其中的程式碼：

.. code-block:: rust

    #[cfg(target_has_atomic = "ptr")]
    mod imp {
        use core::mem;
        use core::sync::atomic::{AtomicPtr, Ordering};

        static OOM_HANDLER: AtomicPtr<()> = AtomicPtr::new(super::default_oom_handler as *mut ());

        #[inline(always)]
        pub fn oom() -> ! {
            let value = OOM_HANDLER.load(Ordering::SeqCst);             // 取出 Handler
            let handler: fn() -> ! = unsafe { mem::transmute(value) };  // 轉型
            handler();                                                  // 呼叫
        }

        /// Set a custom handler for out-of-memory conditions
        ///
        /// To avoid recursive OOM failures, it is critical that the OOM handler does
        /// not allocate any memory itself.
        #[unstable(feature = "oom", reason = "not a scrutinized interface",
                   issue = "27700")]
        pub fn set_oom_handler(handler: fn() -> !) {
            OOM_HANDLER.store(handler as *mut (), Ordering::SeqCst);
        }
    }


開放出去的界面：

.. code-block:: rust

    #[cfg(target_has_atomic = "ptr")]
    pub use self::imp::set_oom_handler;

    /// Common out-of-memory routine
    #[cold]
    #[inline(never)]
    #[unstable(feature = "oom", reason = "not a scrutinized interface",
               issue = "27700")]
    pub fn oom() -> ! {
        self::imp::oom()
    }


而預設的 OOM Handler 其實沒做什麼事：

.. code-block:: rust

    use core::intrinsics;

    fn default_oom_handler() -> ! {
        // The default handler can't do much more since we can't assume the presence
        // of libc or any way of printing an error message.
        unsafe { intrinsics::abort() }
    }


另外各平台也可以更改自己預設的 OOM Handler，
Unix-like 的平台可以在 ``src/libstd/sys/unix/mod.rs`` 找到另外的預設 OOM Handler，
這個 OOM Handler 其實也是沒做什麼事，
但是會先印出訊息告知發生 OOM 後才死掉：

.. code-block:: rust

    #[cfg(not(test))]
    pub fn init() {
        use alloc::oom;

        ...

        oom::set_oom_handler(oom_handler);

        // A nicer handler for out-of-memory situations than the default one. This
        // one prints a message to stderr before aborting. It is critical that this
        // code does not allocate any memory since we are in an OOM situation. Any
        // errors are ignored while printing since there's nothing we can do about
        // them and we are about to exit anyways.
        fn oom_handler() -> ! {
            use intrinsics;
            let msg = "fatal runtime error: out of memory\n";
            unsafe {
                libc::write(libc::STDERR_FILENO,
                            msg.as_ptr() as *const libc::c_void,
                            msg.len());
                intrinsics::abort();
            }
        }

        ...
    }


另外 Windows 平台的也有一個，
在 ``src/libstd/sys/windows/mod.rs`` ：

.. code-block:: rust

    #[cfg(not(test))]
    pub fn init() {
        ::alloc::oom::set_oom_handler(oom_handler);

        // See comment in sys/unix/mod.rs
        fn oom_handler() -> ! {
            use intrinsics;
            use ptr;
            let msg = "fatal runtime error: out of memory\n";
            unsafe {
                // WriteFile silently fails if it is passed an invalid handle, so
                // there is no need to check the result of GetStdHandle.
                c::WriteFile(c::GetStdHandle(c::STD_ERROR_HANDLE),
                             msg.as_ptr() as c::LPVOID,
                             msg.len() as c::DWORD,
                             ptr::null_mut(),
                             ptr::null_mut());
                intrinsics::abort();
            }
        }
    }


用 Rust 撰寫的作業系統 Redox 也有，
在 ``src/libstd/sys/redox/mod.rs`` ：

.. code-block:: rust

    #[cfg(not(test))]
    pub fn init() {
        use alloc::oom;

        oom::set_oom_handler(oom_handler);

        // A nicer handler for out-of-memory situations than the default one. This
        // one prints a message to stderr before aborting. It is critical that this
        // code does not allocate any memory since we are in an OOM situation. Any
        // errors are ignored while printing since there's nothing we can do about
        // them and we are about to exit anyways.
        fn oom_handler() -> ! {
            use intrinsics;
            let msg = "fatal runtime error: out of memory\n";
            unsafe {
                let _ = syscall::write(2, msg.as_bytes());
                intrinsics::abort();
            }
        }
    }


總結：

* 在 Rust 中有提供客製化 OOM Handler 的機制
* 預設的 OOM Handler 會印出錯誤訊息後就讓程式終結
