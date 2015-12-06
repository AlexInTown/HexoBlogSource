title: Windows Python 科学计算环境搭建
date: 2015-12-06 21:53:12
tags: 
- python
- windows
categories:
- tools
----
对于小批量的数据预处理，数据分析，数据可视化等任务，利用numpy, pandas, matplotlib 等Python库能够较大地提高编程效率。同时众多的机器学习库，都基于Python作为上层脚本语言，加快了搭建起模型训练和测试的速度。

## 1. 安装 python 2.7.9
下载地址(64bit)：https://www.python.org/ftp/python/2.7.9/python-2.7.9.amd64.msi
注意安装目录下不要有空格（~~D:/Program Files/Python27~~），例如我使用的是D:/Python27的目录。
将Python 的环境加入到Path变量中。在安装python 2.7.9 同时，安装包会自动配置好setuptools 和pip，本文涉及的python包都主要通过pip来安装。个别已编译的包使用exe文件直接安装，注意选用对应64bit的安装包。

## 2. 安装科学计算环境 


### 2.1 安装 VCForPython27
numpy依赖基础的线性代数库，lapack, 这里我们利用pip直接进行下载安装。此种方式numpy在下载后仍需要编译，VCForPython27是为Python27编译提供了较好的支持。利用mingw或cygwin等工具手动编译比较麻烦，容易出错，Windows 下尽量使用VS等编译工具。
http://www.microsoft.com/en-us/download/details.aspx?id=44266

### 2.2 安装 numpy
``` bat
pip install numpy 
```
等待编译成功，编译需要较长时间(Core i5 > 2分钟)；成功后打开python命令行，进行测试
``` bat
import numpy.core.multiarray
```
如果未报错，证明安装成功。继续步骤2.3，否则如果是Win7系统，尝试步骤2.x。

### 2.3 安装 scipy, scikit-learn, pandas, matplotlib
```
pip install scipy
pip install scikit-learn
pip install pandas
pip install matplotlib
```

### 2.x 安装已编译的 科学计算环境（Only  for Win7及之前版本）
如果前面三步成功进行，那么此步骤可以略过。对于Win7 版本的Windows，intel CPU，有提供的已编译numpy，scipy等环境，Win7环境下经测试直接可用。
非官方Windows Python编译二进制安装文件地址：
http://www.lfd.uci.edu/~gohlke/pythonlibs/#numpy
其中numpy，scipy等常用安装包都能找到，但Win8以上版本的Windows下不能正常安装。



## 3. 安装 IPython 交互式环境
IPython 是一个强大的python交互式环境，能够提供较好的代码补全提示功能，特别方便数据分析，也便于程序debug。但IPython在qtconsole运行模式下才能发挥上述作用，这里交代如何配置IPython qtconsole。

### 3.1 安装 PyQt4
先下载PyQt4 环境，否则qtconsole 模式下运行IPython 会报错
下载地址(64bit)：http://jaist.dl.sourceforge.net/project/pyqt/PyQt4/PyQt-4.11.4/PyQt4-4.11.4-gpl-Py2.7-Qt4.8.7-x64.exe

### 3.2 安装配置 qtconsole
安装qtconsole时会自动安装ipython 包，所以只需要在命令行输入
```
pip install qtconsole
```
完成之后，在命令行执行
```
python -m IPython qtconsole
```
就可以看到qt界面下的ipython了。为避免每次弹出一个黑框，我们配置pythonw桌面快捷方式，修改属性，注意
