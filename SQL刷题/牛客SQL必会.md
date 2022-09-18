- [1. **SQL91** **返回购买价格为 10 美元或以上产品的顾客列表**](#1-sql91-返回购买价格为-10-美元或以上产品的顾客列表)
  - [描述](#描述)
  - [示例1](#示例1)
  - [解法](#解法)

### 1. **SQL91** **返回购买价格为 10 美元或以上产品的顾客列表**

#### 描述

`OrderItems`表示订单商品表，含有字段订单号：`order_num`、订单价格：`item_price`；`Orders`表代表订单信息表，含有顾客`id`：`cust_id`和订单号：`order_num`

`OrderItems`表

| `order_num` | `item_price` |
| ----------- | ------------ |
| `a1`        | 10           |
| `a2`        | 1            |
| `a2`        | 1            |
| `a4`        | 2            |
| `a5`        | 5            |
| `a2`        | 1            |
| `a7`        | 7            |

Orders表

| `order_num` | `cust_id` |
| ----------- | --------- |
| `a1`        | `cust10`  |
| `a2`        | `cust1`   |
| `a2`        | `cust1`   |
| `a4`        | `cust2`   |
| `a5`        | `cust5`   |
| `a2`        | `cust1`   |
| `a7`        | `cust7`   |

【问题】使用子查询，返回购买价格为 10 美元或以上产品的顾客列表，结果无需排序。
注意：你需要使用 `OrderItems` 表查找匹配的订单号（`order_num`），然后使用Order 表检索这些匹配订单的顾客 ID（`cust_id`）。

【示例结果】返回顾客id `cust_id`

| `cust_id` |
| --------- |
| `cust10`  |

【示例解析】

`cust10`顾客下单的订单为`a1`，`a1`的售出价格大于等于10

#### 示例1

输入：

```sql
DROP TABLE IF EXISTS `OrderItems`;
  CREATE TABLE IF NOT EXISTS `OrderItems`(
    order_num VARCHAR(255) NOT NULL COMMENT '商品订单号',
    item_price INT(16) NOT NULL COMMENT '售出价格'
  );
  INSERT `OrderItems` VALUES ('a1',10),('a2',1),('a2',1),('a4',2),('a5',5),('a2',1),('a7',7);

  DROP TABLE IF EXISTS `Orders`;
  CREATE TABLE IF NOT EXISTS `Orders`(
    order_num VARCHAR(255) NOT NULL COMMENT '商品订单号',
    cust_id VARCHAR(255) NOT NULL COMMENT '顾客id'
  );
  INSERT `Orders` VALUES ('a1','cust10'),('a2','cust1'),('a2','cust1'),('a4','cust2'),('a5','cust5'),('a2','cust1'),('a7','cust7');
```

输出：

```sql
cust10
```

#### 解法1 - 内联

```sql
select cust_id
from OrderItems oi
inner join Orders o
on oi.order_num = o.order_num
where item_price >= 10
```

#### 解法2 - 子查询

```sql
select cust_id
from Orders
where order_num in
(
    select order_num
    from OrderItems
    where item_price >= 10
)

```

- 数据量大的情况下，使用连接查询效率更高，因为子查询相当于for循环，要执行多次子查询，而连接只需要查询一次;

- 数据量小的情况下，子查询更容易控制和操作。

### 2. **SQL92** **确定哪些订单购买了 prod_id 为 BR01 的产品（一）**

#### 描述

表OrderItems代表订单商品信息表，prod_id为产品id；Orders表代表订单表有cust_id代表顾客id和订单日期order_date

OrderItems表

| prod_id | order_num |
| ------- | --------- |
| BR01    | a0001     |
| BR01    | a0002     |
| BR02    | a0003     |
| BR02    | a0013     |

Orders表

| order_num | cust_id | order_date          |
| --------- | ------- | ------------------- |
| a0001     | cust10  | 2022-01-01 00:00:00 |
| a0002     | cust1   | 2022-01-01 00:01:00 |
| a0003     | cust1   | 2022-01-02 00:00:00 |
| a0013     | cust2   | 2022-01-01 00:20:00 |

【问题】

编写 SQL 语句，使用子查询来确定哪些订单（在 OrderItems 中）购买了 prod_id 为 "BR01" 的产品，然后从 Orders 表中返回每个产品对应的顾客 ID（cust_id）和订单日期（order_date），按订购日期对结果进行升序排序。

【示例结果】返回顾客id cust_id和定单日期order_date。

| cust_id | order_date          |
| ------- | ------------------- |
| cust10  | 2022-01-01 00:00:00 |
| cust1   | 2022-01-01 00:01:00 |

【示例解析】 

产品id为"BR01"的订单a0001和a002的下单顾客cust10和cust1的下单时间分别为2022-01-01 00:00:00和2022-01-01 00:01:00

#### 示例1

输入：

```
DROP TABLE IF EXISTS `OrderItems`;
  CREATE TABLE IF NOT EXISTS `OrderItems`(
    prod_id VARCHAR(255) NOT NULL COMMENT '产品id',
    order_num VARCHAR(255) NOT NULL COMMENT '商品订单号'
  );
  INSERT `OrderItems` VALUES ('BR01','a0001'),('BR01','a0002'),('BR02','a0003'),('BR02','a0013');

  DROP TABLE IF EXISTS `Orders`;
  CREATE TABLE IF NOT EXISTS `Orders`(
    order_num VARCHAR(255) NOT NULL COMMENT '商品订单号',
    cust_id VARCHAR(255) NOT NULL COMMENT '顾客id',
    order_date TIMESTAMP NOT NULL COMMENT '下单时间'
  );
  INSERT `Orders` VALUES ('a0001','cust10','2022-01-01 00:00:00'),('a0002','cust1','2022-01-01 00:01:00'),('a0003','cust1','2022-01-02 00:00:00'),('a0013','cust2','2022-01-01 00:20:00');
```

复制

输出：

```
cust10|2022-01-01 00:00:00
cust1|2022-01-01 00:01:00
```
#### 解法1 - 内联
```sql
select cust_id, order_date
from OrderItems oi
inner join
Orders o
on oi.order_num = o.order_num
where prod_id = 'BR01'
order by order_date
```

#### 解法2 - 子查询

```SQL
select cust_id, order_date
from Orders
where order_num in
(
    select order_num
    from OrderItems
    where prod_id = 'BR01'
)
order by order_date
```



