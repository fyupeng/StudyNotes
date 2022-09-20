- [1. **SQL91** **返回购买价格为 10 美元或以上产品的顾客列表**](#1-sql91-返回购买价格为-10-美元或以上产品的顾客列表)
  - [描述](#描述)
  - [示例1](#示例1)
  - [解法1 - 内联](#解法1---内联)
  - [解法2 - 子查询](#解法2---子查询)
- [2. **SQL92** **确定哪些订单购买了 prod_id 为 BR01 的产品（一）**](#2-sql92-确定哪些订单购买了-prod_id-为-br01-的产品一)
  - [描述](#描述-1)
  - [示例1](#示例1-1)
  - [解法1 - 内联](#解法1---内联-1)
  - [解法2 - 子查询](#解法2---子查询-1)
- [3. **SQL93** **返回购买 prod_id 为 BR01 的产品的所有顾客的电子邮件（一）**](#3-sql93-返回购买-prod_id-为-br01-的产品的所有顾客的电子邮件一)
  - [描述](#描述-2)
  - [示例1](#示例1-2)
  - [解法1](#解法1)
  - [解法2](#解法2)
- [4. **SQL94** **返回每个顾客不同订单的总金额**](#4-sql94-返回每个顾客不同订单的总金额)
  - [描述](#描述-3)
  - [示例1](#示例1-3)
- [5. **SQL100** **确定最佳顾客的另一种方式（二）**](#5-sql100-确定最佳顾客的另一种方式二)
  - [描述](#描述-4)
  - [示例1](#示例1-4)
  - [解法](#解法)
- [6. SQL108 组合 Products 表中的产品名称和 Customers 表中的顾客名称](#6-sql108-组合-products-表中的产品名称和-customers-表中的顾客名称)
  - [描述](#描述-5)
  - [示例1](#示例1-5)
  - [解法](#解法-1)

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

表`OrderItems`代表订单商品信息表，`prod_id`为产品`id`；`Orders`表代表订单表有`cust_id`代表顾客id和订单日期`order_date`

`OrderItems`表

| `prod_id` | `order_num` |
| --------- | ----------- |
| `BR01`    | `a0001`     |
| `BR01`    | `a0002`     |
| `BR02`    | `a0003`     |
| BR02      | `a0013`     |

`Orders`表

| `order_num` | `cust_id` | `order_date`          |
| ----------- | --------- | --------------------- |
| `a0001`     | `cust10`  | `2022-01-01 00:00:00` |
| `a0002`     | `cust1`   | `2022-01-01 00:01:00` |
| `a0003`     | `cust1`   | `2022-01-02 00:00:00` |
| `a0013`     | `cust2`   | `2022-01-01 00:20:00` |

【问题】

编写 `SQL` 语句，使用子查询来确定哪些订单（在 `OrderItems` 中）购买了 `prod_id` 为 "`BR01`" 的产品，然后从 `Orders` 表中返回每个产品对应的顾客 `ID`（`cust_id`）和订单日期（`order_date`），按订购日期对结果进行升序排序。

【示例结果】返回顾客`id` `cust_id`和定单日期`order_date`。

| `cust_id` | `order_date`          |
| --------- | --------------------- |
| `cust10`  | `2022-01-01 00:00:00` |
| `cust1`   | `2022-01-01 00:01:00` |

【示例解析】 

产品id为"`BR01`"的订单`a0001`和`a002`的下单顾客`cust10`和`cust1`的下单时间分别为`2022-01-01 00:00:00`和`2022-01-01 00:01:00`

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

### 3. **SQL93** **返回购买 prod_id 为 BR01 的产品的所有顾客的电子邮件（一）**

#### 描述

你想知道订购 `BR01` 产品的日期，有表`OrderItems`代表订单商品信息表，`prod_id`为产品`id`；`Orders`表代表订单表有`cust_id`代表顾客`id`和订单日期`order_date`；`Customers`表含有`cust_email` 顾客邮件和`cust_id`顾客`id`

`OrderItems`表

| `prod_id` | `order_num` |
| --------- | ----------- |
| `BR01`    | `a0001`     |
| `BR01`    | `a0002`     |
| `BR02`    | `a0003`     |
| `BR02`    | `a0013`     |

`Orders`表

| `order_num` | `cust_id` | `order_date`          |
| ----------- | --------- | --------------------- |
| `a0001`     | `cust10`  | `2022-01-01 00:00:00` |
| `a0002`     | `cust1`   | `2022-01-01 00:01:00` |
| `a0003`     | `cust1`   | `2022-01-02 00:00:00` |
| `a0013`     | `cust2`   | `2022-01-01 00:20:00` |

`Customers`表代表顾客信息，`cust_id`为顾客`id`，`cust_email`为顾客`email`

| `cust_id` | `cust_email`      |
| --------- | ----------------- |
| `cust10`  | `cust10@cust.com` |
| `cust1`   | `cust1@cust.com`  |
| `cust2`   | `cust2@cust.com`  |

【问题】返回购买 `prod_id` 为`BR01` 的产品的所有顾客的电子邮件（`Customers` 表中的 `cust_email`），结果无需排序。

提示：这涉及 `SELECT` 语句，最内层的从 `OrderItems` 表返回 `order_num`，中间的从 `Customers` 表返回 `cust_id`。

【示例结果】

返回顾客`email` `cust_email`

| `cust_email`      |
| ----------------- |
| `cust10@cust.com` |
| `cust1@cust.com`  |

`【示例解析】 

产品id为`BR01`的订单`a0001`和`a002`的下单顾客`cust10`和`cust1`的顾客`email` `cust_email`分别是：cust10@cust.com 、cust1@cust.com

#### 示例1

输入：

```sql
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

DROP TABLE IF EXISTS `Customers`;
CREATE TABLE IF NOT EXISTS `Customers`(
    cust_id VARCHAR(255) NOT NULL COMMENT '顾客id',
    cust_email VARCHAR(255) NOT NULL COMMENT '顾客email'
  );
INSERT `Customers` VALUES ('cust10','cust10@cust.com'),('cust1','cust1@cust.com'),('cust2','cust2@cust.com');
```

输出：

```sql
cust10@cust.com
cust1@cust.com
```

#### 解法1

```sql
select cust_email
from OrderItems oi
inner join Orders o
inner join Customers c
on oi.order_num = o.order_num and
o.cust_id = c.cust_id
where prod_id = 'BR01'
```

#### 解法2

```sql
select cust_email
from Customers
where cust_id in
(
    select cust_id
    from Orders
    where order_num in
    (
        select order_num
        from OrderItems
        where prod_id = 'BR01'
    )
)
```

### 4. **SQL94** **返回每个顾客不同订单的总金额**

#### 描述

我们需要一个顾客 `ID` 列表，其中包含他们已订购的总金额。

`OrderItems`表代表订单信息，`OrderItems`表有订单号：`order_num`和商品售出价格：`item_price`、商品数量：`quantity`。

| `order_num` | `item_price` | `quantity` |
| ----------- | ------------ | ---------- |
| `a0001`     | `10`         | `105`      |
| `a0002`     | `1`          | `1100`     |
| `a0002`     | `1`          | `200`      |
| `a0013`     | `2`          | `1121`     |
| `a0003`     | `5`          | `10`       |
| `a0003`     | `1`          | `19`       |
| `a0003`     | `7`          | `5`        |

`Orders`表订单号：`order_num`、顾客`id`：`cust_id`

| `order_num` | `cust_id` |
| ----------- | --------- |
| `a0001`     | `cust10`  |
| `a0002`     | `cust1`   |
| `a0003`     | `cust1`   |
| `a0013`     | `cust2`   |

【问题】

编写 `SQL`语句，返回顾客 `ID`（`Orders` 表中的 `cust_id`），并使用子查询返回`total_ordered` 以便返回每个顾客的订单总数，将结果按金额从大到小排序。

提示：你之前已经使用 `SUM()`计算订单总数。

【示例结果】返回顾客`id` `cust_id`和`total_order`下单总额

| `cust_id` | `total_ordered` |
| --------- | --------------- |
| `cust2`   | `2242`          |
| `cust1`   | `1300`          |
| `cust10`  | `1050`          |
| `cust2`   | `104`           |

【示例解析】cust2在Orders里面的订单a0013，a0013的售出价格是2售出数量是1121，总额是2242，最后返回cust2的支付总额是2242。

#### 示例1

输入：

```sql
DROP TABLE IF EXISTS `OrderItems`;
CREATE TABLE IF NOT EXISTS `OrderItems`(
	order_num VARCHAR(255) NOT NULL COMMENT '商品订单号',
	item_price INT(16) NOT NULL COMMENT '售出价格',
	quantity INT(16) NOT NULL COMMENT '商品数量'
);
INSERT `OrderItems` VALUES ('a0001',10,105),('a0002',1,1100),('a0002',1,200),('a0013',2,1121),('a0003',5,10),('a0003',1,19),('a0003',7,5);

DROP TABLE IF EXISTS `Orders`;
CREATE TABLE IF NOT EXISTS `Orders`(
  order_num VARCHAR(255) NOT NULL COMMENT '商品订单号',
  cust_id VARCHAR(255) NOT NULL COMMENT '顾客id'
);
INSERT `Orders` VALUES ('a0001','cust10'),('a0003','cust1'),('a0013','cust2');
```

输出：

```sql
cust2|2242.000
cust10|1050.000
cust1|104.000
```

### 5. **SQL100** **确定最佳顾客的另一种方式（二）**

#### 描述

`OrderItems`表代表订单信息，确定最佳顾客的另一种方式是看他们花了多少钱，`OrderItems`表有订单号`order_num`和`item_price`商品售出价格、`quantity`商品数量

| `order_num` | `item_price` | `quantity` |
| ----------- | ------------ | ---------- |
| `a1`        | `10`         | `105`      |
| `a2`        | `1`          | `1100`     |
| `a2`        | `1`          | `200`      |
| `a4`        | `2`          | `1121`     |
| `a5`        | `5`          | `10`       |
| `a2`        | `1`          | `19`       |
| `a7`        | `7`          | `5`        |

`Orders`表含有字段`order_num` 订单号、`cust_id`顾客id

| `order_num` | `cust_id`  |
| ----------- | ---------- |
| `a1`        | `cust10`   |
| `a2`        | `cust1`    |
| `a3`        | `cust2`    |
| `a4`        | `cust22`   |
| `a5`        | `cust221`  |
| `a7`        | `cust2217` |

顾客表`Customers`有字段`cust_id` 客户`id`、`cust_name` 客户姓名

| `cust_id`  | `cust_name` |
| ---------- | ----------- |
| `cust10`   | `andy`      |
| `cust1`    | `ben`       |
| `cust2`    | `tony`      |
| `cust22`   | `tom`       |
| `cust221`  | `an`        |
| `cust2217` | `hex`       |

【问题】编写 `SQL` 语句，返回订单总价不小于`1000` 的客户名称和总额（`OrderItems` 表中的`order_num`）。

提示：需要计算总和（`item_price` 乘以 `quantity`）。按总额对结果进行排序，请使用`INNER` `JOIN` 语法。

【示例结果】

| `cust_name` | `total_price` |
| ----------- | ------------- |
| `andy`      | `1050`        |
| `ben`       | `1319`        |
| `tom`       | `2242`        |

【示例解析】

总额（`item_price` 乘以 `quantity`）大于等于`1000`的订单号，例如`a2`对应的顾客id为`cust1`，`cust1`的顾客名称`cust_name`是`ben`，最后返回`ben`作为`order_num` `a2`的`quantity` * `item_price`总和的结果`1319`。

#### 示例1

输入：

```sql
DROP TABLE IF EXISTS `OrderItems`;
CREATE TABLE IF NOT EXISTS `OrderItems`(
	order_num VARCHAR(255) NOT NULL COMMENT '商品订单号',
	item_price INT(16) NOT NULL COMMENT '售出价格',
	quantity INT(16) NOT NULL COMMENT '商品数量'
);
INSERT `OrderItems` VALUES ('a1',10,105),('a2',1,1100),('a2',1,200),('a4',2,1121),('a5',5,10),('a2',1,19),('a7',7,5);


DROP TABLE IF EXISTS `Customers`;
CREATE TABLE IF NOT EXISTS `Customers`(
	cust_id VARCHAR(255) NOT NULL COMMENT '客户id',
	cust_name VARCHAR(255) NOT NULL COMMENT '客户姓名'
);
INSERT `Customers` VALUES ('cust10','andy'),('cust1','ben'),('cust2','tony'),('cust22','tom'),('cust221','an'),('cust2217','hex');

DROP TABLE IF EXISTS `Orders`;
CREATE TABLE IF NOT EXISTS `Orders`(
  order_num VARCHAR(255) NOT NULL COMMENT '商品订单号',
  cust_id VARCHAR(255) NOT NULL COMMENT '顾客id'
);
INSERT `Orders` VALUES ('a1','cust10'),('a2','cust1'),('a3','cust2'),('a4','cust22'),('a5','cust221'),('a7','cust2217');
```

输出：

```sql
andy|1050.000
ben|1319.000
tom|2242.000
```

#### 解法

```sql
select cust_name, sum(item_price * quantity) as total_price
from OrderItems oi
inner join Orders o
inner join Customers c
on oi.order_num = o.order_num
and o.cust_id = c.cust_id
group by cust_name
having sum(item_price * quantity) >= 1000
order by total_price
```

### 6. SQL108 组合 Products 表中的产品名称和 Customers 表中的顾客名称

#### 描述

`Products`表含有字段`prod_name`代表产品名称

| `prod_name` |
| ----------- |
| `flower`    |
| `rice`      |
| `ring`      |
| `umbrella`  |

`Customers`表代表顾客信息，`cust_name`代表顾客名称

| `cust_name` |
| ----------- |
| `andy`      |
| `ben`       |
| `tony`      |
| `tom`       |
| `an`        |
| `lee`       |
| `hex`       |

【问题】

编写 `SQL` 语句，组合 `Products` 表中的产品名称（`prod_name`）和 `Customers` 表中的顾客名称（`cust_name`）并返回，然后按产品名称对结果进行升序排序。

【示例结果】

| `prod_name` |
| ----------- |
| `an`        |
| `andy`      |
| `ben`       |
| `flower`    |
| `hex`       |
| `lee`       |
| `rice`      |
| `ring`      |
| `tom`       |
| `tony`      |
| `umbrella`  |

【示例解析】

拼接`cust_name`和`prod_name`并根据结果升序排序

#### 示例1

输入：

```sql
DROP TABLE IF EXISTS `Products`;
CREATE TABLE IF NOT EXISTS `Products` (
`prod_name` VARCHAR(255) NOT NULL COMMENT '产品名称'
);
INSERT INTO `Products` VALUES ('flower'),
('rice'),
('ring'),
('umbrella');

DROP TABLE IF EXISTS `Customers`;
CREATE TABLE IF NOT EXISTS `Customers`(
	cust_name VARCHAR(255) NOT NULL COMMENT '客户姓名'
);
INSERT `Customers` VALUES ('andy'),('ben'),('tony'),('tom'),('an'),('lee'),('hex');
```

输出：

```sql
an
andy
ben
flower
hex
lee
rice
ring
tom
tony
umbrella
```

#### 解法

```sql
select prod_name
from Products
union
select cust_name as prod_name
from Customers
order by prod_name
```





 

