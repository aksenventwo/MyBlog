# PYTHON之装饰器详解

作者: Edpzou  标签:Python 写于: 2017-11-17

### 前言

>我认为装饰器是一种把死的技术用成活的一个经典例子

在Python当中，我感觉语法很自由，这种感觉的根本是因为在Python里，一切皆为对象。

装饰器的作用很广，讲装饰器，应该从函数讲起。

### 函数

Python定义一个函数是这样的：

```python
def hello():
    print 'hello world!'
```
                        
语法相当简单，调用这个函数的结果就是打印出‘hello world!’，如果函数仅仅只是拿来被调用，那么装饰器就不会诞生了。

我们知道可以把数字、字符串、对象赋值给一个变量，而且是理所当然。但是当我们知道一切皆为对象之后，我们是不是漏了函数这个特殊的存在。接下来我尝试把函数赋值给一个变量。

```python
func = hello
```                     

我们知道函数名字后面加()是调用这个函数，肯定不能func = hell()这样赋值，那么去掉()后是不是正确的赋值呢？如果是如何检验？我认为检验函数的最快速的方法就是调用它，如果我执行func会发生什么？执行func()又会发生什么？

```shell
>>>func
<function hello 0x02A123710>               
>>>func()                     
hello wrold!
```   
            
发现执行func只是打印出一个函数的说明信息，执行func()却打印出了hello world!，说明func()拥有了hello()的功能，感觉func是hello函数的克隆体，但是hello函数被删除del hello，func会受到影响吗？答案是不会，说明它们两个互不影响却拥有相同的功能。

这个例子告诉我们的是，函数是可以赋值给变量的。

### 函数里定义函数

如果你想，你可以在函数里定义一个函数，就像电影《盗梦空间》。梦中梦这个设定，我表示很是精彩。不知你是否有过在梦中做梦的经历。在你学会函数的使用时，是否尝试过在函数里定义函数？我不曾。但是总会有人去尝试的。

```python
def hello():
    def world():
        print 'I'm world.'
    world()
```           

上面的代码在hello函数里面又定义了一个world函数，大多数人估计和我感觉一样，在函数里面定义函数没什么用，而且看起来很丑。我就是这么感觉的。但是它能够像函数一样正常的运行，运行hello()结果就是打印出I’m world.

到目前为止，我们知道函数可以赋值给变量，函数里面可以定义函数。看似很鸡肋的两个功能，却造就了装饰器这个很棒的功能。如果我们把这两个功能组合一下，把在函数里面定义的函数赋值给变量，然后把这个变量作为函数的返回值返回，它会发生什么？

```python
def hello():
    def world():
        print "I'm world."
    func = world
    return func
```

一切看起来很正常，就像我们平常定义了一个函数，然后把需要的东西返回。我们定义一个变量接收hello()函数的返回值

```shell
>>>value = hello()
>>> value
<function world 0x02A123714>
>>> value()
I'm world.
```                        

不知你是否会对结果感到惊讶！结果给我们的感觉就是执行了world()函数。由此我们又获得一个功能，在函数中可以返回一个函数。

目前我们知道了三件事，现在我们换一下顺序:

1. 函数里面可以定义函数
2. 函数可以赋值给变量
3. 可以从函数中返回一个函数

虽然知道这些，但是感觉装饰器离我们还是很远。因为我们缺少一个核心内容。

有的函数是有参数的，比如：

```python
def hello(name):
    print 'hello',name
```

```shell                     
>>> hello('Kobe')                
hello kobe
```
                        
这个name就是参数的名字，我们传入Kobe字符串进去，所以打印出来hello Kobe,字符串是对象吧，函数也是对象吧，如果我们把函数当成参数传递进去会发生什么呢？

```python
def kobe():
    print "I' kobe."                        
    
def hello(name):
    name()
```                        

```shell                     
>>> hello(kobe)                     
I'm kobe
```            

我们提前知道hello()函数接受的参数是一个函数，所以我们给这个函数名后面加()的形式来试着执行它。结果很棒，它真的执行了kobe()这个函数。现在我们又知道了一个新的功能。我们再次重复列出它们：

1. 函数里面可以定义函数
2. 函数可以赋值给变量
3. 可以从函数中返回一个函数
4. 函数可以作为参数在函数里面传递，并且可以执行

这时候还需要一个契机，才能把这四个功能进化成装饰器。

现在这个契机来了。假如我有一个已经写好的的函数，它的功能是计算某些值，现在需要知道它计算一次花了多长时间。我们来试着用普通方法来实现。

```python
def calc():               
    return 1+1
```

这就是这个计算函数，它在计算1+1等于几，就是这么简单。

想要知道它花了多少时间，简单，使用伪代码来描述

```shell
开始计时                  
calc()                     
计时结束

运行时间 = 计时结束时间 - 开始计时时间
```

很简单，我现在有一百个这样的函数，请分别算出它们只执行一次的时间。想起一位总理说过，我们国家的问题其实都是小问题，但是乘以13亿，小问题需要重复解决13亿次。

我们需要改变，换个思路，我把上面的过程写成一个函数:

```python
def calc_func_run_time(func):
    开始计时
    func()
    结束计时
    运行时间 = 计时结束时间 - 开始计时时间
```                        

现在，不管你有多少个函数，我把这些函数当成参数一个循化就解决了。这个`calc_func_run_time()`就是一个很土鳖的装饰器了。只要有这个函数，任何一个函数都可以知道自己的运行时间。这是原有函数不曾有的功能。现在我们可以提前来看看装饰器的定义。

### 装饰器的定义

在不改变原有函数的定义前提下，给这个函数动态增加功能的行为，称之为“装饰器”（Decorator）。

我们需要一个更加高大上的装饰器例子，利用之前的那四个功能，我们进行如下编码：

```python
def decorator(func):
    def wrap_func():
        print 'hello'
        func()
        print 'world'
    return wrap_func

def kobe():
    print "I'm kobe."
```

```shell
>>> value = decorator(kobe)
>>> value
<function wrap_func at 0x......>
>>> value()
hello
I'm kobe.
world
```

这个高大上的装饰器有什么不同？通过在`decorator()`函数里面定义了`wrap_func()`函数，并且把真正需要装饰的函数`kobe()`放在`wrap_func()`里面执行，然后返回`wrap_func`。高大上的装饰器与土鳖装饰器的区别在于，当我们定义一个变量`value`来接收`decorator()`函数的返回值，这时，我们可以控制这个装饰器的发生。土鳖装饰器是装饰成功后立即执行被装饰的函数，高大上的装饰器被装饰成功了后，你想什么时候执行被装饰的函数就执行。就像开车，点火成功后，车子不一定走，如果你碰上一个点火成功后立即走的车子，估计连人带车要完。

这个高大上的装饰器还是有点小瑕疵:

```shell
>>> value
<function wrap_func at 0x......>
```

按道理来说，value的结果是:

```shell
<function kobe at 0x.......>
```      

因为装饰器的本质是给函数增加额外的功能而不改变原有函数，你现在把人家的名字都给改了，尴尬！

还好我们可以进行如下改进：

```python
def decorator(func):
    def wrap_func():
        print 'hello'
        func()
        print 'world'
    wrap_func.__name__ = kobe.__name__
    return wrap_func
                        
def kobe():
                        
    print "I'm kobe."
```
                        

```shell                     
>>> value = decorator(kobe)
>>> value
<function kobe at 0x......>
>>> value()
hello
I'm kobe.
world
```

只是把它的名字给改过来而以，`wrap_func.__name__ = kobe.__name__`

发现这个东西也可以通过装饰器来解决有没有，所以python就把这个动作也做成装饰器，真是学以致用啊！用法如下：

```python
from functools import wraps
                        

def decorator(func):
    @wraps(func)
    def wrap_func():
        print 'hello'
        func()
        print 'world'
    return wrap_func

def kobe():
    print "I'm kobe."
```                     

```shell                        
>>> value = decorator(kobe)
>>> value
<function kobe at 0x......>
>>> value()
hello
I'm kobe.
world
```

这个`@wraps(func)`就是Python给定的语法格式，你也可以把我们的写成

```python
@decorator
def kobe():
    print "I'm kobe."
```

这个`@decorator`就相当于`kobe = decorator(kobe)`,不过是语法的简写罢了！

装饰器也是函数，所以它也可以传入参数，上面的例子就有用到。那咱们来看看有参数的装饰器如何编写?

咱们先自顶而下来看，如果装饰器需要传入参数那么它的表现形式应该是这样的：

```
@decorator(name)

```

根据@decorator 等效于 `kobe = decorator(kobe)`, 我们可以推导出：

```python
decorator(name) = decorator(name)(kobe)
```

代码如何写？

```python
from functools import wraps


def decorator_arg(name):
    def decorator(func):
        @wraps(func)
        def wrap_func():
            print 'hello', name
            func()
            print 'world', name
        return wrap_func
    return decorator

                        
def kobe():
                        
    print "I'm kobe."
```

```shell                     
>>> value = decorator_arg('barant')(kobe)
>>> value
<function kobe at 0x......>
>>> value()
hello barant
I'm kobe.
world barant
```

就是增加一层嵌套函数，原理是一样的。

### 结束

装饰器的用法很多，在将来的编程会给你带来很多便利，但是请记住不要滥用装饰器，用它之前需要自己知道在干什么。