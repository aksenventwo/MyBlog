## 内存泄露

内存泄露问题，需要长时间运行的程序或者定时运行的程序才会冒出来的问题，如今的内存已经足够挥霍，但是还是经不起折腾。正所谓富不过三代，要看代码是否有内存泄露问题，跑它一天试试。

### 内存泄露爆发前一个礼拜
一个项目需用同步不同数据库的数据，从Oracle到GP,在保证数据不会丢失的情况下和及时同步的双重前提下。用了我们公司内部自己编写的同步程序Ladder，只需配置好连接参数，表关系映射即可，所以初期这个同步任务很快就运行起来了。

### 内存泄露爆发前3天
随之而来的问题，同步任务老是中断，不能稳定运行。中断时间为凌晨一点左右，第二天重启又可以正常运行。首先想到的是查看日志，一天的日志文件很大，但是就觉得这个日志文件不太正常，但是一开始把问题归结同步程序内部代码问题，日志记录也是突然中断，并没有异常抛出。但是还是先去检查同步程序，期间也修复了几个BUG，但是都不是导致它不能稳定运行的原因。

### 内存泄露爆发前24小时
问题终于被我的老板捕捉到，系统内存耗光，程序自然就终止了。因为是在早上9点重启同步任务，每分钟运行一次，程序跑到凌晨，因为内存泄露，在凌晨终于耗光了所有内存资源，导致程序终止。第一次碰到内存泄露问题，感觉很棘手。

### 内存泄露解决前1小时
善用搜索，找出导致内存泄露的原因。因为是用Python写的同步程序，所以指定Python内存泄露搜索即可。Pythonn是用垃圾回收机制的，它最主要的部分是通过引用计数来实现，一块内存，被一个变量引用一次就加一，当这块内存的引用计数为0时，即表示没有任何变量再引用它，它就会被Python的垃圾回收工人回收，也就是内存释放。所以反过来，当你的Python程序发生额内存泄露问题是，可以肯定是某些使用了大块内存的对象的引用计数不为0，导致Python没有回收这个资源，造成了内存的长期占有，时间越久，导致可用的内存越来越少。终于造成之后的程序无内存可用的情况。因为Python不需要手动释放内存，导致用Python的人对内存管理的疏忽和错误理解。今天就通过这个问题，初步的了解到了Python的内存管理。

- 造成Python内存泄露的原因

1.错误使用list和dict
	
	```
	>>> a = []
	>>> def foo():
	...     aa = []
	...     for i in range(10):
	...             a.append(i)
	...
	>>> a
	[]
	>>> foo()
	>>> a
	[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
	```

本来是想往aa列表添加数据，结果错误的使用了a列表，导致每次调用foo都会往a列表添加数据，造成a越来越大，这个a又因为一直需要用到，导致没有被垃圾回收，最终造成内存泄露，当然这只是个例子。这种情况通常在程序测试的时候就会发现。它有很多变种，复杂的情况也有。目前我没有遇到。

2. 对象的循环引用
先推荐一篇博客，*weakref-Garbage-Collectable References to Object*，主要讲述Python的弱引用和gc机制，当然它也描述了对象的循环引用问题。这里以代码为例：

	class A(objet):
		def __init__(self, name):
			self.name = name
			self.other = other

		def set_next(self, other):
			self.other = other

	a = A('a')
	b = A('b')
	c = A('c')
	a.set_next(b)
	b.set_next(c)
	c.set_next(a)

上面的代码演示了a,b,c三个对象循环用的情况，

	a.other = b
	b.other = c
	c.other = a
	它们的图如： a->b->c-a, b->c->a->b, c->a->b->c

删除a对象，b和c还保存着a的引用，删除a对象充当手动释放资源，垃圾回收机制是根据这个操作把引用计数减一，操作结果如下：

	>>> a
	<__main__.A object at 0x0000000002952390>
	>>> a.other
	<__main__.A object at 0x0000000002961AC8>
	>>> a.other.name
	'b'
	>>> a.other.other.name
	'c'
	>>> a.other.other.other.name
	'a'
	>>> del a
	>>> a
	Traceback (most recent call last):
	  File "<stdin>", line 1, in <module>
	NameError: name 'a' is not defined
	>>> b.other.name
	'c'
	>>> b.other.other.name
	'a'
	>>> b.other.other
	<__main__.A object at 0x0000000002952390>

最后的输出证明，你把a删除了，但是b还保留着对a的引用，所以你还可以访问到a，这个也证明，你再删除b，c还保留a和b的引用，
删除c呢？是否a、b、c的都回收了呢？没有，只是被悬空了，a、b、c的引用计数并没有归0，垃圾回收器不能回收a、b、c的资源，所有造成了内存泄露，更详细和具体的讲解在之前推荐的博客里，直接搜索文章名即可，需要翻墙。

可能还有其他的原因造成内存泄露，因为还未接触过，所以不在这赘述。

### 内存泄露解决前2小时
知道程序内存泄露只是第一步，接下来要做的是定位到具体的代码，哪一行的代码导致了内存泄露。如果只是单个文件的脚本文件，通常只需要根据前面两个内存泄露的原因排查一遍，基本可以解决问题所在。如果是比较大型的程序，需要借助内存泄露查找工具，我这里不推荐
哪一款，搜索找到合适的工具是你自己需要根据实际情况来判断的。有些工具好用但是安装麻烦，有些内存工具需要侵入程序里面打断点。因为时间紧凑，我先使用了一款安装简便，使用极简的内存泄露查找工具[objgraph](http://mg.pov.lt/objgraph/)，使用非常简单，如：

	main()
	objgraph.show_growth(limit=10)

只需要在你的程序入口函数（通常也是第一个调用的方法或一块代码行）的后面加上一行代码`objgraph.show_growth(limit=10)`
它会打印出：程序每次运行至结束，对象引用计数增加个数前十的排行。

	tuple                  5228     +5228
	function               1330     +1330
	wrapper_descriptor      967      +967

上面的就是，它表示tuple类型的对象初期被引用了5228次，但是一次运行完成之后，这些引用并没有减少，还保留着5228次引用，也就表明tuple类型的对象造成了内存泄露，这个时候你就可以专注查找tuple类型对象，objgraph对于大型程序和使用太多第三方库的程序可能并不是那么管用，但是就其简单使用的情况，可以迅速的定位一个大概的范围，效率方面是非常值得推荐的。

### 内存泄露解决
只添加了一行代码和运行了多次，问题被定位到一个Python的logging模块的日志处理Handlers对象和Filters对象。
原因：

	def create_logger(settings=None, install_root_handler=True):
    if install_root_handler:
        logging.root.setLevel(logging.NOTSET)
        handler = _get_handler(settings)
        logging.root.addHandler(handler)
        return handler


每次调用create_logger函数时，都会向logging.root添加一个handler，我本以为程序运行结束，logging.root它会自动释放
里面的handler，结果它不会，内部有一个dict，不停的往一个只有系统重启才会清空的dict添加对象，造成的了上面内存泄露的第一个原因。因为logging日志模块的使用不当，造成了本次的内存泄露问题。这里返回这个handler，然后程序结束完手动删除即可。

	logging.root.removeHandler(handler)

本次内存泄露问题就到此结束了。