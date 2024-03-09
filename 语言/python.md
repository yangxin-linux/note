# 1 基础回顾





## 1-2 安装解释器和pip换源

安装Python解释器背后的故事

~~~
环境变量的设置 可以让 python指令可以在任何路径下执行
~~~



pip换源，提高下载第三方包的速度

~~~python
# pip国内镜像源
阿里云 https://mirrors.aliyun.com/pypi/simple/ 
中国科技大学 https://pypi.mirrors.ustc.edu.cn/simple/ 
豆瓣(douban) http://pypi.douban.com/simple/ 
清华大学 https://pypi.tuna.tsinghua.edu.cn/simple/ 
中国科学技术大学 http://pypi.mirrors.ustc.edu.cn/simple/


# windows换源配置
1 pip3 install pymysql   国外很慢
2 临时配置：pip3 install pymysql -i  地址

# 全局配置
3 配置，以后pip3 install全走配好的源
	-来到C:\Users\Administrator\AppData\Roaming\
    -创建一个pip文件夹
    -新建一个文件pip.ini
    -写入
        [global]
        index-url = http://pypi.douban.com/simple
        [install]
        use-mirrors =true
        mirrors =http://pypi.douban.com/simple/
        trusted-host =pypi.douban.com
~~~







## 1-3 Python解释器多版本共存

一台电脑上可以安装多个不同版本的Python解释器

那如何优雅的管理使用不同版本的Python呢？

关键词：重命名可执行文件 & 环境变量

- 示例

~~~
安装Python2.7
安装后没有自动加环境变量，需要自己加
~~~







## 1-4 虚拟环境

- 使用命令行的方式

~~~
python3 -m venv	虚拟环境路径	# 新建虚拟环境
cd 虚拟环境路径/Script
activate.bat				# 开启虚拟环境
~~~

- 使用Pycharm提供的工具







## 1-5 变量赋值和引用

【变量赋值的本质】

- 每当定义一个新变量时，就会在内存中开辟一块空间用于数据存储。
- 所以不同的变量，他们的内存地址是不同的。使用`id()`获取内存地址，使用`is`判断变量内存地址是否相同。

~~~python
name = "zhangsan"
age = 18

# 如上述两个变量的赋值，就是定义了两个变量，变量名分别是name和age, 
# 本质上分别开辟了两块不同的内存控制，存放字符串"zhangsan"和数字18，
# 当我们访问name或age时，就会在对应的内存空间上获取值。
~~~



【变量引用的本质】

- 把一个变量的内存地址，同时关联到另一个新变量上，于是两个变量对应一块内存地址。

~~~python
a = 100
b = a 

# 此时a和b指向同一块内存空间，即id(a) == id(b)
~~~



通过以下两个示例，可以看出**不可变类型变量和可变类型变量在引用时的区别**。

- 示例1：将a指向另一块新的内存空间，b依然指向自己的内存空间

~~~python
a = 100
b = a

a = 200
print(a, b)		# 200 100
~~~

- 示例2：a和b指向同一块内存空间，只要这块内存空间上的元素发生变化时，关联的变量都受影响。

~~~python
a = [1,2,3,4]
b = a
a[0] = 100
print(a[0], b[0])		# 100, 100

# 像字典、集合都属于可变类型的变量，引用时都存在上述现象
~~~





## 1-6 小整数池

python在内存上有一定的优化，比如小整数池和字符串驻留。

- 【小整数池】：python解释器会在程序开始运行的时候就预先开辟一块内存空间，存放一部分整数。以后程序需要用到一些变量绑定到这些值时，直接绑定到这个内存地址上，不需要再逐个开辟空间(开辟空间访问硬件需要资源消耗)，这是python的一种资源优化机制。范围：-5~256
- 【字符串驻留】：Python的驻留机制对相同的字符串只保留一份拷贝，后续创建相同字符串时，不会开辟新空间，而是把该字符串的地址赋给新创建的变量。范围：英文字母、数字、下划线

~~~python
a = 100
b = 100
print(a is b)		# True

a = 500
b = 500
print(a is b)		# False

a, b = 500, 500
print(a is b)		# True		在同一行定义两个相同值的变量，此时解释器做了优化，执行一个内存地址



a = "abc"
b = "abc"
print(a is b)		# True


a = "abc!"
b = "abc!"
print(a is b)		# False
~~~





## 1-7 垃圾回收机制

变量使用前需要先定义。

定义变量就是在内存中开辟一块空间用于数据储存，如果需要使用很多数据就需要开辟很多内存空间。

内存空间是有限的，如果定义了很多不在使用的变量，会导致内存浪费，甚至内存溢出的问题。

这就需要我们在变量使用后及时释放这个变量所占用的内存空间。

但是这个释放内存空间的工作是复杂且有分险的，幸运的是python解释器帮我们做了这部分工作。

python解释器内存主要使用**引用计数**的方式做垃圾回收。

什么是垃圾：垃圾指的是没有绑定变量名的值，也就是无法在访问的值，即没有用的值。

如何回收垃圾：通过引用计数的方式，每当这个值被引用，即被绑定到一个新的变量名上，引用计数加1

~~~python
x = 10	# x绑定给值10，10的引用计数=1
y = x	# y绑定给值10，10的引用计数=2
z = 10  # z绑定给值10，10的引用计数=3

# 通过赋值可以将变量名绑定到值，将引用计数增加
# 通过del 方法可以将变量名和值解绑，将引用计数减少

del z # z与它所绑定的值解绑，即值10的引用计数减为2
# 注意 del不是不是删除z的意思本质是接触变量名的绑定，内存回收的目的

import sys
sys.getrefcount(a)		# 查看引用计数

def f():
    import sys
    a = 10
    print(sys.getrefcount(a))
    b = a
    print(sys.getrefcount(a))
    c = [a]
    print(sys.getrefcount(a))
    del b
    print(sys.getrefcount(a))
    del c
    print(sys.getrefcount(a))
~~~



python垃圾回收机制，除了引用计数之外，还有【标记回收】、【分代回收】。

~~~python
# 标记清除解决循环引用造成的问题
l1=[]
l2=[]
l1.append(l2)
l2.append(l1) 

del l1, l2

# 分代回收解决引用计数效率低的问题
~~~





## 1-8 迭代器

【可迭代对象（iterable）】指的是可以依次返回其内部成员的对象。比如字符串、列表、字典、文件等都是可迭代对象。

【迭代器（iterator）】内置函数iter(可迭代对象)得到迭代器，本质上迭代器也是可迭代对象。

~~~python
# 可迭代对象, 从语法方面说就是那些具有__iter__的方法的对象
# 迭代器，从语法上说就是那些同时具有__next__和__iter__方法的对象。
	- 迭代器调用__next__方法会调用迭代器中的下一个值；
	- 迭代器调用__iter__方法返回迭代器本身；
~~~

示例：

~~~
>>> s={1,2,3}  # 可迭代对象s
>>> i=iter(s)  # i是迭代器
>>> next(i)    # 返回迭代器中下一个值
1
>>> next(i)
2
>>> next(i)
3
>>> next(i)  #抛出StopIteration的异常，代表无值可取，迭代结束
~~~



迭代器优缺点：

- 优点：惰性取值；每次只取一个数据，不占内存。
- 缺点：只能往后依次取值，不能返回头往前取值。就像象棋中的卒，只进不退。





## 1-9 for循环的本质

思考：python中有while循环，为啥还要有for循环，是不是重复了？

~~~python
ll = [100, 200, 300, 400]

# for
for i in ll:
	print(i)
        
# while
i = 0
while i < len(ll):
    print(ll[i])
    i++
~~~

结论：

- while循环可以做循环遍历操作，但是远远不如for循环简洁实用。
- while循环适合做条件循环，for循环适合做迭代器循环。



【for循环本质】：for循环底层利用了迭代器的原理

~~~python
# while + iterator
goods = ['mac','lenovo','acer','dell','sony']
goods_iterator = iter(goods)
while True:
    try:
    	print(next(goods_iterator))
    except StopIteration:   			# 捕捉异常终止循环
        break
        
        
# for循环
for i in goods:
    print(i)

# for循环底层实现三步：
第一，调用iter()，将goods转化为迭代器goods_iterator；
第二，调用next(goods_iterator)，返回出goods的第一个元素；
第三，循环步骤2，直到迭代器内数据流全部输出，捕获异常。
~~~



 【for循环好处】：**为序列和非序列类型提供了一种统一的迭代取值方式**；





## 1-10 chain

python官方提供了itertools包，方便操作迭代器，这个包提供了chain函数，兼顾内存效率和写法优雅

示例1：基本使用

~~~python
from itertools import chain
a = [1, 2, 3, 4, 5]
b = [6, 7, 8, 9, 10]
c = {'a': 11,'b': 22}

for i in chain(a, b):
  print(i)				# 1, 2, 3, 4, 5, 6, 7, 8, 9, 10

for i in chain(a,c.values()):
  print(i)				# 1, 2, 3, 4, 5, 11, 22

~~~

示例2：去除内嵌列表

~~~python
from itertools import chain
b = [[1, 2], [3, 4], [5, 6]]

for i in chain(*b):
    print(i)			# 1,2,3,4,5,6
    
list(chain(*b))
~~~



# 2 数据结构

## 2-1 基本数据类型总结

- 【按存值个数】

| 标量／原子类型 |        容器类型        |
| :------------: | :--------------------: |
|  数字，字符串  | 列表，元组，字典，集合 |



- 【可变不可变】

|       可变       |       不可变       |
| :--------------: | :----------------: |
| 列表、字典、集合 | 数字，字符串，元组 |

注意：元组中的元素可以是列表，此时修改列表中的值，元组也受影响。



- 【有序无序】

| 直接访问 | 顺序访问（序列类型） | key值访问（映射类型） |
| :------: | :------------------: | :-------------------: |
|   数字   |  字符串，列表，元组  |         字典          |

注意：集合一般看整体而不研究单个元素





## 2-2 深浅拷贝

【什么是拷贝】：原封不动地复制一份新的，在不同的内存地址上，修改旧的不会影响新的。



【变量赋值不是拷贝】

~~~python
list1 = [1, 2, [11, 22]]
list2 = list1				# 变量赋值操作

# 赋值后：id(list1) 等于 id(list2)，说明赋值不是拷贝操作
~~~

![image-20220419175739670](%E7%AC%94%E8%AE%B0.assets/image-20220419175739670.png)

- 变量赋值不是拷贝操作，因为变量名`list1`和变量名`list2`指向的是一块相同的内存地址。
- 赋值操作后，无论是通过`list1`还是`list2`修改列表的元素，都会影响对方。



【浅拷贝】：使用copy()

~~~python
list1 = [1, 2, [11, 22]]
list2 = list1.copy()		# 使用列表的copy方法或使用copy模块中的copy方法，都是浅拷贝

# 浅拷贝后：id(list1) 不等于 id(list2)，说明拷贝了一个新列表。
~~~

![image-20220419180226331](%E7%AC%94%E8%AE%B0.assets/image-20220419180226331.png)

- 执行浅拷贝操作，将在开辟一块新的内存空间，然后将`list2`绑定到这块内存空间上。
- 新内存空间中有三个位置用来存放其三个元素的内存地址。
- 新拷贝的列表的三个位置分别绑定旧列表三个位置上的元素的内存地址。
- 所以修改旧列表的中的字列表时，会影响新列表中的值，因为它们还同步一部分内存空间。



【深拷贝】：使用copy.deepcopy()

~~~python
import copy			# 导入内置copy模块
list1 = [1, 2, [11, 22]]
list2 = copy.deepcopy(list1)	# 调用deepcopy()实现深拷贝
~~~

![image-20220419180757393](%E7%AC%94%E8%AE%B0.assets/image-20220419180757393.png)



- 深拷贝时，当元素是可变数据类型时，会重新开辟一块内存空间，存放这个元素对象的内存地址，且该元素内部的可变数据类型也会再重新开辟内存空间，层层检测，层层拷贝。
- 此时，新旧列表的子元素都不会共享内存地址，所有不会相互影响。



【总结】

- 深浅拷贝讨论的拷贝对象是可变数据类型
- 深浅拷贝的区别在于：是否区分元素的可变还是不可变类型的判断;
- 列表的切片也是浅拷贝，copy模块的`copy.copy()`函数是浅拷贝操作，和列表的内置方法`copy()`功能相同。
- 容器型数据类型的拷贝都存在深浅拷贝问题。
- 深浅拷贝没有有优缺点。





## 2-3 各种推导式

【推导式】也成生成式（comprehensions）是 Python 独有的一种高级特性，它可以使用简单的一行代码实现列表、字典等数据类型的创建或数据类型的转换等任务。

示例：快速生成1到100之间的数字的平方的列表

~~~python
# 普通写法：
nums = []
for i in range(1, 101):
    nums.append(i*i)

# 推导式写法：
nums = [i*i for i in range(1, 101)]
~~~



【列表推导式】

~~~python
# 方式1
nums = [i for i in range(10) if i % 2 == 0]

# 方式2
a = {"name":"jack", "age": 18}
keys = [key for key in a]	
~~~

【字段推导式】

~~~python
# 方式1
nums = {i: i*2 for i in range(5)}

# 方式2
li = [[1,2], (3,4)]
dict(x for x in li)			# {1: 2, 3: 4}
~~~

【集合推导式】

~~~python
# 方式1
b = {i for i in range(1,5)}

# 方式2
a = set(i for i in range(1,5))
~~~

【元组推导式】

~~~python
# 方式1
tuple(x for x in range(1,5))		# (1, 2, 3, 4)

# 数据类型转换
nums = [3,4,5,6]
tuple(x for x in nums)				# (3, 4, 5, 6)

# 元组推导式，按理说应该使用()定义，括号内循环的方式生成元组生成式，
# 但是()被python中的生成器占用，所以只能使用tuple
~~~





## 2-4 namedtuple

- 有名元组（namedtuple）是元组(tuple)的一个子类。它和tuple类似，一旦被定义不能被修改。
- 但是namedtuple比tuple功能更丰富，支持类似对象点的方式做数据访问，且兼容元组的语法。

~~~python
from collections import namedtuple

Point = namedtuple("Point", ["x", "y"])		# 新建一个Point类型，具有x y两个字段
p = Point(11, y=22)			# 实例化p  11赋值给x, 22赋值给y

print(p[0], p[1])
print(p.x, p.y)				# 属性取值
print(p._asdict())			# 返回一个字典：{"x": 11, "y": 22}

d = {"x": 11, "y": 22}
print(Point(**d))		# 通过字典新建

p._replace(x=100)		# 返回一个新namedtuple
~~~





## 2-5 OrderedDict

- python3.6之前字典中插入的key的顺序使不能保证的，
- 此时如果想要保证key是按照插入时的顺序，只能使用OrderedDict

- OrderedDict是dict的子类，兼容dict的方法，且还有扩展方法

~~~python
class OrderedDict(dict):
    'Dictionary that remembers insertion order'
    pass
~~~

示例1：基本使用

~~~python
from collections import OrderedDict

# 初始化时可以传键值对参数
order_dict = OrderedDict(name="jack", age=18)
print(order_dict)

for k, v in order_dict.items():
    print(k, v)

for i in range(5):						# 也可以循环插入键值对
    order_dict[i] = i * 10

for k, v in order_dict.items():
    print(k, v)
~~~

- 示例2：独有方法move_to_end()

~~~python
def move_to_end(self, key, last=True):
    """
    Move an existing element to the end (or beginning if last is false).
    Raise KeyError if the element does not exist.
    """

# 将age这个key移动到最后
order_dict.move_to_end("age")
print(order_dict.keys())
~~~





## 2-6 defaultdict

- defaultdict也是dict类的一个子类
- 它的好处是，可以提供一个初始值，避免出现因为使用不存在的key时造成的KeyError
- defaultdict初始化可以传递参数default_factory（默认是None），当key不存在时返回default_factory的调用结果

示例1：基本使用

~~~python
from collections import defaultdict


dd = defaultdict()		# 因为没有default_factory，所以此时dd和普通dict没有区别

dd = defaultdict(int)	# default_factory为int
d[1] = 100
print(d[1])
print(d[2])		# 因为不存在2这个key，所以返回int的默认值，即0


dd = defaultdict(list)	# default_factory为list
print(d[2])		# 因为不存在2这个key，所以返回list的默认值，即[]
~~~



示例2：default_factory可以为可调用对象

~~~python
from collections import defaultdict


def default_value():
    return 500


dd = defaultdict(default_value)
v1 = dd[1]
print(v1)
~~~





## 2-7 deque

- deque双向队列（是"double-end queue"的简称），类似于list的容器
- deque可以快速的在队列头部和尾部添加、删除元素

示例1：基本使用

~~~python
from collections import deque

dq = deque()

dq.append(100)
dq.append(200)
dq.appendleft(300)		# 从左边追加，即在队列头部添加元素

for i in dq:
    print(i)

print(dq.pop())
print(dq.popleft())		# 从左边弹出元素

dq.rotate(2)            # 把右边2个元素依次放在左边, 默认1，复数则相反方向
~~~



示例2：将可迭代对象变成双向队列

~~~python
from collections import deque

ll = [1, 2, 3, 4, 5]

dq = deque(ll)
print(dq.popleft())
~~~





## 2-8 二分查找

- 最简单的查找算法是遍历，但是遍历的查找效率太低。
- 二分查找（也叫折半查找），是一个常用且简单的查找算法。
- 二分查找原理：选择一个有序列表，确定最左边值和最右边值和中间位置值，比较待查元素和中间位置值。这样每次比较久可以排除一半的查找范围。
- 二分查找前提：有序
- 二分查找特点：速度快



【代码实现】

~~~python
# 需求：实现一个函数，判断一个元素是否在列表中，如果在则返回索引，不存在则返回-1

# 代码实现，假设列表元素升序排列
def binary_search(target, li):
    left = 0
    right = len(li) - 1
    while left <= right:
        mid = (left + right) // 2
        if target == li[mid]:		# 刚好找到
            return mid
        elif target < li[mid]:		# 待查值在候选区的左半部分，调整候选区右边界，
            right = mid - 1
        else:						# 待差值在候选区的右半部分，调整候选区左边界
            left = mid + 1
    else:
        return -1
~~~







## 2-9 冒泡排序

【原理】

~~~
# 列表每两个相邻的数，如果前面的数比后面的大，则交换这两个数。 
# 一趟排序完成后，则无序区减少一个数，有序区增加一个数。 
# 每循环一趟，从无序区冒出来一个最大的数，放入有序区，最终得到一个升序的列表
~~~

![image-20220419211719385](%E7%AC%94%E8%AE%B0.assets/image-20220419211719385.png)



【代码实现】

~~~python
def bubble_sort(li):
    n = len(li)
    for i in range(n):
        for j in range(n - 1):
            if li[j] > li[j+1]:
                li[j+1], li[j] = li[j], li[j+1]
                
# 如果是逆序排序，只需将if判断的中的 '>' 修改为  '<'
~~~

- 上述代码可以优化，感兴趣的同学可以尝试尝试





## 2-10 选择排序

【原理】

~~~
# 循环列表，每次循环找到列表中的最小数，放到一个位置上；
# 再次循环找到剩下列表中的最小数，放在之前找到的最小数下一个位置。
# 最后，实现升序排列
~~~



【代码实现v1】

~~~python
# 循环列表，找到列表中的最小数，将这个最小数从原列表中取出后放入一个空的新列表中

def select_sort_simple(li):
    new_li = []
    for _ in range(len(li)):        # 循环中不实用循环次数的值，故使用占位符 _
        min_val = min(li)           
        new_li.append(min_val)
        li.remove(min_val)         
    return new_li      
~~~

- 上述代码缺点：通过一个新列表实现的，会导致占用额外的内存空间。



【代码实现v2】

~~~python
# 参考冒泡排序的原地交换思路，实现原地交换的选择排序
# 首先默认min_index等于无序区第一个位置，如果后面位置上的数比默认位置上的数小，即重置最小数的位置，
# 这样每趟循环后，min_index都是无序区最小数的位置。
# 将无序区第一个位置的数和min_index上的数交换，实现原地交换


def select_sort(li):
    for i in range(len(li) - 1):    # i表示循环趟数
        min_index = i               # 默认无序区的第一个位置上的元素最小，最为比较的参考对象
        for j in range(i+1, len(li)):    # j比较的起始位置，可以从i+1开始，减少一次和自己比较的循环
            if li[j] < li[min_index]:
                min_index = j               # 记录最小数的位置
        # 一趟循环后，找到无序区最小数的位置，交换无序区第一个位置的数和无序区最小的数
        li[i], li[min_index] = li[min_index], li[i]
~~~

- 优化：多次len操作可以简化为一次操作。





## 2-11 python内置排序算法

- 列表的sort()方法，原地排序
- 内置函数sorted()，复制一份新数据



示例1：基本使用

~~~python
l1 = [5, 2, 8, 1, 4, 5]
l2 = [6, 1, 7, 3, 9, 2]
l1.sort(reverse=True)		# reverse=True降序排

l2 = sorted(l2)
~~~

示例2：高级用法

~~~python
# key指定排序规则
def my_sort(x):
    return x["id"]
ll.sort(key=my_sort)
# 匿名函数
ll = [{"id": 5}, {"id": 1}, {"id": 3}]
ll.sort(key=lambda x: x["id"])


# 按权重排序
# id=3排第一位，id=5排第二位， id=1排第三位
weights = {3: 1, 5: 2, 1: 3}

ll.sort(key=lambda x: weights[x["id"]])
~~~



# 3 函数

## 3-1 类型提示

- Python 3.6后加入了新功能：类型提示，用来声明一个变量的类型
- 通过声明变量的类型，编辑器和一些工具能给你提供更好的支持。

示例1：

~~~python
def get_full_name(first_name, last_name):
    full_name = first_name.title() + " " + last_name.title()
    return full_name


print(get_full_name("john", "doe"))

# 在编写函数get_full_name时，first_name.title()时没有提示和自动补全。
~~~

示例2：

~~~python
# 使用类型提示，类型提示和声明默认值不一样
def get_full_name(first_name: str, last_name: str):
    full_name = first_name.title() + " " + last_name.title()
    return full_name
~~~

示例3：类型校验

~~~python
def get_name_with_age(name: str, age: int):
    name_with_age = name + " is this old: " + str(age)
    return name_with_age
~~~

示例4：返回值做类型提示

~~~python
def get_name_with_age(name: str, age: int) -> str:
    name_with_age = name + " is this old: " + str(age)
    return name_with_age
~~~



- 除了基本可以是使用基本的类型做类型提示，也可以使用复杂的类型提示

~~~python
# 基本类型: int、float、bool、bytes
from typing import List


def process_items(items: List[str]):
    for item in items:
        print(item)
~~~

- 一般使用typing模块做类型提示。







## 3-2 函数是一等公民

- 在编程语言中，一等公民可以作为函数参数，可以作为函数返回值，也可以赋值给变量。

- python中一切且对象，所以函数也是对象，函数也是一等公民。
- 也就是说函数可以作为函数参数，可以作为函数返回值，也可以赋值给变量。

示例1：函数当变量被赋值引用

~~~python
def add(x, y):
    print(x + y)
    
func = add		# func
func(3, 4)		# 等同于 add(x, y); 类似变量的赋值引用
~~~



示例2：函数做容器元素的参数

~~~python
def add(x, y):
    print(x + y)

funcs = [add]

for f in funcs:
    f(1, 1)
~~~

示例3：函数做实参

~~~python
def func(x):
    return x + 1


def add(x, func):
    return func(x)

print(add(3, func))	    # 打印：4
~~~

示例4：函数做返回值

~~~python
def add(x, y):
    return x+y

def foo():
    return add


func = foo()	# 相当于 func = add
func(3, 4)		# x相当于 add(3, 4)
~~~





## 3-3 闭包函数

- 闭包函数：内嵌函数包含对外部函数作用域（而非全局作用域）中变量的引用，那么该’内嵌函数’就是闭包函数，简称闭包(Closures)。
- 正常函数内部变量在函数调用后就被回收了，但是闭包函数的出现，让内部变量可以在函数外部使用。

示例1：

~~~python
def outer():
    x = 10
    def inner():
        return x + 1	# 此时 inner是闭包函数（引用了外层函数作用域的变量）
    return inner


x = 10
def outer():
    def inner():
        return x + 1	# 此时，inner不是闭包函数(没有引用外层函数作用域的变量)
    return inner
~~~

【如何判断闭包函数】

- 方法1：是否引用外层函数作用域中的变量
- 方法2：通过函数的closure属性，查看到闭包函数所包裹的外部变量。不是闭包该值为None

~~~python
func.__closure__
f.__closure__[0].cell_contents
~~~



【闭包函数的用途】

- 为函数体传参。**一次传参，后续使用无须再传参** 。闭包函数的这种特性有时又称为惰性计算。
- 装饰器（重要）

- 示例2：为函数体传参

~~~python
import requests


def outer(url):
    def inner():
        response = requests.get(url)
        print(response.text)
    return inner


baidu = outer("http://www.baidu.com")
baidu()
baidu()
~~~





## 3-4 普通装饰器

问题：统计程序的运行时间

~~~python
import time

def foo():
    time.sleep(2)
~~~

- 方案1

~~~python
import time

def foo():
    start = time.time()
    time.sleep(2)
    end = time.time()
    print(end - start)
    
# 修改了原程序内部代码
# 不符合软件的设计应该遵循开放封闭原则，即对扩展是开放的，而对修改是封闭的。
~~~

- 方案2

~~~python
import time


def foo():
    time.sleep(2)

def foo2():
    start = time.time()
    foo()
    end = time.time()
    print(end - start)
   
 foo2() 
# 缺点：改变了原函数的调用方式，从foo改为了foo2
~~~

- 方案3：闭包函数

~~~python
import time


def foo():
    time.sleep(2)


def outer(func):
    def inner():
        start = time.time()
        func()
        end = time.time()
        print(end - start)

    return inner


foo = outer(foo)     # foo就是就inner
foo()			    # foo()就是inner()
~~~

- 方案4：装饰器，@是语法糖

~~~python
import time


def outer(func):
    def inner():
        start = time.time()
        func()
        end = time.time()
        print(end - start)

    return inner


@outer				# 这样写就相当于 foo = outer(foo)
def foo():
    time.sleep(2)


foo()
~~~



- @语法糖是为了简化定义装饰器的流程。并且这样子看上去更像是foo函数被装饰了一样，更形象。





## 3-5 带参数的装饰器

- 被装饰的函数需要参数

~~~python
import time


def outer(func):
    def inner(*args, **kwargs):
        start = time.time()
        rets = func(*args, **kwargs)
        end = time.time()
        print(end - start)
        return rets

    return inner


@outer
def foo(x, y):
    time.sleep(2)
    return x + y

print(foo(3, 4))
~~~

- 使用装饰器时可以给装饰器传参数

~~~python
import time


def wrapper(flag):
    def outer(func):
        def inner(*args, **kwargs):
            start = time.time()
            rets = func(*args, **kwargs)
            end = time.time()
            if flag == "am":
                print(f'{func}运行用时(am):{end - start}s')
            elif flag == "pm":
                print(f'{func}运行用时(pm):{end - start}s')
            return rets

        return inner
    return outer


@wrapper("am")
def foo(x, y):
    time.sleep(2)
    return x + y


print(foo(3, 4))
~~~

分析：先调用wrapper("am")  返回outer，再看@outer，即普通的装饰器，相当于 foo = outer(foo)





## 3-6 可变长参数的高级用法

- 【可变长位置参数】如果在最后一个形参名前加*号，那么在调用函数时，溢出的位置实参都会被接收，以元组的形式保存下来赋值给该形参。一般使用 *args

~~~python
def foo(x, y, z=1, *args):
    print(x, y, z)
    print(args)
    
foo(1, 2, 3, 4, 5, 6, 7)
# output:
1, 2, 3
(4, 5, 6, 7)
~~~

- 【可变长关键字参数】如果在最后一个形参名前加**号,那么在调用函数时，溢出的关键字参数都会被接收，以字典的形式保存下来赋值给该形参。一般使用  \*\*kwagrs

~~~python
def foo(x, **kwargs):
    print(x)
    print(kwargs)
    
foo(1, y=2, z=3)	# 溢出的关键字实参y=2，z=3都被**接收，以字典的形式保存下来，赋值给kwargs
#output:
1
{'z':3, 'y':2}
~~~



- 【补充 *的用法】  *可以将可迭代元素打散（拆开）

~~~python
def f(x, y):
    print(x + y)

two_nums = [1, 2]

f(*two_nums)
~~~

- 【补充 **的用法】  \*\*可以将字典每个key-value拆开为关键字传参

~~~python
def f(x, y):
    print(x + y)

nums = {"x": 3, "y": 4}

f(**nums)
~~~

- 【限定函数的调用者必须以key=value的形式传值】

~~~python
def register(name, age, *, sex, height): # *后面的参数必须使用关键字传参
     pass
~~~





## 3-7 yield

python中生成器有两种：函数生成器和表达式生成器

- 【函数生成器】若函数体包含yield关键字，再调用函数，并不会执行函数体代码，得到的返回值即生成器对象。
- 生成器对象内置有`__iter__`和`__next__`方法，所以生成器本身就是一个迭代器。可以被for循环遍历。

~~~python
def my_range(start, end, step=1):
    while start < end:
        yield start
        start += step

g = my_range(1, 5)
print(g)
print(next(g))			# 1
print(next(g))			# 2
print(next(g))			# 3
print(next(g))			# 4
print(next(g))			# 抛出异常 StopIteration
~~~

- 普通函数被调用时，会将函数体内代码从上往下依次执行，遇到return语句，立即退出函数并返回返回值；再次调用函数时，函数会从头再执行一遍。
- 函数生成器被调用时，会执行函数体代码，遇到yield语句后，在yield位置出挂起并返回yield后的数据给调用者。当再次被调用时，从上次挂起位置再往下执行函数体代码。

- 生成器函数的使用yiled语句实现的，python的yield表达式也很强大。yield不仅可以从函数体内往外取值，还可以从外部往函数体内传值，传值使用`generator.send(value)`

~~~python
def eater():
    print('Ready to eat')
    while True:
        food = yield
        print('get the food: %s, and start to eat' %food)


g = eater()
next(g)				# 传值前必须先调用一次生成器，让生成器先挂起来，等待接收yield赋值给food
g.send('包子')	   # 通过send方法将数据传给yield赋值给food,生成器内部有循环又会再次被挂起
g.send('牛奶')	   # send（）必须要有一个实参
g.send(None)		# 如果send的是None，则默认执行next(g)
~~~





## 3-8 匿名函数

- 匿名函数就是没有名字的函数，一般用于一次性使用的场景。
- 定义有名字的函数使用关键字`def`，定义没有名字的函数使用关键词`lambda`

示例1：

~~~python
def ff():
    print(123)

mm = lambda: print(123)		# 匿名函数mm

f()
mm()
~~~

示例2：使用lambda的好处是减少冗余代码

~~~python
# use def
def sq(x):
    return x*x

map(sq, [y for y in range(10)])

# use lambda
map(lambda x: x*x,[y for y in range(10)])
~~~

- 配合内置函数一块使用，效果极佳。



## 3-9 内置函数之filter

- filter其实不是函数，是一个类。

- filter() 用于过滤序列，过滤掉不符合条件的元素，返回由符合条件元素组成的新列表或迭代器对象。

- 它接收两个参数，第一个为函数，第二个为序列，序列的每个元素作为参数传递给第一个参数进行判断，然后返回 True 或 False，过滤掉返回False的元素。

~~~
filter(function, iterable)
~~~

示例1：

~~~python
def is_odd(n):
    return n % 2 == 1

# filter返回的是迭代器，需要使用list转化为列表
print(list(filter(is_odd, [1, 2, 3, 4, 5, 6, 7, 8, 9, 10])))
print(list(filter(lambda x: x % 2 == 1, [1, 2, 3, 4, 5, 6, 7, 8, 9, 10])))
~~~



## 3-10 内置函数之enumerate

- enumerate是一个Python内置函数，一个功能强大的内置函数。

示例1：循环一个列表，打印出索引和值

~~~python
names = ["李四", "张三", "王二", "麻子"]

for i in range(len(names)):
    print(i, names[i])
~~~

示例2：

~~~python
names = ["李四", "张三", "王二", "麻子"]

for index, name in enumerate(names):
    print(f'{index}: {name}')
~~~

示例3：

~~~python
names = ["李四", "张三", "王二", "麻子"]
for index, name in enumerate(names, 100):		# 指定index的起始值
    print(f'{index}: {name}')
~~~

示例4：

~~~python
names = {"a": 122, "b": 222, "c": 333}
for index, name in enumerate(names.keys()):
    print(f'{index}: {name}')
~~~



## 3-11 模块的本质

- 一个Python文件就是一个模块。

- 模块是一系列功能体的集合，算是一种代码 "封装" 的方式。

- Python中有三种模块：内置模块、第三方模块、自定义模块

~~~python
# python三类模块：
# 内置模块：python解释器自带的模块，直接使用
# 第三方模块：python社区伙伴们开源提供的python模块，需要下载后使用
# 自定义模块：自定义模块指的是我们自己编写的脚本文件，文件名就是模块名，如 get_sum.py，get_sum就是模块名
~~~

- 使用模块的目的：提高开发效率。
- 模块先导入后使用。导入模块的方式：`import、from、或两者配合使用`

~~~python
# 导入模块的方式
- 方式1：import module_name
- 方式2：from modele_name import model_content1, modele_content2, ...
- 方式3：from model_name import *
- 方式4：import modele_name as nickname

# 导入位置
- 文件头：文件开头导入的模块属于全局作用域
- 函数内：函数内导入的模块属于局部的作用域
~~~

- 导入模块的本质

~~~python
# 1、产生一个新的名称空间（名字是被导入模块名）；
# 2、执行被导入模块源文件代码，将产生的名字存放在新的名称空间中；
# 3、将模块名称空间的名字添加到当前执行文件所的名称空间中

# 补充：
- 导入方式：import ..，在当前执行文件的名称空间中放一个被导入模块的名字，通过这个名字引用模块中的名字。
-导入方式：from .. import..，在当前执行文件的名称空间中直接放一个模块中的名字，不能访问模块的其他名字。 
~~~



~~~python
# calculate.py
def add(x: int, y: int):
    return x + y


def sub(x: int, y: int):
    return x - y


def mul(x: int, y: int):
    return x * y


def div(x: int, y: int):
    return x % y


print("我是calculate")

__all__ = ["add", "sub"]		# 约束导入的变量 from model_name import *
~~~



## 3-12 py文件的两种用途

- 用途1：当脚本被执行
- 用途2：当模块被导入使用

- 两种方式的区别

~~~python
- 脚本文件执行时，产生的名称空间在程序运行结束后失效回收
- 模块导入运行时，产生的名称空间在引用计数为零时回收
~~~

- 每个Py文件都有一个属性：`__name__`

~~~python
# 当py文件被当脚本执行时  __name__ == "__main__"
# 当py文件被当模块导入执行时，__name__ == "模块名"

# calculate.py
def add(x: int, y: int):
    return x + y


if __name__ == '__main__':
    print("我被当做脚本执行了")
else:
    print("我被导入使用了")
~~~



## 3-13 包的本质

- 包是模块的集合。如果把模块比成一个个的python文件，那包就是一个文件夹，里面存放一堆的python文件。

~~~python
# 包是一个包含__init__.py文件的文件夹。package
# 对于普通模块（一个py文件），会发生三件事，其中一件事就是执行模块文件的代码。
# 包是一个文件夹，不能是普通模块那样被执行代码，所以给包提供了一个__init__.py文件，导入包就会执行__init__.py文件，这也是__init__.py文件村的意义。
# python3中，文件夹没有__init__.py也可以，但是在python2中包必须要有该文件。
~~~

示例：

~~~python
- tools
	__init__.py
    calculate.py
- main.py
~~~

- 导入包就是导入包内的`__init__.py`文件

~~~python
#1 包的导入方式和模块的一样：import 和 from...import..两种
	- 无论何种方式，无论任何位置，导入时带点的，点的左边都必须是一个包。
#2 包A和包B下有同名模块也不会冲突，如A.a与B.a来自俩个命名空间
#3 import导入文件时，产生名称空间中的名字来源于文件，import 包，产生的名称空间的名字同样来源于文件，即包下的__init__.py，导入包本质就是在导入该文件
~~~





# 4 面向对象

## 4-1 一切皆对象

- python中一切皆对象
- 自定义的类实例化对象，其实像基本数据类型(int\string\list\dict等)、函数、类都是对象，都可以按照对象的方式来操作。

~~~python
# 自定义Student类 并实例化对象jack
class Student:
    def __init__(self, name, age):
        self.name = name
        self.age = age
 	
    def say_hello(self):
        print(f"My name is {self.name}")
        
        
jack = Student("jack", 18)
print(type(jack))		#	使用type查看变量类型 <class '__main__.Student'>
jack.say_hello()
print(jack.age)



# 普通变量也都是对应类的实例化对象，比如列表变量可以使用 pop()、append()等方法。
ll = []     # 等价于 ll = list()， list就是python 内置类
ll.append(123)


# 函数也是
def foo():
    pass

print(type(foo))		#函数foo是function的实例化对象 <class 'function'>
~~~

- 通过 `type`查看对象类型。
- 通过`dir`查看对象下面有哪些属性。

- 再看看变量的内存地址

~~~python
num1 = 400		
num2 = 500

print(num1 is num2)		# False, 内存地址不一样，开辟两块不同的内存地址
n1 = 100
n2 = 100
print(n1 is n2)			# True, 小整数池优化，通用一块内存地址。

stu1 = Student("jack", 18)
stu2 = Student("jack", 18)
print(stu1 is stu2)		# False, 内存地址不一样，实例化对象都开辟一块新的内存地址。
~~~





## 4-2 面向对象的封装思想

封装思想在python中是随处可见：

- 基本数据类型int|str|bool等功能单一，于是有了容器型数据类型list|tuple|dict|set等
- 独立的代码块使用起来冗余，于是有了函数，便于特定功能代码块的组织管理，重复使用。
- 面向对象：将程序进一步整合，**对象封装了数据属性和方法属性(数据和功能)**，即对象也是"容器"。
- 进一步，许多同类型的对象，是不是也被抽象成了类，类也是"容器"，封装了同类型对象共有的数据和功能。





## 4-3 绑定方法和非绑定方法

- python中，类内定义的函数分为两大类：绑定方法和非绑定方法。
- 绑定方法有两个：绑定给对象的方法、绑定给类的方法；绑定给谁的方法，调用时不需要传第一个参数。
- 非绑定方法：类型普通函数，谁都可以使用，遵循普通函数的传参。目的也是封装在一块。

~~~python
class Student:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def edit_name(self, new_name):
        self.name = new_name

    @classmethod
    def get_student(cls, name, age):
        return cls(name, age)		# 相当于 Student(name, age)

    @staticmethod
    def add(a, b):
        print(a + b)
~~~







## 4-4 类装饰器property

python3中的property就是一个类装饰器，有两个用途：

- **将函数属性伪装成数据属性**
- **统一数据属性的查、改、删操作**



用途1：

比如人的BMI指数是体重健康的一种参数标准。

![image-20220605105932241](%E7%AC%94%E8%AE%B0.assets/image-20220605105932241.png)

它是计算出来的，但是对用户来说BMI指数却更像数据属性，即用户更愿意通过数据属性来访问它。需求就来了。

解决办法：类内部在函数bmi上面加property语法糖，然后对象就可以像访问数据属性那样来使用bmi这个函数属性

~~~python
class Poeple:

    def __init__(self, name, w, h):
        self.__name = name
        self.w = w
        self.h = h

    @property
    def bmi(self):
        return self.w / (self.h ** 2)
    
obj1 = Poeple('jack', 66, 1.75)
print(obj1.bmi)
~~~

用途2：在修改和删除时做逻辑判断

- 当name 遇到查询时，触发被property装饰的函数的执行
- 当name 遇到赋值操作，即 = 时触发被property.setter装饰的函数的执行
- 当name 遇到删除操作，即 del  时触发property.deleter装饰的函数的执行

~~~python
class Poeple:
    def __init__(self, name, w, h):
        self.__name = name
        self.w = w
        self.h = h

    @property
    def name(self):
        return self.__name

    @name.setter
    def name(self, value):
        if type(value) is not str:
            print('必须传入str类型')
            return
        self.__name = value

    @name.deleter
    def name(self):
        print('不能删')


obj1 = Poeple('jack', 66, 1.75)
print(obj1.name)
obj1.name = 'aaa'
del obj1.name
~~~





## 4-5 cached_property

- 将一个类方法转换为特征属性，一次性计算该特征属性的值，然后将其缓存为实例生命周期内的普通属性。
-  类似于对 `property` 但增加了缓存功能。对于不可变的高计算资源消耗的实例特征属性来说该函数非常有用。
- 内置包functools下面的类装饰器cached_property（python3.8）

~~~python
################################################################################
### cached_property() - computed once per instance, cached as attribute
################################################################################


from functools import cached_property


class Student:

    @cached_property
    def add(self):
        # 高计算资源消耗的实例特征属性
        print(111)
        fi = []
        a, b = 1, 1
        fi.append(a)
        for i in range(20 - 1):
            a, b = b, a + b
            fi.append(a)
        return fi


stu = Student()

print(stu.add)
print(stu.add)
print(stu.add)

print("---------------")
stu2 = Student()
print(stu2.add)
~~~

 python官网在3.8版本才加入的cached_property，在此之前有很多第三方包都实现过类似的轮子，看过源码其实可以发现都大同小异。

这些包(包括官方functools.cached_property)一般都不支持asyncio，但pydanny/cached-property 这个包支持asyncio

```
# 源码：pydanny/cached-property
https://github.com/pydanny/cached-property

# 下载
pip3 install cached-property
```





## 4-6 属性查找顺序

属性或方法的查找顺序是面向对象中一个非常重要的知识点，只有掌握熟悉查找的顺序才能理解面向对象代码执行的逻辑。

一个基本的原则：先从对象自身找，有则使用；没有的话，再从类上找，有则使用；没有的话则报错。

示例1：查看对象或类有哪些属性或方法可以使用。

~~~python
class Student:
    school = 'PKU'		# 类属性

    def __init__(self, name, age, gender):
        self.name = name
        self.age = age
        self.gender = gender
        self.course = None

    def get_info(self):		# 绑定给对象的方法
        print('学生信息：名字：%s 年龄：%s 性别：%s' % (
            self.name,
            self.age,
            self.gender
        ))

    def select_course(self, new_class):		# 绑定给对象的方法
        print('正在选课')
        self.course = new_class


print(Student.__dict__)					# 查看类下的属性和方法
print(Student.school)					# .的方式访问类属性/方法。本质是 Student.__dict__["school"]
print(Student.get_info)
print(Student.__dict__["school"])		 # 访问类属性/方法
print(Student.__dict__["get_info"])


stu1 = Student("jack", 18, "男")
print(stu1.name)
print(stu1.get_info)
print(stu1.__dict__)					#  访问对象的属性
stu1.haha = "123"					    # 增加一个数据属性
stu1.add = lambda x: print(x)			 # 增加一个普通函数属性，不是对象的绑定方法哦
~~~



示例2：查找属性的优先级（先对象后父类）

~~~python
...

stu1 = Student("jack", 18, "男")
print(stu1.name)				# 使用对象自己的name
print(stu1.school)				# 对象自己没有，使用父类的school

stu1.school = "THU"				# 等价于：stu1.__dict__["school"] = "THU"
print(stu1.school)				# 对象自己有了，使用对象自己的school

print(stu1.girl)				# 兑现给自己没有，父类也没有，则报错AttributeError
print(Student.girl)			     # 同上 
~~~





## 4-7 隐藏属性

- 如果类的设计者不想某些属性被访问，就可以将该属性给隐藏起来。

- 隐藏属性可以隐藏类中的公有属性和对象的私有属性。

- 具体操作：**使用双下划线开头命名的属性将会被隐藏**

示例1：

~~~python
class Foo:
    __x = 1             # 隐藏类的公有数据属性

    def __init__(self, name, age):
        self.__name = name      # 隐藏对象的私有属性
        self.age = age

    def __f1(self):     # 隐藏对象的私有绑定函数
        print('from test')

    def f2(self):       # f2没有被隐藏，可以被访问到
        print(self.age)


print(Foo.__x)			# 访问不到
print(Foo.__f1)			# 访问不到
print(Foo.f2)			# 可以访问到

obj = Foo('jack', 18)
print(obj.age)			# 可以访问到
print(obj.__name)		# 访问不到

obj.__f1()			   # 访问不到
obj.f2()			   # 可以访问
~~~

示例2：在类外部无法直接访问双下滑线开头的属性，在类内部可以访问到

~~~python
class Foo:
    ...
    
    def __f1(self):     # 隐藏对象的私有绑定函数
        print('from test')

    def f2(self):       		# f2没有被隐藏，可以被访问到
        print(self.age)
        print(self.__name)		
        self.__f1()

        
obj = Foo('jack', 18)
obj.f2()			# 不会报错，可以访问 __name, __f1
~~~

示例3：在类定义阶段，双下划先开头的属性会发生变形

~~~python
# 在类定义阶段，双下划先开头的属性会发生变形，变为 _Foo__x, _Foo__f1, _Foo__name, 所以在在类外无法直接通过过 .__x 的方式访问。
# 但是可以通过变形后的 _Foo__x访问。但这是没有意义的。
# 所以说这种操作并没有严格意义上地限制外部访问，仅仅只是一种语法意义上的变形。
# 类似的，python中开发人员一般通过约定的方式，任务_开头的变量是内部使用的变量，__开头的变量是被保护的变量。
~~~



示例4：只在定义阶段发生形变

~~~python
# 之所以在类内部可以直接通过__x 访问，是因为__开头的属性会在检查类体代码语法时统一发生变形（类定义阶段）
# 这种变形操作只在检查类体语法的时候发生一次，之后再定义的__开头的属性都不会变形，所以可以直接__y访问到

class Foo:
    __x = 1             # 隐藏类的公有数据属性
    ...


Foo.__y = 2				# 增加类Foo的数据属性
print(Foo.__y)			# 可以访问到
~~~



## 4-8 开发接口

定义属性的目的是为了使用，所以隐藏属性的目的不是单纯的隐藏，而是为了更好的使用。

想要这些属性被使用，就必须提供给使用者一些接口，即没有被隐藏属性。

隐藏数据属性：**将数据隐藏起来就限制了类外部对数据的直接操作，然后类内应该提供相应的接口来允许类外部间接地操作数据，接口之上可以附加额外的逻辑来对数据的操作进行严格地控制**。

~~~python
class Student:

    def __init__(self, name):
        self.__name = name

    def get_name(self):
        print(self.__name)  # 通过该接口就可以间接地访问到名字属性
    
    @property
    def name(self):
        return self.__name

    def set_name(self, new_name):
        # 通过改接口判断用户修改的新名字是否合法；非法则修改，不合法就不修改
        if type(new_name) is not str:
            raise TypeError("名字必须是字符串类型")
        self.__name = new_name


stu = Student("jack")
stu.set_name("111")
stu.get_name()
print(stu.name)
~~~





## 4-9 python多继承

继承是面向对象思想的另一个特性。它的存在是为了解决类与类之间代码重复的问题。

示例1：继承减少代码重复，逻辑清晰简洁。

~~~python
# 不使用继承
class Student:
    school = 'PKU'
    def __init__(self, name, age, sex):
        self.name = name
        self.age = age
        self.sex = sex
      
    def select_course(self):
        print(f'学生：{self.name}正在选课。。。')

        
class Teacher:
    school = 'PKU'
    def __init__(self, name, age, sex, level):
        self.name = name
        self.age = age
        self.sex = sex
        self.level = level
    
    def score(self):
        print(f'老师：{self.name}正在打分。。。')
    
    
# 不使用继承
class PkuPeople:
    school = 'PKU'
    def __init__(self, name, age, sex):
        self.name = name
        self.age = age
        self.sex = sex


class Student(PkuPeople):
    def select_course(self):
        print(f'学生：{self.name}正在选课。。。')


class Teacher(PkuPeople):
    def __init__(self, name, age, sex, level):
        super().__init__(name, age, sex)
        self.level = level

    def score(self):
        print(f'老师：{self.name}正在打分。。。')
~~~

示例2：继承的属性查找顺序

~~~python
# 先对象自己，再类，再父类 依次类推

class Animal:
    can_move = True

    def say(self):
        print("animal say something")


class People(Animal):
    def __init__(self, name, age, sex, country):
        self.name = name
        self.age = age
        self.sex = sex
        self.country = country

    def study(self):
        print(f"{self.name} 在认真学习中...")


class Chinese(People):
    def say(self):
        print(f"{self.name} 说他喜欢小猫")


xm = Chinese("小明", 18, "男", "CN")
xm.say()	
xm.study()
print(xm.can_move)
~~~



示例3：多继承（同时继承多个父类）

~~~python
class A():
    def test(self):
        print("from A")

    def foo(self):
        print("foo")


class B():
    def test(self):
        print("from C")

    def bar(self):
        print("bar")


class C(A, B):
    pass

c = C()
c.foo()
c.bar()
c.test()
~~~

- 多继承的优点：同时继承多个父类属性和方法，功能强大。
- 多继承缺点：代码可读性变差。

- 通过类的mro()方法查看多继承的查找顺序。

~~~python
print(C.mro())
# [<class '__main__.C'>, <class '__main__.A'>, <class '__main__.B'>, <class 'object'>]
~~~





## 4-10 深度优先和广度优先

- 继承的菱形结构

- 概念一：经典类和新式类
- 概念二：深度优先和广度优先

![image-20220508171910552](%E7%AC%94%E8%AE%B0.assets/image-20220508171910552.png)

如上图，继承关系成菱形

示例1：经典类和新式类

~~~python
# python2中区分经典类和新式类：
	- 经典类：没有继承object类的子类，以及该子类的子类子子类。。。
	- 新式类：继承了object类的子类，以及该子类的子类子子类。。。
    
# python3中全部默认继承object，所以都是新式类。
	- object类提供了一些常用内置方法的实现，如用来在打印对象时返回字符串的内置方法__str__
    - 通过类的内置属性__bases__可以查看类继承的所有父类
~~~



示例2：深度优先和广度优先

~~~python
# 当多继承关系成菱形结构式，经典类和新式类的属性查找顺序不同
# 经典类按照深度优先的方式，新式类按照广度优先的方式。
~~~

![image-20220508172126646](%E7%AC%94%E8%AE%B0.assets/image-20220508172126646.png)

![image-20220508172148154](%E7%AC%94%E8%AE%B0.assets/image-20220508172148154.png)





## 4-11 Mixin机制

- 多继承的正确打开方式：Mixin（即 `Mix-in`，常被译为“混入”，是一种编程模式）
- Mixins核心机制：就是在多继承的背景下尽可能地提升多继承的可读性，具体体现在命名规范上。

- 当多个类都实现了同一种功能时，这时应该考虑将该功能抽离成 Mixin 类。

~~~python
class Vehicle:  # 交通工具
    pass

class FlyableMixin:
    def fly(self):
        print("I am flying")

class CivilAircraft(Vehicle, FlyableMixin):  # 民航飞机
    pass

class Helicopter(Vehicle, FlyableMixin):  # 直升飞机
    pass

class Car(Vehicle):  # 汽车
    pass
~~~

Minx使用规范

- 首先它必须表示某一种功能，而不是某个物品，python 对于mixin类的命名方式一般以 Mixin, able, ible 为后缀。
- 其次它必须责任单一，如果有多个功能，那就写多个Mixin类，一个类可以继承多个Mixin。
- 它不依赖于子类的实现；子类即便没有继承这个Mixin类，也照样可以工作，就是缺少了某个功能。
- 通常Mixin的类放在子类括号内的右边（表示非核心功能）。



## 4-12 派生和组合

派生，即在原有的基础上发展出新的属性或者方法，在面向对象的继承中尤其突出。

子类可以原封不动的使用父类的属性/方法，也可以重写父类的属性/方法，还可以在使用父类的属性/方法的同时，添加新的内容。

示例1：派生，在使用父类原有方法的基础上，增加新的内容

~~~python
# 方式1：使用只用父类的方法，需要传self参数。
class People:
    def __init__(self, name, age, gender):
        self.name = name
        self.age = age
        self.gender = gender


class Student(People):
    def __init__(self, name, age, gender, code):
        People.__init__(self, name, age, gender)
        self.code = code


stu = Student("jack", 18, "男", 10111)
print(stu.__dict__)


# 方式2：使用super(), 按照MOR列表的顺序往下找
class Student(People):
    def __init__(self, name, age, gender, code):
        super().__init__(name, age, gender)
        self.code = code
~~~



组合：对象的属性是另一个类的对象。

~~~python
class Student:
    def __init__(name, age):
        self.name = name
        self.age = age

class Coursr:
    pass

stu = Student('jack', 18)
course_obj = Course()
stu.course = course_obj

# 继承是一种“是”的关系，比如老师是人、学生是人。
# 组合则是一种“有”的关系，比如老师有生日，老师有多门课程。
~~~





## 4-13 限制子类必须实现方法

父类中定义的方法需要子类必须实现，此时有两种限制方式。

方式1：使用模块abc（即抽象类abstract class的缩写）

~~~python
import abc

# 指定metaclass属性将类设置为抽象类，抽象类本身只是用来约束子类的，抽象类本身不能被实例化
class Animal(metaclass=abc.ABCMeta):
    @abc.abstractmethod 		# 该装饰器限制子类必须定义有一个名为talk的方法
    def talk(self): 			# 抽象方法中无需实现具体的功能
        pass

class Cat(Animal):				# 但凡继承Animal的子类都必须遵循Animal规定的标准
    def talk(self):				# 必须定义talk方法						
        pass

cat=Cat() 				# 若子类中没有一个定义talk的方法则会抛出异常TypeError，无法实例化
~~~



方式2：使用 `NotImplementedError`

~~~python
class Animal:
    def talk(self):
        raise NotImplementedError("该方法必须被实现")
        
# 如果子类没有实现talk方法，子类调用talk时候使用父类的talk,此时直接抛出异常 NotImplementedError
~~~





## 4-14 isinstance

当我们需要获取一个对象的类型是，可以使用`type()`

当我们需要判断一个对象是否是指定类型时可以使用`isinstance`函数快速判断。

~~~python
class People:
    def __init__(self, name):
        self.name = name


p = People("jack")
print(isinstance(p, People))			# 判断p 是否是People类型

a = 10
print(isinstance(a, int))
print(isinstance(a, (str, int)))		# 可以是多个备选类型，以元组的形式做第二个参数
~~~



除此之外，还有一个函数可以判断类与类之间的父子关系，`issubclass`

~~~python
class Animal:
    def __init__(self, name):
        self.name = name


class People(Animal):
    pass


print(issubclass(People, Animal))               # True
print(issubclass(Animal, People))               # False
print(issubclass(People, (list, Animal)))       # True
print(issubclass(People, People))               # True
~~~







## 4-15 反射

反射：在程序运行过程中可以"动态"获取对象的信息。

~~~python
# 比如判断用户输入的一个字符串是不是一个对象的属性, 使用if-elif-else 判断较为繁琐。
class Ftp:
    def get(self):
        pass
    def set(self):
        pass
    def delete(self):
        pass

ftp = Ftp()
cmd = input('请输入指令：').strip()
if cmd == 'get':
    ftp.get()
elif cmd == 'set':
    ftp.set()
elif cmd == "delete":
    ftp.delete()
else:
    print('指令不存在')
~~~



python中使用反射非常方便，仅需要使用4个内置函数

~~~python
hasattr(obj, 'x')	                  # 判断对象是否有一个属性，返回布尔值
getattr(object, name, default=None)	  # 获取一个对象的name属性，如果name属性不存在的返回None
setattr(x, 'y', 'v')	              # 更新对象y属性的值, 等价于 x.y = 'v'，当y不存在的新增
delattr(x, 'y')		                  # 删除对象的一个属性， 等价于 del x.y   属性y不存在则报错
~~~

上面使用条件判断的例子使用反射可以简化如下

~~~python
class Ftp:
    ...

ftp = Ftp()
cmd = input('请输入指令：').strip()
if hasattr(ftp, cmd):
    getattr(ftp, cmd)()
else:
    print('指令不存在')
~~~

反射类的属性

~~~python
class Foo(object):
 
    staticField = "HAHA"
 
    def __init__(self):
        self.name = 'jack'
 
    def func(self):
        return 'func'
 
    @staticmethod
    def bar():
        return 'bar'
 
print getattr(Foo, 'staticField')
print getattr(Foo, 'func')
print getattr(Foo, 'bar')
~~~



反射当前模块成员

~~~python
import sys


def s1():
    print('s1')


def s2():
    print('s2')


this_module = sys.modules[__name__]
print(hasattr(this_module, 's1'))
getattr(this_module, 's2')
~~~



补充：反射的底层原理

~~~python
print(dir(obj))
print(obj.__dict__[dir(obj)[index]])
~~~







## 4-16 单例模式

单例模式是一个软件的设计模式，为了保证一个类，无论调用多少次产生的实例对象，都是指向同一个内存地址，仅仅只有一个实例（**只有一个对象**）。

实现单例模式的手段有很多种，但总的原则是保证一个类只要实例化一个对象，下一次再实例的时候就直接返回这个对象，不再做实例化的操作。所以这里面的**关键一点就是，如何判断这个类是否实例化过一个对象**。

这里介绍两类方式：

- 一类是**通过模块导入**的方式；
- 一类是**通过魔法方法**判断的方式（在第五章介绍）；

```python
# 基本原理：
- 第一类通过模块导入的方式，借用了模块导入时的底层原理实现。
- 当一个模块（py文件）被导入时，首先会执行这个模块的代码，然后将这个模块的名称空间加载到内存。
- 当这个模块第二次再被导入时，不会再执行该文件，而是直接在内存中找。
- 于是，如果第一次导入模块，执行文件源代码时实例化了一个类，那再次导入的时候，就不会再实例化。
```

下面分别介绍这几种不同的实现方式，仅供参考实现思路，不做具体需求。

**通过模块导入**

```python
# cls_singleton.py
class Foo:
    pass

instance = Foo()


# test.py
import cls_singleton

obj1 = cls_singleton.instance
obj2 = cls_singleton.instance
print(obj1 is obj2)

# 原理：模块第二次导入从内存找的机制
```

通过类的绑定方法

```python
class Student:
    _instance = None	# 记录实例化对象

    def __init__(self, name, age):
        self.name = name
        self.age = age

    @classmethod
    def get_singleton(cls, *args, **kwargs):
        if not cls._instance:
            cls._instance = cls(*args, **kwargs)
        return cls._instance

stu1 = Student.get_singleton('jack', 18)
stu2 = Student.get_singleton('jack', 18)
print(stu1 is stu2)

# 原理：类的绑定方法是第二种实例化对象的方式，
# 第一次实例化的对象保存成类的数据属性 _instance，
# 第二次再实例化时，在get_singleton中判断已经有了实例对象，直接返回类的数据属性 _instance
```

> **补充**：这种方式实现的单例模式有一个明显的bug；bug的根源在于如果用户不通过绑定类的方法实例化对象，而是直接通过类名加括号实例化对象，那这样不再是单利模式了。





# 5 魔法方法

## 5-1 init和del

- 魔法方法：python中一切皆对象，因为python是面向对象的编程语言。python给类和对象提供了大量的内置方法，这些内置方法也称魔法方法。这些魔法方法总是在某种条件下自动触发执行，就像魔法一样。

- 魔法方法的格式：双下划线开头，双下划线结尾的函数/方法

- `__init__`又称构造方法，当类实例化对象时，自动执行，给对象初始化属性。

~~~python
class Student:
    def __init__(self, name, age):
        print("init...")
        self.name = name
        self.age = age
        return
        # return 123				# 注意：方法必须返回None，默认不写return语句
~~~



- `__del__` 又称析构方法，当对象被回收时触发执行(程序结束、对象引用计数为零称为垃圾时)

~~~python
class Student:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __del__(self):
        print("del...")


stu1 = Student("jack", 19)
del stu1		# 触发执行 __del__


def foo():
    print("----- 函数内")
    stu2 = Student("jack", 19)
    print(stu2.__dict__)


foo()		# foo函数执行结束，触发执行 __del__
~~~



示例：执行程序，猜一猜打印结果。

~~~python
class Foo:
    def __del__(self):
        print('执行我啦')

print('------->')
~~~





## 5-2 new

- new：`__new__是当类被调用实例化对象时第一个被触发的函数，用来实例化并返回一个空对象`

~~~python
class Student:
    def __init__(self, name, age):
        print("init...")				# 后打印 init
        self.name = name
        self.age = age

    def __new__(cls, *args, **kwargs):
        print("new...")						# 先打印 new
        return super().__new__(cls)		     # 注意：必须返回一个对象


stu1 = Student("jack", 19)
~~~



- new函数在init函数之前被执行，new函数新建一个空对象，然后该空对象交由init函数初始化。
- 实例化对象的背后发生三件事：
  - 1 new函数新建一个空对象并返回；
  - 2 该空对象执行init函数，初始化对象属性；
  - 3  返回初始化完成后的对象，即实例化后的对象。

~~~python
class Student:
    def __init__(self, name, age):
        print("init:", id(self))			# new: 2176264649120
        self.name = name
        self.age = age

    def __new__(cls, *args, **kwargs):
        obj = object.__new__(cls)		    # init: 2176264649120
        print("new:", id(obj))
        return obj


stu1 = Student("jack", 19)
~~~





## 5-3 call

- 当对象加括号被调用时触发执行；类中的__call__在对象被调用时触发。
- 对象或者变量(也是对象)只有实现了call方法，才是可调用对象，猜可以被执行。否则报错`TypeError: 'Student' object is not callable`

~~~python
class Student:
    def __init__(self, name, age):
        self.name = name
        self.age = age
   
    def __call__(self, *args, **kwargs):
        print("call...")


stu = Student("", 10)
stu()


def foo():
    print("foo")

print(foo.__class__)
print(hasattr(foo, "__call__"))
getattr(foo, "__call__")()      # 等价于foo()
~~~





## 5-4 str和repr

- str 当对象被访问打印时触发执行，它必须有一个字符串类型的返回值
- repr，当对象被打印时执行，本质和str一致，一般默认先找str, str没有则使用repr
- 在交互环境中，稍微不一致（其实不重要）

~~~python
class Student:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __str__(self):
        p_dict = {"name": self.name, "age": self.name}
        return str(p_dict)
    
    def __repr__(self):
        return "CLanguage[name="+ self.name +",add=" + self.add +"]"


stu = Student("", 10)
print(stu)		# {'name': '', 'age': ''}


# 补充：object自带__str__方法，默认返回 str(self), 即print(obj) 等价于 print(str(obj))
~~~



- 补充：第三方包objprint，为了更好的打印自定义数据结构，不想重写str或者repr。下载`pip3 install objprint`

~~~python
from objprint import objprint
from objprint import add_objprint

class Student:
    def __init__(self, name, age):
        self.name = name
        self.age = age


stu = Student("jack", 10)
print(stu)
objprint(stu)


@add_objprint
class Player:
    def __init__(self):
        self.name = "jack"
        self.age = 18
        self.items = ["axe", "armor"]
        self.coins = {"gold": 10, "silver": 20, "bronze": 30}
        
 print(Player()) 
~~~







## 5-5 比较系列

两个对象是可以比较的，但是比较的结果可能不是我们预期的，此时可以重写比较系列的魔法方法，实现自定义比较逻辑。

~~~python
class Student:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __eq__(self, other):
        print("执行:__eq__")
        if isinstance(other, Student):
            return self.age == other.age		# 返回布尔值
        return False


stu1 = Student("jack", 18)
stu2 = Student("jane", 18)

print(stu1 == stu2)
print(stu1 == 18)
~~~

- eq，实现的是比较是否相等的逻辑。
- nq，实现的是是够不相等的逻辑。如果没有实现，则默认是eq的结果取反。
- 该系列其他魔法方法：`__lt__、__gt__、__le__、__ge__`分别表示小于、大于、小于等于和大于等于。



## 5-6 attr系列

示例1：在类中实现魔方方法：`__getattr__、__setattr__、__delattr__`

~~~python
class Person:

    def __init__(self, name):
        self.name = name

    def __getattr__(self, item):
        print('调用不存在的属性会触发我')
        return self.__dict__.get(item)
        # return self.item      # 不能使用，会造成递归。

    def __setattr__(self, key, value):
        print('设置修改对象属性时触发我')
        self.__dict__[key] = value

    def __delattr__(self, item):
        print('删除对象属性时触发我')
        self.__dict__.pop(item)


p = Person('jack')      # 触发__setattr__
p.name = 'mack'         # 触发__setattr__
print(p.age)            # 触发__getattr__
p.age = 18              # 触发__setattr__
print(p.age)            
del p.age               # __delattr__
~~~



示例2：字典实现像对象一样的点操作

~~~python
class MyDict(dict):
    abc = "abc"

    def __getattr__(self, item):
        print("字典对象通过点访问不存在的属性时触发")
        return self.get(item)

    def __setattr__(self, key, value):
        if not isinstance(value, str):
            raise ValueError('值必须是字符串类型')
        self[key] = value


d = MyDict({'name': 'jack'})
print(d.name)               # 触发 __getattr__
print(d.abc)                # 没有触发 __getattr__
~~~





## 5-7 item系列

实现普通对象像字典一样通过`[]`操作值

~~~python
class Person(object):

    def __init__(self, name):
        self.name = name

    def __setitem__(self, key, value):
        print("[]设置值时触发")
        setattr(self, key, value)

    def __getitem__(self, item):
        print("[]取值时触发")
        return getattr(self, item)

    def __delitem__(self, key):
        print("del p[key]时触发", key)


p = Person('jack')
p['name'] = 'mack'			 # 需要__setitem__才可以
print(p['name'])           	  # 需要__getitem__才可以
print(p.__dict__)

del p["name"]				# 需要__delitem__才可以
~~~





## 5-8 enter和exit

- enter和exit可以帮助实现上下文管理器，如with语句的功能。

示例1：wit语句操作文件

~~~python
file = open("test.txt", "w")
file.write("hello world")
file.close()

# 上面文件操作可以使用with语句简化
# 进入with语句块时，将文件句柄赋值给 f
# 在with语句块内，通过f做文件的操作
# 在退出with语句时，自动执行 f.close
with open("test.txt", "w") as f:
    f.write("hell world")
~~~

示例2：使用上下文管理器实现上述with文件操作

~~~python
class MyOpen:
    def __init__(self, file_name: str, mode="r"):
        self.file = open(file_name, mode)

    def __enter__(self):
        print("进入with语句块时触发")
        return self.file        # 返回值赋值给 as后面的接收值

    def __exit__(self, exc_type, exc_val, exc_tb):
        print("退出with语句块时触发，不论with语句块内是够有异常报错，__exit__都会被执行")
        self.file.close()


        
with MyOpen("text.txt", "w") as f:
    f.write("hello world")
~~~



## 5-9 iter和next

通过这两个魔法方法实现迭代器功能，比如实现一个range

示例1：range

~~~python
for i in range(10):
    print(i)
~~~

示例2：通过上述两个魔法方法实现自己的range

~~~python
import time


class MyRange:
    def __init__(self, total: int, step: int = 1):
        self.count = 0
        self.total = total
        self.step = step

    def __iter__(self):
        print(1111)
        return self			# for循环第一个进入时执行一次，需要返回一个实现了__next__的迭代器对象
	
    def __next__(self):		# 每次循环取值时执行，最后没有元素可取时需要抛出异常：StopIteration
        time.sleep(0.5)
        self.count += self.step
        if self.count == self.total:
            raise StopIteration
        return self.count


for i in MyRange(10, 2):
    print(i)
~~~





## 5-10 其他魔法方法

上述介绍了一些常规的魔法方法，除此之外，还有很多魔法方法，本节课我们再补充一些林零散散的其他魔法方法。

- 第一组：`__getattribute__` 和 AttributeError
- 当访问属性不存在时执行，`__getattr__`
- 不论属性是否存在都会执行`__getattribute__` 
- 当两者同时存在时，只会执行`__getattribute__` 除非`__getattribute__` 抛出AttributeError（这也解释了为啥属性不存在是触发`__getattr__`）

~~~python
class Foo:
    def __init__(self,x):
        self.x = x

    def __getattr__(self, item):
        print('执行的是我')

    def __getattribute__(self, item):
        print('不管是否存在,我都会执行')
        raise AttributeError('哈哈')


f1=Foo(10)

f1.x
f1.y
~~~



- 第二组：`__module__` 、`__class__`、`__name__`

- 　__module__ 表示当前操作的对象在那个模块

- 　__class__   表示当前操作的对象的类是什么

- 　__name__ 表示对象的名字

  

第三组：`__slot__` 和 `__all__`

- slot控制对象在实例化后可以持支持哪些属性

~~~python
class Foo:

    __slots__ = "a"

    def __init__(self):
        pass

f = Foo()
f.a = 100       # 没问题
f.v = 100       # 报错 AttributeError: 'Foo' object has no attribute 'v'
~~~

- 通过
- 在模块文件中设置 `__all__` 变量，当其它文件以“from 模块名 import *”的形式导入该模块时，该文件中只能使用 `__all__` 列表中指定的成员。

~~~python
# tools.py

def add(a: int, b: int):
    return a + b

def mul(a: int, b: int):
    return a * b


def xsum(nums: list):
    return sum(nums)


__all__ = ["add", "mul"]
~~~



- 第四组：asyncio异步系列的魔法函数（了解）









# 6 元类

## 6-1 类也是对象

我们说对象是类实例化的，其实在python中一切皆对象，也就是说类也可以是一个对象，那问题就来了：类是对象的话，那它是由谁实例化的呢？

答案是：元类【实例化类的类】。

示例：类也是对象，类的类型是 type , type是python默认元类。

~~~python
class Student:
    def __init__(self, name, age):
        self.name = name
        self.age = age


stu = Student("jack", 18)	# Student实例化得到对象 stu
print(type(stu))		    # type()函数可以得到 stu这个对象的类是Student 
print(stu.__class__)		# 类似的方式 得到 stu这个对象的类是Student 


print(type(Student))		# 类似的 通过type()的方式得到Student的类型是 type
print(Student.__class__)	# 类似的通过 __class__ 得到Student的类是 type


# 于是得到结论 类也是对象，类的类是type, 即type实例化得到的对象是类
~~~





## 6-2 class机制

~~~python
class Student:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    
    def talk(self):
        print('hello')
        
# 类的三大特征：
	- 类名：Student
	- 基类们：object,
	- 名称空间：就是类下面对象的属性和方法，构成的一个字典 key是属性/方法名字符串，value是属性/方法本身
~~~



class是python的一个关键字，目的是用来创建类。它在底层实现类的定义本质上有四个步骤。

- 第一步：**获取类名**：class_name = Student
- 第二步：**获取基类们**：class_bases = (object, )
- 第三步：**获取类的名称空间**：`class_dict = {"__init__": __init__, ""talk: talk}`
- 第四步：**调用元类 type实例化产生Studenty类这个对象**

```python
def __init__(self, name, age):
    self.name = name
    self.age = age


def talk(self):
    print('hello')


class_name = "Student"
class_bases = (object, )
class_dict = {"__init__": __init__, "talk": talk}


Student = type(class_name, class_bases, class_dict)

stu = Student("jack", 18)
print(stu.talk())
```

上述四步是定义类的底层原理，也是我们定义产生一个类的第二种方式。有些第三方库的源码中就是这么用的。

但是这种方式是复杂繁琐的，我们肯定会使用python给我们提供的class关键字来定义类。





## 6-3 自定义元类

上面class机制的四步中，我们可以发挥的地方在第四步，即用不同的元类实例化类这个对象，目的是为了按照需求控制类的定义和调用。

这就需要我们先定制出我们自己的元类，即自定义元类。

```python
class Student(metaclass=type):	# class机制默认的元类是type：我们可以修改metaclass参数来选择自定义元类
    def __init__(self, name, age):
        self.name = name
        self.age = age
    
    def talk(self):
        print('hello')
```

在python中`type`是一切类的基石，所以我们自定义的元类也必须继承`type`。

自定义的元类继承`type`的目的是为了使用`type`的大部分功能，我们只定制我们需要的那部分功能。

```python
class Mymeta(type):		# 只有继承了type的类才能作为元类使用
    pass


class Student(metaclass=Mymeta):	# 使用Mytema元类，即Mymeta(class_name, class_bases, class_dict)
    def __init__(self, name, age):
        self.name = name
        self.age = age
        
    def talk(self):
        print('hello')
```



## 6-4 自定义元类控制类的定义

自定义元类定制定义类时的需求。需要在自定义的元类中实现__init__()

```python
class Mymeta(type):
    def __init__(self, class_name, class_bases, class_dict):

        if not class_name.istitle():			# 自定制需求
            raise NameError('类名首字母必须大写')

        if not self.__doc__:
            raise TypeError('类必须要有文档注释')

        super().__init__(class_name, class_bases, class_dict)


# People = Mymeta(class_name, class_bases, class_dict),
class People(object, metaclass=Mymeta):
    """
    deox
    """
    x = 10
    def f(self):
        pass



print(People.__dict__)
```





## 6-5 实例化对象的本质

我们知道实例化一个对象会发生三件事，但其实我们确切知道的只有第二件事：初始化对象(`__init__`的功劳)。

现在，我们详细介绍介绍这三件事是如何发生的。其实这里只谈概念，具体是实现在本文下一节讨论。

```python
class Student:
    def __init__(self, name, age):
    	self.name = name

    
stu = Student('jack', 18)		


# 实例化的三件事：
- 创建一个空对象   __new__
- 初始化空对象    __init__
- 返回初始化完成的对象

- 创建空对象由类的__new__()方法实现，__new__创建并返回一个空对象
- __init__接收这个空对象，并初始化该对象
- 最后返回初始化完成的对象。

整个三步流程是由类的类，即元类中的__call__方法管理的。因为实例化对象，类加括号，即类的调用。
类当一个对象看待时，当它调用的时候就会触发其类的__call__函数的执行，即实例化对象时，触发元类的__call__函数。
在元类__call__内实现实例化的三件事。

当我们默认使用的是type元类时，想要自定制实例化过程中的需求是无法实现的，因为我们无法修改内置元类type的__call__方法。
当我们使用自定义元类时，就可以实现实例化过程需求的自定制，因为我么可以重写自定义元类的__call__方法，即自定制实例化需求。
```





## 6-6 自定义元类控制类的调用

类的调用：类加括号，也就是实例化对象。

控制类的调用就是控制对象的实例化过程。类的调用就会触发元类的`__call__`方法执行

```python
class Mymeta(type):
    def __call__(cls, *args, **kwargs):
        # 第一步: 调用Student下面的__new__创建一个空对象, 无则使用类(type)的__new__
        obj = cls.__new__(cls, *args, **kwargs)
        # 第二步：调用Student下面的__init__初始化对象
        cls.__init__(obj, *args, **kwargs)        # 等价于： obj.__init__(*args, **kwargs)
        # 第三步：返回对象
        return obj


class Student(object, metaclass=Mymeta):
    def __init__(self, name, age):
        self.name = name
        self.age = age


jack = Student('jack', 18)   # People.__call__('jack', 18)
```







## 6-7 通过new实现单例模式

实现单例模式，将判断单例的实现方式交由 `__new__`实现。

```
class Student:

    _instance = None

    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __new__(cls, *args, **kwargs):
        if cls._instance:
            return cls._instance                   # 有实例则直接返回
        else:
            cls._instance = super().__new__(cls)   # 没有实例则new一个并保存
            return cls._instance                   # 返回

        # if not cls._instance:                         # 这是简化的写法，上面注释的写法更容易提现判断思路
        #     cls._instance = super().__new__(cls)
        # return cls._instance


stu1 = Student('jack', 18)
stu2 = Student('jack', 18)
print(stu1 is stu2)
print(stu1.__dict__, stu2.__dict__)
```





## 6-8 通过元类实现单例模式

~~~python
class Mymeta(type):

    def __init__(cls, name, bases, dic):
        super().__init__(name, bases, dic)
        cls._instance = None		                  # 将记录类的实例对象的数据属性放在元类中自动定义了

    def __call__(cls, *args, **kwargs):	    # 此call会在类被调用（即实例化时触发）
        if cls._instance:				  # 判断类有没有实例化对象
            return cls._instance
        else:						  # 没有实例化对象时，控制类造空对象并初始化
            obj = cls.__new__(cls, *args, **kwargs)
            obj.__init__(*args, **kwargs)
            cls._instance = obj			          # 保存对象，下一次再实例化可以直接返回而不用再造对象
            return obj


class Student(metaclass=Mymeta):
    def __init__(self, name, age):
        self.name = name
        self.age = age


stu1 = Student('jack', 18)
stu2 = Student('jack', 18)
print(stu1 is stu2)
print(stu1.__dict__, stu2.__dict__)

# 原理：类定义时会调用元类下的__init__，类调用(实例化对象)时会触发元类下的__call__方法
# 类定义时，给类新增一个空的数据属性，
# 第一次实例化时，实例化之后就将这个对象赋值给类的数据属性；第二次再实例化时，直接返回类的这个数据属性
# 和方式3的不同之处1：类的这个数据属性是放在元类中自动定义的，而不是在类中显示的定义的。
# 和方式3的不同之处2：类调用时触发元类__call__方法判断是否有实例化对象，而不是在类的绑定方法中判断
~~~





# 7 并发编程

并发编程的目的：提高程序处理任务，处理请求的速度/能力。

并发编程的方式：

- 多进程
- 多线程
- 协程



## 7-1 开启多进程的两种方式

- 程序仅仅只是一堆代码而已，而进程指的是程序的运行过程。

- 需要强调的是：**同一个程序执行两次，那就是两个进程**

方式1：Process

~~~python
import time
from multiprocessing import Process


def func(name):
    print(f'{name} is start')
    time.sleep(1)
    print(f'{name} is done')



if __name__ == '__main__':
    # 创建一个子进程, windows下只能使用这种方式，否则报错
    p = Process(target=func, args=('jack',))
    # 开启子进程
    p.start()
    print('主进程结束')
~~~

方式2：继承Process， 重写方法run

~~~python
import time
from multiprocessing import Process


def func(name):
    print(f'{name} is start')
    time.sleep(2)
    print(f'{name} is done')


class MyProcess(Process):
    def __init__(self, name):
        super().__init__()
        self.name = name

    def run(self):  # start会自动调用run
        func(self.name)


if __name__ == '__main__':
    p = MyProcess("jack")
    p.start()
    print('主进程结束')
~~~





## 7-2 主进程等待子进程结束

示例1：使用`join()`优雅的等待子进程结束，不要使用`time.sleep`

~~~python
import time
from multiprocessing import Process


def func(name):
    print(f'{name} is start')
    time.sleep(1)
    print(f'{name} is done')


if __name__ == '__main__':
    p = Process(target=func, args=['jack'])
    p.start()
    p.join(timeout=1)        # time.sleep(10)
    print('主进程结束')
    
    
    # p.join(timeout=1)	timeout设置等待时间
~~~

示例2：等待多个子进程，使用不当造成串行

~~~python
import time
from multiprocessing import Process


def func(name):
    print(f'{name} is start')
    time.sleep(1)
    print(f'{name} is done')


if __name__ == '__main__':
    for i in range(5):
        p = Process(target=func, args=[str(i)])
        p.start()
        p.join()
    print('主进程结束')
~~~

示例3：示例2优化版

~~~python
if __name__ == '__main__':
    ps = []
    for i in range(5):
        p = Process(target=func, args=[str(i)])
        p.start()
        ps.append(p)
        
    for p in ps:
        p.join()
    print('主进程结束')
~~~

示例4：等待总耗时

~~~python
import time
from multiprocessing import Process


def func(i):
    print(f'{i} is start')
    time.sleep(i)
    print(f'{i} is done')


if __name__ == '__main__':
    ps = []
    start = time.time()
    for i in range(1, 4):
        p = Process(target=func, args=[i])
        p.start()
        ps.append(p)

    for p in ps:
        p.join()
    print('主进程结束')
    print(time.time() - start)		# ~3s
~~~



## 7-3 守护进程

**主进程将一个子进程设置为守护进程，守护进程就会在主进程结束时随之结束不再运行**。

- **守护进程内无法再开启子进程**，否则抛出异常：AssertionError: daemonic processes are not allowed to have children
- **在开启子进程之前将其设置为守护进程**
- **主进程代码运行结束，守护进程随即终止**，不受别的子进程的影响。

```python
from multiprocessing import Process
import time

def task():
    print('i am coming')
    time.sleep(2)
    print('i am backing')


if __name__ == '__main__':
    p = Process(target=task)
    p.daemon = True		# p.start()之前将子进程设置为守护进程，陪着主进程一块结束。
    p.start()
    time.sleep(0.1)
    print('i am master')
```

示例2：各子进程相互独立，不会因为一个子进程设置为守护进程而影响另一个

~~~python
from multiprocessing import Process
import time


def task(i):
    print(f'{i} am coming')
    time.sleep(i)
    print(f'{i} am backing')


if __name__ == '__main__':
    for i in range(1, 4):
        p = Process(target=task, args=[i])
        if i == 3:							# 编号为3的子进程设置为守护进程，当主进程执行结束后就被回收了
            p.daemon = True
        p.start()

    time.sleep(2)
    print('master')
~~~





## 7-4 进程隔离内存空间

**进程之间内存空间是隔离的，即进程j间数据是隔离的，互不影响**。

因为这是两个独立的名称空间。子进程有其自己的名称空间，只是修改其名称空间内全局变量的值，而不影响父进程自己名称空间全局变量的值。

```python
from multiprocessing import Process

money = 100


def task():
    global money
    money = 666
    print('子', money)


if __name__ == '__main__':
    p = Process(target=task)
    p.start()
    p.join()
    print('主', money)
```





## 7-5 互斥锁

进程之间数据不共享，但是共享同一套文件系统，所以访问同一个文件，或同一个打印终端，是有问题的。

示例1：进程不共享数据导致的数据混乱问题。

~~~python
from multiprocessing import Process, Lock
import json, time, random


# 查票
def search(i):
    with open('data', 'r', encoding='utf8') as f:
        dic = json.load(f)
    print('用户%s查询余票：%s' % (i, dic.get('ticket_num')))
    return dic


def buy(i):
    dic = search(i)		# 先查票
	# 再买票
    time.sleep(random.randint(1, 3))	
    if dic.get('ticket_num') > 0:
        dic['ticket_num'] -= 1
        with open('data', 'w', encoding='utf8') as f:
            json.dump(dic, f)
        print('用户%s买票成功' % i)
    else:
        print('用户%s买票失败' % i)



if __name__ == '__main__':
    for i in range(1, 11):
        p = Process(target=buy, args=[i])
        p.start()
~~~

共享带来的是竞争，竞争带来的结果就是错乱。控制的方式就是加锁处理，即每个进程依次使用资源。

示例2：互斥锁，保证数据安全

~~~python
from multiprocessing import Process, Lock
import json, time, random


# 查票
def search(i):
    with open('data', 'r', encoding='utf8') as f:
        dic = json.load(f)
    print('用户%s查询余票：%s' % (i, dic.get('ticket_num')))


# 买票  1.先查 2.再买
def buy(i, mutex):
    search(i)
    mutex.acquire()  # 抢锁, 给买票环节加锁处理
    time.sleep(random.randint(1, 3))
    with open('data', 'r', encoding='utf8') as f:
        dic = json.load(f)
        ticket_num = dic["ticket_num"]
    if ticket_num > 0:
        dic['ticket_num'] -= 1
        with open('data', 'w', encoding='utf8') as f:
            json.dump(dic, f)
        print('用户%s买票成功' % i)
    else:
        print('用户%s买票失败' % i)
    mutex.release()  # 释放锁


if __name__ == '__main__':
    # 在主进程中生成一把锁 让所有的子进程抢 谁先抢到谁先买票
    mutex = Lock()
    for i in range(1, 11):
        p = Process(target=buy, args=[i, mutex])
        p.start()
~~~

加锁处理的结果就是：**将并发变成串行，牺牲效率，保证数据的安全**







## 7-6 多线程

基础知识：

- 一个程序的运行过程是一个进程，**每个进程自带一个控制线程**。
- **进程是一个资源单位，线程是具体的执行单位**。
- 进程相当于一个车间，线程相当于车间内的流水线，车间内可以有多条流水线，即一个进程内可以有多个线程。
- 多线程之间共享数据资源，相当于一个车间内有多条流水线，都共用一个车间的资源。

~~~python
进程: 资源单位(起一个进程是在内存空间中开辟一块独立的空间)
线程: 执行单位(真正被干活的是进程里面的线程，线程在执行中所需要使用到的资源都找所在的进程索要)

# 开进需要开内存空间，程资源消耗大
# 开线程无需再次申请内存空间操作，消耗小
# 开线程的开销要远远的小于进程的开销。同一个进程下的多个线程数据是共享的
~~~

为什么使用多线程：如果多个任务共用一块地址空间，那么必须在一个进程内开启多个线程。



示例：开线程的两种方式

~~~python
from threading import Thread


def task():
    print('我是子线程')


# 方式1：直接使用Thread实例化线程对象
if __name__ == '__main__':
    t = Thread(target=task)
    t.start()
    print('我是主线程')


# 方式2：继承Therad，自定自己的线程类，重写run方法
class MyThread(Thread):
    def run(self):
        task()


if __name__ == '__main__':
    t = MyThread()
    t.start()
    print('我是主线程')
~~~





## 7-7 线程共享内存空间

> 补充：线程的知识点和进程的知识点类似，可以举一反三的学习。

**同一个进程下的多个线程是共享当前进程下的数据资源的**

```python
from threading import Thread

n = 100


def func():
    global n
    n = 200
    print('子线程:', n)		# 110


if __name__ == '__main__':
    t = Thread(target=func)
    t.start()
    # t.join()
    print('主线程:', n)
```





## 7-8 GIL

全局解释器锁（GIL， Global Interpreter Lock）。

结论：**在Cpython解释器中，同一个进程下开启的多线程，同一时刻只能有一个线程执行，无法利用多核优势**

```python
# 程序的代码需要交给解释器执行，因此该进程内的每个线程都需要将共享的代码交给解释器执行，这就有了竞争。
# 垃圾回收机制GC也是当前进程内的一个干活的线程，GC会和当前进程内其他线程有抢代码数据的竞争，因此有了GIL。
# GIL保证进程内同一时刻只有一个线程在运行，这样做是为了保证内存管理（垃圾回收机制GC）的运行。
```

总结：

- **GIL不是python语言的特性，它是Cpython解释器引用的一个概念。**
- **GIL本质是一把互斥锁，是将线程从并发变为串行，牺牲效率保证数据安全。**
- **保护不同的数据要加不同的互斥锁，GIL保护的是解释器级别的数据安全。**

![image-20220514173429195](%E7%AC%94%E8%AE%B0.assets/image-20220514173429195.png)

首先明确：**python多线程无法利用计算机的多核优势**。

但是python多线程可以实现并发的，且不是所有的场合都要使用多核的。

场合：

- **对于计算密集型，多进程优于多线程**（多进程的并行计算优势突出了）
- **对于IO密集型，多线程优于多进程**（开进程的开销大缺陷放大了）

结论：**python多线程无法使用多核优势，不等于多线程一无是处**。





## 7-9 进程池和线程池

基础知识：

- 无论是开进程还是开线程，都需要消耗资源，只不过开线程的比开进程的消耗更少。
- 不可能无限制的开进程或线程，因为计算机硬件的资源是有限的。
- 为了保证服务器一定的处理能力，只能采取`池`的概念。
- 池：就是限制开进程或开线程的数量，比如开多线程，限制最多开10个线程，那这个线程池的大小就是10，也就是说最多只有10个线程在干活。

示例1：线程池和进程池的基本使用（都是使用concurrent.futures模块）

```python
from concurrent.futures import ThreadPoolExecutor
import time
import random


def task(n):  # 处理任务的函数
    time.sleep(random.randint(1, 5))
    print(n ** 2)



if __name__ == '__main__':
    pool = ThreadPoolExecutor(5)		# 开一个大小是5的线程池
    for i in range(1, 10):
        pool.submit(task, i)			# 提交任务和任务需要的参数
```

示例2：增加回调函数

~~~python
from concurrent.futures import ThreadPoolExecutor
import time
import random


def task(n):  # 处理任务的函数
    time.sleep(random.randint(1, 5))
    return n ** 2


def call_back(future):		# 需要定义一个形参，这个形参是future，通过future.result()获取任务执行返回值。
    print('call_back>>>:', future.result())


if __name__ == '__main__':
    pool = ThreadPoolExecutor(5)
    for i in range(1, 10):
        pool.submit(task, i).add_done_callback(call_back)		
        # 给任务绑定回调函数，任务结束后自动调用回调函数，并将future对象当实参传给回调函数。
~~~

示例3：map函数快速获取结果

~~~python
from concurrent.futures import ThreadPoolExecutor
import time
import random


def task(n):  # 处理任务的函数
    time.sleep(random.randint(1, 5))
    return n ** 2


if __name__ == '__main__':
    pool = ThreadPoolExecutor(5)
    rets = pool.map(task, range(1, 10))	
    for r in rets:
        print(r)
# map第一个参数是任务，第二个参数是迭代器，迭代器的每个元素依次传给任务当实参
# map返回一个生成器，可以直接获取每个任务的结果
~~~



**总结**：

- 池子一旦造出来后，固定了线程或进程 ，不会再变更，所有的任务都是这些进程或线程处理。
- **这些进程或线程不会再出现重复创建和销毁的过程。**
- **任务的提交是异步的，异步提交任务的返回结果 应该通过回调机制来获取。**
- **回调机制就相当于，把任务交给一个员工完成，它完成后主动找你汇报完成结果。**





## 7-10 协程

协程是单线程下的并发，**协程是一种用户态的轻量级线程，即协程是由用户程序自己控制调度的。**

协程的切换开销更小，属于程序级别的切换，操作系统完全感知不到，因而更加轻量级；**单线程内就可以实现并发的效果，最大限度地利用cpu**

示例1：基于yield实现代码块切换

~~~python
def f1():
    yield 1
    yield from f2()
    yield 2


def f2():
    yield 3
    yield 4


generator = f1()		# f1()是生成器

for i in generator:
    print(i)			# 依次打印：1 3 4 2
~~~

示例2：使用asyncio实现协程【官方推荐】

~~~python
import asyncio


async def f1():
    print(1)
    await asyncio.sleep(2)		# 等 可以等待的对象
    print(2)


async def f2():
    print(3)
    await asyncio.sleep(2)
    print(4)


tasks = [
    asyncio.ensure_future(f1()),
    asyncio.ensure_future(f2())
]

loop = asyncio.get_event_loop()
loop.run_until_complete(asyncio.wait(tasks))
~~~



# 8 web开发

## 8-1 web开发必备技能

只学会python想要做web开发其实是非常困难的！！！

需要在掌握python语法的基础上（面向对象很重要），再学习其他方面的知识：

- 网络协议：TCP/IP协议、HTTP协议等
- Python  Web开发协议：wsgi、asgi（Web框架）等
- 数据库：MySQL、Redis等
- 前端基础：HTML、CSS、JS等
- 开发模式：前后端混合/分离
- 工具：git/docker等





## 8-2 使用socket模块实现CS通信

网络协议是非常复杂的，并且理论知识点多且繁琐，但又是非常重要的，想要学习web开发不得不掌握一定的网络协议基础。

正是这个的原因，本节课我们基于socket实现CS通信这个例子，我们快速介绍下网络协议的。

- 网络协议是分层
- 实际web开发处于应用层，比如使用HTTP协议客户端和服务端通信
- 应用层以下，用抽象出一个socket层，socket层提供接口供一个层使用。每种后端编程语言都会都会有对应的socket包
- 基于网络通信的软件在通信形式上可以分为两类：CS架构 & BS架构
- CS架构：客户度Client - 服务端Server
- BS架构：浏览器Browser - 服务端Server (本质上：浏览器就是一个特殊的客户端)



示例：使用socket模块实现CS通信

~~~python
# server.py
import socket

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server.bind(('127.0.0.1', 8008))	    # 绑定ip/port
server.listen(5)	                    # 监听，半连接池=5
print("wait for connecting...")

while 1:		# 连接循环
    conn, addr = server.accept()	    # 等待结束客户端连接请求，获取连接和客户端ip/port
    print("接收连接：", addr)
    while 1:
        try:
            data = conn.recv(1024)	        # 每次接收1024个字节
            if not data:
                break
            print(data.decode('utf-8'))
            conn.send(data.upper())
        except Exception as e:
            print(e)
            break

    conn.close()	                        # 通信结束，关闭连接
~~~

~~~python
# client.py
import socket

client = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
client.connect(('127.0.0.1', 8008))	    # 直接发起连请求，需要指代服务端的ip/port

while 1:
    haha = input('>>>:').strip()
    if haha == 'q':
        break
    if not haha:	    # 防止客户端发空消息
        continue
    phone.send(haha.encode('utf-8'))	    # 网络传输都是bytes二进制数据
    data = phone.recv(1024)				    # 接收1024个字节
    print(data.decode('utf-8'))

client.close()	                            # 通信结束关闭连接
~~~





## 8-3 服务端支持并发

上面我们实现的CS架构软件，实现了服务端和客户端通信，但是服务同一时间只能和一个客户端通信

想要服务端实现同时和多个客户端通信的能力，我们可以使用线程池(或进程池、协程)的方式实现服务端并发能力。

示例1：使用线程池实现服务端并发

~~~python
# server.py 

from concurrent.futures import ThreadPoolExecutor
import socket

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server.bind(('127.0.0.1', 8009))	    # 绑定ip/port
server.listen(5)	                    # 监听，半连接池=5
print("wait for connecting...")


def handle(conn, addr):
    print("接收连接：", addr)
    while 1:
        try:
            data = conn.recv(1024)  # 每次接收1024个字节
            if not data:
                break
            print(data.decode('utf-8'))
            conn.send(data.upper())
        except Exception as e:
            print(e)
            break

    conn.close()


pool = ThreadPoolExecutor(5)


while 1:
    conn, addr = server.accept()	
    pool.submit(handle, conn, addr)		 # 来一个客户端连接，就交给线程池中的一个线程处理，然后等待客户端连接

~~~

示例2：封装服务端代码

~~~python
from concurrent.futures import ThreadPoolExecutor
import socket


class MyTcpServer:
    def __init__(self, ip, port):
        self.ip = ip
        self.port = port
        self.server = socket.socket()
        self.server.bind((self.ip, self.port))
        self.server.listen(5)

    def wait_accept(self):
        return self.server.accept()

    def handle_request(self, conn, addr):
        print("接收连接：", addr)
        while 1:
            try:
                data = conn.recv(1024)  # 每次接收1024个字节
                if not data:
                    break
                print(data.decode('utf-8'))
                conn.send(data.upper())
            except Exception as e:
                print(e)
                break

        conn.close()


if __name__ == '__main__':
    pool = ThreadPoolExecutor(5)
    server = MyTcpServer('127.0.0.1', 8888)
    while 1:
        conn, addr = server.wait_accept()
        pool.submit(server.handle_request, conn, addr)
~~~





## 8-4 使用socket模块实现BS通信

浏览器做客户端，我们只需要实现服务端即可，但返回数据需要遵循HTTP协议。

~~~python
from concurrent.futures import ThreadPoolExecutor
import socket


class MyTcpServer:
    def __init__(self, ip, port):
        self.ip = ip
        self.port = port
        self.server = socket.socket()
        self.server.bind((self.ip, self.port))
        self.server.listen(5)

    def wait_accept(self):
        return self.server.accept()

    def handle_request(self, conn, addr):
        print("接收连接：", addr)
        while 1:
            try:
               data = conn.recv(1024).decode("utf-8")  # 每次接收1024个字节
               print(data)
               if not data:
                    break
               conn.send("HTTP/1.1 200 OK\r\n\r\n".encode("utf-8"))
        	   conn.send("<h1>hello web<h1>".encode("utf-8"))
             except Exception as e:
                print(e)
                break

        conn.close()


if __name__ == '__main__':
    pool = ThreadPoolExecutor(5)
    server = MyTcpServer('127.0.0.1', 9911)
    while 1:
        conn, addr = server.wait_accept()
        pool.submit(server.handle_request, conn, addr)
~~~

示例2：优化HTTP协议规则，一次请求一个响应，然后断开连接。

~~~python
class MyTcpServer:
	...
    def handle_request(self, conn, addr):
        print("接收连接：", addr)
        data = conn.recv(1024).decode("utf-8")  # 每次接收1024个字节
        print(data)
        if not data:
            return
        conn.send("HTTP/1.1 200 OK\r\n\r\n".encode("utf-8"))
        conn.send("<h1>hello web<h1>".encode("utf-8"))
        conn.close()
~~~

示例3：根据url，响应不同的内容

~~~python
class MyTcpServer:
    ...

    def handle_request(self, conn, addr):
        print("接收连接：", addr)
        data = conn.recv(1024).decode("utf-8")  # 每次接收1024个字节
        print(data)
        if not data:
            return
        # print(data.decode('utf-8'))
        conn.send("HTTP/1.1 200 OK\r\n\r\n".encode("utf-8"))
        # 分割请求体的数据，获取url中携带的index
        current_path = data.split(' ')[1] 
        if current_path == '/index':
            conn.send("<p>index<p>".encode("utf-8"))
        elif current_path == '/about':
            conn.send("about".encode("utf-8"))
        else:
            conn.send("<h1>hello web<h1>".encode("utf-8"))
        conn.close()
~~~

补充：当我们在浏览器访问：http:127.0.0.1:9911时，服务端会收到两个请求，其中一个是我们自己发的，另一个/favicon.ico是浏览器自己发的，目的是要获取浏览浏览器tab页头的图片。



## 8-5 占位



## 8-6 HTTP协议

HTTP：**超文本传输协议（也叫超文本转移协议），用来规定服务端和浏览器之间交互数据的格式。**如果是BS软件，就必须准许HTTP协议。

**HTTP基本特性**

- 一次请求一次响应

- 基于TCP/IP协议作用在应用层的协议

- 无状态，（为了保存状态，后来出现了cookie\session\token技术）

  

**请求数据格式**

```python
# 请求首行\r\n请求头\r\n\r\n请求体\r\n

请求首行：标识HTTP协议版本，当前请求方式
请求头：一大堆k, v键值对
请求体：并不是所有的请求方式都有get没有post有 存放的是post请求提交的敏感数据

# 请求方式
get请求：向服务端要数据		
post请求：向服务端提交数据
...

# 补充；
url：统一资源定位单位，即所谓的网址
```

**响应数据格式**

```python
# 响应首行\r\n响应头\r\n\r\n响应体\r\n

响应首行：标识HTTP协议版本，响应状态码
响应头：一大堆k, v键值对
响应体：返回给浏览器展示给用户的数据

# 响应状态码：一串表示复杂状态或者描述信息的数字
1xx：服务端已经成功接收到了你的数据正在处理，你可以继续提交额外的数据
2xx：服务端成功响应了你想要的数据(200 OK请求成功)
3xx：重定向(301,302)
4xx：请求错误（404，资源不存在；403，没有访问权限）
5xx：服务器内部存错（500）
```





## 8-7 使用wsgiref实现服务端

上面我们实现了简单的BS架构下的网络通信，这也是基本的web通信流程。

但是离真实的web开发还差许多，还可以有很多优化点：

- 1.代码重复(服务端代码所有人都要重复写)
-  2.手动处理http格式的数据,并且只能拿到url后缀,其他数据获取繁琐（代码重复）
- 3.并发的问题（代码重复）

为了解决这些，我们需要借助一些模块，首先介绍`wsgiref`模块，wsgiref模块可以帮我们处理请求信息的处理（避免手动分割请求数据）和响应信息的处理（避免手动处理响数据的格式）。

```python
from wsgiref.simple_server import make_server


def index(enc):
    return "index"

def about(enc):
    return "about"


urls = [
    ('/index', index),
    ('/about', abnout),
]


def app(env, response):
    """
    :param env:请求相关的所有数据
    :param response:响应相关的所有数据
    :return: 返回给浏览器的数据
    """
    print(env)                # 大字典  wsgiref模块帮你处理好http格式的数据 封装成了字典让你更加方便的操作
    print(response)
    response('200 OK', [("AAA", "aaa")])  # 响应首行和响应头
    current_path = env.get('PATH_INFO')

    func = None
    for url in urls:
        if current_path == url[0]:
            func = url[1]
            break
    res = "hello world"
    if func:
        res = func(env)

    return [res.encode('utf-8')]


if __name__ == '__main__':
    server = make_server('127.0.0.1', 8080, app)
    """
    会实时监听127.0.0.1:8080地址 只要有客户端来了
    都会交给run函数处理(加括号触发run函数的运行)
    """
    server.serve_forever()  # 启动服务端
```



## 8-8 wsgi协议

wsgi是为 Python 定义的 Web 服务器和 Web 应用程序或框架之间的一种简单而通用的接口。

![image-20220402113310561](../python%25E5%25BC%2582%25E6%25AD%25A5%25E7%25BC%2596%25E7%25A8%258B/%25E7%25AC%2594%25E8%25AE%25B0.assets/image-20220402113310561.png)



简单来说，wsgi规定了web服务器和web应用之间的通信协议。

~~~python
def app(environ, start_response):
    start_response('200 OK', [('Content-Type', 'text/html')])
    return '<h1>Hello, web!</h1>'

class WSGIHandler:
    def __call__(environ, start_response):
			pass

# environ：一个包含所有HTTP请求信息的dict对象；
# start_response：一个发送HTTP响应的函数。
~~~

整个app()函数本身没有涉及到任何解析HTTP的部分，也就是说，底层代码不需要我们自己编写，我们只负责在更高层次上考虑如何响应请求就可以了。比如**Diango和Flask等等web框架属于web应用这层**。

app()函数必须由WSGI服务器来调用。有很多符合**WSGI规范的服务器，比如uwsgi、gunicorn、gevent**等，Python内置了一个WSGI服务器，这个模块叫wsgiref，它是用纯Python编写的WSGI服务器的参考实现。

>补充：一般web框架不会使用函数式的app接口，会采用面向对象的方式封装成类的形式，比如Django内部定义了一个WSGIHandler类，通过 \_\_call\_\_(self, environ, start_redponse)实现了wsgi接口协议。







## 8-9 Python常用Web框架

- Django
- Flask
- Fastapi

~~~
django
	特点:大而全 自带的功能特别特别特别的多 类似于航空母舰
	不足之处:
		有时候过于笨重

flask
	特点:小而精  自带的功能特别特别特别的少 类似于游骑兵
	第三方的模块特别特别特别的多，如果将flask第三方的模块加起来完全可以盖过django
	并且也越来越像django
	不足之处:
		比较依赖于第三方的开发者
		
fastapi
	特点:异步框架 支持高并发，遵循 asgi, openapi
	不足之处:
		比较新
~~~



# 9. 附录

## 9.1 当前安装库信息

```bash
 # 导出当前项目所用库信息
 workon 当前虚拟环境
 pip freeze >requirements.txt
 
 # 导入当前库信息
 
```

