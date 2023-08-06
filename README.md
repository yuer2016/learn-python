# Python 语言

## Python 的执行方式

一个 python 文件通常有两种使用方法：
* 第一是作为脚本直接执行;
* 第二是 import 到其他的 python 脚本中被调用(模块重用)执行;

```python
'''
在 if __name__ == 'main': 下的代码只有在作为脚本直接执行才会被执行。
'''
if __name__ == '__main__':
     # 下面的代码只有在文件作为脚本直接执行才会被执行   
     print('executor python script')   
```


## Pip 依赖管理

| 命令                     | 说明                                   |
| ------------------------ | -------------------------------------- |
| pip install <package>    | 在线安装包                             |
| pip install -U <package> | 升级包                                 |
| pip install <file/url>   | 安装文件包                             |
| pip uninstall            | 删除包                                 |
| pip search               | 搜索包                                 |
| pip list                 | 显示已安装包列表                       |
| pip list -o              | 显示过期可更新包                       |
| pip list -u              | 显示已经是最新版本的包                 |
| pip freeze               | 以 requirements 格式实现的已安装包列表 |
| pip show <package>       | 显示安装包的信息                       |
| pip wheel                | 生成 wheel 格式的安装包                |

### 国内镜像源

```bash
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple [package-name]
```

### Pip requirement.txt
主要使用目的：任何应用程序通常需要设置安装所需并依赖一组类库来满足工作要求。
requirement.txt 文件是指定和一次性安装包的依赖项具体一整套方法。

```shell
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple -r requirements.txt
```

使用 pipreqs 用于生成 requirements.txt 文件可以根据需要导入的任何项目

```shell
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple pipreqs
pipreqs .  --encoding=utf-8 --force
```

## 基础概念
Python 是一种 **半运行半解释** 的运行时环境。

它会在 "模块" 载入时，将 **源码** 编译成 **字节码(Byte Code)** ,而后这些 **字节码** 会被 **虚拟机** 在一个巨大核心函数里 **解释执行**。

当 Python 虚拟机开始运行时，它通过 **初始化函数** 完成整个 **运行环境设置**:
1. 创建 **解释器和主线程** ***状态对象***，这是整个进程的 **根对象**。
2. 初始化 **内置类型**。***数字、列表*** 等类型都有专门的缓存策略需要处理。
3. 创建 **\_\_builtin\_\_** 模块，该模块持有 **所有内置类型和函数**。
4. 创建 **\_\_sys模块\_\_** ，其中包含了 ***sys.path、modules*** 等 **重要** 的 **运行期信息**。
5. 初始化 **import 机制**。
6. 初始化 **内置 Exception**。
7. 创建 **main模块**，准备运行所需的 **namespace[命名空间]** 。
8. 通过 ***site.py*** 将 ***site-packages*** 中的 **第三方扩展库** 添加到 **搜索路径列表**。
9. 执行 **入口 py 文件**。执行前会将 **\_main\_\_dict\_** 作为 **namespace[命名空间]** 传递进去。
10. 程序执行结束。
11. 执行清理操作，包括调用退出函数，C 清理现场，释放所有模块等。
12. 终止进程。

### 一切皆对象
先有 **类型(Type)** ，而后才能生成 **实例(Instace)** 。
Python 中一切皆是 **对象**，包括类型在内的 **每个对象** 都包括一个标准的 **对象头**，通过 **头部信息** 就可以明确知道其 **具体类型**。

**头部信息** 由 **"引用计数" 和 "类型指针"** 组成:
* **引用计数** 在 **对象被引用** 时增加，超出作用域或手动释放后减少。
* **类型指针** 指向具体的 **类型对象**，其中包含了 **继承关系，静态成员** 等信息。所有 **内置的类型对象** 都能从 ***types 模块*** 中找到。

### namespace [命名空间]
**namespace [命名空间]** 是 Python 中 **最核心** 的概念之一。

和编译语言中，变量名是 **内存地址** 的别名不同，Python 中变量名其实是一个 **字符串对象**，它和所 **指向** 的 **目标对象** 一起在 ***namespace*** 中构成一项 ***{name: object}*** 关联。

**命名空间** 本质上就是一个 **字典(Dict)** ; 

Python 有多种 **命名空间**，比如称为 ***globals*** 的模块 **命名空间**，称为 ***locals*** 的函数 **堆栈帧** 命名空间 ，还有 ***class 、 instance*** **命名空间**。

不同的 **命名空间** 决定了 **对象** 的 **作用域 和 生存周期**。

Python 中 **变量名** 作用仅仅是在某个时刻和 **命名空间** 中的某个对象进行 **关联**。

**变量名** 本身不包含目标对象的 **任何信息**，只有通过 **对象头部** 的 **类型指针** 才能获知其具体类型。进而查找其 **相关成员** 数据。

Python 中没有 **值类型和引用类型** 的区分。即使是 **整数** 也是含有标准头的 **整数** 对象。

```python
"python 虚拟机环境测试"

# class 持有共同行为和共享状态 实例仅保留私有特性。
# python 中存活的实例对象都有 唯一 的 ID 值
print(id(123))
# type 返回实例所属的类型
print(type(1))
# 判断实例是否属于特定类型
print(isinstance(1, int))

class Animal:
    pass
    
class Dog(Animal):
    pass
    
# 判断实例是否属于特定类型
print(issubclass(Dog, Animal))
# 所有类型都有共同的祖先 object
print(issubclass(int, object))
```

```python
# python namespace 是上下文环境中专门用来存储 名字和目标引用关联的容器
print(id(globals()))
print(id(locals()))
print(globals())

def test_namespace():
    x = "hello,test!"
    print(locals())
    print("locals:", id(locals()))
    print("globels:", id(globals()))
    
test_namespace()

globals()["hello"] = "hello,world!"
globals()["number"] = 12345

print(hello)
print(number)
```

```python
# 赋值操作仅是让名字在 namespace 空间重新关联 而非修改原对象，
# python 中没有值类型和引用类型之分，每个对象都很重。
# 即使是简单的数字都有标准的对象头，保存类型指针 和引用计数等信息。
import sys

number = 256
# python3 中 int 也是变长结构
print(sys.getsizeof(number))  

# python 源码必须先编译成字节码(byte code)，才能交由解释器以解释方式执行。
def add(x, y):
    return x + y
    
print(add.__code__)
print(dir(add.__code__))
print(add.__code__.co_varnames)
print(add.__code__.co_code)

import dis
print(dis.dis(add))
```

```python
# 手工完成编译操作
source = """
print(1 + 2)
"""

code = compile(source, "demo", "exec")
print(dis.show_code(code))

# eval 执行单个表达式 exec 应对代码块
exec(code)
```

```python
# 用模板方法拼接字符串
str_tmplete = "/data/{userName}/message/{dateText}.txt"

str_txt = str_tmplete.format(userName="yuer2020", dateText="20220123")

print(str_txt)
```

### 内置类型
按照用途不同,Python 内置类型分为 **数据和程序** 两类。
数据类型:
* 空值: None 
* 数字: bool, int, long, float, complex 
* 序列: str, unicode, list, tuple
* 字典: dict 
* 集合: set, frozense

### 函数

当编译器遇到 def ，会生成创建函数对象指令。
也就是说 def 是 **执行指令** ，而不仅仅是个 **语法关键字**，因此可以在任何地方 **动态创建函数** 对象。

包括函数在内的所有对象都是 **第一类对象** ,可以作为其他函数的参数或者返回值。

* 在命名空间中，名称是唯一主键，因此函数在同一范围内 **不能重载(overload)** 。
* 函数总是由返回值的。即使没有 **return** ,也会返回 None。
* 支持递归调用。但是没有 **尾递归优化**。最大深度为 ***sys.getrecursionlimit()***

**函数** **形参** 和 **内部变量** 都存储在 ***locals*** 命名空间中。
除非使用 ***global*** 和 ***nonlocal*** 特别声明，否则 **函数** 内部使用 **赋值语句** 总是在 **locals 命名空间** 中 **新建** 一个 **关联对象** 。

**赋值** 是指变量名 **指向** 新的对象，**而非** 通过变量名改变 **对象状态**。

**函数变量名** 查找顺序: ***locals -> enclosing function -> globals -> \_\_builtins\_\_***
* **locals** : 函数内部名字空间，包括局部变量和形参。
* **enclosing function** : 外部嵌套函数的命名空间。
* **globals** ：函数定义所在模块的命名空间。
* **\_\_builtins\_\_** ：内置模块的名字空间。

### 模块
Python **模块** 是运行期对象。
**模块** 对应同名 **源码文件**，为成员提供全局 **命名空间**。

**模块对象** 有几个重要 **属性**:
* **\_\_name\_\_** : 模块名 **<package>.<module>**, 在 **sys.modules** 中以此为主键。
* **\_\_file\_\_** : 模块完整文件名
* **\_\_dict\_\_** ：模块 globals 命名空间。

Python 虚拟机按以下顺序 **搜索模块(包)** :
* 当前进程根目录
* **PYTHONPATH 环境变量** 指定的路径列表
* Pyhon 标准库目录列表
* 路径文件(**.pth**) 保存的目录(通常放在 **site-packages 目录** 下)

进程启动后，所有路径都会被组织到 **sys.path** 列表中(顺序可能会被修改)。
任何 **import** 操作都会按照 **sys.path** 列表查找目标模块。
可以用代码往 **sys.path** 添加自定义路径。

Python 虚拟机按以下顺序 **匹配** 目标模块:
* **py 源码** 文件
* **pyc 字节码** 文件
* **egg** 包文件或目录
* **so 、dll 、 pyd** 等扩展文件
* 内置模块
* 其他

要执行程序, 源文件不是必须的。实际上，很多软件发布时都会删掉 **py 文件**，仅保留二进制 **pyc 字节码文件**。
可用 **imp.find_module()** 获取模块的具体文件信息。 

**imp** 另外提供了 **load_source()** 和 **load_compiled()** 几个函数, 可用来载入不在 **sys.path** 搜索路径列表中的模块文件。
优先使用已编译的字节码文件，模块对象会被添加到 **sys.modules** 。
类似于 **reload()** 每次都会新建 **模块对象**。

```python
import iml

imp.find_module("os")
imp.load_source("add", "./test/add.py")
```

将 **多个模块文件** 放到独立目录, 并提供初始化文件 **\_\_init_\_.py** ,就形成了 **包(package)** 。

无论是 **导入包**，还是 **导入包中任何模块或成员**，都会执行 **初始化文件**，且仅执行一次。
可用来初始化包环境，存储帮助、版本等信息。

进程中的 **模块对象** 通常时唯一的。

在首次成功导入后，**模块对象** 被添加到 **sys.modules** ,之后导入操作总是先检查 **模块对象** 是否已经存在。

```python
import sys
import pkgutil, test

#可用以下方式获取当前模块对象
sys.modules[__name__]

#获取包里面的所有模块列表
for _, name, ispkg in pkgutil.iter_modules(test.__path__, test.__name__ + "."): 
  print("name: {0:12}, is_sub_package: {1}".format(name, ispkg))
```

将 **多个模块文件** 放到 **独立目录**，并提供初始化文件 ***\_\_init_\_.py*** , 就形成了 **包[package]** 。
无论是导入包，还是导入包中任何模块或成员，都会执行初始化文件，且仅执行一次。可用来初始化 **包** 环境，存储帮助，版本等信息。

