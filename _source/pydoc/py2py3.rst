python2 和 python3 处理
=====================

windows 下两个版本共存
---------------------
在 windows 下没有 python2 和 python3 可执行程序，python2 和 python3 目录下都只有一个 python.exe。因此为了能够手动区分，可以自己手动将 python2 和 python3 目录下的 python.exe 重命名为 python2.exe 和 python3.exe。然后就能像 *nix 上一样使用 python2 和 python3 分别调用两个版本的 python 。  

同时装了 python2 和 python3，处理 pip 的问题：    
在 python3 的安装包中，存在一个 py.exe。安装之后放在 C:\Windows 下面。这个启动器脚本允许我们指定使用 python2 还是 python3 来运行代码。  

.. code-block:: bash

	py -2 writeInPy2.py
	py -3 writeInPy3.py

以下命令分别调用 python2 和 python3 的 pip：  

.. code-block:: bash

	py -2 -m pip install xxx
	py -3 -m pip install xxx

【参考】
-----------------------------------
1.http://blog.51cto.com/meiling/1863526