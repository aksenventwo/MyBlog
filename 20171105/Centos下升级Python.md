## Centos下Python升级为2.7教程

1.创建一个文件夹，用于存放Python的安装包，我们通过下载安装包升级

	# mkdir /opt/python
	# cd /opt/pyhton
	# wget http://www.python.org/ftp/python/2.7.10/Python-2.7.10.tar.xz //网址可以自己去官网选择自己想安装的python版本
	# unzip Python-2.7.10.tar.xz
	# tar -vxf Python-2.7.10.tar //解压Python安装包
	# cd Python-2.7.10

- 进入Python文件夹后，对其进行编译配置，--enable-shared这个参数很重要，它决定后续是否能顺利升级，
  --enable-loadable-sqlite-extensions这个参数是sqlite的扩展，它作为一个文件数据库，以后需要用到，最好带上
  可以省去以后很多事。

	# ./configure --enable-shared --enable-loadable-sqlite-extensions --with-zlib

- 之后执行

	# vi ./Modules/Setup
  
- 找到下面这行注释，把前面的#号去掉，然后保存退出

	#zlib zlibmodule.c -I$(prefix)/include -L$(exec_prefix)/lib -lz
 
- 然后进行编译和安装

	# make && make install

- 编译成功之后，我们需要把之前的Python2.6版备份，备份的主要目的是之前安装的软件有依赖关系，如yum，不备份使用不了。
  执行以下命令，对Python2.6进行备份，然后为Python2.7创建软链接

	# mv /usr/bin/python /usr/bin/python2.6.6
	# ln -s /usr/local/bin/python2.7 /usr/bin/python

- 接下来修改yum的配置文件，将第一行的 `#!/usr/bin/python` 修改成 `#!/usr/bin/python2.6.6`

	# vim /usr/bin/yum 

- 可以测试一下yum是否可以正常使用，yum --help，没有错误即可正常使用。

- 然后测试下Python是否成功，显示Python2.7的版本，Python -V，有可能会出现个错误

	error while loading shared libraries: libpython2.7.so.1.0: cannot open shared object file:
	No such file or directory

- 解决办法(2种)
1. `vim /etc/ld.so.conf` 添加新的一行内容/usr/local/lib，保存退出，然后

	# /sbin/ldconfig  
	# /sbin/ldconfig -v

2. whereis libpython2.7.so.1.0，找到这个文件，我的是在/usr/local/bin 目录下，
	
	# /usr/local/bin/libpython2.7.so.1.0
	# cp /usr/local/bin/libpython2.7.so.1.0 /usr/bin64(64位，32位的去掉64即可)

至此，Python升级完毕。 