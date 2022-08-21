jdbc

# 一、原理篇

## 1.JDBC：Java DataBase Connectivity 

可以为多种关系型数据库DBMS 提供统一的访问形式，用Java来操作数据库

## 2.JDBC API 主要功能：

三件事，具体时通过以下类/接口实现：
DriverManager：管理jdbc驱动
Connection：连接（通过DriverManager产生）
Statement(PreparedStatement)：增删查改 （通过Connection产生）
CallableStatement：调用数据库中的 存储过程/存储函数
Result：返回的结果集（上面的Statement等产生）



Connection产生操作数据库的对象：
Connection产生Statement对象：createStatement（）
Connection产生PreparedStatement对象：PrepareStatement（）
Connection产生CallableStatement对象：prepareCall（）



Statement操作数据库：
增删改：executeUpdate（）
查询：executeQuery（）



ResultSet：保存结果集 select * from xxx
next（）：光标下移，判断是否有下一条数据；true/false
previous（）：true/false



PrepareStatement操作数据库：
public interface PreparedStatement extends Statement
因此
增删改：executeUpdate（）
查询：executeQuery（）
赋值操作：setXxx（）



PrepareStatement与Statement在使用时的区别：
1.Statement：

```java
sql
executeUpdate（sql）
```

2.preparedStatement：

```java
sql（可能存在占位符？）
//在创建PreparedStatement 对象时，将sql预编译 prepareStatment(sql)
executeUpdate()
setXxx()替换占位符？
```

推荐使用PreparedStatement：原因如下：
（1）编码更加简便

```java
String name = "zs";	
int age = 23;
```

stmt：

```java
String sql = "insert into student(stuno, stuname) values('" + name + "', " + age +") " ;
stmt.executeUpdate(sql);
```

pstmt：

```java
String sql = "insert into student(stuno, stuname) values(?, ?)";
pstmt = connection.prepareStatement(sql);//预编译
pstmt.setString(1, name);
pstmt.setInt(2, age);
pstmt.excuteUpdate();
```

（2）提高性能（因为有 预编译操作， 预编译只需执行一次）
需要重复增加100条数
stmt：

```java
String sql = "insert into student(stuno, stuname) values('" + name + "', " + age +") " ;
for(int i = 1; i <= 100; i++)
stmt.executeUpdate(sql);
```

pstmt：

```java
String sql = "insert into student(stuno, stuname) values(?, ?)";
pstmt = connection.prepareStatement(sql);//预编译
pstmt.setString(1, name);
pstmt.setInt(2, age);
for(int i = 1; i <= 100; i++)
pstmt.excuteUpdate();
```

（3）安全（可以有效防止sql注入）
sql注入：将客户端输入的内容 和 开发人员 的 SQL语句 混为一体
stmt：存在被sql注入的风险；
（例如输入 用户名：任意值 ' or 1=1 --
密码：任意值）
分析：

```sql
select count(*) from login where uanme = '任意值' or 1=1 --' and upwd = '任意值'；
select  count(*) from login where uname = '"+name+"' and upwd = '"+pwd+"'
```

pstmt有效防止sql注入

## 3.jdbc访问数据库的具体步骤：

a.导入驱动，加载具体的驱动类
b.与数据库建立连接
c.发送sql，执行
d.处理结果集(查询)



## 4.数据库驱动



数据库名



驱动jar



具体驱动类



连接字符串



Oracle



ojdbc-x.jar



oracle.jdbc.OracleDriver



jdbc:oracle:this:@localhost:1521:ORCL



MySQL



Mysql-connector-java-x.jar



com.mysql.jdbc.Driver



jdbc:mysql://localhost:3306/数据库实例名



SqlServer



sqljdbc-x.jar



com.microsoft.sqlserver.jdbc.SQLServerDriver



jdbc:sqlserver://localhost:1433;DatabaseName=mydatabase

# 二、操作篇

## 1.使用JDBC连接Oracle

```java
package exp1;


import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;


public class JDBCDemo {
	private static final String URL = "jdbc:oracle:thin:@localhost:1521:ORCL";
	private static final String USERNAME = "system";
	private static final String PWD = "FYp0103";
	
	public static void update() {
		Connection connection = null;
		Statement stmt = null;
		try {
			//a.导入驱动，加载具体的驱动类
			Class.forName("oracle.jdbc.OracleDriver");
			//b.与数据库建立连接
			connection =  DriverManager.getConnection(URL, USERNAME, PWD);
			//c.发送sql，执行（增删查改）
			stmt = connection.createStatement();
			String sql = "insert into student values(1, 'zs', 23, 's1')";
			int count = stmt.executeUpdate(sql);//返回值表示增删改几条数据
			if(count > 0) {
				System.out.println("操作成功！");
			}
		}catch(ClassNotFoundException e){
			e.printStackTrace();
		}catch(SQLException e) {
			e.printStackTrace();
		}catch(Exception e) {
			e.printStackTrace();
		}
		finally {
			try {
				if(stmt !=null) stmt.close();
				if(connection !=null) connection.close();
			}catch(Exception e) {
				e.printStackTrace();
			}
		}
	}
	
	public static void main(String[] args) {
		update();
	}
	
}
```

## 2.Statement增删改、查操作

```java
//JDBC For Statement
package exp1;


import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;


public class JDBCDemo_Statement {
//	private static final String URL = "jdbc:oracle:thin:@localhost:1521:ORCL";
	private static final String URL = "jdbc:mysql://localhost:3306/MyDataBase";
 //新版的 private static final String URL = "jdbc:sqlserver://localhost:1433;DatabaseName=mydatabase";
//旧版的  private static final String URL = "jdbc:microsoft:sqlserver:localhost:1433;database=MyDataBase";
	private static final String USERNAME = "root";
	private static final String PWD = "";
	
	public static void update() {
		Connection connection = null;
		Statement stmt = null;
		ResultSet rs = null;
		try {
			//a.导入驱动，加载具体的驱动类
//			Class.forName("oracle.jdbc.OracleDriver");
			Class.forName("com.mysql.jdbc.Driver");
//       	Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");


			//b.与数据库建立连接
			connection =  DriverManager.getConnection(URL, USERNAME, PWD);
			//c.发送sql，执行（增删改, 查）
/*		//增    
 *         	stmt = connection.createStatement();
			String sql = "insert into student values(1, 23, 'zs', 's1')";
			int count = stmt.executeUpdate(sql);//返回值表示增删改几条数据
			if(count > 0) {
				System.out.println("操作成功！");
			}
*/			
/*		//删    
          	stmt = connection.createStatement();
			String sql = "delete from student where stuno = 1";
			int count = stmt.executeUpdate(sql);//返回值表示增删改几条数据
			if(count > 0) {
				System.out.println("操作成功！");
			}
*/		
			
/*		//改   
          	stmt = connection.createStatement();
			String sql = "update student set stuname = zs5 where stuno = 1";
			int count = stmt.executeUpdate(sql);//返回值表示增删改几条数据
			if(count > 0) {
				System.out.println("操作成功！");
			}
*/
			
/*		//查    
          	stmt = connection.createStatement();
			String sql = "select * from student";
			rs = stmt.executeQuery(sql);//返回值表示增删改几条数据
			
			while(rs.next()) {
				int Sno = rs.getInt("stuno");
				int Sage = rs.getInt("stuage");
				String Sname = rs.getString("stuname");
				String Sclass = rs.getString("stuclass");
				System.out.println(Sno + "--" + Sage + "--" + Sname + "--" + Sclass);
			}
	
			
*/			
		}catch(ClassNotFoundException e){
			e.printStackTrace();
		}catch(SQLException e) {
			e.printStackTrace();
		}catch(Exception e) {
			e.printStackTrace();
		}
		finally {
			try {
				if(stmt !=null) stmt.close();
				if(connection !=null) connection.close();
			}catch(Exception e) {
				e.printStackTrace();
			}
		}
	}
	
	public static void main(String[] args) {
		update();
	}
	
}
```

## 3.preparedStatement增删改、查操作

```java
package exp2;


import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.sql.CallableStatement;
import java.sql.PreparedStatement;


public class JDBCDemo_prepareStatement {
//	private static final String URL = "jdbc:oracle:thin:@localhost:1521:ORCL";
	private static final String URL = "jdbc:mysql://localhost:3306/MyDataBase";
//	private static final String URL = "jdbc:microsofi:sqlserver:localhost:1433;database=MyDataBase";
	private static final String USERNAME = "root";
	private static final String PWD = "";
	
	public static void update() {
		Connection connection = null;
		PreparedStatement pstmt = null;
		ResultSet rs = null;
		try {
			//a.导入驱动，加载具体的驱动类
//			Class.forName("oracle.jdbc.OracleDriver");
			Class.forName("com.mysql.jdbc.Driver");
//			Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
			//b.与数据库建立连接
			connection =  DriverManager.getConnection(URL, USERNAME, PWD);
			//c.发送sql，执行（增删查改）
//			String sql = "insert into student values(?, ?, ?, ?)";
		//增    
/*          	pstmt = connection.createStatement();
			String sql = "insert into student values(1, 23, 'zs', 's1')";
			int count = pstmt.executeUpdate(sql);//返回值表示增删改几条数据
			if(count > 0) {
				System.out.println("操作成功！");
			}
*/			
/*		//删    
          	pstmt = connection.createStatement();
			String sql = "delete from student where stuno = 1";
			int count = pstmt.executeUpdate(sql);//返回值表示增删改几条数据
			if(count > 0) {
				System.out.println("操作成功！");
			}
*/		
/*		//改   
          	pstmt = connection.createStatement();
			String sql = "update student set stuname = zs5 where stuno = 1";
			int count = pstmt.executeUpdate(sql);//返回值表示增删改几条数据
			if(count > 0) {
				System.out.println("操作成功！");
			}
*/
/*		//查    
          	pstmt = connection.createStatement();
			String sql = "select * from student";
			rs = pstmt.executeQuery(sql);//返回值表示增删改几条数据
			
			while(rs.next()) {
				int Sno = rs.getInt("stuno");
				int Sage = rs.getInt("stuage");
				String Sname = rs.getString("stuname");
				String Sclass = rs.getString("stuclass");
				System.out.println(Sno + "--" + Sage + "--" + Sname + "--" + Sclass);
			}
*/						
/*			//增				
			String sql = "insert into student values(1, 23, 'zs', ?)";
			pstmt = connection.prepareStatement(sql);
			pstmt.setString(1, "zs");
			
			int count = pstmt.executeUpdate();
			if(count > 0) {
				System.out.println("操作成功！");
			}
*/		
/*			//删				
			String sql = "delete from student where stuno = ?";
			pstmt = connection.prepareStatement(sql);
			pstmt.setString(1, "1");
			
			int count = pstmt.executeUpdate();
			if(count > 0) {
				System.out.println("操作成功！");
			}
*/			
/*			//改				
			String sql = "update student set stuname = zs5 where stuno = ?";
			pstmt = connection.prepareStatement(sql);
			pstmt.setString(1, "1");
			
			int count = pstmt.executeUpdate();
			if(count > 0) {
				System.out.println("操作成功！");
			}	
*/
/*			//改				
			String sql = "select * from student where stuno = ?";
			pstmt = connection.prepareStatement(sql);
			pstmt.setString(1, "1");
			
			rs = pstmt.executeQuery();
			while(rs.next()) {
				int sno = rs.getInt("stuno");
				int sage = rs.getInt("stuage");
				String sname = rs.getString("stuname");
				String sclass = rs.getString("stuclass");
				System.out.println(sno + "--" + sage + "--" + sname + "--" + sclass);
			}
*/
		}catch(ClassNotFoundException e){
			e.printStackTrace();
		}catch(SQLException e) {
			e.printStackTrace();
		}catch(Exception e) {
			e.printStackTrace();
		}
		finally {
			try {
				if(pstmt !=null) pstmt.close();
				if(connection !=null) connection.close();
			}catch(Exception e) {
				e.printStackTrace();
			}
		}
	}
	
	public static void main(String[] args) {
		update();
	}
	
}
```

## 4.总结

模板、八股文：

a.导入驱动包、加载具体驱动类Class.forName("具体驱动类")；

b.与数据库建立连接connection = DriverManager.getConnection(...);

c.通过connection, 获取操作数据库的对象（Statement/preparedStatement/callablestatement）

stmt = connection.createStatement();

d.(查询)处理结果集 

```java
rs = pstmt.excuteQuery();
while(rs.next()){
  rs.getXxx(..);
}catch(ClassNotFoundExceptione){
...
}catch(SQLException e){
...
}catch(Exception e){
...
}finally{
  //打开顺序，与关闭顺序相反
  if(rs != null)rs.close();
  if(stmt != null)stmt.close();
  if(connection != null)connection.close();
}


--jdbc中， 除了Class.forName()抛出ClassNotFoundException, 其余方法全部抛出SQLEeception
```

## 5.JDBC调用存储过程和存储函数

1.CallableStatement：调用	存储过程、存储函数

connection.prepareCall(参数：存储过程胡哦存储函数名)

参数格式：

存储过程（无返回值return, 用Out参数替代）:

​			{call 		存储过程名（参数列表）}

存储函数（有返回值return）:

​			{? = call 存储函数名（参数列表）}

```sql
create or replace procedure addTwoNum(num1 in number, num2 in number, result out number)
as
begin
			result := num1 + num2;
end;
```

如果通过sql plus 访问数据库，只需要开启：OracleServiceSID

通过其他程序访问数据（sqldevelop、navicate、JDBC）,需要开启OracleServiceSID、XxxListener

JDBC调用存储过程的步骤：

a.产生	调用存储过程的对象（CallableStatement） cstmt = connection.prepareCall（“....”）;

b.通过setXxx（）处理	输出参数值 cstmt.setInt（1，30）；

c.通过	registerOutParameter(...)处理输出参数类型

d.cstmt.execute（）执行

e.接受	输出值	（返回值）getXxx()

调用存储函数：

```sql
create or replace function  addTwoNumfunction(num1 in number, num2 in number)
    return number
    as
    result number;
    begin
            result := num1 + num2;
            return result;
    end;
```

JDBC调用存储函数：与存储过程的区别：

在调用时，注意参数：“{？ = call addTwoNumfunction(?, ?)}”

3.处理CLOB/BLOB类型

处理稍大型数据：

a.存储路径 E:/A.txt

​	通过JDBC存储文件路径，然后根据IO操作处理

​	例如：JDBC将E:/A.txt 文件 以字符串形式 “E：/A.txt”存储到数据库中

​			获取：1.获取该路径“E：/A.txt”2.IO

b.

​	CLOB：大文本数据（小说→ 数据）

​	BLOB：二进制

clob：

存：

1.先通过pstmt的	？	代替小说内容	（占位符）

2.再通过pstmt.setCharacterStream(2, reader, (int)file.length());

将上一步的	？	替换为小说流，注意第三个参数需要是Int类型

取：

1.通过Reader reader = rs.getCharacterStream("NOVEL");

将clob类型的数据	保存到	Reader	对象中

2.将Reader通过Writer输出即可。

blob：二进制	字节流 InputStream OutputStream

与CLOB步骤基本一致，区别：setBinaryStream(...)	getBinaryStream(...)