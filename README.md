
我们在帖子[MySQL数据的导出 \- brucexia \- 博客园 (cnblogs.com)](https://github.com)中讲了MySQL数据的导出，本文讲讲解MySQL数据的导入。


MySQL数据的导入包括使用LOAD DATA INFILE命令导入和使用mysqlimport命令导入。


### 使用LOAD DATA INFILE方式导入文本文件


MySQL允许将数据导出到外部文件，也可以从外部文件导入数据。MySQL提供了一些导入数据的工具，包括LOAD DATA语句、source命令和mysql命令。LOAD DATA INFILE语句用于高速地从一个文本文件中读取行，并输入一张表中。文件名称必须为文字字符串。


LOAD DATA语句的基本格式如下：




```
LOAD DATA  INFILE 'filename.txt' INTO TABLE tablename [OPTIONS] [IGNORE number LINES]
```


在LOAD DATA语句中，关键字INFILE后面的filename文件为导入数据的来源；tablename表示待导入的数据表名称；\[OPTIONS]为可选参数选项，OPTIONS部分的语法包括FIELDS和LINES子句，其可能的取值有：


  FIELDS  TERMINATED BY 'value'：设置字段之间的分隔字符，可以为单个或多个字符，默认情况下为“\\t”。


  FIELDS  \[OPTIONALLY] ENCLOSED BY 'value'：设置字段的包围字符，只能为单个字符。如果使用了OPTIONALLY，则只有CHAR和VERCHAR等字符数据字段被包围。


  FIELDS  ESCAPED BY 'value'：控制如何写入或读取特殊字符，只能为单个字符，即设置转义字符，默认值为“\\”。


  LINES  STARTING BY 'value'：设置每行数据开头的字符，可以为单个或多个字符，默认情况下不使用任何字符。


  LINES  TERMINATED BY 'value'：设置每行数据结尾的字符，可以为单个或多个字符，默认值为“\\n”。


IGNORE number LINES选项表示忽略文件开始处的行数，number表示忽略的行数。执行LOAD DATA语句需要FILE权限。


【例11\.19】使用LOAD DATA语句将D:\\person0\.txt文件中的数据导入test\_db数据库的person表中，SQL语句如下：


LOAD DATA  INFILE 'D:\\person0\.txt' INTO TABLE test\_db.person;


导入数据之前，要将person表中的数据全部删除，即登录MySQL，使用DELETE语句删除person表中的数据：




```
mysql> USE test_db;
Database changed;
mysql> DELETE FROM person;
Query OK, 10 rows affected (0.00 sec)
```



```
从person0.txt文件中导入数据，SQL语句如下：
```



```
mysql> LOAD DATA  INFILE 'D:\person0.txt' INTO TABLE test_db.person;
Query OK, 10 rows affected (0.00 sec)
Records: 10  Deleted: 0  Skipped: 0  Warnings: 0

 
mysql> SELECT * FROM person;
+----+---------+-----+------------+
| id | name    | age | info       |
+----+---------+-----+------------+
|  1 | Green   |  21 | Lawyer     |
|  2 | Suse    |  22 | dancer     |
|  3 | Mary    |  24 | Musician   |
|  4 | Willam  |  20 | sports man |
|  5 | Laura   |  25 | NULL       |
|  6 | Evans   |  27 | secretary  |
|  7 | Dale    |  22 | cook       |
|  8 | Edison  |  28 | singer     |
|  9 | Harry   |  21 | magician   |
| 10 | Harriet |  19 | pianist    |
+----+---------+----+-------------+
```


可以看到，语句执行成功之后，person0\.txt文件中的数据导入person表中了。


【例11\.20】使用LOAD DATA语句将D:\\person1\.txt文件中的数据导入test\_db数据库中的person表中，使用FIELDS选项和LINES选项，要求字段之间使用“,”间隔，所有字段值用双引号括起来，定义转义字符为“\\'”，每行记录以“\\r\\n”结尾，SQL语句如下：




```
LOAD DATA INFILE 'D:\person1.txt' INTO TABLE test_db.person
FIELDS
TERMINATED BY ','
ENCLOSED BY '\"'
ESCAPED BY '\''
LINES
TERMINATED BY '\r\n';
```


导入数据之前，使用DELETE语句将person表中的数据全部删除，执行过程如下：




```
mysql> DELETE FROM person;
Query OK, 10 rows affected (0.00 sec)
从person1.txt文件中导入数据，执行过程如下：
mysql> LOAD DATA  INFILE 'D:\person1.txt' INTO TABLE test_db   .person
    -> FIELDS
    -> TERMINATED BY ','
    -> ENCLOSED BY '\"'
    -> ESCAPED BY '\''
    -> LINES
    -> TERMINATED BY '\r\n';
Query OK, 10 rows affected (0.00 sec)
Records: 10  Deleted: 0  Skipped: 0  Warnings: 0
```


语句执行成功后，使用SELECT语句查看person表中的记录，结果与【例11\.19】的相同。


### 使用mysqlimport命令导入文本文件


使用mysqlimport命令可以导入文本文件，并且不需要登录MySQL客户端。mysqlimport命令提供许多与LOAD DATA INFILE语句相同的功能，大多数选项直接对应LOAD DATA INFILE子句。使用mysqlimport命令需要指定所需的选项、导入的数据库名称以及导入的数据文件的路径和名称。mysqlimport命令的基本语法格式如下：




```
mysqlimport -u root-p dbname filename.txt [OPTIONS]
```


dbname为导入的表所在的数据库名称。注意，mysqlimport命令不指定导入数据库的表名称，数据表的名称由导入文件名称确定，即文件名作为表名，导入数据之前该表必须存在。\[OPTIONS]为可选参数选项，其常见的取值有：


  \-\-fields\-terminated\-by\= 'value'：设置字段之间的分隔字符，可以为单个或多个字符，默认情况下为“\\t”。


  \-\-fields\-enclosed\-by\= 'value'：设置字段的包围字符。


  \-\-fields\-optionally\-enclosed\-by\= 'value'：设置字段的包围字符，只能为单个字符，包括CHAR和VERCHAR等字符数据字段。


  \-\-fields\-escaped\-by\= 'value'：控制如何写入或读取特殊字符，只能为单个字符，即设置转义字符，默认值为“\\”。


  \-\-lines\-terminated\-by\= 'value'：设置每行数据结尾的字符，可以为单个或多个字符，默认值为“\\n”。


  \-\-ignore\-lines\=n：忽视数据文件的前n行。


【例11\.21】使用mysqlimport命令将D盘目录下的person.txt文件内容导入test\_db数据库中，字段之间使用“,”分隔，字符类型字段值用双引号引起来，将转义字符定义为“?”，每行记录以“\\r\\n”结尾，SQL语句如下：




```
C:\ >mysqlimport -u root -p test_db D:\person.txt --fields-terminated-by=, --fields-optionally-enclosed-by=\"--fields-escaped-by=?--lines-terminated-by=\r\n
```


上面的语句要在一行中输入，语句执行成功后，将把person.txt中的数据导入数据库test\_db中。


除了前面介绍的几个选项之外，mysqlimport命令还支持许多别的选项，例如：


  \-\-columns\=column\_list, \-c column\_list：采用逗号分隔的列名作为其值。列名的顺序指示如何匹配数据文件列和表列。


  \-\-compress，\-C：压缩在客户端和服务器之间发送的所有信息（如果二者均支持压缩）。


  \-d，\-\-delete：导入文本文件前清空表。


  \-\-force，\-f：忽视错误。例如，当某个文本文件的表不存在时，继续处理其他文件。不使用\-\-force时，如果表不存在，则mysqlimport退出。


  \-\-host\=host\_name，\-h host\_name：将数据导入给定主机上的MySQL服务器。默认主机是localhost。


  \-\-ignore，\-i：参见\-\-replace选项的描述。


  \-\-ignore\-lines\=n：忽视数据文件的前n行。


  \-\-local，\-L：从本地客户端读入输入文件。


  \-\-lock\-tables，\-l：处理文本文件前锁定所有表，以便写入。这样可以确保所有表在服务器上保持同步。


  \-\-password\[\=password]，\-p\[password]：连接服务器时使用的密码。如果使用短选项形式（\-p），则选项和密码之间不能有空格。如果在命令行中\-\-password或\-p选项后面没有密码值，则提示输入一个密码。


  \-\-port\=port\_num，\-P port\_num：用于连接的TCP/IP端口号。


  \-\-protocol\={TCP \| SOCKET \| PIPE \| MEMORY}：使用的连接协议。


  \-\-replace和\-\-ignore选项控制复制唯一键值已有记录的输入记录的处理方式。如果指定\-\-replace，新行替换有相同的唯一键值的已有行；如果指定\-\-ignore，复制已有的唯一键值的输入行将被跳过，不发生替换；如果不指定这两个选项，当发现一个复制键值时会出现一个错误，并且忽视文本文件的剩余部分。


  \-\-silent，\-s：沉默模式。只有出现错误时才输出信息。


  \-\-user\=user\_name，\-u user\_name：连接服务器时MySQL使用的用户名。


  \-\-verbose，\-v：冗长模式。打印出程序操作的详细信息。


  \-\-version，\-V：显示版本信息并退出。


![](https://img2024.cnblogs.com/blog/270128/202410/270128-20241017085727536-1296856894.jpg)


 


 本博客参考[FlowerCloud机场](https://hushicha.org)。转载请注明出处！
