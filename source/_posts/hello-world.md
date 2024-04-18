---
title: A good-to-know for PyCharm in M series chipset
---
PyCharm is a powerful tool in Python development for most of the time, but occasionally it stumbles. 
Recently, I encountered a strange issue when using it on a new M2 MacBook: my code ran smoothly in the terminal but but not in 
PyCharm’s run configuration.

For the sake of demo, let's narrow down the issue to the problem area—nothing fancy, 
just a simple 'import numpy' statement: 
![image](..\images\p1-1.jpg)
Running this in a standalone terminal outside PyCharm was fine like this:
![image](..\images\p1-2.png)
Then in PyCharm I make a run configuration:
![image](..\images\p1-3.jpg)
You see there's nothing special: same Python path and no other environment variables.
However, running it leads to this long error:
``` bash
/usr/bin/python3 /Users/wenyuanma/PycharmProjects/pythonProject/main.py
Traceback (most recent call last):
  File "/Library/Python/3.9/site-packages/numpy/core/__init__.py", line 24, in <module>
    from . import multiarray
  File "/Library/Python/3.9/site-packages/numpy/core/multiarray.py", line 10, in <module>
    from . import overrides
  File "/Library/Python/3.9/site-packages/numpy/core/overrides.py", line 8, in <module>
    from numpy.core._multiarray_umath import (
ImportError: dlopen(/Library/Python/3.9/site-packages/numpy/core/_multiarray_umath.cpython-39-darwin.so, 0x0002): tried: '/Library/Python/3.9/site-packages/numpy/core/_multiarray_umath.cpython-39-darwin.so' (mach-o file, but is an incompatible architecture (have 'arm64', need 'x86_64')), '/System/Volumes/Preboot/Cryptexes/OS/Library/Python/3.9/site-packages/numpy/core/_multiarray_umath.cpython-39-darwin.so' (no such file), '/Library/Python/3.9/site-packages/numpy/core/_multiarray_umath.cpython-39-darwin.so' (mach-o file, but is an incompatible architecture (have 'arm64', need 'x86_64'))

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "/Library/Python/3.9/site-packages/numpy/__init__.py", line 130, in <module>
    from numpy.__config__ import show as show_config
  File "/Library/Python/3.9/site-packages/numpy/__config__.py", line 4, in <module>
    from numpy.core._multiarray_umath import (
  File "/Library/Python/3.9/site-packages/numpy/core/__init__.py", line 50, in <module>
    raise ImportError(msg)
ImportError: 

IMPORTANT: PLEASE READ THIS FOR ADVICE ON HOW TO SOLVE THIS ISSUE!

Importing the numpy C-extensions failed. This error can happen for
many reasons, often due to issues with your setup or how NumPy was
installed.

We have compiled some common reasons and troubleshooting tips at:

    https://numpy.org/devdocs/user/troubleshooting-importerror.html

Please note and check the following:

  * The Python version is: Python3.9 from "/Applications/Xcode.app/Contents/Developer/usr/bin/python3"
  * The NumPy version is: "1.26.4"

and make sure that they are the versions you expect.
Please carefully study the documentation linked above for further help.
```

It’s basically asking for numpy package in x86 architecture instead of ARM. 
That is odd considering M2 MacBook is using ARM. And why did it work outside PyCharm?
At that time, I really needed PyCharm to do some debugging, 
so I circumvented it by switching my Python packages (not only numpy) to x86—and it worked!

BTW, the command to install packages for specific architecture is useful:
```commandline
arch -x86_64 pip3 install numpy --compile --no-cache-dir
```

But the mystery lingered and I still needed to figure this out. 
The only possible culprit is PyCharm so I decided to reinstall it. In their download 
page, I came up with a plausible explanation: there is actually a specific 
version of PyCharm tailored for Apple Silicon and I was using the wrong one. 
![image](..\images\p1-4.jpg)

As expected, after installing the correct PyCharm and changing all my packages
to ARM architecture, those import issues vanished.
I verified my guess by using the Intel-version PyCharm to reproduce the error. After 
a deeper dive on the error message, my further guess is that the Intel-version
PyCharm is somehow using the Intel-based `dlopen`, which can't load ARM-based library.

After all it might be just a stupid install mistake, but it's good to know that
always ensure you're using the correct version of PyCharm
for your system's architecture.



