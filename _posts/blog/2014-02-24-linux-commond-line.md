---
layout:		post
title:		Linux（Mac）常用命令-持续更新
description: Mac做开发确实不错，命令操作比Windows强很多，命令的操作和Linux差不多，
category:	blog
---

## cp命令强制覆盖方式实现
方法一：
输入alias命令，看到系统内部使用的是cp的别名。

	#alias
	#alias cp='cp -i'

输入unalias cp命令，解除别名。

	#unaslias cp   //（这只是临时取消cp的别名，不是永久的）
	#cp a test/a   //呵呵，这下正常了吧。

方法二：
输入\cp命令，作用也是取消cp的别名。

	#\cp a test\a    //呵呵，这么用也一样好使。

方法三：
输入yes|cp a test\a，使用管道自动输入yes。

	#yes | cp a test\a   //看到了吧，自动打出一堆yes，替你输入了。

呵呵，至于以上方法你喜欢那种，就由你自己选择了。

## Linux tar打包命令

将整个 /etc 目录下的文件全部打包成为 /tmp/etc.tar

	[root@linux ~]# tar -cvf /tmp/etc.tar /etc <==仅打包，不压缩！
	[root@linux ~]# tar -zcvf /tmp/etc.tar.gz /etc <==打包后，以 gzip 压缩
	[root@linux ~]# tar -jcvf /tmp/etc.tar.bz2 /etc <==打包后，以 bzip2 压缩
	# 特别注意，在参数 f 之后的文件档名是自己取的，我们习惯上都用 .tar 来作为辨识。
	# 如果加 z 参数，则以 .tar.gz 或 .tgz 来代表 gzip 压缩过的 tar file ～
	# 如果加 j 参数，则以 .tar.bz2 来作为附档名啊～
	# 上述指令在执行的时候，会显示一个警告讯息：
	# 『tar: Removing leading `/” from member names』那是关於绝对路径的特殊设定。

范例二：查阅上述 /tmp/etc.tar.gz 文件内有哪些文件？

	[root@linux ~]# tar -ztvf /tmp/etc.tar.gz
	# 由於我们使用 gzip 压缩，所以要查阅该 tar file 内的文件时，
	# 就得要加上 z 这个参数了！这很重要的！

范例三：将 /tmp/etc.tar.gz 文件解压缩在 /usr/local/src 底下
	
	[root@linux ~]# cd /usr/local/src
	[root@linux src]# tar -zxvf /tmp/etc.tar.gz
	# 在预设的情况下，我们可以将压缩档在任何地方解开的！以这个范例来说，
	# 我先将工作目录变换到 /usr/local/src 底下，并且解开 /tmp/etc.tar.gz ，
	# 则解开的目录会在 /usr/local/src/etc 呢！另外，如果您进入 /usr/local/src/etc
	# 则会发现，该目录下的文件属性与 /etc/ 可能会有所不同喔！

范例四：在 /tmp 底下，我只想要将 /tmp/etc.tar.gz 内的 etc/passwd 解开而已
	
	[root@linux ~]# cd /tmp
	[root@linux tmp]# tar -zxvf /tmp/etc.tar.gz etc/passwd
	# 我可以透过 tar -ztvf 来查阅 tarfile 内的文件名称，如果单只要一个文件，
	# 就可以透过这个方式来下达！注意到！ etc.tar.gz 内的根目录 / 是被拿掉了！

范例五：将 /etc/ 内的所有文件备份下来，并且保存其权限！
	
	[root@linux ~]# tar -zxvpf /tmp/etc.tar.gz /etc
	# 这个 -p 的属性是很重要的，尤其是当您要保留原本文件的属性时！

范例六：在 /home 当中，比 2005/06/01 新的文件才备份

	[root@linux ~]# tar -N ‘2005/06/01′ -zcvf home.tar.gz /home

范例七：我要备份 /home, /etc ，但不要 /home/dmtsai

	[root@linux ~]# tar –exclude /home/dmtsai -zcvf myfile.tar.gz /home/* /etc

范例八：将 /etc/ 打包后直接解开在 /tmp 底下，而不产生文件！
	
	[root@linux ~]# cd /tmp
	[root@linux tmp]# tar -cvf – /etc | tar -xvf –
	# 这个动作有点像是 cp -r /etc /tmp 啦～依旧是有其有用途的！
	# 要注意的地方在於输出档变成 – 而输入档也变成 – ，又有一个 | 存在～
	# 这分别代表 standard output, standard input 与管线命令啦！
	# 这部分我们会在 Bash shell 时，再次提到这个指令跟大家再解释啰！

.tar
解包：

	tar xvf FileName.tar

打包：

	tar cvf FileName.tar DirName

（注：tar是打包，不是压缩！）
———————————————
.gz

	解压1：gunzip FileName.gz
	解压2：gzip -d FileName.gz
	压缩：gzip FileName
	.tar.gz 和 .tgz
	解压：tar zxvf FileName.tar.gz
	压缩：tar zcvf FileName.tar.gz DirName

———————————————
.bz2

	解压1：bzip2 -d FileName.bz2
	解压2：bunzip2 FileName.bz2
	压缩： bzip2 -z FileName
	.tar.bz2
	解压：tar jxvf FileName.tar.bz2 或tar –bzip xvf FileName.tar.bz2
	压缩：tar jcvf FileName.tar.bz2 DirName

———————————————
.bz

	解压1：bzip2 -d FileName.bz
	解压2：bunzip2 FileName.bz
	压缩：未知
	.tar.bz
	解压：tar jxvf FileName.tar.bz
	压缩：未知

———————————————
.Z

	解压：uncompress FileName.Z
	压缩：compress FileName
	.tar.Z
	解压：tar Zxvf FileName.tar.Z
	压缩：tar Zcvf FileName.tar.Z DirName

———————————————
.zip

	解压：unzip FileName.zip
	压缩：zip FileName.zip DirName
	压缩一个目录使用 -r 参数，-r 递归。例： $ zip -r FileName.zip DirName

———————————————
.rar

	解压：rar x FileName.rar
	压缩：rar a FileName.rar DirName
	rar请到：http://www.rarsoft.com/download.htm 下载！
	解压后请将rar_static拷贝到/usr/bin目录（其他由$PATH环境变量指定的目录也可以）：

