彻底明白Python package和模块

brakchen
1
2018.12.22 20:53:36
字数 1,552
阅读 10,447
python 是通过module组织代码的，每一个module就是一个python文件，但是modules是通过package来组织的。

如果我们自己写着玩，有的时候就是一两个Python文件在同级目录下，但是当我们开始尝试开发更为复杂的项目的时候，package这个概念的使用就有助于我们组织我们写的一个个modules。

module的概念相对简单，所以不会再多说，主要是说一下package。

Python package
package的定义很简单，在当面目录下有__init__.py文件的目录即为一个package。

但是这会分为两种情况，第一种情况是一个空的__init__.py文件，另外一个情况是写了代码的__init__.py文件。不管是空的还是有内容的，这个目录都会被认为是一个package,这是一个标识。

package的初始化工作
一个package 被导入，不管在什么时候__init__.py的代码都只会被执行一次

>>> import package
hello world
>>> import package
>>> import package
由于 package 被导入时 __init__.py 中的可执行代码会被执行，所以小心在 package 中放置你的代码，尽可能消除它们产生的副作用，比如把代码尽可能的进行封装成函数或类。

__init__.py内的导入顺序
当我尝试导入

from package import something
import语句会首先检查something是不是__init__.py的变量，然后检查是不是subpackage，再检查是不是module，最后抛出ImportError

所以检查顺序如下：

__init__.py 文件内变量
是不是package内的subpackage
是不是package内的module
看个例子
我们有一个如下结构的package


在a.py文件内有一个函数

def bar():
    print("Hello, function 'bar' from module 'a' calling")
在b.py文件内有一个函数

def foo():
    print("Hello, function 'foo' from module 'b' calling")
然后我们添加一个空的__init__.py 文件在simple_package里面。

我们看下，当我们import simple_package的时候到底会发生什么事情(在simple_package内激活Python shell 或者simple_package的的路径被包含在python的sys.path或者在PYTHONPATH的环境变量中)

>>> import simple_package
>>> 
>>> simple_package
<module 'simple_package' from '/home/bernd/Dropbox (Bodenseo)/websites/python-course.eu/examples/simple_package/__init__.py'>
>>> 
>>> simple_package.a
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'a' is not defined
>>> 
>>> simple_package.b
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'b' is not defined
我们可以看到simple_package已经被成功导入，但是a.py和b.py并没有被导入

当然了，如果你希望使用import simple_package后自动加载a或者b 模块，这里有两种办法。

第一种就是在__init__.py内导入a或者b模块，然后保存再激活python的交互环境

#__init__.py
import a
import b
当你再次尝试import simple_package后，就可以使用simple_package.a.bar()来使用模块a中的bar()函数了。

第二办法就是手动导入，当你想使用模块a中的bar()函数时，需要手动导入

import simple_package.a as a
然后就是可以a.bar()来使用bar()函数了。

一个更复杂的例子
这是一个来自官方的例子

文件结构如下

sound
|-- effects
|   |-- echo.py
|   |-- __init__.py
|   |-- reverse.py
|   `-- surround.py
|-- filters
|   |-- equalizer.py
|   |-- __init__.py
|   |-- karaoke.py
|   `-- vocoder.py
|-- formats
|   |-- aiffread.py
|   |-- aiffwrite.py
|   |-- auread.py
|   |-- auwrite.py
|   |-- __init__.py
|   |-- wavread.py
|   `-- wavwrite.py
`-- __init__.py
你可以将这个package的例子下载下来。如果直接使用import sound来导入这个package，我们可以导入package sound，但是sound的子package（effects,filters,formats）并不会被自动导入。子package不会被自动导入的原因是因为在sound目录下的__init__.py文件并没有任何关于导入子package的代码。

我们来看下在sound目录下的__init__.py的代码

"""An empty sound package

This is the sound package, providing hardly anything!"""


print("sound package is getting imported!")

然后我们导入sound试下

>>> import sound
sound package is getting imported!
>>> sound
<module 'sound' from '/home/bernd/packages/sound/__init__.py'>
>>> sound.effects
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: module 'sound' has no attribute 'effects'
如果你想使用子package的内容，但是在父package的__init__.py的文件内并没有导入，你需要手动导入

>>> import sound.effects
effects package is getting imported!
>>> sound.effects
<module 'sound.effects' from '/home/bernd/packages/sound/effects/__init__.py'>
如果你希望python帮你自动导入sound.effects你可以往sound目录下的__init__.py文件写入

"""An empty sound package

This is the sound package, providing hardly anything!"""

import sound.effects
print("sound package is getting imported!")
那么你下次运行的时候python就会自动帮你导入sound.effects

>>> import sound
effects package is getting imported!
sound package is getting imported!
当然了，除了使用绝对路径你可以使用相对路径来导入sound.effects

"""An empty sound package

This is the sound package, providing hardly anything!"""

from . import effects
print("sound package is getting imported!")
这跟linux的命令行比较像，.代表当前目录，..代表上级目录

所以你可以在sound.effects的__init__.py文件内写入

from .. import formats
来导入sound.formats。

当你使用sound的时候就会发现，sound.effects和sound.formats都被导入了

>>> import sound
formats package is getting imported!
effects package is getting imported!
sound package is getting imported!
最后我想给你展示下，怎么从sound.effects导入sound.filters.karaoke模块，将一下代码加入到sound.effects的__init__.py文件中

"""An empty effects package

This is the effects package, providing hardly anything!"""

from .. import formats
from ..filters import karaoke
print("effects package is getting imported!")
激活python的交互环境以后，尝试import sound

>>> import sound
formats package is getting imported!
filters package is getting imported!
Module karaoke.py has been loaded!
effects package is getting imported!
sound package is getting imported!
现在我们可以使用karaoke的函数了

>>> sound.filters.karaoke.func1()
Funktion func1 has been called!
>>> 
把你的整个package都导入进来
还是用前面的例子，这一次，我会额外的加入一个叫做foobar的模块在主目录，你可以在这里下载例子

我们尝试使用*来进行全部的导入

>>> from sound import *
sound package is getting imported!
我们可以看到仅仅是导入了sound这个package但是其他的内容并没有导入。

>>> dir()
['__builtins__', '__doc__', '__loader__', '__name__', '__package__', '__spec__']
__all__
我们可以使用__all__这个魔法变量来手动导入模块和子package，当你定义了__all__到__init__.py文件以后，python会根据你在list内给出的元素进行逐个导入

__all__ = ["formats", "filters", "effects", "foobar"]
所以我们可以再次导入试试

>>> from sound import *
sound package is getting imported!
formats package is getting imported!
filters package is getting imported!
effects package is getting imported!
The module foobar is getting imported
看下dir()

>>> dir()
['__builtins__', '__doc__', '__loader__', '__name__', '__package__', '__spec__', 'effects', 'filters', 'foobar', 'formats']
>>>
你会发现所有模块都已经被顺利导入。

那如果我们仅仅导入sound.effectspackage内所有内容呢，会发生什么，我们import的时候到底import的是什么。

我们看下结果

>>> from sound.effects import *
sound package is getting imported!
effects package is getting imported!
>>> dir()
['__builtins__', '__doc__', '__loader__', '__name__', '__package__', '__spec__']
>>> 
你会发现他仅仅是导入了sound.effects这个package，跟你没有修改sound的__init__.py之前是类似情况，仅仅是导入了这个package。

所以你也可以修改sound.effects的__init__.py文件来导入effects内的所有模块

__all__ = ["echo", "surround", "reverse"]
看下结果

>>> from sound.effects import *
sound package is getting imported!
effects package is getting imported!
Module echo.py has been loaded!
Module surround.py has been loaded!
Module reverse.py has been loaded!
>>> 
>>> dir()
['__builtins__', '__doc__', '__loader__', '__name__', '__package__', '__spec__', 'echo', 'reverse', 'surround']
>>> 
总结
在 from package import * 语句中，如果 __init__.py 中定义了 __all__ 魔法变量，那么在__all__内的所有元素都会被作为模块自动被导入（ImportError任然会出现，如果自动导入的模块不存在的话）。
如果 __init__.py 中没有 __all__ 变量，导出将按照以下规则执行：
此 package 被导入，并且执行 __init__.py 中可被执行的代码
__init__.py 中定义的 variable 被导入
__init__.py 中被显式导入的 module 被导入
reference
