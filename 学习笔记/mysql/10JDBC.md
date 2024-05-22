[toc]
<font size=3>
- Java DateBase Connectivity（java语言连接数据库）
- JDBC是SUN公司制定的一套接口（interface）java.sql.*下
- JDBC开发前的准备工作，先从官网下载对应的驱动jar包，然后将其配置到环境变量classpath当中。
```
D:\MySql Connector Java 5.1.23\mysql-connector-java-5.1.23-bin.jar
//以上的配置是针对于文本编辑器的方式开发，使用IDEA工具的时候，不需要配置以上的环境变量。
//IDEA有自己的配置方式。
```
# JDBC编程六步
- 第一步：注册驱动（作用：告诉Java程序，即将要连接的是哪个品牌的数据库）
- 第二步：获取连接（表示JVM的进程和数据库进程之间的通道打开了，这属于进程之间的通信，重量级的，使用完之后一定要关闭通道。）
- 第三步：获取数据库操作对象（专门执行sql语句的对象）
- 第四步：执行SQL语句（DQL DML....）
- 第五步：处理查询结果集（只有当第四步执行的是select语句的时候，才有这第五步处理查询结果集。）
- 第六步：释放资源（使用完资源之后一定要关闭资源。Java和数据库属于进程间的通信，开启之后一定要关闭。）


```java
//DML executeUpdate(insert/delete/update)
public static void main(Strin[] args){
    Connnection conn=null;
    Statement stmt=null;
    try{
    //1注册驱动
        DriverManager.registerDriver(new com.mysql.jbdc.Driver());
    //注册驱动的第二种方法（常用），利用了反射机制
    //比较灵活，因为可以将字符串写入配置文件中
    //forName()执行会导致：类加载，而静态代码块会在类加载之前执行
    //在com.mysql.jdbc.Driver中有一段静态代码块
    /*
        Stactic{
            try{
                java.sql.DriverManager.registerDriver(new Driver());
            }
            catch(SQLException e){
                throw new RuntimeException("Can`t register driver!");
            }
        }
    */
        Class.forName("com.mysql.jbdc.Driver")
    //2获取连接
        conn=DriverManager.getConnection("jdbc:mysql://localhost:3306/bjpowernode",
        "root","QZ872311305");
    //3获取数据库操作对象
        stmt=conn.creatStatement();
    //4执行SQL语句
    //JDBC中的SQL语句不需要写分号
        String sql="delete from dept where deptno=40";
        int count=stmt.executeUpdate(sql)//返回值：影响数据库中的记录条数
        System.out.println(count==1?"success":"fail");
    }catch(SQLException e){
        e.printStackTrace();
    }catch(ClassNotFoundException e){//用第二种注册驱动的方法需要处理这个异常
        e.printStackTrace();   
    }finally{
    //6释放资源
    //为保证一定会关闭在finally中关闭
    //按照从小到大的顺序关闭
    //分别进行try...catch
        try{
            if(stmt!=null){
                stmt.close();
            }
        }catch(SQLException e){
            e.printStackTrace();
        }
        try{
            if(conn!=null){
                conn.close();
            }
        }catch(SQLException e){
            e.printStackTrace();
        }
    }
}
```

```java
//DQL executeQuery(select)
public static void main(Strin[] args){

    ResourceBundle bundle=ResourceBunle.getBundle("JDBC");
    String driver=bundle.getString("driver");
    String url=bundle.getString("url");
    String user=bundle.getString("user");
    String password=bundle.getString("password");
    
    Connnection conn=null;
    Statement stmt=null;
    ResultSet rs=null;
    
    try{
    //1注册驱动
        Class.forName(driver);
    //2获取连接
        conn=DriverManager.getConnection(url,root,password);
    //3获取数据库操作对象
        stmt=conn.creatStatement();
    //4执行SQL语句
    //JDBC中的SQL语句不需要写分号
        String sql="select ename,empno,sal from emp";
        rs=stmt.executeQuery(sql);
    //5处理查询结果集
    //开始的时候不在第一行，故需要先next指向第一行
        boolean flag1=rs.next();
    while(flag1){
        //getString()特点:不管数据库中是什么数据类型，都能按照String形式取出
        //JDBC中所有的下标都是从1开始的
        String ename=rs.getString(1);
        String empno=rs.getString(2);
        String sal=rs.getString(3);
        System.out.println(ename+","+empno+","+sal);
        
        String ename=rs.getString("ename");
        String empno=rs.getString("empno");
        String sal=rs.getString("sal");
        System.out.println(ename+","+empno+","+sal);
        
        //如果SQL语句为select ename as a,empno,sal from emp
        //则需要用别名来获得结果
        String ename=rs.getString("a");
        String empno=rs.getString("empno");
        String sal=rs.getString("sal");
        System.out.println(ename+","+empno+","+sal);
        
        //除了可以以String类型取出，还可以以特定的类型取出
        String ename=rs.getString("ename");
        int empno=rs.getInt("empno");
        double sal=rs.getDouble("sal");
        System.out.println(ename+","+empno+","+sal);
    }
    }catch(SQLException e){
        e.printStackTrace();
    }catch(ClassNotFoundException e){
        e.printStackTrace();   
    }finally{
    //6释放资源
        try{
            if(rs!=null){
                rs.close();
            }
        }catch (SQLException e){
            e.printStackTrace();
        }
        try{
            if(stmt!=null){
                stmt.close();
            }
        }catch(SQLException e){
            e.printStackTrace();
        }
        try{
            if(conn!=null){
                conn.close();
            }
        }catch(SQLException e){
            e.printStackTrace();
        }
    }
}
```

# SQL注入
当java程序中的SQL语言为
```
String sql =
"select * from t_user where loginName = '"+loginName+"' and loginPwd = '"+loginPwd+"'"

用户名：fdsa
密码：fdsa' or '1'='1
登陆成功
这种现象被称为SQL注入（存在安全隐患)
```
- 导致SQL注入的根本原因：用户输入的信息中含有SQL语句的关键字，并且这些关键字参与SQL语句的编译过程，导致sql的原意被扭曲

## 解决SQL注入
- 要想使用户信息不参与SQL语句的编译，必须使用java.sql.PreparedStatement
- PreparedStatement接口继承了java.sql.Statement
- PreparedStatement是属于预编译的数据库操作对象
- PreparedStaement的原理：预先对SQL语句的框架进行编译，然后再给SQL语句传“值”

```java
//使用PreparedStatement的JDBC框架

//1 注册驱动
Class.forName("com.mysql.jdbc.Driver");
//2 获取连接
conn=DriverManager.getConnection("jdbc:mysql://localhost:3306/bjpowernode",
        "root","QZ872311305");
//3 获取预编译的数据库操作对象
//SQL语句的框子，一个  问号  表示一个占位符，一个  问号将来接受一个“值”
//占位符不能用单引号括起来
Strinig sql = "select * from t_user where loginName = ? and loginPwd = ?";
//程序执行到此处，会发送SQL语句的框子给DBMS，然后DBMS进行SQL语句的预先编译
ps = conn.prepareStatement(sql);
//给占位符传值（第一个问号的下标是 1 ，第二个是 2 ）
ps.setString(1,loginName);
ps.setString(2,loginPwd);
//当然，也有setInt()等
//4 执行SQL
rs = ps.executeQuery();
//5 处理结果集
if(rs.next()){
    loginSuccess=true;
}catch

······

```
- **Statement和PreparedStatement的区别**
    - Statement存在sql注入的问题，preparedStatement解决了sql问题
    - Statement是编译一次执行一次，PreparedStatement是编译一次，可执行N次，故PreparedStatement的效率高
        > 如果两条SQL语句一模一样，后边的一句不再编译
    - PreparedStatament会在编译阶段做类型的安全检查
> - PreparedStatement使用较多，但是有的时候必须使用Statement（业务方面要求必须支持SQL注入的时候，例如返回升序降序的情况）
> - 使用PreparedStatement方法给占位符传值的时候，拼进SQL语句中的是'desc'或者'asc'，带单引号，会导致语法错误，所以必须使用Statement


# JDBC事物
- JDBC中的事物是自动提交的：只要执行任意一条DML语句，则自动提交一次。
```java
//对于事物的控制十分重要

void setAutoCommit(boolean autoCommit) 
//将此连接的自动提交模式设置为给定状态。 
//autoCommit为false的时候表示关闭自动提交
//是Connection对象的方法
//用法conn.setAutoCommit(flase);

void commit() 
//使所有上一次提交/回滚后进行的更改成为持久更改，
//并释放此 Connection 对象当前持有的所有数据库锁。
//所有sql语句都处理完之后则可以提交

void rollback() 
//取消在当前事务中进行的所有更改，
//并释放此 Connection 对象当前持有的所有数据库锁。 
//遇到异常必须回滚
catch(Exception e){
    if(conn != null){
        try{
            conn.rollback();
        }
        catch(SQLException e1){
            e1.printStackTrace();
        }
    }
}
```

# JDBC工具类的封装
- 在使用JDBC时候很多操作都是固定，故可以封装为工具类，实现几个静态方法方便之后的操作
```java
package com.qz.jdbc.untils;

import java.sql.*;

public class DBUtil {
    //工具类中的构造方法是私有的
    //因为工具类中的方法都是静态的，不需要new对象，直接采用类名调用
    //但是不是必须的，sun的源码是这样的，模仿下
    private DBUtil() { }
    //静态代码块在类加载的时候执行，并且只执行一次
    static {
        try {
            Class.forName("com.mysql.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }

    /**
     *建立连接
     * @return 连接对象
     * @throws SQLException
     */
    public static Connection getConnection() throws SQLException {
        //异常需要在外边处理。throws
        return DriverManager.getConnection("jdbc:mysql://localhost:3306/bjpowernode","root","QZ872311305");
    }

    /**
     * 关闭资源
     * @param conn 连接对象
     * @param stmt 数据库操作对象
     * @param rs 结果集
     */
    public static void close(Connection conn, Statement stmt, ResultSet rs){
        if (rs != null) {
            try {
                rs.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (stmt != null) {
            try {
                stmt.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (conn != null) {
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```
# DAO类的封装
- DAO：DataBase Access Object（数据库访问对象）
- 作用：数据库访问对象在开发时提供针对**某张表**的操作细节（增上改查）
- 优点：
    - 通过数据库访问对象可以避免反复的SQL命令书写
    - 通过数据库访问对象可以避免反复的JDBC开发步骤书写
- DAO类：提供数据库访问对象的类
- **开发规则**：一个DAO类封装的是**一张表**的操作细节
- DAO类命名规则：表名字+DAO，例如：`EmpDao，DeptDao`
```java
//一个DAO类的例子，仅实现了查询，通过provinId进行查询，返回一个City集合
public class CityDao {
    public static List<City> getCityByProId(Integer provinceId){
        String sql = "select * from city where provinceid=?";
        Connection conn = null;
        PreparedStatement ps = null;
        ResultSet rs = null;
        Integer id;
        String  name;
        City city = null;
        List<City> cityList = new ArrayList<>();

        try {
            conn = DBUtil.getConnetcion();
            ps = conn.prepareStatement(sql);
            ps.setInt(1,provinceId);
            rs = ps.executeQuery();
            while (rs.next()){
                id = rs.getInt("id");
                name = rs.getString("name");
                city = new City(id,name,provinceId);
                cityList.add(city);
            }
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            DBUtil.close(conn,ps,rs);
        }
        return cityList;
    }
}
```
# 实体类的封装
- 一个实体类用于描述一张表的结构
- 实体类的类名应该与关联的表名保持一致，但是可以忽略大小写
```
DEPT.frm---------------->public class Dept{}
```
- 实体类的属性应该与关联的表文件字段保持一致
```
Dept.frm--------------------------->public class Dept{
DEPTNO  INT                              private Integer deptNo;
DNAME   VARCHAR(20)                      private String dname;
LOC     VARCHAR(20)                      private String loc;    
                                    }
```
- 实体类的一个实例对象用于在内存中存储对应的表文件中一个数据行
> 上边DAO类封装中的City就是一个实体类

# 行级锁
- 在sql语句后边加for update，则查出来的几条记录在当前事务没有结束之前被锁定，其他事务无法对它们进行修改操作
> 这是悲观锁
- 乐观锁与悲观锁的区别见wps
</font>