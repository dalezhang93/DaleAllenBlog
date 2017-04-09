- **静态 SQL**：静态 SQL 语句一般用于嵌入式 SQL 应用中，在程序运行前，SQL 语句必须是确定的，例如 SQL 语句中涉及的列名和表名必须是存在的。**静态 SQL 语句的编译是在应用程序运行前进行的**，编译的结果会存储在数据库内部。而后程序运行时，数据库将直接执行编译好的 SQL 语句，降低运行时的开销。
- **动态 SQL**：动态 SQL 语句是在应用程序运行时被编译和执行的，例如，使用 DB2 的交互式工具 CLP 访问数据库时，用户输入的 SQL 语句是不确定的，因此 SQL 语句只能被动态地编译。动态 SQL 的应用较多，常见的 CLI 和 JDBC 应用程序都使用动态 SQL。

-----

- `DDL`(Data Definition Language):CREATE, ALTER, DROP, TRUNCATE, COMMENT, RENAME
- `DML`(Data Manipulation Language):SELECT, INSERT, UPDATE, DELETE, MERGE, CALL, EXPLAIN PLAN, LOCK TABLE
- `DCL`(Data Control Language):GRANT,REVOKE 取消授权
- `TCL`(Transaction Control Language):SAVEPOINT 设置保存点,ROLLBACK 回滚,SET TRANSACTION


------

## 事务的ACID属性

1.  原子性（Atomicity）
原子性是指事务是一个不可分割的工作单位，事务中的操作要么都发生，要么都不发生

2. 一致性（Consistency）
事务必须使数据库从一个一致性状态变换到另外一个一致性状态(数据不被破坏)

3. 隔离性（Isolation）
事务的隔离性是指一个事务的执行不能被其他事务干扰，即一个事务内部的操作及使用的数据对并发的其他事务是隔离的，并发执行的各个事务之间不能互相干扰

4. 持久性（Durability）
持久性是指一个事务一旦被提交，它对数据库中数据的改变就是永久性的，接下来的其他操作和数据库故障不应该对其有任何影响
