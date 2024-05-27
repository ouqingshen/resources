# Bug记录

### 1.乱码

**现象**

​		编译通过、运行通过

![image-20231106230450303](https://gitee.com/ou-qingshen/markdown/raw/master/image-20231106230450303-16992854916522.png)

**根本原因：**

![image-20240527203123923](C:/Users/ikun/AppData/Roaming/Typora/typora-user-images/image-20240527203123923.png)

​		vs显示无乱码->源码字符集正确

​		编译通过->编译字符集正确

​		执行有乱码->执行字符集不正常

​		源码字符集与执行字符集不一致，GCC的源码字符集与执行字符集默认是UTF-8编码，这是因为现在的Linux系统大多使用UTF-8编码。就算调整了Linux系统语言后，只是区域发生了变化，字符编码依然是UTF-8。所以我们的程序在“简体中文”与“英语”下，均能正确的显示中文字符。
　　MinGW中的GCC也是这样的，源码字符集与执行字符集默认是UTF-8编码。但是简体中文的Windows的默认编码是GB2312，会将printf输出UTF-8字符串误认为是GB2312，造成乱码。

**解决方案：**

1. ##### **源码字符集的设置**

​		（1）将所有的源代码格式设置为UTF-8（no Bom），VS：扩展—>搜索Force UTF-8（no Bom）—>关闭重启

![image-20231106232320714](https://gitee.com/ou-qingshen/markdown/raw/master/image-20231106232320714.png)

​		

​		（2）添加：/source-charset:utf-8

![image-20231106232607624](https://gitee.com/ou-qingshen/markdown/raw/master/image-20231106232607624-16992855068795.png)

2. ##### **执行字符集的设置**

   命令行添加：/execution-charset:utf-8 

![image-20231106233002744](https://gitee.com/ou-qingshen/markdown/raw/master/image-20231106233002744-16992855114728.png)

### 2.出现大量语法错误

**现象：**

​		编译不通过，明显语法没有错误，编译出现大量的错误

![image-20231108213720896](https://gitee.com/ou-qingshen/markdown/raw/master/image-20231108213720896.png)

**根本原因：**

1. 源码字符集与编译字符集不一致

**解决方案：**

​	在命令行追加/source-charset:utf-8 /execution-charset:utf-8 

![image-20231109122230384](https://gitee.com/ou-qingshen/markdown/raw/master/image-20231109122230384.png)

### 3.编译器、编译套件等问题

##### 1.打开项目出现错误>>> qmake(16968): Project ERROR: msvc-version.conf loaded but QMAKE_MSC_VER isn't set

**现象：**

![image-20231129220235374](https://gitee.com/ou-qingshen/markdown/raw/master/image-20231129220235374.png)

**错误原因：**

Qt中的版本与VS的版本不匹配

D:\Qt5.12.9\msvc2017_64\mkspecs\common\msvc-version.conf

**总结：**在Windows系统上设置`QMAKE_MSC_VER`环境变量通常是为了告诉Qt使用的MSVC（Microsoft Visual C++）编译器的版本。这个环境变量的值应该与当前使用的Visual Studio版本相对应。

**解决办法：**

1. **找到Visual Studio安装目录：** 在通常情况下，Visual Studio被安装在`C:\Program Files (x86)\Microsoft Visual Studio`目录下。
2. **找到MSVC版本号：** 进入Visual Studio安装目录，然后进入`VC\Tools\MSVC`目录，你会看到一个或多个以版本号命名的子目录，比如`14.0xxxxx`、`14.1`xxxxxxx、`14.3x`xxxxxx等。这个版本号就是需要设置的`QMAKE_MSC_VER`的值。![image-20231129221006595](https://gitee.com/ou-qingshen/markdown/raw/master/image-20231129221006595.png)
3. **根据下载安装QT路径修改：msvc-version.conf配置文件**
   D:\Qt\Qt5.12.9\5.12.9\msvc2017_64\mkspecs\common\msvc-version.conf

![image-20231129220809867](https://gitee.com/ou-qingshen/markdown/raw/master/image-20231129220809867.png)

我用的是msvc_2017所有对应的msvc版本是1916

**对应表如下：**

```null
MSC 1.0 _MSC_VER == 100
MSC 2.0 _MSC_VER == 200
MSC 3.0 _MSC_VER == 300
MSC 4.0 _MSC_VER == 400
MSC 5.0 _MSC_VER == 500
MSC 6.0 _MSC_VER == 600
MSC 7.0 _MSC_VER == 700
MSVC++ 1.0 _MSC_VER == 800
MSVC++ 2.0 _MSC_VER == 900
MSVC++ 4.0 _MSC_VER == 1000 (Developer Studio 4.0)
MSVC++ 4.2 _MSC_VER == 1020 (Developer Studio 4.2)
MSVC++ 5.0 _MSC_VER == 1100 (Visual Studio 97 version 5.0)
MSVC++ 6.0 _MSC_VER == 1200 (Visual Studio 6.0 version 6.0)
MSVC++ 7.0 _MSC_VER == 1300 (Visual Studio .NET 2002 version 7.0)
MSVC++ 7.1 _MSC_VER == 1310 (Visual Studio .NET 2003 version 7.1)
MSVC++ 8.0 _MSC_VER == 1400 (Visual Studio 2005 version 8.0)
MSVC++ 9.0 _MSC_VER == 1500 (Visual Studio 2008 version 9.0)
MSVC++ 10.0 _MSC_VER == 1600 (Visual Studio 2010 version 10.0)
MSVC++ 11.0 _MSC_VER == 1700 (Visual Studio 2012 version 11.0)
MSVC++ 12.0 _MSC_VER == 1800 (Visual Studio 2013 version 12.0)
MSVC++ 14.0 _MSC_VER == 1900 (Visual Studio 2015 version 14.0)
MSVC++ 14.1 _MSC_VER == 1910 (Visual Studio 2017 version 15.0)
MSVC++ 14.11 _MSC_VER == 1911 (Visual Studio 2017 version 15.3)
MSVC++ 14.12 _MSC_VER == 1912 (Visual Studio 2017 version 15.5)
MSVC++ 14.13 _MSC_VER == 1913 (Visual Studio 2017 version 15.6)
MSVC++ 14.14 _MSC_VER == 1914 (Visual Studio 2017 version 15.7)
MSVC++ 14.15 _MSC_VER == 1915 (Visual Studio 2017 version 15.8)
MSVC++ 14.16 _MSC_VER == 1916 (Visual Studio 2017 version 15.9)
MSVC++ 14.2 _MSC_VER == 1920 (Visual Studio 2019 Version 16.0)
MSVC++ 14.21 _MSC_VER == 1921 (Visual Studio 2019 Version 16.1)
MSVC++ 14.22 _MSC_VER == 1922 (Visual Studio 2019 Version 16.2)
```

##### 2.编译套件配置路径错误导致

### 4.QT+VS+FFmpeg编译找不到库

![image-20240106104221786](https://gitee.com/ou-qingshen/markdown/raw/master/image-20240106104221786.png)

![image-20240106104320454](https://gitee.com/ou-qingshen/markdown/raw/master/image-20240106104320454.png)

项目->属性->C/C++->附加包含目录

![image-20240106104527988](https://gitee.com/ou-qingshen/markdown/raw/master/image-20240106104527988.png)

![image-20240106104634876](https://gitee.com/ou-qingshen/markdown/raw/master/image-20240106104634876.png)

再不行就将FFmpeg的bin目录下的所有文件*.dll都拷贝到我们的运行环境下（.exe所在的目录）

动态库下载路径https://github.com/BtbN/FFmpeg-Builds/releases?page=4

![image-20240106113003741](https://gitee.com/ou-qingshen/markdown/raw/master/image-20240106113003741.png)

![image-20240106113104030](https://gitee.com/ou-qingshen/markdown/raw/master/image-20240106113104030.png)

### 5.“is not a member of ‘std’

错误原因是：C＋＋标准库实现有问题，解决方法如下：

在主源代码中加入：#include <stddef.h>

然后出错的地方，去掉 "std::"

### 6.LINK : fatal error LNK1168: 无法打开 ...\LessWidgetPro.exe 进行写入

编译的exe文件正在运行，而再次编译是要修改这个exe文件的，因为不让写，所以就抱着这个错。解决办法是看有没有运行编译后文件，如果正运行着，就将其关掉。

方法1（比较靠谱）：

进入项目所在文件夹，强制删除debug文件夹（使用360解除占用或者使用360强力删除）。注意：解决方案下，项目下共两个debug。

方法2：

打开任务管理器，找到***.exe 把这个进程关掉就可以了。

方法3:

 开始运行，输入taskill -im .exe -f 回车就行了，也是结束进程.exe。

### 7.[ERR] 1273 - Unknown collation: 'utf8mb4_0900_ai_ci'

原因：这是8.0以上的新版本数据库的编码，旧版本不支持

更新数据库版本或者更改为旧版本支持的编码格式

推荐：更改编码格式

复制数据库脚本到word，全部替换为utf8mb4_unicode_ci

### 8.springboot项目启动命令过长！-Error running Application. Command line is too long. Shorten the command line via JAR manifest or via a classpath file and rerun

1. 点击项目启动配置项
2. *shorten command line* 选项选择 JAR manifest 或者 classpath file 选项
3. 重新启动工程运行即可

![](https://gitee.com/ou-qingshen/markdown/raw/master/1709906746564-f5718283-4321-4e77-982e-a4c22333aa3a.png)

### 9.sha256加盐加密踩坑

注册：

```c++
 QByteArray salt = QCryptographicHash::hash(QByteArray::number(QRandomGenerator::global()->bounded(1000000000)), QCryptographicHash::Sha256);
 qDebug() << salt << endl;
 QByteArray passwordHash = QCryptographicHash::hash((password + salt).toUtf8(), QCryptographicHash::Sha256);
 qDebug() << passwordHash;

 QString sql = "INSERT INTO users (username, salt ,password_hash) VALUES (:username,:salt, :password_hash )";
 QSqlQuery query;

 query.prepare(sql);
 query.bindValue(":username", userName);
 query.bindValue(":salt", salt.toHex()); 
 query.bindValue(":password_hash", passwordHash.toHex());
```

注册时存储在数据库中的salt是经过了从ByteArray经过toHex()的转换，登录的时候不要将salt取出来后直接toByteArray()否则这样的盐值是无法读取的，而是需要将salt以字符串的形式读取，然后使用ByteArray::fromHex()转换为ByteArray

```c++
    const QByteArray storedHash = query.value(0).toByteArray();
    const QString salt111 = query.value(1).toString();
    const QByteArray salt = QByteArray::fromHex(salt111.toUtf8());

    // 使用相同的哈希算法和盐值对用户提供的密码进行哈希处理  
    QByteArray passwordHash = QCryptographicHash::hash((password + salt).toUtf8(), QCryptographicHash::Sha256).toHex();
    // 比较存储的哈希值和计算的哈希值是否相同  
    if (passwordHash==storedHash) {
        // 哈希值匹配，登录成功  
        return true;
    }
```
