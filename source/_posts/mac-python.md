---
title: mac-python
date: 2017-12-13 09:53:59
tags:
  - mac
  - python
---

### 1.Mac OS自带python
MAC OS会自带python, 通常我们不需要使用和修改它，我们自己安装python,同时保留系统python。设置方法就是$PATH中/usr/local/bin/优先级高于/usr/bin/,优先使用local用户的pyton。

> 系统python路径
/System/Library/Frameworks/Python.framework/Versions/2.7/bin

注意：系统自带的python没有pip，我们也不需要安装pip到系统自带的python中。

<!-- more -->

### 2.使用brew安装python
brew 安装pyton2.7 python3都是相互独立的，直接brew install python2 或者brew intall python3.
> python 2.7路径
/usr/local/Cellar/python

> python 3路径
/usr/local/Cellar/python3

各自版本的pip也是在各自的bin中

### 3.注意事项
用brew安装python，虽然python,pip是独立的，但是使用pip安装包后，包的路径不在python路径下，而是在/usr/local/lib下

> python 2.7 的包
/usr/local/lib/python2.7

> python 3的包
/usr/local/lib/python3.6 #按版本号区分

理论上它们是共享的，多个python 2.7版本会共享一个目录，python 3也同理。

解决方法，设置pip的指定包安装目录,同时需要把此目录添加到PYTONPATH。我不推荐此解决方法，因为需要设置PYTHONPATH，我推荐使用virtualenv或者Docker。
> 用户目录下面，.pip目录下建立pip.conf文件。
``` 
[install]
install-option=--prefix=~/.local
```

然后pip的库就安装到用户目录下的.local下面了
[ref](http://jyd.me/linux/pip-install-to-different-home/)



### 4.使用virtualenv
如果想要一个独立的python环境，可以使用virtualenv.它们的python, 包都是独立的，相互不影响。Docker也可以做到同样的事情。

``` bash
# 安装virtualenv
pip install --upgrade virtualenv 

# for Python 2.7, $your_target_directory like tensorflow
virtualenv --system-site-packages $your_target_directory
# for Python 3.n
# virtualenv --system-site-packages -p python3 $your_target_directory 

# go into $your_target_directory 
cd $your_target_directory

# active virtualenv
source ~/$your_target_directory /bin/activate

# install tensroflow
pip install --upgrade tensorflow  # for Python 2.7

# deactive virtualenv
deactivate

```

[ref](https://www.tensorflow.org/install/install_mac)

### 5.visual code 中设置交互命令
在 "name": "Python: Terminal (external)"下，设置cwd的值为工作目录
"cwd": "${workspaceFolder}",

### 6.python的调试工具
#### 6.1 pdb,ipdb
默认是pdb,增强版ipdb，使用
``` python
pip install ipdb
python -m ipdb test.py
#python -m pdb test.py
```

#### 6.2 pudb
如果需要GUI,可以使用[pudb](https://pypi.python.org/pypi/pudb)
``` python 
pip install pudb
python -m pudb.run test.py
```

### 6.3 crash_on_ipy

create `crash_on_ipy.py` :
``` python
import sys
creat 
class ExceptionHook:
    instance = None

    def __call__(self, *args, **kwargs):
        if self.instance is None:
            from IPython.core import ultratb
            self.instance = ultratb.FormattedTB(mode='Plain',
                 color_scheme='Linux', call_pdb=1)
        return self.instance(*args, **kwargs)

sys.excepthook = ExceptionHook()
```
链接：https://www.zhihu.com/question/21572891/answer/26046582

如果要调试某个文件，import crash_on_ipy,只要有异常，就会自动进入ipdb


