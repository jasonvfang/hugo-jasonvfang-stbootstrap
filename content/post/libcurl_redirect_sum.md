
+++
banner = ""
categories = ["Programming"]
date = "2018-06-23T12:10:51+08:00"
description = ""
images = []
menu = ""
tags = ["libcurl"]
title = "Libcurl中的HTTP重定向问题"
+++


最近一调试AMAZON的Prime Music音源接口，主要是HTTP POST/GET这种方法获取的JSON结果进行解析，工作内容相对比较简单，但是需要注意的地方比较多：

<!--more-->

#### 几个注意点
* JSON的内存释放
* HTTP请求RESP是否有重定向问题
在处理这个HTTP返回时就有遇到过重定向的坑，导致浪费太多的时间

##### CURL的HTTP选项

基本参考CURL的SAMPLE CODE就可以完成：
先上大致的code:
```
        curl_easy_setopt(g_curl_handle, CURLOPT_SSL_VERIFYPEER, 1L);
        curl_easy_setopt(g_curl_handle, CURLOPT_SSL_VERIFYHOST, 2L);
        curl_easy_setopt(g_curl_handle, CURLOPT_MAXCONNECTS, 10);
        curl_easy_setopt(g_curl_handle, CURLOPT_TIMEOUT, 15);
        curl_easy_setopt(g_curl_handle, CURLOPT_SERVER_RESPONSE_TIMEOUT, 15);
        curl_easy_setopt(g_curl_handle, CURLOPT_DEBUGFUNCTION, my_trace);
        curl_easy_setopt(g_curl_handle, CURLOPT_DEBUGDATA, &config);
        curl_easy_setopt(g_curl_handle, CURLOPT_WRITEFUNCTION, write_data);
        curl_easy_setopt(g_curl_handle, CURLOPT_WRITEDATA, result_buffer);
        curl_easy_setopt(g_curl_handle, CURLOPT_HEADERFUNCTION, header_callback);
        curl_easy_setopt(g_curl_handle, CURLOPT_WRITEHEADER, header_buffer);
        curl_easy_setopt(g_curl_handle, CURLOPT_USERAGENT, "libcurl-agent/1.0");
        curl_easy_setopt(g_curl_handle, CURLOPT_NOSIGNAL, 1L); 
```
 
在执行http request的时候一直得到的空数据，利用POSTMAN进行模拟都能够正确返回数据，后来通过命令行CURL模拟发现HTTP STATUS返回的是3XX，于是才恍然大悟，需要设置CURL的重定向跟踪功能：
```    
//follow redirect url
curl_easy_setopt(g_curl_handle, CURLOPT_FOLLOWLOCATION, 1L);
```
加上这个设置后完美解决，建议后面实现HTTP请求的所有功能都应该加上这个设置，避免得到空数据。


