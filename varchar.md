### 背景
提交申请请求里某个字段要求100字节内，该字段为40个中文，请求报错。

### utf8
由于使用utf8编码，所以普通中文一个文字对应三个字节。

- js计算字符串utf8字节数
https://www.jianshu.com/p/b222a15da0e4


### 数据库
oracle里VARCHAR2
mysql varchar表示字符。即varchar(10)即可写入10个中文


MySQL中
char是定长的，varchar是变长的。

oracle中
CHAR的长度是固定的，而VARCHAR2的长度是可以变化的

在oracle中有三种比较常用的类型：varchar2(byte)、varchar2(char)、nvarchar2()
无论是varchar2还是nvarchar2，最大字节数都是4000
所以，对于GBK编码的数据库而言，安全的写法为：varchar2(2000 char)、nvarchar2(2000)，对于UTF8编码的数据库而言，安全的写法为：varchar2(1333 char)、nvarchar2(2000)。

#### varchar2(byte)
就是默认的表示方式，比如我们写成：varchar2(100)，就相当于varchar2(100 byte)，表示最大字节数是100，该字段最多能容纳100个字节，强调空间大小。由于我们描述的是字节，因此，保存汉字等字符时，就要小心了。如果你的数据库用的是GBK编码，那么一个汉字将占用2个字节，最多能存50个汉字，如果你的数据库用的是UTF8编码，那么一个汉字将占用3个字节，最多能存33个汉字。

#### varchar2(char)
表示最大字符数是100，该字段最多能容纳100个字符，强调个数。假设我们写成varchar2(100 char)，那么无论是数字、字母、汉字，都看成一个字符，最多写100个，当然，汉字越多，占用的空间越大，同样遵循的数据库编码原则。例如：存入一个汉字，底层占2或3个字节，存入一个字母，占1个字节，绝对不是某些文章所说1个字母或数字也占2或3个字节！

#### nvarchar2()
没有byte、char之分，类似于varchar2(char)，只不过nvarchar2()屏蔽了数据库编码，无论是何种编码，nvarchar2()中一个汉字都占两个字节