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

> 使用brew安装的pip,pip3安装模块时，不要带`sudo`,不然安装的模块权限是root，不是当前用户，以后安装依赖此模块的新模块会再度需要root权限。

### 4.使用virtualenv
如果想要一个独立的python环境，可以使用virtualenv.它们的python, 包都是独立的，相互不影响。Docker也可以做到同样的事情。

``` bash
# 安装virtualenv
pip install --upgrade virtualenv 

# for Python 2.7, $your_target_directory like tensorflow
# virtualenv --system-site-packages $your_target_directory
# for Python 3.n
virtualenv --system-site-packages -p python3 $your_target_directory 

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

### 7. pipenv
pipenv和npm yarn很像，一个项目一个环境。等于是pip+virtual的功能结合。但是我觉得这样有点冗余，而且对IDE支持不佳，要把项目的路径包含到python的环境中。
pipenv install 会创建一个virtualenv，目录在~/.local/share/virtualenvs/xxxx。当使用pipenv install django后，就会在这个目录下面安装moudle。我觉得如果在本地项目目录就好了，和node一样。否则这样删除项目还要去~/.loda/sahre/virtualenvs/里面再删除一次module。
- 每次需要激活虚拟环境时，运行命令`pipenv shell`，就是调用virtualenv的activate。

http://www.itboth.com/d/aIrM3u/python

### 8. vs code debug时卡在imp.py
在 .vscode/launch.json 中修改 "type":"python" 为 "type":"pythonExperimental"。
[github issue imp.py](https://github.com/Microsoft/vscode-python/issues)
或者把stopOnEntry设置成false
https://stackoverflow.com/questions/45229745/debugging-django-in-vscode-fails-on-imp-py

### 9. python import路径
python中，文件是module, 文件夹是package(必须要__init__.py),所以一般是packageName.moudleName.functionName。
#### 9.1 绝对路径，相对路径
    app/
        __init__.py
        mod.py
        string.py
- 绝对导入，按照sys.path顺序搜索，先主目录（sys.path中第一项''），然后PYTHONPATH环境变量、标准库路径、pth指定路径等。
- 相对导入，在模块所在同一个包内搜索，注意该包目录与主目录的区别。

> 当在app/目录下执行python mod.py时为绝对导入，当在app上层目录执行python -m app.mod时为相对导入。

> 在python3.3中默认只搜索绝对路径，要使用相对导入，执行以下语句：
from . import string or from ..myMoudle import myFunc

#### 9.2 相对路径
相对导入使用模块的__name__属性来决定模块在包结构中的位置。当__name__属性不包含包信息（i.e. 没有用'.'表示的层次结构，比如`__main__`），**则相对导入将模块解析为顶层模块，而不管模块在文件系统中的实际位置。**, 比如python -m unittest test.testIsland,就不会去查找test的上层模块(如src.leetCode)，如果testIsland代码用到了，就会报错（ValueError: attempted relative import beyond top-level package），应该到src的上层目录使用`python -m unittest src.test.testIsland`。

#### 9.3 package
文件夹被python解释器视作package需要满足两个条件：
　　1、文件夹中必须有__init__.py文件，该文件可以为空，但必须存在该文件。
　　2、不能作为顶层模块来执行该文件夹中的py文件（即不能作为主函数的入口）。
> 补充：在"from YY import XX"这样的代码中，无论是XX还是YY，只要被python解释器视作package，就会首先调用该package的__init__.py文件。如果都是package，则调用顺序是YY，XX。

> 另外，练习中“from . import XXX”和“from .. import XXX”中的'.'和'..'，可以等同于linux里的shell中'.'和'..'的作用，表示当前工作目录的package和上一级的package。

https://blog.csdn.net/liberize/article/details/8882659
https://www.cnblogs.com/ArsenalfanInECNU/p/5346751.html


#### 10. brew安装3.6.5
因为TensorFlow版本依赖的python问题，我要使用3.6.5。但是brew安装不了3.6.5,要使用特殊的Formula.
``` bash
brew unlink python # ONLY if you have installed (with brew) another version of python 3
brew install --ignore-dependencies https://raw.githubusercontent.com/Homebrew/homebrew-core/f2a764ef944b1080be64bd88dca9a1d80130c558/Formula/python.rb
```
这个CPU版本的TensorFlow,没有独显的Mac需要这个版本，不然有warning。
https://github.com/lakshayg/tensorflow-build

> 不要直接使用brew upgrade,这个会升级所有的Formula,这个会导致一些版本依赖问题，会自动升级pyton.加上brew pin python保平安。
https://www.jianshu.com/p/c2bdc1997a9d

这个方法已经brew已经不支持了，所以推荐用pyenv

#### 11. pyenv
``` bash
brew install pyenv
brew install pyenv-virtualenv
```

修改.bashrc文件,添加以下内容
``` bash
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
```

#### 1.1 安装python特定版本
pyenv install 3.6.5

#### 1.2 创建一个virtualenv环境
pyenv virtualenv 3.6.5 google_sheet
这个会在$HOME/.pyenv/versions/3.6.5/envs的目录下面，envs里面创建一个virtualenv环境。
``` bash
# 激活环境
pyenv activate google_sheet
# 退出环境
pyenv deactivate google_sheet
```

> 注意同一个versions/3.6.5下面的virtualenv会共享一个python bin目录，但是site-packages第三方包是独立的。