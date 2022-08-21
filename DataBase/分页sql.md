分页SQL（Oracle、MySQL、SQLSERVER)

1.分页

要实现分页，必须知道	某一页的	数据	从哪里开始	到哪里结束



假设每页显示10条数据

sqlserver/oracle：从1开始计数



| 第n页 | 开始       | 结束 |
| ----- | ---------- | ---- |
| 1     | 1          | 10   |
| 2     | 11         | 20   |
| 3     | 21         | 30   |
| n     | (n-1)*10+1 | n*10 |



mysql：从0开始计数



| 第n页 | 开始 | 结束       |
| ----- | ---- | ---------- |
| 0     | 0    | 9          |
| 1     | 10   | 19         |
| 2     | 20   | 29         |
| n     | n*10 | (n+1)*10-1 |



总结：

分页：

​			第n页的数据：	第(n-1)*10+1条		--		第n*10条



a.

**MYSQL**	实现分页的sql：

limit		开始，多少条

第0页

select * from student limit 0,10;

第1页

select * from student limit 10,10;

第2页

select * from studentl limit 20,10;

第n页

select * from studentl limit n*10,10;



b.

**oracle**实现分页



select * from

(

​	select rownum r, t.* from

​	(select s.* from student s order by sno asc) t

​	where rownum <= n * 10

)

where r >= (n - 1) * 10 + 1;



c.SQLServer分页： 3种分页sql



**sqlserver2003：top**

select top 页面大小 * from student where id not in

(select top(页数 - 1) * 页面大小 id from student)



 **sqlserver2012之后支持：**

offset fetch next only

select * from student order by sno

offset (页数 - 1) * 页面大小 + 1 rows fetch next 页面大小 rows only;



row_number()  over(字段)：



select * from

(

​	select row*number() over (order by sno asc) as r, t.**  from student as t

)  as s

where r <= n * 10 and *r >= (n-1)* 10 +1;



**注意：子查询作为表时，字段后面要自命名，例如：查询的字段 [as] [自定义字段名字]**

**子查询出来的表后也要自命名，因为表都是要有名字才能查询**



SQLServer此种分页sql与oracle分页sql的区别：

1.rownum	, row_number()

2.oracle需要排序（为了排序，单独写了一个子查询），但是在sqlserver中可以省略改排序的子查询，

因为sqlserver中恶意通过over直接排序