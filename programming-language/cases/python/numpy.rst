========================================
Numpy
========================================


.. contents:: 目錄


介紹
========================================

Numpy 是 N 維 array 的 library，
實做眾多相關的操作，
而發展到現在幾乎已經是 Python 科學運算相關的公認 API，
就算發展新一套 library 也常常會跟 Numpy 具有一定程度的 API 相容。


Numpy API 相容的其他 library：

* CuPy: Numpy API for CUDA
		- https://cupy.chainer.org/
* Sparse: Numpy API for sparse arrays
		- https://sparse.pydata.org/
* Dask: Numpy API in parallel for multi-core workstations or distributed clusters
		- https://dask.pydata.org/



Numpy Slicing
========================================

.. code-block:: python

    >>> import numpy as np
    >>> data = np.mat('1 2 3; 4 5 6; 7 8 9')
    >>> data
    matrix([[1, 2, 3],
            [4, 5, 6],
            [7, 8, 9]])


    >>> data[0, 1]      # data[0][1]
    2

    >>> data[:, 0]      # row : all, col : 0
    matrix([[1],
            [4],
            [7]])
    >>> data[1:, 1:]
    matrix([[5, 6],
            [8, 9]])
    >>> data[:, (0, 2)]         # row : all, col : (0, 2)
    matrix([[1, 3],
            [4, 6],
            [7, 9]])
    >>> data[1:-1, 1:-1]
    matrix([[5]])


    >>> data[(0, 2), (1, 1)]    # parameters : rows & cols, access (0, 1) & (2, 1)
    matrix([[2, 8]])
    >>> data[(0, 2),
    ...      (1, 1)]
    matrix([[2, 8]])


    >>> data[[1, 0]]
    matrix([[4, 5, 6],
            [1, 2, 3]])
    >>> data[[[1, 0]]]          # the same
    matrix([[4, 5, 6],
            [1, 2, 3]])
    >>> data[[[[1, 0]]]]
    matrix([[[4, 5, 6],
             [1, 2, 3]]])


    >>> data[((0, 0), (1, 2)),  # rows
    ...      ((2, 1), (2, 1))]  # cols
    matrix([[3, 2],
            [6, 8]])
    >>> data[(((0, 0), (1, 2)), ((0, 0), (1, 2))),
    ...      (((2, 1), (2, 1)), ((2, 1), (2, 1)))]
    matrix([[[3, 2],
            [6, 8]],

            [[3, 2],
            [6, 8]]])


    >>> data[ np.mat('0 0; 1 2') ]
    matrix([[[1, 2, 3],
            [1, 2, 3]],

            [[4, 5, 6],
            [7, 8, 9]]])


Numpy get n smallest data
========================================

``np.partition(a, k)`` 會把 a 的 k-th smallest element 放在 a[k]，
較小的值放在 a[:k]，較大的值放在 a[k+1:]，
index 從 0 開始 (所以第五小的值會在 index 4)

.. code-block:: python

    >>> import numpy as np
    >>> data = np.array([7, 8, 1, 2, 5, 9, 0])
    >>> data
    array([7, 8, 1, 2, 5, 9, 0])

    >>> np.argpartition(data, 0)    # indices
    array([6, 1, 2, 3, 4, 5, 0])
    >>> data[np.argpartition(data, 0)]      # guarantee the smallest value is at index 0
    array([0, 8, 1, 2, 5, 9, 7])
    >>> data[np.argpartition(data, 0)[0]]   # smallest
    0

    >>> np.argpartition(data, 3)    # indices
    array([2, 6, 3, 4, 0, 5, 1])
    >>> data[np.argpartition(data, 3)]      # guarantee the 4th smallest value is at index 3
    array([1, 0, 2, 5, 7, 9, 8])
    >>> data[np.argpartition(data, 3)[3]]   # 4th smallest
    5

    >>> np.partition(data, 3)       # new array, like ``data[np.argpartition(data, 3)]``
    array([1, 0, 2, 5, 7, 9, 8])
    >>> np.partition(data, 3)[3]    # 4th smallest
    5

    >>> data = np.mat('2 3 1; 5 6 4; 8 9 7')
    >>> data
    matrix([[2, 3, 1],
            [5, 6, 4],
            [8, 9, 7]])
    >>> np.partition(data, 0)
    matrix([[1, 3, 2],
            [4, 6, 5],
            [7, 9, 8]])
    >>> np.partition(data, 0)[:, 0]
    matrix([[1],
            [4],
            [7]])

    >>> data = np.array([[7, 8, 1, 2, 5, 9, 0], [7, 8, 1, 2, 5, 9, 0]])
    >>> data
    array([[7, 8, 1, 2, 5, 9, 0],
           [7, 8, 1, 2, 5, 9, 0]])
    >>> np.argpartition(data, 0)
    array([[6, 1, 2, 3, 4, 5, 0],
           [6, 1, 2, 3, 4, 5, 0]])
    >>> data.flat   # flat iterator
    <numpy.flatiter object at 0x13c04b0>
    >>> np.argpartition(data.flat, 0)
    array([ 6,  1,  2,  3,  4,  5,  0,  7,  8,  9, 10, 11, 12, 13])
    >>> data.flat[np.argpartition(data.flat, 0)[0]]
    0
    >>> np.argpartition(data.flat, 1)
    array([ 6, 13,  2,  3,  4,  5,  0,  7,  8,  9, 10, 11, 12,  1])
    >>> data.flat[np.argpartition(data.flat, 1)[1]]
    0


Assign with condition
========================================

.. code-block:: python

    >>> import numpy as np
    >>> data = np.array(range(10))
    >>> data
    array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
    >>> data > 5
    array([False, False, False, False, False, False,  True,  True,  True,  True], dtype=bool)
    >>> data[data > 5]
    array([6, 7, 8, 9])
    >>> data[data > 5] = -1
    >>> data
    array([ 0,  1,  2,  3,  4,  5, -1, -1, -1, -1])


Extend Numpy Array With Values
========================================

.. code-block:: python

    >>> import numpy as np
    >>> data = np.array([range(5)]*3)
    >>> print(data)
    array([[0, 1, 2, 3, 4],
           [0, 1, 2, 3, 4],
           [0, 1, 2, 3, 4]])
    >>> np.pad(data, ((0, 2), (2, 0)), 'constant', constant_values=9)
    array([[9, 9, 0, 1, 2, 3, 4],
           [9, 9, 0, 1, 2, 3, 4],
           [9, 9, 0, 1, 2, 3, 4],
           [9, 9, 9, 9, 9, 9, 9],
           [9, 9, 9, 9, 9, 9, 9]])


Print The Full Numpy Array
========================================

.. code-block:: python

    >>> import numpy
    >>> data = numpy.array(range(10000))
    >>> print(data)
    [   0    1    2 ..., 9997 9998 9999]
    >>> numpy.set_printoptions(threshold=numpy.inf)
    >>> print(data)     # print all the data


Generate An Array By Specific Function And Index
================================================

.. code-block:: python

    >>> import numpy
    >>> numpy.fromfunction(lambda x, y: x+y, (3, 3), dtype=int)
    array([[0, 1, 2],
           [1, 2, 3],
           [2, 3, 4]])
    >>> numpy.fromfunction(lambda x, y: (-1)**(x+y), (3, 3), dtype=int)
    array([[ 1, -1,  1],
           [-1,  1, -1],
           [ 1, -1,  1]])
    #
    # numpy.fromfunction
    #
    #     function accept coordinate
    #     shape of array you want


"Complex" Numpy Array To "Real" Numpy Array
===========================================

.. code-block:: python

    >>> data = np.array([i+i*2j for i in range(10)])
    >>> data
    array([ 0. +0.j,  1. +2.j,  2. +4.j,  3. +6.j,  4. +8.j,  5.+10.j,
            6.+12.j,  7.+14.j,  8.+16.j,  9.+18.j])
    >>> data.real
    array([ 0.,  1.,  2.,  3.,  4.,  5.,  6.,  7.,  8.,  9.])
    >>> data.imag
    array([  0.,   2.,   4.,   6.,   8.,  10.,  12.,  14.,  16.,  18.])


Insert Numpy Array
========================================

.. code-block:: python

    >>> data = np.array([range(5), range(5)])
    >>> data
    array([[0, 1, 2, 3, 4],
           [0, 1, 2, 3, 4]])
    >>> np.insert(data, slice(None, None), 42, axis=1)
    array([[42,  0, 42,  1, 42,  2, 42,  3, 42,  4],
           [42,  0, 42,  1, 42,  2, 42,  3, 42,  4]])


檢查使用狀況
========================================

.. code-block:: python

    >>> a = np.array((range(10),range(10)))
    >>> np.who()
    Name            Shape            Bytes            Type
    ===========================================================

    a               2 x 10           160              int64

    Upper bound on total bytes  =       160


自己為 NumPy Array 加額外 Attributes
========================================

.. code-block:: python

    # 測試資料
    old_array = np.array([1, 2, 3])

    # 客製化 class
    # 用於增加 Attributes
    class MyNumpyClass(np.ndarray):
        myattribute = 42

    # 直接利用原本的 NumPy Array （用同一塊記憶體）
    new_array = MyNumpyClass(old_array.shape,
                             buffer=old_array,
                             dtype=old_array.dtype)

    new_array[0] = 42
    print(new_array)
    print(old_array)    # 發現跟著一起變動

    old_array[1] = 5566
    print(new_array)    # 發現跟著一起變動
    print(old_array)

    print(new_array.myattribute)    # 42

    # 以下可以把 new_array 傳給任何 NumPy 內的 Function 做操作



NumPy Array 轉 Bytes
========================================

.. code-block:: python

    >>> import numpy
    >>> data = numpy.array(range(10))
    >>> data.tobytes()
    b'\x00\x00\x00\x00\x00\x00\x00\x00\x01\x00\x00\x00\x00\x00\x00\x00\x02\x00\x00\x00\x00\x00\x00\x00\x03\x00\x00\x00\x00\x00\x00\x00\x04\x00\x00\x00\x00\x00\x00\x00\x05\x00\x00\x00\x00\x00\x00\x00\x06\x00\x00\x00\x00\x00\x00\x00\x07\x00\x00\x00\x00\x00\x00\x00\x08\x00\x00\x00\x00\x00\x00\x00\t\x00\x00\x00\x00\x00\x00\x00'
    >>> data.view('S8')   # S 表示 bytestring
    array([b'', b'\x01', b'\x02', b'\x03', b'\x04', b'\x05', b'\x06', b'\x07',
           b'\x08', b'\t'],
          dtype='|S8')


* `NumPy - Data type objects (dtype) <https://docs.scipy.org/doc/numpy/reference/arrays.dtypes.html>`_



效能
========================================

不管幾維的資料，記憶體內的儲存方式都是一維，
存放順序則會跟 layout 方式有關，
C-order 是 Row Major Order，
會依序先存 row 1 再存 row 2，
Fortran-order 是 Column Major Order，
會依序先存 column 1 再存 column 2。

.. image:: /images/numpy/numpy-order.png


.. code-block:: python

    import numpy as np

    col_major = np.zeros((10,10), order='C')    # C
    row_major = np.zeros((10,10), order='F')    # Fortran


講求效能的話要注意用的 method 是 copy 還是 view 版本。

.. code-block:: python

    > import numpy as np

    > a = np.zeros((200, 200), order='C')
    > b = np.zeros((200, 200), order='F')
    > np.concatenate((a, a), axis=0)
    > np.concatenate((b, b), axis=0)

    # 在 IPython 內
    > %timeit np.concatenate((a, a), axis=0)
    10000 loops, best of 3: 20.8 µs per loop

    > %timeit np.concatenate((b, b), axis=0)
    10000 loops, best of 3: 32.8 µs per loop


    > %timeit np.vstack((a, a))
    10000 loops, best of 3: 23.8 µs per loop

    > %timeit np.vstack((b, b))
    10000 loops, best of 3: 34.8 µs per loop


    > %timeit a.ravel()     # flat, no copy
    1000000 loops, best of 3: 234 ns per loop   # 1 ns = 0.001 µs

    > %timeit a.flatten()   # flat, copy
    100000 loops, best of 3: 10.3 µs per loop




參考
========================================

* `NumPy Reference <http://docs.scipy.org/doc/numpy-dev/reference/>`_
* `scipy array tip sheet <http://pages.physics.cornell.edu/~myers/teaching/ComputationalMethods/python/arrays.html>`_
* `Wikipedia - NumPy <https://en.wikipedia.org/wiki/NumPy>`_
