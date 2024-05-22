[toc]
<font size=3>
# 将数据库当中的数据导出
## 在windows的dos命令窗口中执行：（导出整个库）
```
mysqldump bjpowernode>D:\bjpowernode.sql -uroot -p333
//将bjpowernode导出到D盘下的bjpowernode.sql中，需要密码
```		
## 在windows的dos命令窗口中执行：（导出指定数据库当中的指定表）
```
mysqldump bjpowernode emp>D:\bjpowernode.sql -uroot –p123
```
# 导入数据
```
create database bjpowernode;
use bjpowernode;
source D:\bjpowernode.sql
```

















</font>