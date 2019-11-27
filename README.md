http://localhost:2000/

v1、v3都是可以运行的版本，但是在v3上开发。

文件的读写，还没有掌握。

## 20191126

### bug

1.三个上传框，若只选择二个上传文件，服务器会出错，浏览器收不到回应。

### todo

* List

1.自动识别请求方式（GET或POST），并进行相应处理。 -- OK
	
2.转发动态请求给CGI（如PHP）处理。

	获取GET参数
	获取POST数据（表单数据：文本 + 文件）

	转发动态请求，是直接将Uri转发过去，还是需要附加上获取到的数据呢？

	不知道，尝试吧。

	转发PHP文件。

3.代码封装

已经无力再封装了，封装耗费了很多时间

20191127

1.完善和彻底理解fastcig协议

从 php 获取中文后，输出内容，乱码，如何解决？

在java中直接输出中文，不乱码。

乱码原因，读取的时候，8字节为一次读取单位，然后进行了字符串转换（可能刚好不足以转为完整的中文）。

解决措施，调整为1024 * 8字节为一次读取单位，我觉得仍然会存在问题。

2.完善POST和GET请求

有针对静态页面的POST请求吗？

先进行动态请求与静态请求的区分与处理，

再进行GET与POST请求的处理：看上一步的结果，有，返回该结果；无，返回静态资源。

fastcgi,header type == 4 时，传递的数据全部在PHP的$_SERVER中。

如何向 PHP-FPM 传递 $_FILES、$_POST 数据？

fastcig 协议

https://fastcgi-archives.github.io/FastCGI_Specification.html

web服务器是把从浏览器接收到的数据，全部原样交给php-fpm，由php-fpm去解析entitybody吗？

基本可以确定，是的。

/Users/cg/data/www/cg/tool/tmp.php:65:
array (size=1)
  'picture' =>
    array (size=5)
      'name' => string 'tooopen_sy_104114411474007.jpg' (length=30)
      'type' => string '' (length=0)
      'tmp_name' => string '' (length=0)
      'error' => int 3
      'size' => int 0

上传图片，tmp_name 和 size 还缺乏，需要web服务器深入到 entity 去补充这些数据吗？

PHP 文件不存在时，php-fpm返回下面的数据
Primary script unknown
 . k Status: 404 Not Found
X-Powered-By: PHP/7.2.12
Content-type: text/html; charset=UTF-8

File not found.

已经完善。

PHP 404，网页不正常，此时调试效率已经非常低。

20191128

1.反向代理

location / {
               proxy_pass http://myServer;     # 注意这里的写法，必须加上 http
              #root   /Users/cg/data/www/code;
              #index  index.html index.htm;
          }

upstream myServer{
          server 127.0.0.1:2000; # 注意这里的写法，不能加上 http
          server 127.0.0.1:2000;
  }

本服务器接收 HTTP 请求报文后，将其转发给目标服务器，然后将数据返回给客户端。

1.接收全部数据后，再转发

2.一边接收数据，一边转发

采用第二种。此种思路实现反向代理（不包括负载均衡），通过验证了，还有细节需要完善。



