
## Hibernate面试题

1. Hibernate工作原理及为什么要用？

    Hibernate工作原理：

    1. 读取并解析配置文件
    2. 读取并解析映射信息，创建SessionFactory
    3. 打开Sesssion
    4. 创建事务Transation
    5. 持久化操作
    6. 提交事务
    7. 关闭Session
    8. 关闭SesstionFactory

    使用Hibernate框架就不用我们写很多繁琐的SQL语句。Hibernate实现了ORM，能够将对象映射成数据库表，从而简化我们的开发！

2. Hibernate是如何延迟加载(懒加载)?

    通过设置属性lazy进行设置是否需要懒加载

    当Hibernate在查询数据的时候，数据并没有存在与内存中，当程序真正对数据的操作时，对象才存在与内存中，就实现了延迟加载，他节省了服务器的内存开销，从而提高了服务器的性能。

3. Hibernate中怎样实现类之间的关系?(如：一对多、多对多的关系)

    它们通过配置文件中的many-to-one、one-to-many、many-to-many来实现类之间的关联关系的。

4. hibernate的三种状态之间如何转换

    Hibernate中对象的状态：
    * 临时/瞬时状态
    * 持久化状态
    * 游离状态

    1. 临时/瞬时状态
        当我们直接new出来的对象就是临时/瞬时状态的..
        * 该对象还没有被持久化【没有保存在数据库中】
        * 不受Session的管理

    2. 持久化状态

        当保存在数据库中的对象就是持久化状态了

        * 当调用session的save/saveOrUpdate/get/load/list等方法的时候，对象就是持久化状态
        * 在数据库有对应的数据
        * 受Session的管理
        * 当对对象属性进行更改的时候，会反映到数据库中!

    3. 游离状态

        当Session关闭了以后，持久化的对象就变成了游离状态了...

        * 不处于session的管理
        * 数据库中有对应的记录

    有了上面的基础，我们就很容易说出它们之间的转换了

    **new出来的对象是瞬时状态->保存到数据库中(受Session管理)就是持久化状态->将session close掉就是游离状态**

5. 比较hibernate的三种检索策略优缺点

    1. 立即检索：
        * 优点： 对应用程序完全透明，不管对象处于持久化状态，还是游离状态，应用程序都可以方便的从一个对象导航到与它关联的对象；
        * 缺点： 1.select语句太多；2.可能会加载应用程序不需要访问的对象白白浪费许多内存空间；
        * 立即检索:lazy=false；

    2. 延迟检索：

        优点： 由应用程序决定需要加载哪些对象，可以避免可执行多余的select语句，以及避免加载应用程序不需要访问的对象。因此能提高检索性能，并且能节省内存空间；
        缺点： 应用程序如果希望访问游离状态代理类实例，必须保证他在持久化状态时已经被初始化；
        延迟加载：lazy=true；

    3. 迫切左外连接检索：

        优点： 1对应用程序完全透明，不管对象处于持久化状态，还是游离状态，应用程序都可以方便地冲一个对象导航到与它关联的对象。2使用了外连接，select语句数目少；
        缺点： 1 可能会加载应用程序不需要访问的对象，白白浪费许多内存空间；2复杂的数据库表连接也会影响检索性能；
        预先抓取： fetch=“join”；

6. hibernate都支持哪些缓存策略

    usage的属性有4种：

    * 放入二级缓存的对象，只读(Read-only);
    * 非严格的读写(Nonstrict read/write)
    * 读写； 放入二级缓存的对象可以读、写(Read/write)；
    * 基于事务的策略(Transactional)

7. hibernate里面的sorted collection 和ordered collection有什么区别

    * sorted collection

        是在内存中通过Java比较器进行排序的

    * ordered collection

        是在数据库中通过order by进行排序的

    对于比较大的数据集，为了避免在内存中对它们进行排序而出现 Java中的OutOfMemoryError，最好使用ordered collection。

8. 说下Hibernate的缓存机制

    * 一级缓存：

        Hibenate中一级缓存，也叫做session的缓存，它可以在session范围内减少数据库的访问次数！ 只在session范围有效！ Session关闭，一级缓存失效！
        只要是持久化对象状态的，都受Session管理，也就是说，都会在Session缓存中！

        Session的缓存由hibernate维护，用户不能操作缓存内容； 如果想操作缓存内容，必须通过hibernate提供的evit/clear方法操作。

    * 二级缓存：

        二级缓存是基于应用程序的缓存，所有的Session都可以使用
        Hibernate提供的二级缓存有默认的实现，且是一种可插配的缓存框架！如果用户想用二级缓存，只需要在hibernate.cfg.xml中配置即可； 不想用，直接移除，不影响代码。

        如果用户觉得hibernate提供的框架框架不好用，自己可以换其他的缓存框架或自己实现缓存框架都可以。

        Hibernate二级缓存：存储的是常用的类

9. Hibernate的查询方式有几种

    * 对象导航查询(objectcomposition)
    * HQL查询
        1. 属性查询
        2. 参数查询、命名参数查询
        3. 关联查询
        4. 分页查询
        5. 统计函数
    * Criteria 查询
    * SQLQuery本地SQL查询

10. 如何优化Hibernate？

    * 数据库设计调整
    * HQL优化
    * API的正确使用(如根据不同的业务类型选用不同的集合及查询API)
    * 主配置参数(日志，查询缓存，fetch_size, batch_size等)
    * 映射文件优化(ID生成策略，二级缓存，延迟加载，关联优化)
    * 一级缓存的管理
    * 针对二级缓存，还有许多特有的策略

    详情可参考资料：https://www.cnblogs.com/xhj123/p/6106088.html

11. 谈谈Hibernate中inverse的作用

    inverse属性默认是false,就是说关系的两端都来维护关系。

    * 比如Student和Teacher是多对多关系，用一个中间表TeacherStudent维护。
    * 一共四种情况
        * Student设置inverse="true"，关系由另一端Teacher维护，只有Teacher插入或删除时才会触发对中间表的操作。
        * Teach设置inverse="true"，关系由另一端Student维护，只有Student插入或删除时才会触发对中间表的操作。
        * 两边都设置inverse="true"，这是不对的，会导致任何操作都不触发对中间表的影响；
        * 两边都inverse=”false”或默认时，会导致在中间表中插入两次关系。

    如果表之间的关联关系是“一对多”的话，那么inverse只能在“一”的一方来配置！

    详情可参考：https://zhongfucheng.bitcron.com/post/hibernate/hibernate-inversehe-cascadeshu-xing-zhi-shi-yao-dian

12. jdbc， hibernate 和 ibatis 的区别

    1. jdbc:手动
        * 手动写sql
        * delete、insert、update要将对象的值一个一个取出传到sql中,不能直接传入一个对象。
        * select:返回的是一个resultset，要从ResultSet中一行一行、一个字段一个字段的取出，然后封装到一个对象中，不直接返回一个对象。
    2. ibatis的特点:半自动化
        * sql要手动写
        * delete、insert、update:直接传入一个对象
        * select:直接返回一个对象
    3. hibernate:全自动
        * 不写sql,自动封装
        * delete、insert、update:直接传入一个对象
        * select:直接返回一个对象

13. 在数据库中条件查询速度很慢的时候,如何优化?

    1. 建索引
    2. 减少表之间的关联
    3. 优化sql，尽量让sql很快定位数据，不要让sql做全表查询，应该走索引,把数据量大的表排在前面
    4. 简化查询字段，没用的字段不要，已经对返回结果的控制，尽量返回少量数据

14. 什么是SessionFactory,她是线程安全么

    SessionFactory是Hibrenate创建Session的来源，单例数据存储和线程安全的，因此可以多线程同时访问。一个SessionFactory在启动的时候只能建立一次。SessionFactory应该包装各种单例以至于它能很简单的在一个应用代码中储存.

15. get和load区别

    get立即查询，load懒加载

    1. get如果没有找到会返回null， load如果没有找到会抛出异常。
    2. get会先查一级缓存， 再查二级缓存，然后查数据库；load会先查一级缓存，如果没有找到，就创建代理对象， 等需要的时候去查询二级缓存和数据库。

16. merge的含义：

    * 如果session中存在相同持久化标识(identifier)的实例，用用户给出的对象的状态覆盖旧有的持久实例
    * 如果session没有相应的持久实例，则尝试从数据库中加载，或创建新的持久化实例,最后返回该持久实例
    * 用户给出的这个对象没有被关联到session上，它依旧是脱管的

    详情可参考：http://cp3.iteye.com/blog/786019

17. persist和save的区别

    * persist不保证立即执行，可能要等到flush；
    * persist不更新缓存；
    * save, 把一个瞬态的实例持久化标识符，及时的产生,它要返回标识符，所以它会立即执行Sql insert
    * 使用 save() 方法保存持久化对象时，该方法返回该持久化对象的标识属性值(即对应记录的主键值)；
    * 使用 persist() 方法来保存持久化对象时，该方法没有任何返回值。

    参考资料：http://blog.csdn.net/u010739551/article/details/47253881

18. 主键生成策略有哪些

    1. 主键的自动生成策略
        * identity 自增长(mysql,db2)
        * sequence 自增长(序列)， oracle中自增长是以序列方法实现**
        * native 自增长【会根据底层数据库自增长的方式选择identity或sequence】
            如果是mysql数据库, 采用的自增长方式是identity
            如果是oracle数据库， 使用sequence序列的方式实现自增长
        * increment 自增长(会有并发访问的问题，一般在服务器集群环境使用会存在问题)

    2. 指定主键生成策略为手动指定主键的值
        * assigned

    3. 指定主键生成策略为UUID生成的值
        * uuid
    4. foreign(外键的方式)

19. 简述hibernate中getCurrentSession和openSession区别
    1. getCurrentSession会绑定当前线程，而openSession不会，因为我们把hibernate交给我们的spring来管理之后，我们是有事务配置，这个有事务的线程就会绑定当前的工厂里面的每一个session，而openSession是创建一个新session。
    2. getCurrentSession事务是有spring来控制的，而openSession需要我们手动开启和手动提交事务，
    3. getCurrentSession是不需要我们手动关闭的，因为工厂会自己管理，而openSession需要我们手动关闭。
    4. getCurrentSession需要我们手动设置绑定事务的机制，有三种设置方式:
        * jdbc本地的Thread
        * JTA
        * spring提供的事务管理机制org.springframework.orm.hibernate4.SpringSessionContext，而且spring默认使用该种事务管理机制

20. Hibernate中的命名SQL查询指的是什么?

    命名查询指的是用\<sql-query>标签在影射文档中定义的SQL查询，可以通过使用Session.getNamedQuery()方法对它进行调用。命名查询使你可以使用你所指定的一个名字拿到某个特定的查询。

    Hibernate中的命名查询可以使用注解来定义，也可以使用我前面提到的xml影射问句来定义。在Hibernate中，@NameQuery用来定义单个的命名查询，@NameQueries用来定义多个命名查询。

21. 为什么在Hibernate的实体类中要提供一个无参数的构造器这一点非常重要？

    每个Hibernate实体类必须包含一个无参数的构造器, 这是因为Hibernate框架要使用Reflection API，通过调用Class.newInstance()来创建这些实体类的实例。如果在实体类中找不到无参数的构造器，这个方法就会抛出一个InstantiationException异常。

22. 可不可以将Hibernate的实体类定义为final类?

    你可以将Hibernate的实体类定义为final类，但这种做法并不好。因为Hibernate会使用代理模式在延迟关联的情况下提高性能，如果你把实体类定义成final类之后，因为Java不允许对final类进行扩展，所以Hibernate就无法再使用代理了， 如此一来就限制了使用可以提升性能的手段。

原文：https://www.cnblogs.com/Java3y/p/8535459.html