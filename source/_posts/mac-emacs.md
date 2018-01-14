---
title: mac-emacs
date: 2018-01-13 20:29:10
tags:
  - mac
  - emacs
---

### 1. 安装emacs
使用brew
``` bash
brew install emacs --with-cocoa
```

### 2. 配置emacs
原来是用的[prelude](https://github.com/bbatsov/prelude),后来发现太重，反应有点慢。因为我主要是为了python，所以重新配置。参考了几篇文章:
* [Emacs：最好的Python编辑器？](http://codingpy.com/article/emacs-the-best-python-editor/)
注意有几处不同，elpy的ipython配置现在是
``` elisp
(setq python-shell-interpreter "ipython"
      python-shell-interpreter-args "-i --simple-prompt")
```

<!-- more -->

### 2.1 elpy
看elpy的配置，M-X elpy-config，查看python的版本，package的情况。

### 2.2 emacs 临时文件和备份文件
* 临时文件(Auto-Save file)
Emacs的临时文件的文件名是使用“#”包围，这种文件一般是在Emacs中编辑时，emacs自动保存的文件，他在编辑文件的同一个目录内生成一个以#file-name#这样的文件，这个文件会在我们保存文件之后被emacs删除。这个文件的作用是为了防止在进行编辑的时候异常退出造成的损失。

* 备份文件（Backup files）
备份文件是在第一次保存的时候，如果保存的文件已经存在，Emacs会自动将当前已经存在的文件重命名作为备份文件，并将当前buffer的内容写入同名的文件中来替代原来的文件。而备份文件使用的是一个“～”作为后缀(如：file-name~)。

https://www.emacswiki.org/emacs/AutoSave
``` lisp
    (defconst emacs-tmp-dir (expand-file-name (format "emacs%d" (user-uid)) temporary-file-directory))
    (setq backup-directory-alist
        `((".*" . ,emacs-tmp-dir)))
    (setq auto-save-file-name-transforms
        `((".*" ,emacs-tmp-dir t)))
    (setq auto-save-list-file-prefix
        emacs-tmp-dir)
```

[Emacs的临时文件和备份文件](http://blog.useasp.net/archive/2014/07/18/emacs-temporary-files-and-backup-files-for-edited-file.aspx)

#### 3 emacs 快捷键

##### 3.1 C-j 执行lisp
打开一个emacs的缓冲区，如默认的临时缓冲区scratch,输入lisp命令 (xxx),在末尾 C-j就会执行。

##### 3.2 C-c C-c 执行python


### 4. jupyter
jupyter notebook 原来是ipython notebook，因为原来支持ipython,现在的支持语言多了，有了一个kernel的概念，用pip3下载，默认就是python3。

``` bash
pip install jupyter
```

安装成功后配置，先配置jupyter server

#### 4.1 jupyter server 配置文件

先生成一个config.py文件，`jupyter notebook --generate-config`,在~/.jupyter下面就会有一个jupyter_notebook_config.py文件，修改其中的参数。

##### 4.1.1. 修改密码 
`from notebook.auth import passwd; passwd()`,输入一个你想使用的密码，会产生一个长的字符串结果，拷贝这个字符串到c.NotebookApp.password的值中

``` python
c.NotebookApp.password='' #这个不是原始密码，而是靠notebo.auth.passwd生成的。
c.NotebookApp.password_required = True
```

##### 4.1.2 配置端口
c.NotebookApp.port = 7812

##### 4.1.3 ssh
* 生成秘钥
``` bash
openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mykey.key -out mycert.pem
``` 


* 修改jupyter_notebook_config.py
``` python
c.NotebookApp.certfile = u'/absolute/path/to/your/certificate/mycert.pem'
c.NotebookApp.keyfile = u'/absolute/path/to/your/certificate/mykey.key'
# Set ip to '*' to bind on all interfaces (ips) for the public server
c.NotebookApp.ip = '*'
```
我没有使用ssh，因为emacs连接时失败了，所以我关掉了。

[ipython notebook使用教程](http://blog.csdn.net/jerr__y/article/details/53248570)


### 5. emacs intergrate jupyter
记得使用顺序
1. M-x ein: notebooklist-login,输入port,password，成功后进入第二步。
2. M-x ein: notebooklist-open,输入port,成功后会有一个缓冲区对于jupyter notebook服务器消息。
3. 在第二步中的缓冲区中，先选择Kernel，python 3，移动到那个 `( ) python 3`,按下enter键;移动光标到`[New Notebook]`,按下回车键。就会出现ipython的交互窗口，这里就是一个cell,可以输入python,然后按C-c C-c，就会提交到jupyter notebook服务器，然后返回执行结果。

*注意*：Emacs的terminal版本(Emacs -new)不支持matlabplot的图片结果，GUI版本（Emacs)支持,所以先用GUI版本试试。

#### 5.1 ein的快捷键
TODO:

