mysql

# 一、配置篇

## 1.链接信息配置

- 5.6版本以下

jdbc.properties

```java
driver=com.mysql.jdbc.Driver 
url=jdbc:mysql://192.168.10.102:3306/springboot?useUnicode=true&characterEncoding=utf8 
username=root 
password=root
```

springboot:

```java
spring.datasource.driver-class-name=com.mysql.jdbc.Driver 
spring.datasource.url=jdbc:mysql://192.168.10.102:3306/springboot?useUnicode=true&characterEncoding=utf8 
spring.datasource.username=root 
spring.datasource.password=root
```

注意：

1.低版本的mysql对应mybatis逆向工程：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">


<generatorConfiguration>
    <!--指定连接数据库的JDBC驱动包所在的位置，指定到你本机的完整路径-->
    <classPathEntry location="D:\study\software\java\jdbc\mysql-connector-java-5.1.35.jar"/>
    <!--配置table表信息内容体，targerRuntime指定采用MyBatis3的版本-->
    <context id="tables" targetRuntime="MyBatis3">
        <commentGenerator>
            <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
            <!-- true:自动生成实体类、SQL映射文件时没有注释 -->
            <!-- false:自动生成实体类、SQL映射引进，并附有注释 -->
            <property name="suppressAllComments" value="true" />
        </commentGenerator>
        <!--数据库连接的信息：驱动类、连接地址、用户名、密码 -->
        <jdbcConnection driverClass="com.mysql.jdbc.Driver"
                        connectionURL="jdbc:mysql://192.168.10.102:3306/springboot"
                        userId="root"
                        password="root">
            <!-- 若为 8.0 版本以上的 mysql-connector-java 驱动，需要设置 nullCatalogMeansCurrent = true -->
            <!--<property name="nullCatalogMeansCurrent" value="true"/>-->
        </jdbcConnection>


        <!--<jdbcConnection driverClass="oracle.jdbc.OracleDriver"
                        connectionURL="jdbc:oracle:thin:@127.0.0.1:1521:ORCL"
                        userId="scott"
                        password="tiger">
        </jdbcConnection>-->


        <!-- 默认false，把JDBC DECIMAL 和 NUMERIC 类型解析为 Integer，为 true时把JDBC DECIMAL 和
            NUMERIC 类型解析为java.math.BigDecimal -->
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false" />
        </javaTypeResolver>


        <!-- targetProject:生成PO类的位置 -->
        <javaModelGenerator targetPackage="com.zhkucst.springboot.model"
                            targetProject="src/main/java">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false" />
            <!-- 从数据库返回的值被清理前后的空格 -->
            <!-- true:对数据库的查询结果进行trim操作 -->
            <!-- false(默认)：不进行trim操作 -->
            <property name="trimStrings" value="true" />
        </javaModelGenerator>
        <!-- targetProject:mapper映射文件生成的位置 -->
        <sqlMapGenerator targetPackage="com.zhkucst.springboot.mapper"
                         targetProject="src/main/java">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false" />
        </sqlMapGenerator>
        <!-- targetPackage：mapper接口生成的位置 -->
        <javaClientGenerator type="XMLMAPPER"
                             targetPackage="com.zhkucst.springboot.mapper"
                             targetProject="src/main/java">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false" />
        </javaClientGenerator>
        <!-- 指定数据库表及对应的Java模型类名 -->
        <table tableName="t_student" domainObjectName="Student"
            enableCountByExample="false"
            enableUpdateByExample="false"
            enableDeleteByExample="false"
            enableSelectByExample="false"
            selectByExampleQueryId="false"/>


        <!-- 有些表的字段需要指定java类型
         <table schema="" tableName="">
            <columnOverride column="" javaType="" />
        </table> -->
    </context>
</generatorConfiguration>
```

- 8.0版本以上

 jdbc.properties  

```xml
 driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://192.168.10.102:3306/springboot?useUnicode=true&characterEncoding=utf8&useJDBCComplliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC
username=root
password=root
```

springboot:

```xml
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://192.168.10.102:3306/springboot?useUnicode=true&characterEncoding=utf8&useJDBCComplliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=root
```

注意：

1.高版本的mysql对应mybatis逆向工程：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">




<generatorConfiguration>
    <!--指定连接数据库的JDBC驱动包所在的位置，指定到你本机的完整路径-->
    <classPathEntry location="D:\study\software\java\jdbc\mysql-connector-java-8.0.25.jar"/>
    <!--配置table表信息内容体，targerRuntime指定采用MyBatis3的版本-->
    <context id="tables" targetRuntime="MyBatis3">
        <commentGenerator>
            <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
            <!-- true:自动生成实体类、SQL映射文件时没有注释 -->
            <!-- false:自动生成实体类、SQL映射引进，并附有注释 -->
            <property name="suppressAllComments" value="true" />
        </commentGenerator>
        <!--数据库连接的信息：驱动类、连接地址、用户名、密码 -->
        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                        connectionURL="jdbc:mysql://192.168.10.102:3306/springboot?useUnicode=true&characterEncoding=utf8&useJDBCComplliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC"
                        userId="root"
                        password="root">
            <!-- 若为 8.0 版本以上的 mysql-connector-java 驱动，需要设置 nullCatalogMeansCurrent = true -->
            <property name="nullCatalogMeansCurrent" value="true"/>
        </jdbcConnection>




        <!--<jdbcConnection driverClass="oracle.jdbc.OracleDriver"
                        connectionURL="jdbc:oracle:thin:@127.0.0.1:1521:ORCL"
                        userId="scott"
                        password="tiger">
        </jdbcConnection>-->




        <!-- 默认false，把JDBC DECIMAL 和 NUMERIC 类型解析为 Integer，为 true时把JDBC DECIMAL 和
            NUMERIC 类型解析为java.math.BigDecimal -->
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false" />
        </javaTypeResolver>




        <!-- targetProject:生成PO类的位置 -->
        <javaModelGenerator targetPackage="com.zhkucst.springboot.model"
                            targetProject="src/main/java">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false" />
            <!-- 从数据库返回的值被清理前后的空格 -->
            <!-- true:对数据库的查询结果进行trim操作 -->
            <!-- false(默认)：不进行trim操作 -->
            <property name="trimStrings" value="true" />
        </javaModelGenerator>
        <!-- targetProject:mapper映射文件生成的位置 -->
        <sqlMapGenerator targetPackage="com.zhkucst.springboot.mapper"
                         targetProject="src/main/java">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false" />
        </sqlMapGenerator>
        <!-- targetPackage：mapper接口生成的位置 -->
        <javaClientGenerator type="XMLMAPPER"
                             targetPackage="com.zhkucst.springboot.mapper"
                             targetProject="src/main/java">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false" />
        </javaClientGenerator>
        <!-- 指定数据库表及对应的Java模型类名 -->
        <table tableName="t_student" domainObjectName="Student"
            enableCountByExample="false"
            enableUpdateByExample="false"
            enableDeleteByExample="false"
            enableSelectByExample="false"
            selectByExampleQueryId="false"/>




        <!-- 有些表的字段需要指定java类型
         <table schema="" tableName="">
            <columnOverride column="" javaType="" />
        </table> -->
    </context>
</generatorConfiguration>
```



# 二、错误解决篇

## 1.数据库本身数据乱码

mysql数据库字符集默集latin

需要手动设置成utf-8

![img](https://cdn.nlark.com/yuque/0/2022/png/25800859/1642918494005-5deb5bfa-1caf-4f00-a448-c044e524570d.png)

## 2.调用数据库数据乱码

在db.priperties中在url末尾添加

```xml
?useUnicode=true&characterEncoding=utf8
```

# 三、安装配置篇

第一步：上传软件包到linux操作系统
第二步：对mysql压缩包进行解压操作：tar.gz

```bash
tar –zxf mysql-5.6.44-linux-glibc2.12-x86_64.tar.gz
```

第三步：移动mysql文件夹到/usr/local目录下并更名为mysql

```bash
mv mysql-5.6.44-linux-glibc2.12-x86_64.tar /usr/local/mysql
```

第四步：创建一个mysql用户并更改/usr/local/mysql目录权限（用户和组）



```bash
useradd –r –s /sbin/nologin mysql
```

第五步：初始化数据库

```bash
[root@localhost  /usr/local/mysql/] # scripts/mysql_install_db --user=mysql 
```

初始化之前需要安装autoconf库【命令：yum-y install autoconf】
第六步：移除mariadb-libs库文件

```bash
[root@localhost  /usr/local/mysql/] # yum remove mariadb-libs 
```

第七步：移动support-files目录下的mysql.server脚本到/etc/init.d目录一份=>service

```bash
[root@localhost  /usr/local/mysql/] # cp support-files/mysql.server /etc/init.d/mysql 
```

第八步：启动mysql脚本

```bash
service mysql start
```

第九步：设置密码并测试mysql数据库

```bash
bin/mysqladmin -u root password 'root';
bin/mysql -uroot -p
Enter Password:root
```

第十一步：mysql远程连接Navicate
授予其他用户访问权限：

```sql
mysql> grant all privileges on . to root@"%" identified by "root";
mysql> flush privileges;
```

第十二步：
My.conf配置：

```bash
cp /usr/local/mysql/support-files/my-default.cnf /etc/my.cnf
```

注释不带绿色，授予my.cnf具有644权限

```bash
chmod 644 /etc/my.cnf
```



附：
拒绝远程连接：
回收权限：

```sql
Mysql>revoke all privileges,grant option from 'root'@”%”;
```