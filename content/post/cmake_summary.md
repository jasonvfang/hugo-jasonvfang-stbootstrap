+++
banner = ""
categories = ["cmake"]
date = "2018-07-15T23:10:51+08:00"
description = ""
images = []
menu = ""
tags = ["cmake"]
title = "cmake学习简要总结"
+++


今天有时间去研究一下cmake的使用，之前一直是想看一下的，只是时间没排出来，今天总算排上了，收获也挺多，感觉
这样的编译工具确实比我们自己写Makefile管理起来要好的多，这也就是为什么在开源社区会有这么多的开源软件的编译都采用的cmake吧。

不废话了，今天按照官方文档把整个例子跑了一遍，基本上知道如何使用了。


<!--more-->

#### 大致总结一下吧：

* cmake根据CMakeLists.txt文件自动生成Makefile
也就是cmake是可配置的，其次它有一个方便的GUI工具实现配置，这个我暂时没有研究到。

* cmake可指定宏编译选项、子目录、install、目标

常用的也应该就这些，理解这些大致修改足够了：

宏：option()
子目录：
头文件子目录：include_directories  编译子目录：add_subdirectory

install: install()可指定FILES或者TARGETS

add_executable():指定生成的target及需要的src文件

target_link_libraries：指定链接的lib

这里是官方文档中的一个例子，写的很清楚了：
```
cmake_minimum_required (VERSION 2.6)
project (Tutorial)
# The version number.
set (Tutorial_VERSION_MAJOR 1)
set (Tutorial_VERSION_MINOR 0)

 # should we use our own math functions?
option (USE_MYMATH 
        "Use tutorial provided math implementation" ON) 
		
# configure a header file to pass some of the CMake settings
# to the source code
configure_file (
  "${PROJECT_SOURCE_DIR}/TutorialConfig.h.in"
  "${PROJECT_BINARY_DIR}/TutorialConfig.h"
  )
 
# add the binary tree to the search path for include files
# so that we will find TutorialConfig.h
include_directories("${PROJECT_BINARY_DIR}")
 
# add the MathFunctions library?
#
if (USE_MYMATH)
  include_directories ("${PROJECT_SOURCE_DIR}/MathFunctions")
  add_subdirectory (MathFunctions)
  set (EXTRA_LIBS ${EXTRA_LIBS} MathFunctions)
endif (USE_MYMATH)

 	 
# add the executable
add_executable (Tutorial tutorial.cxx)
target_link_libraries (Tutorial  ${EXTRA_LIBS})

# add the install targets
install (TARGETS Tutorial DESTINATION ~/bin)
install (FILES "${PROJECT_BINARY_DIR}/TutorialConfig.h"        
         DESTINATION ~/include)
```
		 
在github中已经把这些步骤的尝试都上传了，可见[这里](https://github.com/jasonvfang/cmake-examples).

参考详细教程：https://cmake.org/cmake-tutorial/

