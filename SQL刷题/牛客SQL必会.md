[toc]

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

#### 解法

```sql
select cust_id
from OrderItems oi
inner join Orders o
on oi.order_num = o.order_num
where item_price >= 10
```

