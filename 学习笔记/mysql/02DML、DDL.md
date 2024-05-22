[toc]
<font size=3>
# 创建表
```
    create table 表名(
		字段名1 数据类型,
		字段名2 数据类型,
		字段名3 数据类型,
			....
	);
```
> 表名在数据库当中一般建议以：t_或者tbl_开始。

数据类型|解释
---|---
int	|	整数型(java中的int)
bigint|	长整型(java中的long)
float|		浮点型(java中的float double)
char|		定长字符串(String)
varchar|	可变长字符串(StringBuffer/StringBuilder)
date|		日期类型 （对应Java中的java.sql.Date类型）
BLOB|		二进制大对象（存储图片、视频等流媒体信息） Binary Large OBject （对应java中的Object）
CLOB|		字符大对象（存储较大文本，比如，可以存储4G的字符串。） Character Large OBject （对应java中的Object）

- char和varchar：
    - char(6)存储的时候超过6个报错，不足6个分配的空间也是6
    - varchar(6)存储的时候超过6个报错，不足6个，是多少分配多少的空间
    - char的效率比较高
- BLOB和CLOB：不能使用insert插入，只能使用java中的IO流进行插入
```
电影表: t_movie
		id(int)	name(varchar)		playtime(date/char)		haibao(BLOB)		history(CLOB)
		----------------------------------------------------------------------------------------
		1			season3	
		2
		3
```
- 在mysql当中怎么计算两个日期的“年差”，差了多少年？

```
	TimeStampDiff(间隔类型, 前一个日期, 后一个日期)
	
	timestampdiff(YEAR, hiredate, now())

	间隔类型：
		SECOND   秒，
		MINUTE   分钟，
		HOUR   小时，
		DAY   天，
		WEEK   星期
		MONTH   月，
		QUARTER   季度，
		YEAR   年 
```

# 插入数据
```sql
insert into 表名(字段名1,字段名2,字段名3,....) values(值1,值2,值3,....),(值1,值2,值3,....),(值1,值2,值3,....)...
	要求：字段的数量和值的数量相同，并且数据类型要对应相同。
```
> 当一条insert语句执行成功之后，表格当中必然会多一行记录。
	即使多的这一行记录当中某些字段是NULL，后期也没有办法在执行
	insert语句插入数据了，只能使用update进行更新。

# 表的复制
```sql
create table 表名 as select语句;
#将查询结果当做表创建出来。
	
insert into dept1 select * from dept;	
#将查询结果插入到一张表	
#对表的结构有要求
```

# 修改数据
```sql
update 表名 set 字段名1=值1,字段名2=值2... where 条件;
```
> 没有条件整张表数据全部更新。

# 删除数据
```sql
delete from 表名 where 条件;
```
> 没有条件全部删除

- 怎么删除大表的数据（重点）
```sql
truncate table 表名; #表被截断，不可回滚。永久丢失。
```

# 删除表
```sql
drop table 表名; #这个通用。
drop table if exists 表名; #oracle不支持这种写法。
```


# 修改表结构
- 使用工具


# 出现在java中的sql
- 包括：insert delete update select（CRUD)
- Create（增） Retrieve（检索） Update（修改） Delete（删除）










</font>