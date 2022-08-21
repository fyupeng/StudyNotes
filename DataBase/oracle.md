oracle

# 一、数据库启动状态

**1 状态查询**

| **启动状态**                      | **SQL语句**                                     | **结果** |
| --------------------------------- | ----------------------------------------------- | -------- |
| nomount                           | select status from v$instance;                  | STARTED  |
| select open_mode from v$database; | ERROR at line 1:ORA-01507: database not mounted |          |
| mount                             | select status from v$instance;                  | MOUNTED  |
| select open_mode from v$database; | MOUNTED                                         |          |
| open                              | select status from v$instance;                  | OPEN     |
| select open_mode from v$database; | READ WRITE 或者 READ ONLY                       |          |

# 二、 表空间

## oracle删除表空间语句

```
 --删除空的表空间，但是不包含物理文件
drop tablespace tablespace_name;
--删除非空表空间，但是不包含物理文件
drop tablespace tablespace_name including contents;
--删除空表空间，包含物理文件
drop tablespace tablespace_name including datafiles;
--删除非空表空间，包含物理文件
drop tablespace tablespace_name including contents and datafiles;
--如果其他表空间中的表有外键等约束关联到了本表空间中的表的字段，就要加上CASCADE CONSTRAINTS
drop tablespace tablespace_name including contents and datafiles CASCADE CONSTRAINTS; 
```