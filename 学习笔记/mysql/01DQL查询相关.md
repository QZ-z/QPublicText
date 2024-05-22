[toc]
<font size=3>
# 查询
- distinct关键字去除重复记录，只能出现在所有字段的最前边
- select rand():随机返回0到1间的一个小数（mysql中独有的）
    > 随机排序`select * from question order by rand() desc limit 4`
## 简单查询
```
select 字段名1，字段名2，字段名3··· from 表名

//别名
select 字段名 as 别名 from 表名
//别名是中文用  单引号  括起来
//标准sql语句中要求字符串使用单引号括起来，但是MySQL可以用双引号，但是不通用，不建议使用
//as可以省略
// * 代表所有，实际开发不建议用，效率低。
```
> - 任何一条sql语句由“；”结尾
> - sql语句不区分大小写
> - 字段可以参与数学运算
---

## 条件查询
```
select
    字段，字段···
from
    表名
where
    条件;

//先from，再where，最后select
```
运算符|说明
---|---
=|	等于
<>或!=|	不等于
<	|小于
<=|	小于等于
>|	大于
>=	|大于等于
between … and ….|	两个值之间,等同于 >= and <=
is null	|为null（is not null 不为空）
and	|并且
or	|或者
in	|包含，相当于多个or（not in不在这个范围中）
not	|not可以取非，主要用在is 或in中
like|	like称为模糊查询，支持%或下划线匹配 ，%匹配任意个字符，一个下划线只匹配一个字符

> - 注意运算符的优先级，在优先级不确定的时候添加小括号
> - 在数据库中NULL不是一个值，代表什么也没有，为空。不能用“=”衡量，必须是用is null或is not null
> - not in后小括号中注意手动排除NULL

# 数据排序
```
order by xxx asc/desc
//默认升序
//asc升序，desc降序
//多个字段，前边的起主导作用，前边相同，排后边
//by后可以跟数字（代表第几列）
//order by最后执行
```

# 分组函数

count|	取得记录数
--|--
sum	|求和
avg	|取平均
max	|取最大的数
min	|取最小的数

> - 都是对“某一组”数据进行操作
> - 分组函数自动忽略NULL
> - 所有数据都规定：只要有NULL参与的运算结果一定是NULL
> - 是多行处理函数，输入多行，最后的输出结果是一行
> - **分组函数不能直接使用在where语句中**，group by在where之后执行

- count(*):不是统计某个字段中数据的个数，而是统计总记录条数。（和某个字段无关）
- count(comm): 表示统计comm字段中不为NULL的数据总数量。

# 单行处理函数
- 什么是单行处理函数？输入一行，输出一行
- ifnull(可能为NULL的数据，被当做什么处理)
- 使用例子：`select distinct ifnull(comm,0) from emp;`
    > 如果comm为null则当作0处理

# 分组查询
- group by：按照某个字段或者某些字段进行分组
- 多个字段的时候，不同字段之间通过逗号隔开
- having：having是对分组之后的数据进行再次过滤，先分组再过滤效率低，如果能使用where提前过滤则使用where
> - 分组函数一般都会和group by联合使用，这也是为什么它被称为分组函数的原因。
> - 并且任何一个分组函数（count sum avg max min）都是在group by语句执行结束之后才会执行的。
> - 当一条sql语句没有group by的话，整张表的数据会自成一组。
> - 分组函数自动忽略null

<font color=red>当一条语句中有group by的话，select后边只能跟分组函数和参与分组的字段</font>

# 连接查询
语法：
- SQL92（一些老的DBA可能还在使用这种语法。DBA：DataBase Administrator 数据管理员）
- SQL99（比较新的语法）
---
- 连接分类：
    - 内连接：
        - 等值连接
        - 非等值连接
        - 自连接
    - 外连接：
        - 左外连接（左边的是主表）
        - 右外连接（右边的是主表）
        - 左连接有右连接的写法，右连接也有左连接的写法
        - **外连接和内连接的区别**：
            - 假设A和B进行连接，如果进行内连接，凡是A表和B表能够匹配上的记录查询出来，A，B没有主副之分
            - 如果使用外连接，A，B中有一张表是主表，有一张是副表，主要查询主表中的数据，当副表中的数据没有和主表中的数据匹配上，副表自动模拟NULL与之连接
    - 全连接（很少用）
```sql
SQL99:
//内连接中的等值连接
    select 
		e.ename,d.dname
	from
		emp e
	(inner)join
		dept d
	on
		e.deptno = d.deptno;

语法：
		...
			A
		join
			B
		on
			连接条件
		where
			...

//左外连接
select 
	a.ename '员工', b.ename '领导'
from
	emp a
left (outer) join
	emp b
on
	a.mgr = b.empno;

//三张表以上的连接
...
    A
join
    B
on
...
join
    C
on
...
//表示A表先和B表进行连接，连接之后继续和C表进行连接
```

# 子查询 
- select语句中嵌套select语句，被嵌套的select语句是子查询
```
#子查询出现的位置
select 
    ..(select)
from 
    ..(select)
where
    ..(select)
    
#例子
#找出高于平均薪资的员工信息
select * from emp where sal > (select avg(sal) from emp);

#找出每个部门平均薪水的等级。
select 
	t.*,s.grade
from
	(select deptno,avg(sal) as avgsal from emp group by deptno) t
join
	salgrade s
on
	t.avgsal between s.losal and s.hisal;
	
#找出每个员工所在的部门名称，要求显示员工名和部门名。
select 
	e.ename,(select d.dname from dept d where e.deptno = d.deptno) as dname 
from 
	emp e;
#上边这个写法不常用
#一般的写法
select 
	e.ename,d.dname
from
	emp e
join
	dept d
on
	e.deptno = d.deptno;

```

# union
- 可以将查询结果相加
- 第一个查询的列的数量必须和第二个查询的列的数量相同
```sql
#找出工作岗位是SALESMAN和MANAGER的员工
第一种：select ename,job from emp where job = 'MANAGER' or job = 'SALESMAN';
第二种：select ename,job from emp where job in('MANAGER','SALESMAN');
+--------+----------+
| ename  | job      |
+--------+----------+
| ALLEN  | SALESMAN |
| WARD   | SALESMAN |
| JONES  | MANAGER  |
| MARTIN | SALESMAN |
| BLAKE  | MANAGER  |
| CLARK  | MANAGER  |
| TURNER | SALESMAN |
+--------+----------+
第三种：union
select ename,job from emp where job = 'MANAGER'
union
select ename,job from emp where job = 'SALESMAN';
+--------+----------+
| ename  | job      |
+--------+----------+
| JONES  | MANAGER  |
| BLAKE  | MANAGER  |
| CLARK  | MANAGER  |
| ALLEN  | SALESMAN |
| WARD   | SALESMAN |
| MARTIN | SALESMAN |
| TURNER | SALESMAN |
+--------+----------+
#前两种方法和第三种方法的数据顺序是不一样的
```

# ==limit==
- 分页查询全靠它
- limit是mysql特有的，其他数据库中没有，不通用
```
limit startIndex, length
#tartIndex表示起始位置，从0开始，0表示第一条数据。
#length表示取几个
#startIndex可省略，省略按从0位置开始（即第一条数据）
```
- 是最后执行的一个环节

```
每页显示pageSize条记录：
第pageNo页：(pageNo - 1) * pageSize, pageSize

pageSize：是每页显示多少条记录
pageNo：显示第几页

```

# 完整的DQL语句
- 序号表示执行顺序
```
    select		    5
		..
	from			1	
		..
	where			2
		..
	group by		3
		..
	having		    4
		..
	order by		6
		..
    limit
        ··          7
```

















</font>