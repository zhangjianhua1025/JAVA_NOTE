# 1.mysql

## 1.1索引

### 1.1.1分类

（1）普通索引index：加速查找

（2）唯一索引unique：加速查找+约束（唯一）

（3）主键索引primary：加速查找+约束（唯一且不为空）

（4）全文索引fulltext：用于搜索很长一段文章时效果最好

（5）空间索引spatial：几乎不用

联合索引：

联合主键索引

联合普通索引

联合唯一索引

索引类型:

（1）hash类型：查询单条快，范围查询慢

（2）btree类型：b+树，层数越多，数量级指数增加就是用它（innodb默认支持它）

索引可以在创建表的时候添加，也可以在之后创建

添加主键索引：alter table 表名 add primary key (id)

添加普通索引：alter table 表名 add index 索引名称 (`字段名称`)

添加唯一索引：alter table 表名 add unique 索引名称(`字段名称`)

添加全文索引：alter table 表名 add fulltext 索引名称(`字段名称`)