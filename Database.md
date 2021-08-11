## MySQL使用

> 练习题：
> 
> 176. 第二高的薪水：LIMIT用法
> 181. 超过经理收入的员工：自联结
> 182. 查找重复的电子邮箱：GROUP By + Having用法
> 183. 从不订购的客户：左联结用法
> 196. 删除重复的电子邮箱：DELETE用法

#### 联结
![stackoverflow](https://i.stack.imgur.com/VQ5XP.png)
> 无论何时对表进行联结，应该至少有一个列出现在不止一个表中（被
联结的列）。

- 内联结（Inner Join）
  
  也即等值联结，例如：
  ```
  SELECT vend_name, prod_name, prod_price
  FROM vendors INNER JOIN products
  ON vendors.vend_id = products.vend_id;
  ```
  等价于
  ```
  SELECT vend_name, prod_name, prod_price
  FROM vendors, products
  WHERE vendors.vend_id = products.vend_id;
  ```

- 自联结：表自己联结自己
  
  多次使用相同的表进行查询，例如

  ```
  SELECT prod_id, prod_name
  FROM products as p1, products as p2
  WHERE p1.vend_id = p2.vend_id 
  AND p2.vend_id = 'DTNTR';
  ```

- 自然联结：排除不同表中多次出现的列，使每个列只出现一次（由用户实现）
  
- 外部联结：许多联结将一个表中的行与另一个表中的行相关联。但有时候会需要包含没有关联行的那些行
  - LEFT OUTER JOIN：从**左边表**中列出所有行
  - RIGHT OUTER JOIN：从右边表中列出所有行

    使用OUTER JOIN时必须指明是左外部联结还是右外部联结

    例子：
    ```
    SELECT customers.cust_id, orders.order_num
    FROM customers LEFT OUTER JOIN orders
    ON customers.cust_id = orders.cust_id;
    ```
  
#### 组合查询（UNION）

将多个SQL语句的执行结果按照一个结果集返回

#### 更新和删除数据

- 更新（一定要与where搭配使用）
  ```
  UPDATE customers
  SET cust_email = 'e@qq.com'
  WHERE cust_id = 123;
  ```
- 删除：DELETE


#### 创建和操纵表
> Notes：
>  - 可以为不同的表指定不同的引擎，但外键不能跨引擎
- 创建表
  
    一个例子：
    ```
    CREATE TABLE customers
    (
        cust_id int NOT NULL AUTO_INCREMENT, /*主键自增*/
        cust_name char(50) NOT NULL DEFAULT 'test', /*不允许为空，默认值为test*/
        cust_addr char(50) NULL,
        ...
        PRIMART KEY (cust_id) /*指明主键*/
    ) ENGINE=InnoDB; /*指明引擎，InnoDB不支持全文搜索*/
    ```
- 删除表

    例子：
    ```
    DROP TABLE customer2;
    ```

#### 事务处理
> 事务是指一系列SQL语句要么都执行，要么都不执行。
> - InnoDB支持事务处理，MyISAM不支持
> - 事务处理只能管理INSERT、UPDATE和DELETE，不能回退SELECT、DROP、CREATE操作

- 提交：将未存储的SQL语句结果写入数据库表
- 回退：撤销指定的SQL语句
- 保留点（savepoint）指事务处理中设置的临时占位符，可以对它发布回退（与回退整个事务处理不同），能灵活控制回退的点
  用法：
  ```
  SAVEPOINT delete1;
  ...
  ...
  ROLLBACK TO delete1;
  ```
- 开始事务：
  ```
  START TRANSACTION
  ...SQL
  ...
  COMMIT
  ```

- 更改默认的提交行为
  
  对表的修改分为两步：更改然后再提交，MySQL是默认提交更改，即每个SQL都是针对表执行的，想要更改这个默认行为，可以采用如下操作：
  ```
  SET autocommit = 0;
  ```
  注意：autocommit标志是针对每个连接而不是服务器的


## MySQL底层知识

### 事务与隔离
> [Innodb中的事务隔离级别和锁的关系](https://tech.meituan.com/2014/08/20/innodb-lock.html)

- 四种隔离级别：
  - READ UNCOMMITTED：未提交读，事务中还未提交的修改依然对其他事务可见（脏读）
  - READ COMMITTED：提交读，只能看到已提交的事务修改（不可重复读）
  - REPEATABLE READ：可重复读，通过第一次读时创建snapshot实现
  > This is the default isolation level for InnoDB. Consistent reads within the same transaction read the snapshot established by the first read.
  > 幻读是指两次相同的读返回的行数不同
  > 脏读是指两次对相同行的读，数据不一致（针对行的内容）
  - Serializable（串行读）：完全串行化的读，每次读都需要获得表级共享锁，读写相互都会阻塞

    
  