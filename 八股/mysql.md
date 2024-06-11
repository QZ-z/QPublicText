## [MySQL 基础](#mysql-基础)

### [什么是关系型数据库？](#什么是关系型数据库)

建立在关系模型上，表明了存储数据之间的关系（一对一，一对多，多对多）

大部分关系型数据库采用`SQL`操作数据，且支持事务的四大特性（ACID）

> 常见关系型数据库：MySQL、PostgreSQL、Oracle、SQL Server、SQLite（微信本地的聊天记录的存储就是用的 SQLite） ……

### [MySQL 有什么优点？](#mysql-有什么优点)

MySQL 主要具有下面这些优点：

1. 成熟稳定，功能完善。
2. 开源免费。
3. 文档丰富，既有详细的官方文档，又有非常多优质文章可供参考学习。
4. 开箱即用，操作简单，维护成本低。
5. 兼容性好，支持常见的操作系统，支持多种开发语言。
6. 社区活跃，生态完善。
7. 事务支持优秀， InnoDB 存储引擎默认使用 RR并不会有任何性能损失，并且，其实现的RR可以解决幻读的问题（基于锁）
8. 支持分库分表、读写分离、高可用。

## [MySQL 字段类型](https://javaguide.cn/database/mysql/mysql-questions-01.html#mysql-字段类型)

![image-20240425210101739](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240425210101739.png)

:question:各自都是多少字节？能够表示的范围是多少

### [整数类型的 UNSIGNED 属性有什么用？](#整数类型的-unsigned-属性有什么用)

选用`UNSIGNED`属性表示不允许负值的无符号整数，将正整数上限提高一倍

### [CHAR 和 VARCHAR 的区别是什么？](#char-和-varchar-的区别是什么)

- CHAR定长，右边填充空格达到指定长度，检索时去掉空格
- VARCHAR变长，使用 1 或 2 个额外字节记录字符串的长度，检索时不需要处理。

`CHAR(M)` 和 `VARCHAR(M)` 的 M 都代表能够保存的字符数的最大值，无论是字母、数字还是中文，每个都只占用一个字符。

### [VARCHAR(100)和 VARCHAR(10)的区别是什么？](#varchar-100-和-varchar-10-的区别是什么)

VARCHAR(100)和 VARCHAR(10)都是变长类型，表示能存储最多 100 个字符和 10 个字符。

存储相同的字符串，占用的==磁盘存储空间==是一样的

不过，VARCHAR(100) 会消耗更多的内存。这是因为 VARCHAR 类型在==内存==中操作时，通常会分配固定大小的内存块来保存值，即使用字符类型中定义的长度。例如在进行排序的时候，VARCHAR(100)是按照 100 这个长度来进行的，也就会消耗更多内存。

### [DECIMAL 和 FLOAT/DOUBLE 的区别是什么？](#decimal-和-float-double-的区别是什么)

DECIMAL 和 FLOAT 的区别是：**DECIMAL 是定点数，FLOAT/DOUBLE 是浮点数。DECIMAL 可以存储精确的小数值，FLOAT/DOUBLE 只能存储近似的小数值。**

DECIMAL 用于存储具有精度要求的小数，例如与货币相关的数据，可以避免浮点数带来的精度损失。

在 Java 中，MySQL 的 DECIMAL 类型对应的是 Java 类 `java.math.BigDecimal`

### [为什么不推荐使用 TEXT 和 BLOB？](https://javaguide.cn/database/mysql/mysql-questions-01.html#为什么不推荐使用-text-和-blob)

TEXT 类型类似于 CHAR（0-255 字节）和 VARCHAR（0-65,535 字节），但可以存储更长的字符串，即长文本数据，例如博客内容。

![image-20240425211045985](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240425211045985.png)

BLOB 类型主要用于存储二进制大对象，例如图片、音视频等文件。

![image-20240425211059482](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240425211059482.png)

在日常开发中，很少使用 TEXT 类型，但偶尔会用到，而 BLOB 类型则基本不常用。如果预期长度范围可以通过 VARCHAR 来满足，建议避免使用 TEXT。

数据库规范通常不推荐使用 BLOB 和 TEXT 类型，缺点和限制如下：

- 不能有默认值。
- 在使用临时表时无法使用内存临时表，只能在磁盘上创建临时表（《高性能 MySQL》书中有提到）。
- 检索效率较低。
- 不能直接创建索引，需要指定前缀长度。
- 可能会消耗大量的网络和 IO 带宽。
- 可能导致表上的 DML 操作变慢。
- ……

### [DATETIME 和 TIMESTAMP 的区别是什么？](#datetime-和-timestamp-的区别是什么)

DATETIME 类型没有时区信息，TIMESTAMP 和时区有关。

TIMESTAMP 只需要使用 4 个字节的存储空间

 DATETIME 需要耗费 8 个字节的存储空间。

但是，这样同样造成了一个问题，Timestamp 表示的时间范围更小。

- DATETIME：1000-01-01 00:00:00 ~ 9999-12-31 23:59:59
- Timestamp：1970-01-01 00:00:01 ~ 2037-12-31 23:59:59

MySQL 5.6.4 开始，它们的存储空间会根据毫秒精度的不同而变化，DateTime 的范围是 5~8 字节，Timestamp 的范围是 4~7 字节

![image-20240508154612493](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240508154612493.png)

### [NULL 和 '' 的区别是什么？](#null-和-的区别是什么)

`NULL` 跟 `''`(空字符串)是两个完全不一样的值，区别如下：

- `NULL` 代表一个不确定的值,就算是两个 `NULL`,它俩也不一定相等。
  - 例如，`SELECT NULL=NULL`的结果为 false
  - 但是在我们使用`DISTINCT`,`GROUP BY`,`ORDER BY`时,`NULL`又被认为是相等的。
- `''`的长度是 0，是不占用空间的，而`NULL` 是需要占用空间的。
- `NULL` 会影响聚合函数的结果。
  - 例如，`SUM`、`AVG`、`MIN`、`MAX` 等聚合函数会忽略 `NULL` 值。 
  - `COUNT` 的处理方式取决于参数的类型。如果参数是 `*`(`COUNT(*)`)，则会统计所有的记录数，包括 `NULL` 值；如果参数是某个字段名(`COUNT(列名)`)，则会忽略 `NULL` 值，只统计非空值的个数。
- 查询 `NULL` 值时，必须使用 `IS NULL` 或 `IS NOT NULLl` 来判断，而不能使用 =、!=、 <、> 之类的比较运算符。而`''`是可以使用这些比较运算符的。

### [Boolean 类型如何表示？](https://javaguide.cn/database/mysql/mysql-questions-01.html#boolean-类型如何表示)

MySQL 中没有专门的布尔类型，而是用 TINYINT(1) 类型来表示布尔值。TINYINT(1) 类型可以存储 0 或 1，分别对应 false 或 true。

## [MySQL 基础架构](https://javaguide.cn/database/mysql/mysql-questions-01.html#mysql-基础架构)

![image-20240425212055295](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240425212055295.png)

**连接器：** 身份认证和权限相关(登录 MySQL 的时候)。

**查询缓存：** 执行查询语句的时候，会先查询缓存（MySQL 8.0 版本后移除，因为这个功能不太实用）。

**分析器：** 没有命中缓存的话，SQL 语句就会经过分析器，分析器说白了就是要先看你的 SQL 语句要干嘛，再检查你的 SQL 语句语法是否正确。

**优化器：** 按照 MySQL 认为最优的方案去执行。

**执行器：** 执行语句，然后从存储引擎返回数据。 执行语句之前会先判断是否有权限，如果没有权限的话，就会报错。

**插件式存储引擎**：主要负责数据的存储和读取，采用的是插件式架构，支持 InnoDB、MyISAM、Memory 等多种存储引擎

### 执行流程细则

#### 连接器

通过TCP三次握手连接

空闲连接超过8小时（默认）连接器将自动断开

MySQL 服务支持的最大连接数由 `max_connections `参数控制

MySQL 的连接也跟 HTTP 一样，有短连接和长连接的概念

怎么解决长连接占用内存的问题？

- **定期断开长连接**
- **客户端主动重置连接**

连接器流程：

- 与客户端进行 TCP 三次握手建立连接；
- 校验客户端的用户名和密码，如果用户名或密码不对，则会报错；
- 如果用户名和密码都对了，会读取该用户的权限，然后后面的权限逻辑判断都基于此时读取到的权限

#### 解析器

- **词法分析**。MySQL 会根据你输入的字符串识别出关键字出来，例如，SQL语句 select username from userinfo，在分析之后，会得到4个Token，其中有2个Keyword，分别为select和from
- **语法分析**。根据词法分析的结果，语法解析器会根据语法规则，判断你输入的这个 SQL 语句是否满足 MySQL 语法，如果没问题就会构建出 SQL 语法树，这样方便后面模块获取 SQL 类型、表名、字段名、 where 条件等等。

> 表和字段不存在，不在这个阶段做

#### 执行SQL

##### 预处理器

我们先来说说预处理阶段做了什么事情。

- 检查 SQL 查询语句中的表或者字段是否存在；
- 将 `select *` 中的 `*` 符号，扩展为表上的所有列；

#####  优化器

**优化器主要负责将 SQL 查询语句的执行方案确定下来**

##### 执行器

根据执行计划执行 SQL 查询语句，从存储引擎读取记录，返回给客户端

## [MySQL 存储引擎](https://javaguide.cn/database/mysql/mysql-questions-01.html#mysql-存储引擎)

### [MySQL 支持哪些存储引擎？默认使用哪个？](https://javaguide.cn/database/mysql/mysql-questions-01.html#mysql-支持哪些存储引擎-默认使用哪个)

![image-20240425212431515](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240425212431515.png)

只有InnoDB支持事务，5.5.5之后默认InnoDB，之前是MyISAM

### [MySQL 存储引擎架构了解吗？](#mysql-存储引擎架构了解吗)

MySQL 存储引擎采用的是 **插件式架构** ，支持多种存储引擎，我们甚至可以为不同的数据库表设置不同的存储引擎以适应不同场景的需要。**存储引擎是基于表的，而不是数据库。**

可以根据MySQL定义的存储引擎实现标准接口来编写第三方存储引擎。

### [MyISAM 和 InnoDB 有什么区别？](#myisam-和-innodb-有什么区别)

InnoDB最大的三大特点就是==事务，外键，行级锁==

- InnoDB 支持行级别的锁粒度，MyISAM 不支持，只支持表级别的锁粒度。
- MyISAM 不提供事务支持。InnoDB 提供事务支持，实现了 SQL 标准定义了四个隔离级别。
- MyISAM 不支持外键，而 InnoDB 支持。
- MyISAM 不支持 MVCC，而 InnoDB 支持。
- 虽然 MyISAM 引擎和 InnoDB 引擎都是使用 B+Tree 作为索引结构，但是两者的实现方式不太一样。(InnoDB，表结构，数据和索引都在idb文件中存放)
- MyISAM 不支持数据库异常崩溃后的安全恢复，而 InnoDB 支持。（基于`redo log`）
- InnoDB 的性能比 MyISAM 更强大

### [MyISAM 和 InnoDB 如何选择？](https://javaguide.cn/database/mysql/mysql-questions-01.html#myisam-和-innodb-如何选择)

某些情况下你并不在乎可扩展能力和并发能力，也不需要事务支持，也不在乎崩溃后的安全恢复问题的话，选择 MyISAM 也是一个不错的选择。

但是普遍要考虑，所以说MyISAM不用。。。

## 表空间文件结构

![image-20240607145351851](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240607145351851.png)

**在表中数据量大的时候，为某个索引分配空间的时候就不再按照页为单位分配了，而是按照区（extent）为单位分配。每个区的大小为 1MB，对于 16KB 的页来说，连续的 64 个页会被划为一个区，这样就使得链表中相邻的页的物理位置也相邻，就能使用顺序 I/O 了**

段的分类：

- 索引段：存放 B + 树的非叶子节点的区的集合；
- 数据段：存放 B + 树的叶子节点的区的集合；
- 回滚段：存放的是回滚数据的区的集合。

### 数据页的结构

<img src="https://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240610174948967.png" alt="image-20240610174948967" style="zoom:50%;" />

- 文件头 File Header：文件头，表示页的信息
- 页头Page Header：页头，表示页的状态信息
- 最小和最大记录Infmum+supremum：两个虚拟的伪记录，分别表示页中的最小记录和最大记录
- 用户记录User Records：存储行记录内容
- 空闲空间Free Space：页中还没被使用的空间
- 页目录Page Directory：存储用户记录的相对位置，对记录起到索引作用
- 文件尾File Tailer：校验页是否完整


数据页的组织形式：

![image-20240610175159494](https://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240610175159494.png)

数据页中记录按照【主键】顺序组成单向链表

![image-20240610175302180](https://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240610175302180.png)

页目录会分组进行索引，每个槽记录该分组【最后一条记录】的地址偏移量

记录按照【主键值】从大到小排列，页内查找，通过槽进行二分查找

槽内记录数量会有限制，防止通过槽查询到组之后，通过单向链表查询时，O（n）的n过大

- 第一个分组中的记录只能有 1 条记录；
- 最后一个分组中的记录条数范围只能在 1-8 条之间；
- 剩下的分组中记录条数范围只能在 4-8 条之间。

### InnoDB 行格式有哪些？

- `Redundant `是很古老的行格式了， MySQL 5.0 版本之前用的行格式
- `Compact` 是一种紧凑的行格式，设计的初衷就是为了让一个数据页中可以存放更多的行记录，从 MySQL 5.1 版本之后，行格式默认设置成 `Compact`。
- `Dynamic `和 `Compressed `两个都是紧凑的行格式，它们的行格式都和 `Compact `差不多，因为都是基于 `Compact` 改进一点东西。从 MySQL5.7 版本之后，默认使用 `Dynamic `行格式。

![image-20240607145746632](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240607145746632.png)

#### 变长字段长度列表

对于变长数据，通过这个字段来标识实际的长度

变长字段的真实数据占用的字节数会按照列的顺序**逆序存放**，逆序排放的好处：

- 【记录头信息】中指向下一个记录的指针，指向下一条记录的【记录信息头】和【真实数据】之间的位置，往左读就是记录头信息，往右读就是真实数据，方便
- 位置靠前的记录的真实数据和对应的长度信息可以放在一个 **CPU Cache Line 中，这样就可以提高 CPU Cache 的命中率**

变长字段的字节数：

- 如果变长字段允许存储的最大字节数`小于等于 255 字节`，就会用 1 字节表示「变长字段长度」；
- 如果变长字段允许存储的最大字节数`大于 255 字节`，就会用 2 字节表示「变长字段长度」；

#### NULL值列表

逆序存放，只能是字节的整数倍（8个字节，16个字节...），通过bit位的方式标识字段是否是NULL

#### 记录头信息

部分信息如下：

- `delete_mask `：标识此条数据是否被删除。从这里可以知道，我们执行 detele 删除记录的时候，并不会真正的删除记录，只是将这个记录的 delete_mask 标记为 1。
- `next_record`：下一条记录的位置。从这里可以知道，记录与记录之间是通过链表组织的。在前面我也提到了，指向的是下一条记录的「记录头信息」和「真实数据」之间的位置，这样的好处是向左读就是记录头信息，向右读就是真实数据，比较方便。
- `record_type`：表示当前记录的类型，0表示普通记录，1表示B+树非叶子节点记录，2表示最小记录，3表示最大记录

#### row_id, trx_id, roll_pointer

对应的字节数是667

### varchar(n) 中 n 最大取值为多少？

n标识的是最大的字符数目，不是字节大小，具体多少字符要看字符集，ascii字符集一个字符占用一个字节

一行记录最多65535字节，包括【变长字段长度列表】和【NULL值列表】

### 行溢出之后的处理

一个页的大小一般是 `16KB`，也就是 `16384字节`，而一个 varchar(n) 类型的列最多可以存储 `65532字节`，一些大对象如 TEXT、BLOB 可能存储更多的数据，这时一个页可能就存不了一条记录。

这个时候会产生行溢出，多的数据会存到另外的【溢出页】中

![image-20240607204447456](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240607204447456.png)

上图是Compact行格式溢出后的处理，用20字节指向溢出页的地址

![image-20240607204535455](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240607204535455.png)

Compressed 和 Dynamic 这两个行格式和 Compact 非常类似，主要的区别在于处理行溢出数据时有些区别。

采用完全的行溢出方式，只用20字节指针指向溢出页

## [MySQL索引]()

### [索引介绍](https://javaguide.cn/database/mysql/mysql-index.html#索引介绍)

**索引是一种用于快速查询和检索数据的数据结构，其本质可以看成是一种排序好的数据结构。**

### [索引的优缺点](#索引的优缺点)

**优点**：

- 加快检索速度，减少IO次数
- 通过创建唯一性索引，可以保证数据库表中每一行数据的唯一性。

**缺点**：

- 创建和维护需要时间。增删改索引列会修改索引，进而影响SQL执行效率
- 索引需要使用物理文件存储，也会耗费一定空间。

但是，**使用索引一定能提高查询性能吗?**

大多数情况下，索引查询都是比全表扫描要快的。但是如果数据库的数据量不大，那么使用索引也不一定能够带来很大提升

### [索引底层数据结构选型](https://javaguide.cn/database/mysql/mysql-index.html#索引底层数据结构选型)

#### [Hash 表](https://javaguide.cn/database/mysql/mysql-index.html#hash-表)

MySQL 的 InnoDB 存储引擎不直接支持常规的哈希索引，但是，InnoDB 存储引擎中存在一种特殊的“自适应哈希索引”（Adaptive Hash Index）。

InnoDB存储引擎会监控对表上各索引页的查询，如果观察到在特定的条件下hash索引可以提升速度，则建立hash索引，称之为自适应hash索引。

自适应哈希索引的每个哈希桶实际上是一个小型的 B+Tree 结构。这个 B+Tree 结构可以存储多个键值对，而不仅仅是一个键。这有助于减少哈希冲突链的长度，提高了索引的效率

#### [B 树& B+树](https://javaguide.cn/database/mysql/mysql-index.html#b-树-b-树)

**B 树& B+树两者有何异同呢？**

- B 树的所有节点既存放键(key) 也存放数据(data)，而 B+树只有叶子节点存放 key 和 data，其他内节点只存放 key。
- B 树的叶子节点都是独立的;B+树的叶子节点有一条引用链指向与它相邻的叶子节点。
- B 树的检索的过程相当于对范围内的每个节点的关键字做二分查找，可能还没有到达叶子节点，检索就结束了。而 B+树的检索效率就很稳定了，任何查找都是从根节点到叶子节点的过程，叶子节点的顺序检索很明显。
- 在 B 树中进行范围查询时，首先找到要查找的下限，然后对 B 树进行中序遍历，直到找到查找的上限；而 B+树的范围查询，只需要对链表进行遍历即可。
- B+ 树有大量的冗余节点（所有非叶子节点都是冗余索引），这些冗余索引让 B+ 树在插入、删除的效率都更高，比如删除根节点的时候，不会像 B 树那样会发生复杂的树的变化；

:star:B+树与 B 树相比，具备更少的 IO 次数、更稳定的查询效率和更适于范围查询这些优势。

M阶的含义：

- B树：M阶表示每个节点最多有M-1个数据，M个子节点
- B+树：非叶子节点的子节点的个数为 M ，而索引的个数为 M-1

### [索引分类]()

存储角度：

- 聚簇索引（聚集索引）：索引结构和数据一起存放的索引，InnoDB 中的主键索引就属于聚簇索引。(有且只有一个！！！！！)
- 非聚簇索引（非聚集索引）：索引结构和数据分开存放的索引，二级索引(辅助索引)就属于非聚簇索引。

> MySQL 的 MyISAM 引擎，不管主键还是非主键，使用的都是非聚簇索引。

按照应用维度划分：

- 主键索引：加速查询 + 列值唯一（不可以有 NULL）+ 表中只有一个。
- 普通索引：仅加速查询。
- 唯一索引：加速查询 + 列值唯一（可以有 NULL）。
- 覆盖索引：一个索引包含（或者说覆盖）所有需要查询的字段的值。
- 联合索引：多列值组成一个索引，专门用于组合搜索，其效率大于索引合并。
- 前缀索引：当字段类型为字符串（varchar，text，longtext等）时，只使用其一部分前缀建立索引
- 全文索引：对文本的内容进行分词，进行搜索。目前只有 `CHAR`、`VARCHAR` ，`TEXT` 列上可以创建全文索引。一般不会使用，效率较低，通常使用搜索引擎如 ElasticSearch 代替。

### [聚集索引和非聚集索引]()

聚集索引选取规则:

- 如果存在主键，主键索引就是聚集索引
- 如果不存在主键，将使用第一个唯一（UNIQUE）索引作为聚集索引。
- 如果表没有主键，或没有合适的唯一索引，则InnoDB会自动生成一个rowid作为隐藏的聚集索 引。

![image-20240426151835472](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240426151835472.png)

对比：

- 聚集索引

  - 优点
    - 查询快
    - 对排序查找和范围查找优化
  - 缺点：
    - 依赖有序数据
    - 更新代价大

- 非聚集索引

  - 优点：
    - 更新代价小
  - 缺点：
    - 依赖有序数据
    - 可能回表查询

  > 也不是一定会回表，如果是覆盖索引，就不需要回表了

### [最左前缀匹配]()

如果索引关联了多列（联合索引），要遵守最左前缀法则，最左前缀法则指的是查询从索引的最左列开始，并且不跳过索引中的列。如果跳跃某一列，==索引将部分失效（后面的字段索引失效）==

### [索引失效]()

- 联合索引中，出现范围查询(>,<)，范围查询右侧的列索引失效。（当范围查询使用>= 或 <= 时，走联合索引）
- 在索引列上进行运算，会导致失效
- 字符串类型使用，不加引号，索引将失效
- 在字段头部使用模糊匹配，索引失效；但是在尾部没事
- 用or分割开的条件， 如果or前后的条件中有一个列没有索引，那么涉及的索引都不会被用到
- 如果MySQL评估使用索引比全表更慢，则不使用索引

> 第一条中的失效是因为：
>
> - 当只有>或者<存在的时候，在这个区间中，右侧的索引其实是无序的
> - 但是存在=的时候，右侧的索引是可以根据有序筛选的
> - 具体看[小林coding_联合索引范围查询](https://xiaolincoding.com/mysql/index/index_interview.html#%E8%81%94%E5%90%88%E7%B4%A2%E5%BC%95%E8%8C%83%E5%9B%B4%E6%9F%A5%E8%AF%A2)

### [索引设计原则]()

1. 针对于数据量较大，且查询比较频繁的表建立索引。
2. 针对于常作为查询条件（where）、排序（order by）、分组（group by）操作的字段建立索引。
3. 尽量选择区分度高的列作为索引，尽量建立唯一索引，区分度越高，使用索引的效率越高。
4. 如果是字符串类型的字段，字段的长度较长，可以针对于字段的特点，建立前缀索引。
5. 尽量使用联合索引，减少单列索引，查询时，联合索引很多时候可以覆盖索引，节省存储空间，避免回表，提高查询效率。
6. 要控制索引的数量，索引并不是多多益善，索引越多，维护索引结构的代价也就越大，会影响增删改的效率。
7. 如果索引列不能存储NULL值，请在创建表时使用NOT NULL约束它。当优化器知道每列是否包含NULL值时，它可以更好地确定哪个索引最有效地用于查询。

> 3中存在一些弊端：
>
> - order by 就无法使用前缀索引；
> - 无法把前缀索引用作覆盖索引

### [索引下推]()

```sql
SELECT * FROM user WHERE zipcode = '431200' AND MONTH(birthdate) = 3;
```

存在联合索引(zipcode, birthdate)，因为在birthdate上进行了运算，这列的索引部分失效

没有索引下推之前：

- 存储引擎层先根据 `zipcode` 索引字段找到所有 `zipcode = '431200'` 的用户的主键 ID，然后二次回表查询，获取完整的用户数据；
- 存储引擎层把所有 `zipcode = '431200'` 的用户数据全部交给 Server 层，Server 层根据`MONTH(birthdate) = 3`这一条件再进一步做筛选。

有了索引下推之后：

- 存储引擎层先根据 `zipcode` 索引字段找到所有 `zipcode = '431200'` 的用户，然后直接判断 `MONTH(birthdate) = 3`，筛选出符合条件的主键 ID；
- 二次回表查询，根据符合条件的主键 ID 去获取完整的用户数据；
- 存储引擎层把符合条件的用户数据全部交给 Server 层。

> 相当于运算没有导致索引失效

## [MySQL 查询缓存](https://javaguide.cn/database/mysql/mysql-questions-01.html#mysql-查询缓存)

执行查询语句的时候，会先查询缓存。不过，MySQL 8.0 版本后移除，因为这个功能不太实用

## [MySQL日志]()

MySQL 中常见的日志类型主要有下面几类（针对的是 InnoDB 存储引擎）：

- 错误日志（error log） ：对 MySQL 的启动、运行、关闭过程进行了记录。
- :star:二进制日志（binary log，binlog） ：主要记录的是更改数据库数据的 SQL 语句。
- 一般查询日志（general query log） ：已建立连接的客户端发送给 MySQL 服务器的所有 SQL 记录，因为 SQL 的量比较大，默认是不开启的，也不建议开启。
- 慢查询日志（slow query log） ：执行时间超过 long_query_time秒钟的查询，解决 SQL 慢查询问题的时候会用到。
- :star:事务日志(redo log 和 undo log) ：redo log 是重做日志，undo log 是回滚日志。
- 中继日志(relay log) ：relay log 是复制过程中产生的日志，很多方面都跟 binary log 差不多。不过，relay log 针对的是主从复制中的从库。
- DDL 日志(metadata log) ：DDL 语句执行的元数据操作。

### [binlog]()

binlog(binary log 即二进制日志文件) 主要记录了对 MySQL 数据库执行了更改的所有操作(数据库执行的所有 DDL 和 DML 语句)

- 表结构变更（CREATE、ALTER、DROP TABLE…）
- 表数据修改（INSERT、UPDATE、DELETE...）
- 不包括 SELECT、SHOW 这类不会对数据库造成更改的操作

MySQL 内置了 binlog 查看工具 mysqlbinlog

#### [binlog 的格式有哪几种？]()

一共有 3 种类型二进制记录方式：

- Statement 模式 ：每一条会修改数据的sql都会被记录在binlog中，如inserts, update, delete。

![image-20240508143525155](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240508143525155.png)

> `update_time=now()`这里会获取当前系统时间，直接执行会导致与原库的数据不一致。

- Row 模式 （推荐）: 每一行的具体变更事件都会被记录在binlog中，数据库通过解析 Binlog 来逐行执行相应的修改操作。

![image-20240508143629906](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240508143629906.png)

> 条件后面的@1、@2、@3 都是该行数据第 1 个~3 个字段的原始值（**假设这张表只有 3 个字段**）

- Mixed 模式 ：Statement 模式和 Row 模式的混合。默认使用 Statement 模式，少数特殊具体场景自动切换到 Row 模式（如上边会导致数据不一致的情况）。

#### [binlog主要用来做什么]()

最主要：主从复制

主备、主主、主从都离不开binlog，利用binlog同步数据，保持一致

主从复制过程：

1. 主库将数据库中数据的变化写入到 binlog
2. 从库连接主库
3. 从库会创建一个 I/O 线程向主库请求更新的 binlog
4. 主库会创建一个 binlog dump 线程来发送 binlog ，从库中的 I/O 线程负责接收
5. 从库的 I/O 线程将接收的 binlog 写入到 relay log 中。
6. 从库的 SQL 线程读取 relay log 同步数据本地（也就是再执行一遍 SQL ）。

#### [binlog刷盘时机]()

InnoDB事务执行中，先把日志写入`binlog cache`，提交的时候才持久化到磁盘

那么 binlog 是什么时候刷到磁盘中的呢？ 可以通过  `sync_binlog` 参数控制 biglog 的刷盘时机，取值范围是 0-N，默认为 0 ：

- 0：不去强制要求，由系统自行判断何时写入磁盘；（5.7之前默认这个）
- 1：每次提交事务的时候都要将binlog写入磁盘；（5.7之后默认）
- N：每 N 个事务，才会将binlog写入磁盘。

> 每次事务提交的时候都只是write，`sync_binlog`来控制`fsync`刷盘的时机

![image-20240508144415617](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240508144415617.png)

#### [什么情况下重新生成binlog]()

当遇到以下 3 种情况时，MySQL会重新生成一个新的日志文件，文件序号递增：

- MySQL服务器停止或重启；
- 使用 flush logs 命令后；
- binlog 文件大小超过 max_binlog_size变量的阈值后。

### [redolog]()

#### [redo log如何保证事务的持久性]()

为了减少磁盘 IO 开销，有一个叫做 Buffer Pool(缓冲池) 的区域，存在于内存中。当我们的数据对应的页不存在于 Buffer Pool 中的话， MySQL 会先将磁盘上的页缓存到 Buffer Pool 中，这样后面我们直接操作的就是 Buffer Pool 中的页，这样大大提高了读写性能。

![image-20240611130133887](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240611130133887.png)

如果缓冲池中的还没来得及刷新到磁盘就宕机了，违背了事务的持久性。

redo log 主要做的事情就是记录页的修改，比如某个页面某个偏移量处修改了几个字节的值以及具体被修改的内容是什么。

【事务提交的时候】，redo log 按照刷盘策略刷到磁盘上去

![image-20240426162325264](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240426162325264.png)

#### [InnoDB将redo log刷到磁盘的几种情况]()

> InnoDB 存储引擎有一个后台线程，每隔`1` 秒，就会把 `redo log buffer` 中的内容写到文件系统缓存（`page cache`），然后调用 `fsync` 刷盘。
>
> 所以说没有事务提交的时候也会刷盘

1. 事务提交：当事务提交时，log buffer 里的 redo log 会被刷新到磁盘（可以通过innodb_flush_log_at_trx_commit参数控制，后文会提到）。
2. log buffer 空间不足时：log buffer 中缓存的 redo log 已经占满了 log buffer 总容量的大约一半左右，就需要把这些日志刷新到磁盘上。
3. 事务日志缓冲区满：InnoDB 使用一个事务日志缓冲区（transaction log buffer）来暂时存储事务的重做日志条目。当缓冲区满时，会触发日志的刷新，将日志写入磁盘。:question:
4. Checkpoint（检查点）：InnoDB 定期会执行检查点操作，将内存中的脏数据（已修改但尚未写入磁盘的数据）刷新到磁盘，并且会将相应的重做日志一同刷新，以确保数据的一致性。
5. 后台刷新线程：InnoDB 启动了一个后台线程，负责周期性（每隔 1 秒）地将脏页（已修改但尚未写入磁盘的数据页）刷新到磁盘，并将相关的重做日志一同刷新。也就是说，一个没有提交事务的 redo log 记录，也可能会被刷盘。
6. 正常关闭服务器：MySQL 关闭的时候，redo log 都会刷入到磁盘里去。

`innodb_flush_log_at_trx_commit` 的值有 3 种，也就是共有 3 种刷盘策略：

- 0：设置为 0 的时候，表示每次事务提交时不进行刷盘操作。这种方式性能最高，但是也最不安全，因为如果 MySQL 挂了或宕机了，可能会丢失最近 1 秒内的事务。

- 1：设置为 1 的时候，表示每次事务提交时都将进行刷盘操作。这种方式性能最低，但是也最安全，因为只要事务提交成功，redo log 记录就一定在磁盘里，不会有任何数据丢失。

- 2：设置为 2 的时候，表示每次事务提交时都只把 log buffer 里的 redo log 内容写入 page cache（文件系统缓存）。page cache 是专门用来缓存文件的，这里被缓存的文件就是 redo log 文件。这种方式的性能和安全性都介于前两者中间。

  > 因为只是mysql挂了的话，page cache还能用

![image-20240426164408209](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240426164408209.png)

#### [redo log的写入方式]()

采用循环的方式

![image-20240426165016566](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240426165016566.png)

`write pos` 表示 redo log 当前记录写到的位置，` check point `表示当前要擦除的位置。当`write pos`追上`check point`时，表示 redo log 文件被写满了

#### [会产生数据丢失的情况]()

1. redo log 写入` log buffer `但还未写入` page cache` ，此时数据库崩溃，就会出现数据丢失（刷盘策略`innodb_flush_log_at_trx_commit `的值为 0 时可能会出现这种数据丢失）；
2. redo log 已经写入 `page cache` 但还未写入磁盘，操作系统奔溃，也可能出现数据丢失（刷盘策略`innodb_flush_log_at_trx_commit `的值为 2 时可能会出现这种数据丢失）

#### [页修改之后不是直接刷盘的原因]()

InnoDB一个页16KB，只修改几个字节，也要把整个页刷盘，这些页不相邻的情况下还是随机IO

采用 redo log 的方式就可以避免这种性能问题，因为 redo log 的刷盘性能很好。首先，redo log 的写入属于顺序 IO。 其次，一行 redo log 记录只占几十个字节

#### [binlog和redolog的区别]()

1. binlog 主要用于数据库还原，属于数据级别的数据恢复，主从复制是 binlog 最常见的一个应用场景。redolog 主要用于保证事务的持久性，属于事务级别的数据恢复。
2. redolog 属于 InnoDB 引擎特有的，binlog 属于所有存储引擎共有的，因为 binlog 是 MySQL 的 Server 层实现的。
3. redolog 属于物理日志，主要记录的是某个页的修改。binlog 属于逻辑日志，主要记录的是数据库执行的所有 DDL 和 DML 语句。
4. binlog 通过追加的方式进行写入，大小没有限制。redo log 采用循环写的方式进行写入，大小固定，当写到结尾时，会回到开头循环写日志。

### [两阶段提交]()

![image-20240508152037337](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240508152037337.png)

由于 binlog 没写完就异常，这时候 binlog 里面没有对应的修改记录。因此，之后用 binlog 日志恢复数据时，就会少这一次更新，恢复出来的这一行`c`值是`0`，而原库因为 redo log 日志恢复，这一行`c`值是`1`，最终数据不一致。

![image-20240508152053267](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240508152053267.png)

MySQL 根据 redo log 日志恢复数据时，发现 redo log 还处于`prepare`阶段，并且没有对应 binlog 日志，就会回滚该事务

redo log 设置`commit`阶段发生异常，不会回滚

![image-20240508152021083](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240508152021083.png)

#### 两阶段提交的问题

- **磁盘 I/O 次数高**：对于“双1”配置，每个事务提交都会进行两次 fsync（刷盘），一次是 redo log 刷盘，另一次是 binlog 刷盘。
- **锁竞争激烈**：两阶段提交虽然能够保证「单事务」两个日志的内容一致，但在「多事务」的情况下，却不能保证两者的提交顺序一致，因此，在两阶段提交的流程基础上，还需要加一个锁来保证提交的原子性，从而保证多事务的情况下，两个日志的提交顺序一致。

解决方案：组提交

### [undolog]()

InnoDB层面的实现

回滚日志，用于记录数据被修改前的信息 , 作用包含两个 : 提供回滚(保证事务的原子性) 和MVCC(多版本并发控制) 。

undo log 是一种用于撤销回退的日志。在事务没提交之前，MySQL 会先记录【更新前的数据】到 undo log 日志文件里面，当事务回滚时，可以利用 undo log 来进行回滚

undo log和redo log记录物理日志不一样，它是逻辑日志。

- 可以认为当delete一条记录时，undolog中会记录一条对应的insert记录
- 反之亦然，当update一条记录时，它记录一条对应相反的update记录。当执行rollback时，就可以从undo log中的逻辑记录读取到相应的内容并进行回滚。

Undo log销毁：undo log在事务执行时产生，事务提交时，并不会立即删除undo log，因为这些日志可能还用于MVCC。

Undo log存储：undo log采用段的方式进行管理和记录，存放在前面介绍的 rollback segment回滚段中，内部包含1024个undo log segment。

> ndo log 和数据页的刷盘策略是一样的，都需要通过 redo log 保证持久化。
>
> buffer pool 中有 undo 页，对 undo 页的修改也都会记录到 redo log。redo log 会每秒刷盘，提交事务时也会刷盘，数据页和 undo 页都是靠这个机制保证持久化的。

#### undo log和redo log异同

同：都是InnoDB存储引擎的日志

异：

- redo log 记录了此次事务「**完成后**」的数据状态，记录的是更新**之后**的值；
- undo log 记录了此次事务「**开始前**」的数据状态，记录的是更新**之前**的值；

> 事务提交之前发生了崩溃，重启后会通过 undo log 回滚事务
>
> 事务提交之后发生了崩溃，重启后会通过 redo log 恢复事务

### Buffer pool

Buffer Pool 是在 MySQL 启动的时候，向操作系统申请的一片连续的内存空间，默认配置下 Buffer Pool 只有 `128MB` 。

可以通过调整 `innodb_buffer_pool_size` 参数来设置 Buffer Pool 的大小，一般建议设置成可用物理内存的 60%~80%

#### 脏页的刷新时机

- 当 redo log 日志满了的情况下，会主动触发脏页刷新到磁盘；
- Buffer Pool 空间不足时，需要将一部分数据页淘汰掉，如果淘汰的是脏页，需要先将脏页同步到磁盘；
- MySQL 认为空闲时，后台线程会定期将适量的脏页刷入到磁盘；
- MySQL 正常关闭之前，会把所有的脏页刷入到磁盘；

#### Innodb 对页的管理

- Free List （空闲页链表），管理空闲页；
- Flush List （脏页链表），管理脏页；
- LRU List，管理脏页+干净页，将最近且经常查询的数据缓存在其中，而不常查询的数据就淘汰出去。

#### InnoDB 对 LRU 的优化

- 将 LRU 链表 分为**young 和 old 两个区域**，加入缓冲池的页，优先插入 old 区域；页被访问时，才进入 young 区域，目的是为了解决预读失效的问题。
- 当**「页被访问」且「 old 区域停留时间超过 `innodb_old_blocks_time` 阈值（默认为1秒）」**时，才会将页插入到 young 区域，否则还是插入到 old 区域，目的是为了解决批量数据访问，大量热数据淘汰的问题。

## [MySQL 事务](https://javaguide.cn/database/mysql/mysql-questions-01.html#mysql-事务)

### [数据库事务]()

**原子性**（`Atomicity`）：事务是最小的执行单位，不允许分割。事务的原子性确保动作要么全部完成，要么完全不起作用；

**一致性**（`Consistency`）：执行事务前后，数据保持一致，例如转账业务中，无论事务是否成功，转账者和收款人的总额应该是不变的；

**隔离性**（`Isolation`）：并发访问数据库时，一个用户的事务不被其他事务所干扰，各并发事务之间数据库是独立的；

**持久性**（`Durability`）：一个事务被提交之后。它对数据库中数据的改变是持久的，即使数据库发生故障也不应该对其有任何影响。

:star:，aid是手段，最后都是为了实现一致性c

### [并发事务带来了哪些问题?](https://javaguide.cn/database/mysql/mysql-questions-01.html#并发事务带来了哪些问题)

- 脏读：读到了未提交的数据
- 丢失修改：两个事务先后修改同一条数据，后发覆盖先发
- 不可重复读：一个事务期间在其开启时间中的t1和t2时间段分别读数据，但是t1和t2之间有其他事务提交对这条数据进行了修改，那么t2读到的和t1的就不一样了。
- 幻读：一个事务期间在其开启时间中的t1和t2时间段分别读范围数据，但是t1和t2之间有其他事务提交，插入数据到这个范围之间，t2和t1读到的不一致了。

### [不可重复读和幻读有什么区别？](#不可重复读和幻读有什么区别)

- 不可重复读的重点：多次读取同意记录值发现内容被修改，或者记录减少
- 幻读的重点：发现查到的记录增加了。

幻读其实可以看作是不可重复读的一种特殊情况，区分是因为解决的方案不同

举个例子：执行 `delete` 和 `update` 操作的时候，可以直接对记录加锁，保证事务安全。而执行 `insert` 操作的时候，由于记录锁（Record Lock）只能锁住已经存在的记录，为了避免插入新记录，需要依赖间隙锁（Gap Lock）。也就是说执行 `insert` 操作的时候需要依赖 Next-Key Lock（Record Lock+Gap Lock） 进行加锁来保证不出现幻读。

### [并发事务的控制方式有哪些？](#并发事务的控制方式有哪些)

MySQL 中并发事务的控制方式无非就两种：**锁** （悲观）和 **MVCC**（乐观）。

**锁** 控制方式下会通过锁来显示控制共享资源而不是通过调度手段，MySQL 中主要是通过 **读写锁** 来实现并发控制。

- **共享锁（S 锁）**：又称读锁，事务在读取记录的时候获取共享锁，允许多个事务同时获取（锁兼容）。
- **排他锁（X 锁）**：又称写锁/独占锁，事务在修改记录的时候获取排他锁，不允许多个事务同时获取。如果一个记录已经被加了排他锁，那其他事务不能再对这条记录加任何类型的锁（锁不兼容）。

读读并行，读写，写写都不可以并行

根据粒度：**表级锁(table-level locking)** 和 **行级锁(row-level locking)** （默认）

> 两种锁都有共享锁和排它锁

**MVCC** 是多版本并发控制方法，即对一份数据会存储多个版本，通过事务的可见性来保证事务能看到自己应该看到的版本。通常会有一个全局的版本分配器来为每一行数据设置版本号，版本号是唯一的。

MVCC 在 MySQL 中实现所依赖的手段主要是: **隐藏字段、read view、undo log**。

- undo log : undo log 用于记录某行数据的多个版本的数据。
- read view 和 隐藏字段 : 用来判断当前版本数据的可见性。

![image-20240425234232111](https://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240425234232111.png)

![image-20240425234254520](https://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240425234254520.png)

不同的隔离级别，生成ReadView的时机不同：

- READ COMMITTED ：在事务中每一次执行快照读时生成ReadView。
- REPEATABLE READ：仅在事务中第一次执行快照读时生成ReadView，后续复用该ReadView

[详细解释](https://frxcat.fun/database/MySQL/MySQL_InnoDB_engine/#mvcc)

### [SQL 标准定义了哪些事务隔离级别?](#sql-标准定义了哪些事务隔离级别)

SQL 标准定义了四个隔离级别：

- **READ-UNCOMMITTED(读取未提交)** ：最低的隔离级别，允许读取尚未提交的数据变更，可能会导致脏读、幻读或不可重复读。
- **READ-COMMITTED(读取已提交)** ：允许读取并发事务已经提交的数据，可以阻止脏读，但是幻读或不可重复读仍有可能发生。
- **REPEATABLE-READ(可重复读)** ：对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，可以阻止脏读和不可重复读，但幻读仍有可能发生。
- **SERIALIZABLE(可串行化)** ：最高的隔离级别，完全服从 ACID 的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，该级别可以防止脏读、不可重复读以及幻读。

![image-20240425234412346](https://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240425234412346.png)

### [MySQL 的隔离级别是基于锁实现的吗？](#mysql-的隔离级别是基于锁实现的吗)

MySQL 的隔离级别基于锁和 MVCC 机制共同实现的。

- SERIALIZABLE 隔离级别是通过锁来实现的
- READ-COMMITTED 和 REPEATABLE-READ 隔离级别是基于 MVCC 实现的。

> 不过， SERIALIZABLE 之外的其他隔离级别可能也需要用到锁机制，就比如 RR 在当前读情况下需要使用加锁读来保证不会出现幻读。

## [MySQL 锁](https://javaguide.cn/database/mysql/mysql-questions-01.html#mysql-锁)

锁是一种常见的并发事务的控制方式。

### [表级锁和行级锁了解吗？有什么区别？](#表级锁和行级锁了解吗-有什么区别)

行级锁的粒度更小，仅对相关的记录上锁即可（对一行或者多行记录加锁），对于并发写入来说，性能高

**表级锁和行级锁对比**：

- **表级锁：** MySQL 中锁定粒度最大的一种锁（全局锁除外），是针对==非索引字段==加的锁，对当前操作的整张表加锁，
  - 实现简单，资源消耗也比较少，加锁快，不会出现死锁。
  - 不过，触发锁冲突的概率最高，高并发下效率极低。
  - 表级锁和存储引擎无关，MyISAM 和 InnoDB 引擎都支持表级锁。

- **行级锁：** MySQL 中锁定粒度最小的一种锁，是 **针对索引字段加的锁** ，只针对当前操作的行记录进行加锁。
  -  行级锁能大大减少数据库操作的冲突。其加锁粒度最小，并发度高，
  - 但加锁的开销也最大，加锁慢，会出现死锁。
  - **行级锁和存储引擎有关，是在存储引擎层面实现的.**

### 元数据锁

我们不需要显示的使用 MDL，因为当我们对数据库表进行操作时，会自动给这个表加上 MDL：

- 对一张表进行 CRUD 操作时，加的是 **MDL 读锁**；
- 对一张表做结构变更操作的时候，加的是 **MDL 写锁**；

MDL 是在事务提交后才会释放，这意味着**事务执行期间，MDL 是一直持有的**

MDL存在的问题：

- A执行select，不提交事务，加MDL读锁
- B执行select，不会阻塞，读读不冲突
- C修改表字段，MDL读锁还在被占用，无法申请到MDL写锁，会阻塞

> 这是因为申请 MDL 锁的操作会形成一个队列，队列中**写锁获取优先级高于读锁**，一旦出现 MDL 写锁等待，会阻塞后续该表的所有 CRUD 操作，这种情况很危险，可以通过kill掉加MDL读锁的长事务解决

### 插入意向锁

**插入意向锁是一种特殊的间隙锁，但不同于间隙锁的是，该锁只用于并发插入操作**

尽管「插入意向锁」也属于间隙锁，但两个事务却不能在同一时间内，一个拥有间隙锁，另一个拥有该间隙区间内的插入意向锁（当然，插入意向锁如果不在间隙锁区间内则是可以的）。

插入意向锁的生成时机：

- 每插入一条新记录，都需要看一下待插入记录的下一条记录上是否已经被加了间隙锁，如果已加间隙锁，此时会生成一个插入意向锁，然后锁的状态设置为等待状态（*PS：MySQL 加锁时，是先生成锁结构，然后设置锁的状态，如果锁状态是等待状态，并不是意味着事务成功获取到了锁，只有当锁状态为正常状态时，才代表事务成功获取到了锁*），现象就是 Insert 语句会被阻塞。

### [行级锁的使用有什么注意事项？](#行级锁的使用有什么注意事项)

InnoDB 的行锁是针对索引字段加的锁，表级锁是针对非索引字段加的锁。

当我们执行 `UPDATE`、`DELETE` 语句时，如果 `WHERE`条件中字段没有命中唯一索引或者索引失效的话，就会导致扫描全表对表中的所有行记录进行加锁。

不过，很多时候即使用了索引也有可能会走全表扫描，这是因为 MySQL 优化器会进行谁比较快的判断。

### 加行级锁的几种情况

```sql
//对读取的记录加共享锁(S型锁)
select ... lock in share mode;

//对读取的记录加独占锁(X型锁)
select ... for update;

//对操作的记录加独占锁(X型锁)
update table .... where id = 1;

//对操作的记录加独占锁(X型锁)
delete from table where id = 1;
```

### [InnoDB 有哪几类行锁？](#innodb-有哪几类行锁)

InnoDB 行锁是通过对索引数据页上的记录加锁实现的，MySQL InnoDB 支持三种行锁定方式：

- **记录锁（Record Lock）**：也被称为记录锁，属于单个行记录上的锁。
- **间隙锁（Gap Lock）**：锁定一个范围，不包括记录本身。
- **临键锁（Next-Key Lock）**：Record Lock+Gap Lock，锁定一个范围，包含记录本身，主要目的是为了解决幻读问题（MySQL 事务部分提到过）。记录锁只能锁住已经存在的记录，为了避免插入新记录，需要依赖间隙锁。

![image-20240426000006323](https://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240426000006323.png)

**在 InnoDB 默认的隔离级别 RR 下，行锁默认使用的是 Next-Key Lock。但是，如果操作的索引是唯一索引或主键，InnoDB 会对 Next-Key Lock 进行优化，将其降级为 Record Lock，即仅锁住索引本身，而不是范围。**

### 临键锁的加锁范围

默认情况下，InnoDB在RR事务隔离级别运行，InnoDB使用 next-key 锁进行搜索和索引扫描，以防止幻读。

- 针对唯一索引进行检索时，对已存在的记录进行等值匹配时，将会自动优化为行锁

- 索引上的等值查询(唯一索引)，给不存在的记录加锁时, 优化为间隙锁 。
- 索引上的等值查询(非唯一普通索引)，向右遍历时最后一个值不满足查询需求时，next-key lock 退化为间隙锁。（左边是临建锁，右边是间隙锁）
- **非唯一索引范围查询，索引的 next-key lock 不会有退化为间隙锁和记录锁的情况**
- 索引上的范围查询(唯一索引)--会访问到不满足条件的第一个值为止。

> 注意:
>
> 间隙锁唯一目的是防止其他事务插入间隙。间隙锁可以共存，一个事务采用的间隙锁不会阻止另一个事务在同一间隙上采用间隙锁。
>
> 注意3中的情况：
>
> - 如果表中最后一个记录的 order_no 为 1005，那么等值查询 order_no = 1006（不存在），就是 next key lock，锁(1006, +∞]
>
> - 如果表中最后一个记录的 order_no 为 1010，那么等值查询 order_no = 1006（不存在），就是间隙锁

### [共享锁和排他锁呢？](#共享锁和排他锁呢)

不论是表级锁还是行级锁，都存在共享锁（Share Lock，S 锁）和排他锁（Exclusive Lock，X 锁）这两类：

- **共享锁（S 锁）**：又称读锁，事务在读取记录的时候获取共享锁，允许多个事务同时获取（锁兼容）。
- **排他锁（X 锁）**：又称写锁/独占锁，事务在修改记录的时候获取排他锁，不允许多个事务同时获取。如果一个记录已经被加了排他锁，那其他事务不能再对这条事务加任何类型的锁（锁不兼容）。

### [意向锁有什么作用？](#意向锁有什么作用)

通过意向锁判断当前表是否有行锁，进一步判断是否能施加表锁

意向锁是表级锁，共有两种：

- **意向共享锁（Intention Shared Lock，IS 锁）**：事务有意向对表中的某些记录加共享锁（S 锁），加共享锁前必须先取得该表的 IS 锁。
- **意向排他锁（Intention Exclusive Lock，IX 锁）**：事务有意向对表中的某些记录加排他锁（X 锁），加排他锁之前必须先取得该表的 IX 锁。

**意向锁是由数据引擎自己维护的，用户无法手动操作意向锁，在为数据行加共享/排他锁之前，InooDB 会先获取该数据行所在在数据表的对应意向锁。**

意向锁之间是互相兼容的。也不会和行级的共享锁和独占锁发生冲突

意向锁共享锁和表读锁兼容，和表写锁互斥；意向排他锁与表读锁，表写锁互斥（见下图）

![image-20240426144845408](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240426144845408.png)

### [当前读和快照读有什么区别？](https://javaguide.cn/database/mysql/mysql-questions-01.html#当前读和快照读有什么区别)

简单的select（不加锁）就是快照读，快照读是一致性非锁定读

**当前读** （一致性锁定读）就是给行记录加 X 锁或 S 锁。读取最新版本，同时保证读取的时候别的事务不能修改。

```sql
# 当前读例子
# 对读的记录加一个X锁
SELECT...FOR UPDATE
# 对读的记录加一个S锁
SELECT...LOCK IN SHARE MODE
# 对读的记录加一个S锁
SELECT...FOR SHARE
# 对修改的记录加一个X锁
INSERT...
UPDATE...
DELETE...
```

### [自增锁有了解吗？](https://javaguide.cn/database/mysql/mysql-questions-01.html#自增锁有了解吗)

与自增列有关的表级锁

### update锁的情况

并不是没加索引就会导致锁全表，还是要看语句实际上的执行计划，如果走全表扫描，那么就是对全表的记录加锁

:warning:实际上，加锁都是针对索引项加的，update 不带索引就是全表扫扫描，也就是表里的索引项都加锁，相当于锁了整张表，看起来是表锁，但是实际上不是

### insert是怎么加行级锁的

:question:细节不是太懂

Insert 语句在正常执行时是不会生成锁结构的，它是靠聚簇索引记录自带的 trx_id 隐藏列来作为**隐式锁**来保护记录的

隐式锁：当事务需要加锁的时，如果这个锁不可能发生冲突，InnoDB会跳过加锁环节，这种机制称为隐式锁

特殊情况下，隐式锁会转换为显式锁：

1. 如果记录之间加有间隙锁，为了避免幻读，此时是不能插入记录的；

2. 如果 Insert 的记录和已有记录存在唯一键冲突，此时也不能插入记录

1中的处理：

每插入一条新记录，都需要看一下待插入记录的下一条记录上是否已经被加了间隙锁，如果已加间隙锁，此时会生成一个插入意向锁，然后锁的状态设置为等待状态（*PS：MySQL 加锁时，是先生成锁结构，然后设置锁的状态，如果锁状态是等待状态，并不是意味着事务成功获取到了锁，只有当锁状态为正常状态时，才代表事务成功获取到了锁*），现象就是 Insert 语句会被阻塞。

2中的处理：

如果在插入新记录时，插入了一个与「已有的记录的主键或者唯一二级索引列值相同」的记录（不过可以有多条记录的唯一二级索引列的值同时为NULL，这里不考虑这种情况），此时插入就会失败，然后对于这条记录加上了 **S 型的锁**。

- 如果主键索引重复，插入新记录的事务会给已存在的主键值重复的聚簇索引记录**添加 S 型记录锁**。
- 如果唯一二级索引重复，插入新记录的事务都会给已存在的二级索引列值重复的二级索引记录**添加 S 型 next-key 锁**。

##  [MVCC]()

### [MVCC➕Next-key-Lock 防止幻读](https://javaguide.cn/database/mysql/innodb-implementation-of-mvcc.html#mvcc➕next-key-lock-防止幻读)

`InnoDB`存储引擎在 RR 级别下通过 `MVCC`和 `Next-key Lock` 来解决幻读问题：

**1、执行普通 `select`，此时会以 `MVCC` 快照读的方式读取数据**

在快照读的情况下，RR 隔离级别只会在事务开启后的第一次查询生成 `Read View` ，并使用至事务提交。所以在生成 `Read View` 之后其它事务所做的更新、插入记录版本对当前事务并不可见，实现了可重复读和防止快照读下的 “幻读”

**2、执行 select...for update/lock in share mode、insert、update、delete 等当前读**

在当前读下，读取的都是最新的数据，如果其它事务有插入新的记录，并且刚好在当前事务查询范围内，就会产生幻读！`InnoDB` 使用 [Next-key Lock](https://dev.mysql.com/doc/refman/5.7/en/innodb-locking.html#innodb-next-key-locks) 来防止这种情况。当执行当前读时，会锁定读取到的记录的同时，锁定它们的间隙，防止其它事务在查询范围内插入数据。只要我不让你插入，就不会发生幻读

### [幻读完全解决了么]()

:one:第一种情况

![image-20240509194936180](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240509194936180.png)

最开始表中没有id=5的数据，事务B提交之后，事务A的update语句可以操作这条记录，此时undo log中该记录的最新事务id就是事务A的id，第二个select的时候，快照虽然一样，但是能访问到了

:two:第二种情况

- T1 时刻：事务 A 先执行「快照读语句」：select * from t_test where id > 100 得到了 3 条记录。
- T2 时刻：事务 B 往插入一个 id= 200 的记录并提交；
- T3 时刻：事务 A 再执行「当前读语句」 select * from t_test where id > 100 for update 就会得到 4 条记录，此时也发生了幻读现象。

避免方法：开启事务之后，尽快执行`select ... for update`等语句进行加临建锁，避免其他事务插入

## [MySQL 性能优化](https://javaguide.cn/database/mysql/mysql-questions-01.html#mysql-性能优化)

### [能用 MySQL 直接存储文件（比如图片）吗？](https://javaguide.cn/database/mysql/mysql-questions-01.html#能用-mysql-直接存储文件-比如图片-吗)

可以直接存储文件对应的二进制数据，但是十分不推荐，影响性能，消耗存储空间。

建议使用文件存储服务，数据库中只存储对应地址

### [尽量用 UNION ALL 代替 UNION]()

UNION 会把两个结果集的所有数据放到临时表中后再进行去重操作，更耗时，更消耗 CPU 资源。

UNION ALL 不会再对结果集进行去重操作，获取到的数据包含重复的项。

不过，如果实际业务场景中不允许产生重复数据的话，还是可以使用 UNION。

### [尽量避免多表做 join]()

阿里巴巴《Java 开发手册》中有这样一段描述：

> 【强制】超过三个表禁止 join。需要 join 的字段，数据类型保持绝对一致;多表关联查询时，保证被关联 的字段需要有索引。

join 的效率比较低，主要原因是因为其使用嵌套循环（Nested Loop）来实现关联查询，三种不同的实现效率都不是很高

避免多表join的方法：

- 单表查询后在内存中自己做关联 ：对数据库做单表查询，再根据查询结果进行二次查询，以此类推，最后再进行关联（推荐）。
  - 单表更容易复用
  - 维护简单

- 数据冗余，把一些重要的数据在表中做冗余，尽可能地避免关联查询。很笨的一种做法，表结构比较稳定的情况下才会考虑这种做法。进行冗余设计之前，思考一下自己的表结构设计的是否有问题。

### [有哪些常见的 SQL 优化手段？](https://javaguide.cn/database/mysql/mysql-questions-01.html#有哪些常见的-sql-优化手段)

#### [避免使用`SELECT *`]()

- SELECT * 会消耗更多的 CPU。
- SELECT * 无用字段增加网络带宽资源消耗，增加数据传输时间
- SELECT * 无法使用 MySQL 优化器覆盖索引的优化
- SELECT <字段列表> 可减少表结构变更带来的影响。

#### [分页优化]()

通过==覆盖索引加子查询==的方法进行优化：先通过创建覆盖索引，将覆盖索引上的列排序查出之后（所需要排序的条数）作为子查询查其他的列

#### [插入数据]()

多条插入数据的时候：

1. 批量插入

```mysql
Insert into tb_test values(1,'Tom'),(2,'Cat'),(3,'Jerry');
```

2. 手动控制事务

```sql
start transaction;

insert into tb_test values(1,'Tom'),(2,'Cat'),(3,'Jerry');

insert into tb_test values(4,'Tom'),(5,'Cat'),(6,'Jerry');

insert into tb_test values(7,'Tom'),(8,'Cat'),(9,'Jerry');

commit;
```

3. 主键顺序插入，性能要高于乱序插入。

```sql
主键乱序插入 : 8 1 9 21 88 2 4 15 89 5 7 3
主键顺序插入 : 1 2 3 4 5 7 8 9 15 21 88 89
```

4. 大批量插入的时候使用`load`指令，而不是使用`insert`

#### [主键优化]()

主键设计原则：

1. 满足业务需求的情况下，尽量降低主键的长度。
2. 插入数据时，尽量选择顺序插入，选择使用`AUTO_INCREMENT`自增主键。
3. 尽量不要使用`UUID做主键`或者是`其他自然主键`，如身份证号。
4. 业务操作时，`避免对主键的修改`

#### [order by优化]()

MySQL的排序，有两种方式：

`Using filesort` : 通过表的索引或全表扫描，读取满足条件的数据行，然后在排序缓冲区sort buffer中完成排序操作，所有==不是通过索引==直接返回排序结果的排序都叫 FileSort 排序。

`Using index` : 通过有序索引顺序扫描直接返回有序数据，这种情况即为 using index，不需要额外排序，操作效率高。

`order by优化原则`:

1. 根据排序字段建立合适的索引，多字段排序时，也<mark>遵循最左前缀法则</mark>。
2. 尽量使用覆盖索引。
3. 多字段排序, 一个升序一个降序，此时需要<mark>注意联合索引在创建时的规则（ASC/DESC）</mark>。
4. 如果不可避免的出现filesort，大数据量排序时，可以适当增大排序缓冲区大小`sort_buffer_size(默认256k)`。

#### [group by优化]()

1. 在分组操作时，可以通过索引来提高效率。
2. 分组操作时，索引的使用也是满足最左前缀法则的

#### [count 优化]()

- MyISAM 引擎把一个表的总行数存在了磁盘上，因此执行 count(*) 的时候会直接返回这个数，效率很高； 但是如果是带条件的count，MyISAM也慢。
- InnoDB 引擎就麻烦了，它执行 count(*) 的时候，需要把数据一行一行地从引擎里面读出来，然后累积计数。

![image-20240428163127078](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240428163127078.png)

count(1)、 count(*)、 count(主键字段)在执行的时候，如果表里存在二级索引，优化器就会选择二级索引进行扫描，使用时尽量在数据表上建立二级索引，这样优化器会自动采用 key_len 最小的二级索引进行扫描，相比于扫描主键索引效率会高一些。

不要使用 count(字段) 来统计记录个数，因为它的效率是最差的，会采用全表扫描的方式来统计。如果你非要统计表中该字段不为 NULL 的记录个数，建议给这个字段建立一个二级索引

:question:如果count（字段）所查询的字段上有索引，是不是会快一点？用索引了？

还有两种优化方式：

- 使用`explain select count(*)`统计近似值
- 使用额外的字段保存计数值

#### [update 优化]()

行级锁是针对索引的锁，注意索引，不要让行级锁升级为表锁

### [MySQL 如何存储 IP 地址？](#mysql-如何存储-ip-地址)

可以将 IP 地址转换成整形数据存储，性能更好，占用空间也更小。

MySQL 提供了两个方法来处理 ip 地址

- `INET_ATON()`：把 ip 转为无符号整型 (4-8 位)
- `INET_NTOA()` :把整型的 ip 转为地址

插入数据前，先用 `INET_ATON()` 把 ip 地址转为整型，显示数据时，使用 `INET_NTOA()` 把整型的 ip 地址转为地址显示即可

### [如何分析 SQL 的性能？](https://javaguide.cn/database/mysql/mysql-questions-01.html#如何分析-sql-的性能)

我们可以使用 `EXPLAIN` 命令来分析 SQL 的 **执行计划** 。执行计划是指一条 SQL 语句在经过 MySQL 查询优化器的优化会后，具体的执行方式。

对于执行计划，参数有：

- `possible_keys `字段表示可能用到的索引；
- `key `字段表示实际用的索引，如果这一项为 NULL，说明没有使用索引；
- `key_len `表示索引的长度；
- `rows `表示扫描的数据行数。
- `type `表示数据扫描类型，我们需要重点看这个。

type 字段就是描述了找到所需数据时使用的扫描方式是什么，常见扫描类型的**执行效率从低到高的顺序为**：

- `All`（全表扫描）；
- `index`（全索引扫描）；
- `range`（索引范围扫描）；
- `ref`（非唯一索引扫描）；
- `eq_ref`（唯一索引扫描）；
- `const`（结果只有一条的主键或唯一索引扫描）。

extra字段中的几个常见结果：

- `Using filesort` ：当查询语句中包含 group by 操作，而且无法利用索引完成排序操作的时候， 这时不得不选择相应的排序算法进行，甚至可能会通过文件排序，效率是很低的，所以要避免这种问题的出现。
- `Using temporary`：使了用临时表保存中间结果，MySQL 在对查询结果排序时使用临时表，常见于排序 order by 和分组查询 group by。效率低，要避免这种问题的出现。
- `Using index`：所需数据只需在索引即可全部获得，不须要再到表中取数据，也就是使用了覆盖索引，避免了回表操作，效率不错。

## [主键和外键有什么区别?](https://javaguide.cn/database/basis.html#主键和外键有什么区别)

**主键(主码)**：主键用于唯一标识一个元组，不能有重复，不允许为空。一个表只能有一个主键。

**外键(外码)**：外键用来和其他表建立联系用，外键是另一表的主键，外键是可以有重复的，可以是空值。一个表可以有多个外键。

## [读写分离和分库分表]()

### [如何实现读写分离？](https://javaguide.cn/high-performance/read-and-write-separation-and-library-subtable.html#如何实现读写分离)

:one:代理方式

![image-20240508161252011](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240508161252011.png)

提供类似功能的中间件有 **MySQL Router**（官方， MySQL Proxy 的替代方案）、**Atlas**（基于 MySQL Proxy）、**MaxScale**、**MyCat**

在 MySQL 8.2 的版本中，MySQL Router 能自动分辨对数据库读写/操作并把这些操作路由到正确的实例上

:two:组件方式

引入第三方组件来帮助我们读写请求，采用这种方式的话，推荐使用 `sharding-jdbc` ，直接引入 jar 包即可使用，非常方便。同时，也节省了很多运维的成本

### [主从复制原理是什么？](https://javaguide.cn/high-performance/read-and-write-separation-and-library-subtable.html#主从复制原理是什么)

1. 主库将数据库中数据的变化写入到 binlog
2. 从库连接主库
3. 从库会创建一个 I/O 线程向主库请求更新的 binlog
4. 主库会创建一个 binlog dump 线程来发送 binlog ，从库中的 I/O 线程负责接收
5. 从库的 I/O 线程将接收的 binlog 写入到 relay log 中。
6. 从库的 SQL 线程读取 relay log 同步数据到本地（也就是再执行一遍 SQL ）。

> 阿里开源的canal，可以帮助实现mysql和其他数据源同步，依赖于binlog

### 主从复制的模型

- 同步复制：MySQL 主库提交事务的线程要等待所有从库的复制成功响应，才返回客户端结果
  - 性能差，所有的都响应才行
  - 可用性差，主从任何一个出问题，都影响业务
- 异步复制（默认模型）：：MySQL 主库提交事务的线程并不会等待 binlog 同步到各从库，就返回客户端结果。
  - 一旦主库宕机，数据就会发生丢失
- 半同步复制：MySQL 5.7 版本之后增加的一种复制方式，介于两者之间，事务线程不用等待所有的从库复制成功响应，只要一部分复制成功响应回来就行，比如一主二从的集群，只要数据成功复制到任意一个从库上，主库的事务线程就可以返回给客户端。
  - 这种**半同步复制的方式，兼顾了异步复制和同步复制的优点，即使出现主库宕机，至少还有一个从库有最新的数据，不存在数据丢失的风险**

### [如何避免主从延迟？](https://javaguide.cn/high-performance/read-and-write-separation-and-library-subtable.html#如何避免主从延迟)

#### [强制将读请求路由到主库处理](https://javaguide.cn/high-performance/read-and-write-separation-and-library-subtable.html#强制将读请求路由到主库处理)

既然你从库的数据过期了，那我就直接从主库读取嘛！这种方案虽然会增加主库的压力，但是，实现起来比较简单，也是我了解到的使用最多的一种方式。

比如 `Sharding-JDBC` 就是采用的这种方案。通过使用 Sharding-JDBC 的 `HintManager` 分片键值管理器，我们可以强制使用主库。

```java
HintManager hintManager = HintManager.getInstance();
hintManager.setMasterRouteOnly();
// 继续JDBC操作
```

#### [延迟读取](https://javaguide.cn/high-performance/read-and-write-separation-and-library-subtable.html#延迟读取)

还有一些朋友肯定会想既然主从同步存在延迟，那我就在延迟之后读取啊，比如主从同步延迟 0.5s,那我就 1s 之后再读取数据。这样多方便啊！方便是方便，但是也很扯淡。

不过，如果你是这样设计业务流程就会好很多：对于一些对数据比较敏感的场景，你可以在完成写请求之后，避免立即进行请求操作。比如你支付成功之后，跳转到一个支付成功的页面，当你点击返回之后才返回自己的账户。

> 没办法完全避免主从延迟，只能说可以减少出现延迟的概率而已，实际项目中一般不会使用

### [什么情况下会出现主从延迟？如何尽量减少延迟？](https://javaguide.cn/high-performance/read-and-write-separation-and-library-subtable.html#什么情况下会出现主从延迟-如何尽量减少延迟)

MySQL 主从同步延时是指从库的数据落后于主库的数据，这种情况可能由以下两个原因造成：

1. 从库 I/O 线程接收 binlog 的速度跟不上主库写入 binlog 的速度，导致从库 relay log 的数据滞后于主库 binlog 的数据；
2. 从库 SQL 线程执行 relay log 的速度跟不上从库 I/O 线程接收 binlog 的速度，导致从库的数据滞后于从库 relay log 的数据。

### [什么是分库？](https://javaguide.cn/high-performance/read-and-write-separation-and-library-subtable.html#什么是分库)

**分库** 就是将数据库中的数据分散到不同的数据库上

- 垂直分库：就是把单一数据库按照业务进行划分，不同的业务使用不同的数据库，进而将一个数据库的压力分担到多个数据库。

![image-20240508163503973](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240508163503973.png)

- 水平分库：是把同一个表按一定规则拆分到不同的数据库中，每个库可以位于不同的服务器上，这样就实现了水平扩展，解决了单表的存储和性能瓶颈的问题

![image-20240508163529196](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240508163529196.png)

### [什么是分表？](https://javaguide.cn/high-performance/read-and-write-separation-and-library-subtable.html#什么是分表)

![image-20240508163627034](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240508163627034.png)

水平分表通常与分库一起使用

### [什么情况下需要分库分表？](https://javaguide.cn/high-performance/read-and-write-separation-and-library-subtable.html#什么情况下需要分库分表)

- 单表的数据达到千万级别以上，数据库读写速度比较缓慢。
- 数据库中的数据占用的空间越来越大，备份时间越来越长。
- 应用的并发量太大（应该优先考虑其他性能优化方法，而非分库分表）。

分库分表的成本太高，如非必要尽量不要采用

### [常见的分片算法有哪些？](https://javaguide.cn/high-performance/read-and-write-separation-and-library-subtable.html#常见的分片算法有哪些)

分片算法主要解决了数据被水平分片之后，数据究竟该存放在哪个表的问题。

常见的分片算法有：

- **哈希分片**：求指定分片键的哈希，然后根据哈希值确定数据应被放置在哪个表中。
  - 哈希分片比较适合随机读写的场景，不太适合经常需要范围查询的场景。
  - 哈希分片可以使每个表的数据分布相对均匀，但对动态伸缩（例如新增一个表或者库）不友好。
- **范围分片**：按照特定的范围区间（比如时间区间、ID 区间）来分配数据，比如 将 `id` 为 `1~299999` 的记录分到第一个表， `300000~599999` 的分到第二个表。
  - 范围分片适合需要经常进行范围查找且数据分布均匀的场景，不太适合随机读写的场景（数据未被分散，容易出现热点数据的问题）。
- **映射表分片**：使用一个单独的表（称为映射表）来存储分片键和分片位置的对应关系。映射表分片策略可以支持任何类型的分片算法，如哈希分片、范围分片等。
  - 映射表分片策略是可以灵活地调整分片规则，不需要修改应用程序代码或重新分布数据。
  - 不过，这种方式需要维护额外的表，还增加了查询的开销和复杂度。
- **一致性哈希分片**：将哈希空间组织成一个环形结构，将分片键和节点（数据库或表）都映射到这个环上，然后根据顺时针的规则确定数据或请求应该分配到哪个节点上，解决了传统哈希对动态伸缩不友好的问题。[一致性哈希介绍文章](https://zhuanlan.zhihu.com/p/129049724)
- **地理位置分片**：很多 NewSQL 数据库都支持地理位置分片算法，也就是根据地理位置（如城市、地域）来分配数据。
- **融合算法分片**：灵活组合多种分片算法，比如将哈希分片和范围分片组合。

### [分片键如何选择？](https://javaguide.cn/high-performance/read-and-write-separation-and-library-subtable.html#分片键如何选择)

分片键（Sharding Key）是数据分片的关键字段。分片键的选择非常重要，它关系着数据的分布和查询效率。一般来说，分片键应该具备以下特点：

- 具有共性，即能够覆盖绝大多数的查询场景，尽量减少单次查询所涉及的分片数量，降低数据库压力；
- 具有离散性，即能够将数据均匀地分散到各个分片上，避免数据倾斜和热点问题；
- 具有稳定性，即分片键的值不会发生变化，避免数据迁移和一致性问题；
- 具有扩展性，即能够支持分片的动态增加和减少，避免数据重新分片的开销。

实际项目中，分片键很难满足上面提到的所有特点，需要权衡一下。并且，分片键可以是表中多个字段的组合，例如取用户 ID 后四位作为订单 ID 后缀

### [分库分表会带来什么问题呢？](https://javaguide.cn/high-performance/read-and-write-separation-and-library-subtable.html#分库分表会带来什么问题呢)

- **join操作**：同一个数据库中的表分到不同的数据库，join操作不能自动完成，需要手动封装，比如在一个数据库中查询到一个数据，再根据这个数据去另外一个数据库中找对应的数据。join操作也可以使用多次查询解决。
- **事务问题**：需要分布式事务
- **分布式ID**：之前的自增主键没法满足要求了，需要分布式ID
- **跨库聚合查询问题**：分库分表会导致常规聚合查询操作，如 group by，order by 等变得异常复杂。这是因为这些操作需要在多个分片上进行数据汇总和排序，而不是在单个数据库上进行
- 引入分库分表之后，一般需要 DBA 的参与，同时还需要更多的数据库服务器，这些都属于成本

### [分库分表有没有什么比较推荐的方案？](https://javaguide.cn/high-performance/read-and-write-separation-and-library-subtable.html#分库分表有没有什么比较推荐的方案)

:one:Apache ShardingSphere 是一款分布式的数据库生态系统， 可以将任意数据库转换为分布式数据库，并通过数据分片、弹性伸缩、加密等能力对原有数据库进行增强。

除了支持读写分离和分库分表，还提供分布式事务、数据库治理、影子库、数据加密和脱敏等功能。

现在很多公司都是用的类似于 TiDB 这种分布式关系型数据库，不需要我们手动进行分库分表（数据库层面已经帮我们做了），也不需要解决手动分库分表引入的各种问题，直接一步到位，内置很多实用的功能（如无感扩容和缩容、冷热存储分离）

:two:Mycat是开源的、活跃的、基于Java语言编写的MySQL数据库中间件。可以像使用mysql一样来使用mycat，对于开发人员来说根本感觉不到mycat的存在

开发人员只需要连接MyCat即可，而具体底层用到几台数据库，每一台数据库服务器里面存储了什么数据，都无需关心。 具体的分库分表的策略，只需要在MyCat中配置即可

### [分库分表后，数据怎么迁移呢？](https://javaguide.cn/high-performance/read-and-write-separation-and-library-subtable.html#分库分表后-数据怎么迁移呢)

:one:停机迁移

:two:双写方案 

- 对老库进行更新操作（增删改）的同时，也要对写入新库（双写）。操作数据不在新库，则插入新库
- 双写只会让被更新操作过的老库中的数据同步到新库，我们还需要自己写脚本将老库中的数据和新库的数据做比对。
  - 新库没有，老库有，插入新库
  - 新库有，老库没有，新库对应数据删除（冗余数据清理）
  - 重复直到数据一致

想要在项目中实施双写还是比较麻烦的，很容易会出现问题。我们可以借助上面提到的数据库同步工具 Canal 做增量数据迁移（还是依赖 binlog，开发和维护成本较低）

## [深度分页如何优化？](https://javaguide.cn/database/mysql/mysql-questions-01.html#深度分页如何优化)

慢的原因，例子，[参考文章](https://juejin.cn/post/7012016858379321358)

表结构

```sql
CREATE TABLE account (
  id int(11) NOT NULL AUTO_INCREMENT COMMENT '主键Id',
  name varchar(255) DEFAULT NULL COMMENT '账户名',
  balance int(11) DEFAULT NULL COMMENT '余额',
  create_time datetime NOT NULL COMMENT '创建时间',
  update_time datetime NOT NULL ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  PRIMARY KEY (id),
  KEY idx_name (name),
  KEY idx_update_time (update_time) //索引
) ENGINE=InnoDB AUTO_INCREMENT=1570068 DEFAULT CHARSET=utf8 ROW_FORMAT=REDUNDANT COMMENT='账户表';

```

除了主键的聚集索引外，在name，和update_time上有单列索引

```sql
select id,name,balance 
from account 
where update_time> '2020-09-19' 
limit 100000,10;
```

执行过程：

1. 通过**普通二级索引树**idx_update_time，过滤update_time条件，找到满足条件的记录ID。
2. 通过ID，回到**主键索引树**，找到满足记录的行，然后取出展示的列（**回表**）
3. 扫描满足条件的100010行，然后扔掉前100000行，返回

:star:子查询优化

```sql
select id,name,balance 
FROM account 
where id >= (select a.id from account a where a.update_time >= '2020-09-19' limit 100000, 1) 
LIMIT 10;#（可以加下时间条件到外面的主查询）
```

子查询 table a查询是用到了`idx_update_time`索引。首先在索引上拿到了聚集索引的主键ID,省去了回表操作，然后第二查询直接根据第一个查询的 ID往后再去查10个就可以了!

:star:INNER JOIN 延迟关联

```sql
SELECT  acct1.id,acct1.name,acct1.balance 
FROM account acct1 
INNER JOIN (SELECT a.id FROM account a WHERE a.update_time >= '2020-09-19' ORDER BY a.update_time LIMIT 100000, 10) AS  acct2 
on acct1.id= acct2.id;
```

## [数据冷热分离如何做？](https://javaguide.cn/database/mysql/mysql-questions-01.html#数据冷热分离如何做)

### [什么是数据冷热分离？](https://javaguide.cn/high-performance/data-cold-hot-separation.html#什么是数据冷热分离)

数据冷热分离是指根据数据的访问频率和业务重要性，将数据分为冷数据和热数据，

- 冷数据一般存储在存储在低成本、低性能的介质中
- 热数据高性能存储介质中

冷数据和热数据的区分方法：

- **时间维度区分**：按照数据的创建时间、更新时间、过期时间等，将一定时间段内的数据视为热数据，超过该时间段的数据视为冷数据。例如，订单系统可以将 1 年前的订单数据作为冷数据，1 年内的订单数据作为热数据。这种方法适用于数据的访问频率和时间有较强的相关性的场景。
- **访问频率区分**：将高频访问的数据视为热数据，低频访问的数据视为冷数据。例如，内容系统可以将浏览量非常低的文章作为冷数据，浏览量较高的文章作为热数据。这种方法需要记录数据的访问频率，成本较高，适合访问频率和数据本身有较强的相关性的场景。

### [数据冷热分离的优缺点](https://javaguide.cn/high-performance/data-cold-hot-separation.html#数据冷热分离的优缺点)

- 优点：
  - 热数据的查询性能得到优化（用户的绝大部分操作体验会更好）
  - 节约成本（可以冷热数据的不同存储需求，选择对应的数据库类型和硬件配置，比如将热数据放在 SSD 上，将冷数据放在 HDD 上）
- 缺点：
  - 系统复杂性和风险增加（需要分离冷热数据，数据错误的风险增加）
  - 统计效率低（统计的时候可能需要用到冷库的数据）。

### [冷数据如何迁移？](https://javaguide.cn/high-performance/data-cold-hot-separation.html#冷数据如何迁移)

冷数据迁移方案：

1. 业务层代码实现：当有对数据进行写操作时，触发冷热分离的逻辑，判断数据是冷数据还是热数据，冷数据就入冷库，热数据就入热库。
   - 缺点：这种方案会影响性能且冷热数据的判断逻辑不太好确定，还需要修改业务层代码，因此一般不会使用。
2. 任务调度：可以利用 xxl-job 或者其他分布式任务调度平台定时去扫描数据库，找出满足冷数据条件的数据，然后批量地将其复制到冷库中，并从热库中删除。这种方法修改的代码非常少，非常适合按照时间区分冷热数据的场景。
3. 监听数据库的变更日志 binlog ：将满足冷数据条件的数据从 binlog 中提取出来，然后复制到冷库中，并从热库中删除。这种方法可以不用修改代码，但不适合按照时间维度区分冷热数据的场景。

如果你的公司有 DBA 的话，也可以让 DBA 进行冷数据的人工迁移，一次迁移完成冷数据到冷库。然后，再搭配上面介绍的方案实现后续冷数据的迁移工作。

### [冷数据如何存储？](https://javaguide.cn/high-performance/data-cold-hot-separation.html#冷数据如何存储)

冷数据的存储要求主要是容量大，成本低，可靠性高，访问速度可以适当牺牲。

冷数据存储方案：

- 中小厂：直接使用 MySQL/PostgreSQL 即可（不改变数据库选型和项目当前使用的数据库保持一致），比如新增一张表来存储某个业务的冷数据或者使用单独的冷库来存放冷数据（涉及跨库查询，增加了系统复杂性和维护难度）
- 大厂：Hbase（常用）、RocksDB、Doris、Cassandra

如果公司成本预算足的话，也可以直接上 TiDB 这种分布式关系型数据库，直接一步到位。TiDB 6.0 正式支持数据冷热存储分离，可以降低 SSD 使用成本。使用 TiDB 6.0 的数据放置功能，可以在同一个集群实现海量数据的冷热存储，将新的热数据存入 SSD，历史冷数据存入 HDD。

## [为什么不推荐使用外键与级联？](https://javaguide.cn/database/basis.html#为什么不推荐使用外键与级联)

阿里开发手册强制：不得使用外键与级联，一切外键概念必须在应用层解决。

说明: 以学生和成绩的关系为例，学生表中的 student_id 是主键，那么成绩表中的 student_id 则为外键。如果更新学生表中的 student_id，同时触发成绩表中的 student_id 更新，即为级联更新。外键与级联更新适用于单机低并发，不适合分布式、高并发集群；级联更新是强阻塞，存在数据库更新风暴的风险；外键影响数据库的插入速度

不使用外键的原因

- **增加了复杂性：**
  -  a. 每次做 DELETE 或者 UPDATE 都必须考虑外键约束，会导致开发的时候很痛苦, 测试数据极为不方便;
  -  b. 外键的主从关系是定的，假如那天需求有变化，数据库中的这个字段根本不需要和其他表有关联的话就会增加很多麻烦。
- **增加了额外工作**：数据库需要增加维护外键的工作，比如当我们做一些涉及外键字段的增，删，更新操作之后，需要触发相关操作去检查，保证数据的的一致性和正确性，这样会不得不消耗资源；（个人觉得这个不是不用外键的原因，因为即使你不使用外键，你在应用层面也还是要保证的。所以，我觉得这个影响可以忽略不计。）
- **对分库分表不友好**：因为分库分表下外键是无法生效的。

## [SQL 和 NoSQL 有什么区别？](#sql-和-nosql-有什么区别)

|              | SQL 数据库                                                   | NoSQL 数据库                                                 |
| :----------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 数据存储模型 | 结构化存储，具有固定行和列的表格                             | 非结构化存储。文档：JSON 文档，键值：键值对，宽列：包含行和动态列的表，图：节点和边 |
| 发展历程     | 开发于 1970 年代，重点是减少数据重复                         | 开发于 2000 年代后期，重点是提升可扩展性，减少大规模数据的存储成本 |
| 例子         | Oracle、MySQL、Microsoft SQL Server、PostgreSQL              | 文档：MongoDB、CouchDB，键值：Redis、DynamoDB，宽列：Cassandra、 HBase，图表：Neo4j、 Amazon Neptune、Giraph |
| ACID 属性    | 提供原子性、一致性、隔离性和持久性 (ACID) 属性               | 通常不支持 ACID 事务，为了可扩展、高性能进行了权衡，少部分支持比如 MongoDB 。不过，MongoDB 对 ACID 事务 的支持和 MySQL 还是有所区别的。 |
| 性能         | 性能通常取决于磁盘子系统。要获得最佳性能，通常需要优化查询、索引和表结构。 | 性能通常由底层硬件集群大小、网络延迟以及调用应用程序来决定。 |
| 扩展         | 垂直（使用性能更强大的服务器进行扩展）、读写分离、分库分表   | 横向（增加服务器的方式横向扩展，通常是基于分片机制）         |
| 用途         | 普通企业级的项目的数据存储                                   | 用途广泛比如图数据库支持分析和遍历连接数据之间的关系、键值数据库可以处理大量数据扩展和极高的状态变化 |
| 查询语法     | 结构化查询语言 (SQL)                                         | 数据访问语法可能因数据库而异                                 |

## [字符集](https://javaguide.cn/database/character-set.html#unicode-utf-8)

- **ASCII** (**A**merican **S**tandard **C**ode for **I**nformation **I**nterchange，美国信息交换标准代码) 是一套主要用于现代美国英语的字符集（这也是 ASCII 字符集的局限性所在），一个字节表示一个字符
- GB2312收录6700多汉字，基本涵盖了绝大部分常用汉字。不过，GB2312 字符集不支持绝大部分的生僻字和繁体字，英语一字节，非英语二字节
- GBKGBK 字符集可以看作是 GB2312 字符集的扩展，兼容 GB2312 字符集，共收录了 20000 多个汉字
- GB18030 完全兼容 GB2312 和 GBK 字符集，纳入中国国内少数民族的文字，且收录了日韩汉字，是目前为止最全面的汉字字符集，共收录汉字 70000 多个
- BIG5 主要针对的是繁体中文，收录了 13000 多个汉字

- Unicode 字符集中包含了世界上几乎所有已知的字符。不过，Unicode 字符集并没有规定如何存储这些字符（也就是如何使用二进制数据表示这些字符）。
  - 然后，就有了 **UTF-8**（**8**-bit **U**nicode **T**ransformation **F**ormat）。类似的还有 UTF-16、 UTF-32。
  - UTF-8 使用 ==1 到 4 个字节==为每个字符编码， UTF-16 使用 2 或 4 个字节为每个字符编码，UTF-32 固定位 4 个字节为每个字符编码.
  - UTF-8 可以根据不同的符号自动选择编码的长短，像英文字符只需要 1 个字节就够了，这一点 ASCII 字符集一样 。因此，对于英语字符，UTF-8 编码和 ASCII 码是相同的

MySQL 支持很多种字符集的方式，比如 GB2312、GBK、BIG5、多种 Unicode 字符集（UTF-8 编码、UTF-16 编码、UCS-2 编码、UTF-32 编码等等

在 MySQL5.7 中，默认字符集是 `latin1` ；在 MySQL8.0 中，默认字符集是 `utf8mb4`

MySQL 字符编码集中有两套 UTF-8 编码实现：

- **`utf8`**：`utf8`编码只支持`1-3`个字节 。 在 `utf8` 编码中，中文是占 3 个字节，其他数字、英文、符号占一个字节。但 emoji 符号占 4 个字节，一些较复杂的文字、繁体字也是 4 个字节。

  > 实际上不是完成的uft-8实现，涉及到mysql的一个遗留问题

- **`utf8mb4`**：UTF-8 的完整实现，正版！最多支持使用 4 个字节表示字符，因此，可以用来存储 emoji 符号。



