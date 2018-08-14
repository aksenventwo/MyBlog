## 开始使用ANTLR V4


官方有一个安装教程，是英文版，写的太不太小白。链接在这[Install ANTLR](https://github.com/antlr/antlr4/blob/master/doc/getting-started.md)

### WINDOWS

1. 安装java，需要安装1.6版本以上的jdk, 搜索JAVA安装教程即可，配置好环境变量，保证java安装成功。
2. 下载 antlr-x.x.x-complete.jar java包，http://www.antlr.org/download/，然后在C盘新建一个目录Javalib，绝对路径是`C:\Javalib`，把下载的包放入该目录下。
3. 把 `antlr-x.x.x-complete.jar`添加到`CLASSPATH`环境中，在系统环境变量新建`CLASSPATH`，它的值为`C:\Javalib\antlr-x.x.x-complete.jar`
4. 编写一个`antlr4.bat`脚本，放在系统目录下，如：`C:\Windows\System32`，脚本内容如下：`java org.antlr.v4.Tool %*`
5. 测试是否设置成功，打开cmd，执行`antlr4`, 如下表示成功:

```shell
$ java org.antlr.v4.Tool
ANTLR Parser Generator  Version 4.7.1
 -o ___              specify output directory where all output is generated
 -lib ___            specify location of grammars, tokens files
 -atn                generate rule augmented transition network diagrams
 -encoding ___       specify grammar file encoding; e.g., euc-jp
 -message-format ___ specify output style for messages in antlr, gnu, vs2005
 -long-messages      show exception details when available for errors and warnings
 -listener           generate parse tree listener (default)
 -no-listener        don't generate parse tree listener
 -visitor            generate parse tree visitor
 -no-visitor         don't generate parse tree visitor (default)
 -package ___        specify a package/namespace for the generated code
 -depend             generate file dependencies
 -D<option>=value    set/override a grammar-level option
 -Werror             treat warnings as errors
 -XdbgST             launch StringTemplate visualizer on generated code
 -XdbgSTWait         wait for STViz to close before continuing
 -Xforce-atn         use the ATN simulator for all predictions
 -Xlog               dump lots of logging info to antlr-timestamp.log
 -Xexact-output-dir  all output goes into -o dir regardless of paths/package
```