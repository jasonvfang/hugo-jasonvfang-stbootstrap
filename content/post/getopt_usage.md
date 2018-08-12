+++
banner = ""
categories = ["programming"]
date = "2018-08-05T13:32:51+08:00"
description = ""
images = []
menu = ""
tags = ["linux-c"]
title = "Linux 命令行参数处理"
+++

前几天遇到对不同情况下，有个命令的处理条件需要区别的任务，看了下原始的代码，发现难以改动，太多的HARD CODE了，直接使用argv[i]的方式获取参数值显得特别
不好修改，于是想到Linux下有专门的getopt方法，好在不算改动特别大，对以前的做法作了兼容处理，好吧，这里还是对getopt作个简要的整理吧，方便日后回顾。

### 简要说明

getopt/getopt_long都是linux平台下为应用程序提供处理命令行参数的接口，区别在于后者有参数名称长度较长的情况，一般在Linux下的命令行参数以横框“-”或者双横杠
“--”进行传递，比如我们经常用到的ls -l 或者 ls --help ，其中的l和help为选项。

### 如何在程序中使用
<!--more-->
在man手册上的定义如下：

```
#include <unistd.h>

int getopt(int argc, char * const argv[], const char *optstring);

extern char *optarg;
extern int optind, opterr, optopt;

#include <getopt.h>

int getopt_long(int argc, char * const argv[], const char *optstring,
			const struct option *longopts, int *longindex);
```

这两个API的参数不用多说了，需要注意的是getopt/getopt_long需要循环调用处理命令行参数，也就是一般在while循环中调用。

一般参数选项有需要接值的类型和不需指定参数值以及可选指定值三种情况，不同的情况有特定的写法，

对于getopt来讲：

* 需要指定参数值要在选项后面接一个冒号
* 不需要指定参数值则选项后面不接任何符号
* 可选参数值则在选项后接两个冒号

对于getopt_long来说，在option结构体中
struct option {
               const char *name;
               int         has_arg;
               int        *flag;
               int         val;
           };
的has_arg变量有三个值可以指定：

* no_argument 
* required_argument 
* optional_argument 


### 返回值

对于短的选项：
如果getopt/getopt_log在命令行参数中找到相应字符，则会返回该选项的字符，如果所有的命令参数选项都解析完成，则返回-1，
如果发现命令行参数选项中的字符不在指定的optstring中出现，则返回特殊的问号字符？。如果遇到该参数选项需要指定值而命令行中未指定时，
返回值取决于optstring中的第一个冒号字符：，如果它是冒号，则返回：，否则返回？。

对于长选项：
正常情况下，如果flag为NULL，则返回option结构体中的val值，否则返回0。其他出错，未匹配以及-1的情况同短选项是一样的。


### 两个示例

官网给出的两个示例非常实用：

1. getopt

```
       #include <unistd.h>
       #include <stdlib.h>
       #include <stdio.h>

       int
       main(int argc, char *argv[])
       {
           int flags, opt;
           int nsecs, tfnd;

           nsecs = 0;
           tfnd = 0;
           flags = 0;
           while ((opt = getopt(argc, argv, "nt:")) != -1) {
               switch (opt) {
               case 'n':
                   flags = 1;
                   break;
               case 't':
                   nsecs = atoi(optarg);
                   tfnd = 1;
                   break;
               default: /* '?' */
                   fprintf(stderr, "Usage: %s [-t nsecs] [-n] name\n",
                           argv[0]);
                   exit(EXIT_FAILURE);
               }
           }

           printf("flags=%d; tfnd=%d; nsecs=%d; optind=%d\n",
                   flags, tfnd, nsecs, optind);

           if (optind >= argc) {
               fprintf(stderr, "Expected argument after options\n");
               exit(EXIT_FAILURE);
           }

           printf("name argument = %s\n", argv[optind]);

           /* Other code omitted */

           exit(EXIT_SUCCESS);
       }
```

2. getopt_long

```
		#include <stdio.h>     /* for printf */ 
		#include <stdlib.h>    /* for exit */ 
		#include <getopt.h>

        int main(int argc, char **argv) {
           int c;
           int digit_optind = 0;

           while (1) {
               int this_option_optind = optind ? optind : 1;
               int option_index = 0;
               static struct option long_options[] = {
                   {"add",     required_argument, 0,  0 },
                   {"append",  no_argument,       0,  0 },
                   {"delete",  required_argument, 0,  0 },
                   {"verbose", no_argument,       0,  0 },
                   {"create",  required_argument, 0, 'c'},
                   {"file",    required_argument, 0,  0 },
                   {0,         0,                 0,  0 }
               };

               c = getopt_long(argc, argv, "abc:d:012",
                        long_options, &option_index);
               if (c == -1)
                   break;

               switch (c) {
               case 0:
                   printf("option %s", long_options[option_index].name);
                   if (optarg)
                       printf(" with arg %s", optarg);
                   printf("\n");
                   break;

               case '0':
               case '1':
               case '2':
                   if (digit_optind != 0 && digit_optind != this_option_optind)
                   printf("digits occur in two different argv elements.\n");
                   digit_optind = this_option_optind;
                   printf("option %c\n", c);
                   break;

               case 'a':
                   printf("option a\n");
                   break;

               case 'b':
                   printf("option b\n");
                   break;

               case 'c':
                   printf("option c with value '%s'\n", optarg);
                   break;

               case 'd':
                   printf("option d with value '%s'\n", optarg);
                   break;

               case '?':
                   break;

               default:
                   printf("?? getopt returned character code 0%o ??\n", c);
               }
           }

           if (optind < argc) {
               printf("non-option ARGV-elements: ");
               while (optind < argc)
                   printf("%s ", argv[optind++]);
               printf("\n");
           }

           exit(EXIT_SUCCESS); }
```

超详细参考资料在[这里](http://man7.org/linux/man-pages/man3/getopt.3.html).
