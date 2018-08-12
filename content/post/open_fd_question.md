+++
banner = ""
categories = ["Programming"]
date = "2018-07-18T00:10:51+08:00"
description = ""
images = []
menu = ""
tags = ["linux c"]
title = "Linux FD操作问题"
+++


主要总结一之前工作中遇到的问题，在对一个fifo进行操作的时候出现无法open的情况，但是fifo文件是一直存在的，百思不得解，
后面还是同事的提醒，才想起看到man手册中的说明，先上代码吧，自己写的一个小程序测试：
![Demo 代码](/images/open_demo_codes.png)

注意这里的模式是只写模式加非阻塞模式,运行结果是：
![运行结果](/images/open_error.png)

MAN手册上的说明：
![MAN解释](/images/man_open_flag.png)

## 总结：
使用O_RDONLY或O_WRONLY设置打开FIFO时:

* 如果设置了O_NONBLOCK，则open()用于读取时只会立即返回。如果当前没有任何进程打开文件进行读取，则以只写的方式写FD时会返回一个错误。
* 如果O_NONBLOCK没有指定，那么只读的open()将阻塞调用线程，直到线程打开文件进行写入。以只写方式open()调用将会阻塞调用线程，直到线程打开文件进行读取。


因此本例中的解决办法：

* 1.可以设置open的方式为block方式  
* 2. 在write之前，其他进程一直在读fd
