### 安装问题

`pip install cx_Oracle`

安装好包后，还需下载Oracle的客户端，必须要登录才能下载

[原文在这](https://oracle.github.io/odpi/doc/installation.html#oracle-client-and-database-versions)

根据提示安装好Oracle的客户端后，环境变量的设置分很重要，这里举例说明：

- 安装Oracle客户
	
	mkdir -p /opt/oracle
	cd /opt/oracle
	unzip instantclient-basic-linux.x64-12.2.0.1.0.zip

- 安装libaio包

	sudo yum install libaio

- 设置Oracle客户端的环境变量，它的路径和你下载的包的路径有关，一定要关联起来
	
	export LD_LIBRARY_PATH=/opt/oracle/instantclient_12_2:$LD_LIBRARY_PATH

- Virtualenv环境下使用cx_Oracle常见问题之一是找不到Oracle客户端的环境变量，原因之一就是因为你的虚拟环境文件夹在
上面的步骤执行之前生成的，导致它启动虚拟环境的脚本activae没有写入这个环境变量，修改即可。

	vim /xxx/venv/Scripts/activate
	添加
	export LD_LIBRARY_PATH=/opt/oracle/instantclient_12_2
	他就可以在虚拟环境下使用了，

### 中文乱码问题

在对Oracle数据操作时，必须要解决的就是字符集的问题，这里有三个主要地方，数据库的字符集设置，程序运行环境的字符集，和需要插入数据的的字符集。

++三者统一，则显示正常++。

还有就是可以不管数据库字符集是什么，程序运行环境的字符集和插入数据的字符集一致也可以显示正常。

### python如何设置运行环境字符集?

python设置环境变量可以使用:

```
import os

os.environ['NLS_LANG'] = 'SIMPLIFIED CHINESE_CHINA.AL32UTF8'

```

这两行代码的位置很重要，如果你的python脚本只是一个单文件，那么就放在该脚本里，如果你的python代码不只是一个文件，它是一个大型程序，那么这两行代码必须放在运行脚本里，也就是有`if __name__ == '__main__:'`的文件里。

原因是， 这种设置的环境变量是临时的，只针对当前进程有效，并且当前进程下是有的子进程、线程都是有效。所以要设置环境变量必须设置的父进程的开始处。

### python如何处理编码问题?
字符串在Python内部的表示是unicode编码，因此，在做编码转换时，通常需要以unicode作为中间编码，即先将其他编码的字符串解码（decode）成unicode，再从unicode编码（encode）成另一种编码。 
decode的作用是将其他编码的字符串转换成unicode编码，如str1.decode('gb2312')，表示将gb2312编码的字符串str1转换成unicode编码。 

encode的作用是将unicode编码转换成其他编码的字符串，如str2.encode('gb2312')，表示将unicode编码的字符串str2转换成gb2312编码。 

因此，转码的时候一定要先搞明白，字符串str是什么编码，然后decode成unicode，然后再encode成其他编码

