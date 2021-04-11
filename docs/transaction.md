# 数据库事务

## 一、事务特性

原子性：事务所包含的所有操作要么全成功，要么全部失败，不存在成功一部分失败一部分。

一致性：事务的执行结果必须使数据库从一个一致性状态变成另外一个一致性状态。如：a给b转账300，总共1000，转完之后总和也是1000.

隔离性：并发事务中，事务之间互不影响，如同串行执行一样。

持久性：事务一旦提交，其对数据库的操作是永久的且不可更改。

## 二、事务隔离级别

并发环境下事务可能产生一下问题：

1.**脏读**：事务a读取到了事务b未提交的数据

2.**不可重复读**：一个事务里读取了两次同一条数据，两次读取到的数据不一致。

3.**幻读**：在一个事务里发现了两次查询数据，后一次发现未被前一次查询到的数据

而事物的隔离级别就是解决上述问题的。

spring中定义的五种隔离级别：

```
public enum Isolation {
    DEFAULT(-1),
    READ_UNCOMMITTED(1),
    READ_COMMITTED(2),
    REPEATABLE_READ(4),
    SERIALIZABLE(8);

    private final int value;

    private Isolation(int value) {
        this.value = value;
    }

    public int value() {
        return this.value;
    }
}
```

1.DEFAULT：数据库默认的隔离级别，MySQL默认的隔离级别是REPEATABLE_READ，可使用select @@tx_isolation;查看。

2.READ_UNCOMMITTED：读未提交，即可以读到未提交的事务。无法解决上述3种情况中的任何一种，因此很少用。

3.READ_COMMITTED：读已提交，即只能读到已提交的事务。可以解决脏读，但无法解决不可重复读和幻读。

4.REPEATABLE_READ：可重复读，即在读出数据之后加锁，直到这个事务结束之后其它事务才可以访问这些数据。可以解决脏读和不可重复读。

5.SERLALIZABLE：串行执行，即所有事务按顺序一个一个执行，可以防止脏读，幻读，和不可重复度，但消耗比较大效率比较低，因此很少使用。

```
public @interface Transactional {
    @AliasFor("transactionManager")
    String value() default "";

    @AliasFor("value")
    String transactionManager() default "";

    Propagation propagation() default Propagation.REQUIRED;

    Isolation isolation() default Isolation.DEFAULT;

    int timeout() default -1;

    boolean readOnly() default false;

    Class<? extends Throwable>[] rollbackFor() default {};

    String[] rollbackForClassName() default {};

    Class<? extends Throwable>[] noRollbackFor() default {};

    String[] noRollbackForClassName() default {};
}
```

spring中可指定isolation级别。

## 三、事务的传播行为