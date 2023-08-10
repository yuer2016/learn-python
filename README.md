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


```C
/*python 函数结构体定义*/
typedef struct { 
    PyObject_HEAD 
    PyObject *func_code; // PyCodeObject 
    PyObject *func_globals; // 所在模块全局名字空间 
    PyObject *func_defaults;//参数默认值列表 
    PyObject *func_closure;//闭包列表
    PyObject *func_doc; // __doc__ 
    PyObject *func_name; // __name__ 
    PyObject *func_dict; // __dict__ 
    PyObject *func_weakreflist; // 弱引用列表
    PyObject *func_module; // 所在 Module
} PyFunctionObject
```

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

### 列表

***列表*** 是由多个 **值** 组成的 **序列**，在 **列表** 中，值可以是 **任何数据类型**。
**列表** 中的值称为 **元素 (element)** ，有时也被称为 **项 (item)** 。

```python
# 创建新列表的方法有多种；最简单的方法是用方括号 ( [ 和 ] ) 将元素包括起来
[10 , 20 , 30 , 40]
['spam ', 2.0 , 5 , [10 , 20]]

# 列表是可变的；任何整数表达式都可以用作下标；
# 如果下标是负数，它将从列表的末端开始访问列表；
# 读或写一个不存在的元素，将会得到一个索引错误。
numbers = [42 , 123]
numbers [1] = 5
numbers
# => [42 , 5]

# in 运算符
cheeses = ['Cheddar ', 'Edam ', 'Gouda ']
'Edam' in cheeses
 #=> True
'Brie' in cheeses
#=> Flase

#列表遍历
for cheese in cheeses : 
    print(cheese)

for i in range(len(numbers)) :
    numbers[i] = numbers[i] * 2

# 列表操作
# 加号运算符+ 拼接多个列表
a = [1 , 2 , 3]
b = [4 , 5 , 6] 
c = a + b 
#>>> c [1 , 2 , 3 , 4 , 5 , 6]

# 乘号运算符 *
[0] * 4 
#=> [0 , 0 , 0 , 0] 
[1 , 2 , 3] * 3 
#=> [1 , 2 , 3 , 1 , 2 , 3 , 1 , 2 , 3]

# 列表切片
t = ['a', 'b', 'c', 'd', 'e', 'f']
t [1:3]
# => ['b', 'c']
t [:4]
# => ['a', 'b', 'c', 'd']
t [3:] 
# => ['d', 'e', 'f']
t [:] 
# => ['a', 'b', 'c', 'd', 'e', 'f']
t = ['a', 'b', 'c', 'd', 'e', 'f']
t[1:3] = ['x', 'y']
t 
# => ['a', 'x', 'y', 'd', 'e', 'f']

# 列表方法
# append 添加一个新元素到列表的末端
t = ['a', 'b', 'c']
t.append('d')
t 
# => ['a', 'b', 'c', 'd']

# extend 将接受一个列表作为参数，并将其其中的所有元素添加至目标列表中
 t1 = ['a', 'b', 'c'] 
 t2 = ['d', 'e'] 
 t1.extend(t2)
 t1
 #=> ['a', 'b', 'c', 'd', 'e']

# 对列表元素进行求和
def add_all(t) : 
    total = 0 
    for x in t :
        total += x 
    return total

# 删除元素
# 知道元素的下标，你可以使用 pop
t = ['a', 'b', 'c'] 
x = t.pop(1) 
t 
#=> ['a', 'c']
x
#=> 'b'

# 如果不需要被移除的元素，可以使用 del 运算符
t = ['a', 'b', 'c'] 
del t[1] 
t 
#=> ['a', 'c']

# 删除的值 [不知道其下标]，可以使用 remove
t = ['a', 'b', 'c']
t.remove('b') 
t
#=> ['a', 'c']

# 可以使用 list 将一个字符串转换为字符的列表
s = 'spam'
t = list(s)
t
# => ['s', 'p', 'a', 'm']

s = 'spam−spam−spam ' 
delimiter = '−'  
t = s.split(delimiter) 
t 
#=> ['spam ', 'spam ', 'spam ']

t = ['pining', 'for', 'the', 'fjords'] 
delimiter = '_' 
s = delimiter.join(t)
s
#=> 'pining_for_the_fjords'
```

### 字典

**字典** 包含了一个索引的集合，被称为 **键 (keys)** ，和一个 **值 (values)** 的集合。
这种一一对应的关联被称为 **键值对 (key-value pair)** ，有时也被称为 **项 (item)** 。

```python
# dict 函数生成一个不含任何项的新字典
eng2sp = dict() 
eng2sp
# => {}

eng2sp ['one'] = 'uno'
eng2sp
# => {'one ': 'uno '}

eng2sp = {'one ': 'uno', 'two': 'dos', 'three': 'tres'}
# => {'one ': 'uno ', 'three ': 'tres ', 'two ': 'dos '}

eng2sp ['two'] 
#=> 'dos'

# 如果键不存在字典中，会抛出一个异常
eng2sp ['four'] 
# KeyError : 'four'

# len 函数也适用于字典；它返回键值对的个数
len(eng2sp)
# => 3

# in 操作符也适用于字典；它可以用来检验字典中是否存在某个键
'one' in eng2sp 
# => True
'uno' in eng2sp 
# => False

# 字典中是否存在某个值，你可以使用 values 方法，它返回值的集合
vals = eng2sp.values()
'uno' in vals
# => True

def histogram(s): 
    d = dict () 
    for c in s:
        if c not in d: 
            d[c] = 1
        else: 
            d[c] += 1 
  return d

h = histogram('brontosaurus')
h 
# => {'a': 1 , 'b': 1 , 'o': 2 , 'n': 1 , 's': 2 , 'r': 2 , 'u': 2 , 't': 1}

def print_hist(h) : 
    for c in h: 
        print(c , h[c])
        
h = histogram ('parrot ')
print_hist (h)
# => a 1  p 1  r 2  t 1  o 1

for key in sorted(h): 
    print(key , h[key])    
# => a 1 o 1 p 1 r 2 t 1

# 倒转字典的函数
def invert_dict(d): 
    inverse = dict() 
    for key in d:
        val = d[key] 
        if val not in inverse :
            inverse[val] = [key] 
         else: 
            inverse[val].append(key) 
    return inverse

hist = histogram('parrot')
hist
# => {'a': 1 , 'p': 1 , 'r': 2 , 't': 1 , 'o': 1}
inverse = invert_dict(hist)
inverse
# => {1: ['a', 'p', 't', 'o'], 2: ['r']}

# 列表可以作为字典中的值，但是不能是键
```

### 元组

**元组** 是不可变的,**元组** 是一组值的 **序列**。
其中的值可以是 **任意类型**，使用整数索引其位置，因此 **元组** 与 **列表** 非常相似。
不同之处在于 **元组** 的不可变性。

```python
# 元组是用逗号隔开一系列值的列表
t = 'a', 'b', 'c', 'd', 'e'
# 元组通常用括号括起来
t = ('a', 'b', 'c', 'd', 'e')
# 另一个建立元组的方法是使用内建函数 tuple
t = tuple('lupins')
t 
# => ('l', 'u', 'p', 'i', 'n', 's')

t = ('a', 'b', 'c', 'd', 'e')
t[0] 
# => 'a'

t[1:3] 
# => ('b', 'c')
```

### 类
#### 名字空间
类型是类型，实例是实例。

如同 **def** ，关键字 **class** 的作用是创建 **类型对象**。 在整个进程中是**单例** 的，是不被回收的。

```C
typedef struct { 
    PyObject_HEAD
    PyObject *cl_bases;  /* A tuple of class objects */ 
    PyObject *cl_dict;   /* A dictionary */ 
    PyObject *cl_name; /* A string */ 

    PyObject *cl_getattr;
    PyObject *cl_setattr; 
    PyObject *cl_delattr;
} PyClassObject;
```
类型 **class** 存储了所有的 **静态字段和方法**，而实例 **instance** 仅存储 **实例字段**，从基类 **object** 开始，所有继承层次上的实例字段。

```C
typedef struct { 
    PyObject_HEAD
    PyClassObject *in_class;/* The class object */ 
    PyObject *in_dict; /* A dictionary */ 
    PyObject *in_weakreflist; /* List of weak references */
} PyInstanceObject;
```
**类型和实例** 各自拥有自己的 **名字空间**。访问对象成员时，就从这几个名字空间中查找，而非以往的 **globals 和 locals** 。

成员查找顺序：**instance.\_\_dict_\_ -> class.\_\_dict_\_ -> baseclass.\_\_dict_\_**

#### 字段

字段 (**Field**) 和 属性 (**Property**) 是不同的。

* **实例字段** 存储在 **instance.\_\_dict_\_** , 代表单个 **对象实体** 的 **状态**。
* **静态字段** 存储在 **class.\_\_dict_\_** ,为所有同类型实例共享。
* 必须通过 **类型和实例对象** 才能 **访问字段**。
* 以双下划线开头的 **class 和 instance** 成员视为 **私有**，会被重命名。(module 成员不变)

```python

class User(object): 
    table = "t_user" 
    
    def __init__(self, name, age):
        self.name = name
        self.age = age
    
# 实例存储在 instance.__dict__
u1 = User("user1", 20)
u1.__dict__
#==> {'age': 20, 'name': 'user1'}

# 静态字段存储在 class.__dict__
for k, v in User.__dict__.items():
    print("{0:12} = {1}".format(k, v))

# 使用 class.<name> 查找静态成员。
User.table
# 使用 instance.<name> 查找静态成员。
u1.table 
```

**私有字段** 以双下划线开头，无论是静态还是实例成员，都会被 **重命名** 为:  **\_<class>\_\_<name>** 。

```python
class User(object): 
     __table = "t_user" 

     def __init__(self, name, age):
         self.__name = name
         self.__age = age

     def __str__(self): 
        return "{0}: {1}, {2}".format(self.__table, self.__name, self.__age)
```
某些时候，即想使用私有字段，又不想放弃 **外部访问权限**。

* 用重命名后的格式访问。
* 只用一个下划线，仅提醒，不重命名。

#### 属性
**属性(property)** 是由 ***getter setter deleter*** 几个方法构成的逻辑。

```python
class User(object):
     @property
     def name(self): return self.__name
    
    @name.setter 
    def name(self, value): self.__name = value
    
    @name.deleter 
    def name(self): del self.__nam
        
u = User() 
u.name = "Tom"

u.__dict__
# {'_User__name': 'Tom'}
u.name
#  'Tom'
del u.name
u.__dict__
# {}

for k, v in User.__dict__.items(): 
  print ("{0:12} = {1}".format(k, v))
#  __module__ = __main__ 
# __dict__ = <attribute '__dict__' of 'User' objects>
# name = <property object at 0x106ed6100>      
```

从 **class.\_\_dict_\_** 可以看出, 几个属性方法最终变成了  **property object** ，因此我们也可以用 **property()** 方法直接实现属性。

```python
 class User(object):
    def get_name(self): return self.__name
    def set_name(self, value): self.__name = value
    def del_name(self): del self.__name
    name = property(get_name, set_name, del_name, "help...")

u = User()
u.name = "Tom" 
u.__dict__
# {'_User__name': 'Tom'}

```

**属性** 总比同名实例字段 **优先** ，尽可能 **优先使用属性** 而不是直接暴漏内部字段。

#### 方法

**实例方法** 和 **函数** 最大的 **区别** 是 ***self*** 这个 **隐式参数**。

```python
class User(object):
    def print_id(self):
        print(hex(id(self)))

u = User()
u.print_id
#<__main__.User object at 0x10cf58b50>>
u.print_id()
#0x10cf58b50


class User(object):
    def a(): pass 
    
    @staticmethod 
    def b(): pass
    
    @classmethod 
    def c(cls): pass
```

python **对象**，总会有几个 **特殊** 的 **可选方法**。
* \_\_new\_\_ 创建对象实例
* \_\_init\_\_ 初始化对象状态
* \_\_del\_\_ 对象回收前被调用

```python
class User(object): 
    #构造方法 __new__ 可返回任意类型，但不同类型会导致 __init__ 方法不被调用
    def __new__(cls, *args, **kwargs): 
        print("__new__", cls, args, kwargs) 
        return object.__new__(cls)   

    def __init__(self, name, age): 
        print("__init__", name, age)   

    def __del__(self): 
        print("__del__")
```
所有 **方法** 都存储在 ***class.\_\_dict\_\_*** 不可能出现同名主键，所以不支持 **方法重载(overload)** 。

#### 继承
除了所有基类的实例字段都存储在 ***instance.\_\_dict\_\_*** 。

```python
class User(object):
    table = "t_user" 

    def __init__(self, name, age): 
        self._name = name 
        self._age = age  

    def test(self):  
        print(self._name, self._age) 

class Manager(User): 
    table = "t_manager" 

    def __init__(self, name, age, title): 
        #必须显示调用基类初始化方法
        User.__init__(self, name, age) 
        self._title = title 

    def kill(self):
        print("213...")

m = Manager("Tom", 40, "CXO")
m.__dict__
#{'_age': 40, '_title': 'CXO', '_name': 'Tom'}

for k, v in Manager.__dict__.items():    
    print("{0:5} = {1}").format(k, v)

#基类引用存储在 __base__
Manager.__base__
#<class '__main__.User>'

#直接派生类存储在 __subclasses__
User.__subclasses__() 
#[<class '__main__.Manager'>]

#可以用 issubclass() 判断是否继承自某个类型
issubclass(Manager, User)

#用 isinstance() 判断实例对象的基类
isinstance(m,Manager)
```
如果派生类不提供初始化方法，则默认会查找并使用基类的方法。

如果 **派生类** 有一个与**基类实例方法** ***同名*** 的 **静态成员**，那么先找到的是该静态成员，而不是基类的实例方法了。因为 **派生类的命名空间 (namespace) 优先于基类**。

#### 多重继承

```python
class A(object): 
    def __init__(self, a):
        self._a = a 

class B(object): 
    def __init__(self, b):
        self._b = b 
    
class C(A, B):  # 多重继承。基类顺序影响成员搜索顺序
    def __init__(self, a, b): 
        A.__init__(self, a) # 依次调用所有基类初始化方法
        B.__init__(self, b)

C.__bases__
#(<class '__main__.A'>,<class '__main__.B'>)

c = C(1, 2)

# 包含所有基类实例字段
c.__dict__
#{'_b': 2, '_a': 1}
```
**多重继承成员** 搜索顺序，从下到上(**深度优先，从派生类到基类**)，从左到右(基类声明顺序)。

```python
C.mro()
# [<class '__main__.C'>,<class '__main__.A'>,<class '__main__.B'>，<type 'object'>]
```
#### super

***super()*** 起到其他语言 **base** 关键字的作用，它依照 **mro** 顺序搜索基类成员。

#### \_\_bases\_\_

**类型对象** 有两个相似的成员:
* ***\_\_base\_\_*** : 只读 总是返回 \_\_bases\_\_[0]。
* ***\_\_bases\_\_*** : 基类列表，可直接修改来更换基类，影响 mro 顺序。

```python
class A(object): pass 
class B(object): pass 
class C(B): pass

C.__bases__ # 直接基类型元组
# (<class '__main__.B'>)

C.__base__ # __bases__[0]
#<class ''__main__.B>

C.__mro__
#(<class '__main__.C'>,<class '__main__.B'>,<type 'object'>)

C.__bases__ = (A,) # 通过 __bases__ 修改基类

C.__base__ # __base__ 变化
#<class '__main__.A'>

C.__mro__ # mro 变化
# (<class '__main__.C'>,<class '__mian__.A', <type 'object'>>)
```
多继承一样有效，比如调整基类顺序。

#### 抽象类

**抽象类(Abstract Class)** 无法实例化，且派生类必须“完整”实现所有抽象成员才可创建实例。

```python
from abc import ABCMeta, abstractmethod, abstractproperty

class User(object): 
    __metaclass__ = ABCMeta  # 通过元类来控制抽象类型行为
    
    def __init__(self, uid): 
        self._uid = uid
        
    @abstractmethod 
    def print_id(self): pass # 抽象方法
    
    name = abstractproperty() # 抽象属性

class Manager(User): 
    def __init__(self, uid): 
        User.__init__(self, uid) 
        
    def print_id(self):
        print(self._uid, self._name)
        name = property(lambda s: s._name, lambda s, v: setattr(s, "_name", v))

u = User(1) 
# 抽象类无法实例化
# TypeError: Can't instantiate abstract class User with abstract methods name, print_id

m = Manger(1)
m.name = "Tom"
m.print_id()
```

如果派生类也是抽象类，那么可以部分实现或完全不实现基类抽象成员。

```python
class Manager(User): 
    __metaclass__ = ABCMeta
    
   def __init__(self, uid, name):
        User.__init__(self, uid) 
        self.name = name

    uid = property(lambda o: o._uid)
    name = property(lambda o: o._name, lambda o, v: setattr(o, "_name", v)) 
    title = abstractproperty()

class CXO(Manager): 
    def __init__(self, uid, name):
        Manager.__init__(self, uid, name)
        
    def print_id(self): 
        print(self.uid, self.name, self.title)
        
    title = property(lambda s: "CXO")

c = CXO(1, "Tom") 
c.print_id() 
# 1 Tom CXO
```

#### 开放类

**Open Class** 几乎是所有动态语言的标配，也是精华所在，即便是在 **运行期** 也能随意 **改动对象** , **增加或删除** 成员。

```python

class User(object): pass 
 
def print_id(self): print(hex(id(self))) 
 
u = User()
 
u.print_id = print_id # 添加到 instance.__dict__ 
 
u.__dict__ 
 # {'print_id': <function print_id at 0x10c88e320>} 
 
u.print_id() 
 # 失败不是 bound method TypeError: print_id() takes exactly 1 argument (0 given) 
 
u.print_id(u) 
 # 仅当做一个普通函数字段来用 0x10c91c0d0
 
User.__dict__["print_id"] = print_id 
# dictproxy 显然是只读的
# TypeError: 'dictproxy' object does not support item assignment

User.print_id = print_id 
# 同 setattr(User, "print_id", print_id)

User.__dict__["print_id"]
# <function print_id at 0x10c88e320>

u = User()
u.print_id
# <bound method User.print_id of <__main__.User object at 0x10c91c090>>

u.print_id()
# 0x10c91c090
```

静态方法必须用 **装饰器** ***staticmethod*** 和 ***classmethod*** 包装一下，否则会被当作 **实例方法** 。

```python
def mstatic(): print("static method")

# 使用装饰器包装
User.mstatic = staticmethod(mstatic) 

# 正常的静态方法
User.mstatic 
# <function mstatic at 0x10c88e398>

User.mstatic()
# static method

# 注意 classmethod 和 staticmethod 的区别
def cstatic(cls): 
    print("class method:", cls)

User.cstatic = classmethod(cstatic)

User.cstatic
# <bound method type.cstatic of <class '__main__.User> >

User.cstatic()
#  class method: <class '__main__.User'>
```