# MySql

## DDL

操作数据库和表.

1. 查询

   ````mysql
   -- 查询数据库
   show databases;
   
   -- 查询表
   show tables; -- 查询当前数据库下所有表的名称
   desc 表名称; -- 查询表的结构信息
   ````

2. 创建

   ````mysql
   -- 创建数据库
   create database 数据库名称;
   create database if not exists 数据库名称;
   
   -- 创建表
   create table 表名 (
   	字段名1 数据类型1,
   	...
       字段名n 数据类型n
   );
   ````

3. 删除

   ````mysql
   -- 删除数据库
   drop database 数据库名称;
   drop database if exists 数据库名称;
   
   -- 删除表
   drop table 表名;
   drop table if exists 表名;
   ````

4. 使用/进入数据库

   ````mysql
   select database(); -- 使用/查看当前数据库
   use 数据库名称;
   ````

5. 修改表

   ````mysql
   -- 修改表名
   alter table 表名 rename to 新表名;
   -- 增加一列
   alter table 表名 add 列名 数据类型;
   -- 修改数据类型
   alter table 表名 modify 列名 新数据类型;
   -- 修改列名和数据类型
   alter table 表名 change 列名 新列名 新数据类型;
   -- 删除列
   alter table 表名 drop 列名;
   ````

- 数据类型
  ![image-20211122132146849](C:\Users\admin2\AppData\Roaming\Typora\typora-user-images\image-20211122132146849.png)

----

## DML

对表中的数据进行增删改.

1. 添加

   ````mysql
   -- 给指定列添加数据
   insert into 表名 
   (列名1, 列名2, ...) 
   values 
   (值1, 值2, ... );
   
   -- 给指定列批量添加数据
   insert into 表名 
   (列名1, 列名2, ...)
   values
   (值1, 值2, ...),
   ...
   (值1, 值2, ...);
   
   
   -- 给全部列添加数据
   insert into 表名 
   values
   (值1, 值2, ...);
   
   -- 给全部列批量添加数据
   insert into 表名 
   values
   (值1, 值2, ...),
   ...
   (值1, 值2, ...);
   ````

2. 修改

   ````mysql
   -- 修改表数据
   update 表名 
   set -- 设置需要修改的值
   列名1 = 值1, 
   列名2 = 值2, 
   ...
   where -- 如果不控制条件, 则改变该表中所有的值
   条件列表;
   ````

3. 删除

   ````mysql
   delete from 表名
   where -- 如果不控制条件, 则删除该表中所有数据
   条件列表;
   
   truncate 表名; -- 整体清空表
   ````
   
   

---

## DQL

对表中的数据进行查询.

1. 基础查询

   ````mysql
   -- 查询多个字段
   select
   字段列表
   from
   表名;
   
   -- 查询字段并删除重复记录
   select distinct
   字段列表
   from
   表名;
   
   -- 起别名
   select
   字段列表 (as) 别名
   from
   表名;
   ````

2. 条件查询

   ````mysql
   select
   字段列表
   from
   表名
   where
   条件列表;
   ````

   ![image-20211122142431537](C:\Users\admin2\AppData\Roaming\Typora\typora-user-images\image-20211122142431537.png)

3. 分组查询

   ````mysql
   select
   字段列表 -- 必须为聚合函数和分组字段, 查询其他字段无意义
   from
   表名
   [where 分组前条件限定] -- 不能对聚合函数进行判断
   group by
   分组字段名
   [having 分组后条件过滤] -- 可以对聚合函数进行判断;
   ````

   - 执行顺序: where > 聚合函数 > having.

   - 聚合函数

     ![image-20211122143355189](C:\Users\admin2\AppData\Roaming\Typora\typora-user-images\image-20211122143355189.png)

     注意: null不参与聚合函数运算.

4. 排序查询

   ````mysql
   select
   字段列表
   from
   表名
   order by
   排序字段名1 [asc], -- 升序, 不写默认升序
   排序字段名2 [desc], -- 降序
   ...;
   ````

5. 分页查询

   ````mysql
   select
   字段列表
   from
   表名
   limit
   起始索引, 查询条目数;
   ````

   - 起始索引从0开始.
   - 起始索引 = (当前页码 - 1) * 每页条目数.

----

## 约束

约束是作用于表中列上的规则, 用于限制加入表中的数据. 保证了数据的正确性, 有效性和完整性.

![image-20211122145018201](C:\Users\admin2\AppData\Roaming\Typora\typora-user-images\image-20211122145018201.png)

- 主键 = 非空 + 唯一. 自增长: auto_increment.

- 添加和删除约束

  ````mysql
  -- 建完表后添加约束
  alter table 表名
  modify 字段名 数据类型 not null/unique;
  
  alter table 表名
  add primary key(字段名);
  
  alter table 表名
  alter 列名
  set default 默认值;
  
  -- 删除约束
  alter table 表名
  modify 字段名 数据类型; -- 删除非空
  
  alter table 表名
  drop index 字段名; -- 删除唯一
  
  alter table 表名
  drop primary key; -- 删除主键
  
  alter table 表名
  alter 列名 drop default; -- 删除默认
  ````

- 外键

  ````mysql
  -- 创建表时添加
  create table 表名
  (列名 数据类型, ...)
  ...
  constraint 外键名称
  foreign key (外键列名)
  references 主表名(主表列名);
  
  -- 创建表后添加
  alter table 表名
  add constraint 外键名称
  foreign key (外键字段名称)
  references 主表名(主表列名);
  
  -- 删除约束
  alter table 表名
  drop foreign key 外键名称;
  ````

  - 先建主表, 再建从表. 先添加主表数据, 再添加从表数据. 先删除从表信息, 再删除对应的主表信息.

---

## 数据库设计

- 一对多
  在多的一方建立外键, 指向一的一方的主键.
  
- 多对多
  建立中间表, 中间表至少包含两个外键, 分别关联两方主键.
  
- 一对一
  在任意一方加入外键, 关联另一方主键, 设置外键为唯一.
  
- 补充: 查询外键名称

  ````mysql
  select
  * 
  from
  information_schema.TABLE_CONSTRAINTS  t 
  where 
  t.TABLE_NAME='表名' 
  and 
  CONSTRAINT_TYPE='FOREIGN KEY';
  ````


----

## 多表查询

1. 连接查询

   - 内连接: 取交集

     ````mysql
     -- 隐式
     select
     字段列表 -- 表名.字段名
     from
     表1 [别名], 
     表2 [别名], 
     ...
     where
     条件;
     
     -- 显式
     select
     字段列表
     from
     表1 [别名]
     [inner] join 
     表2 [别名]
     on 
     条件;
     ````

   - 外连接

     - 左外连接: 左表与交集 left join
     - 右外连接: 右表与交集 right join

2. 子查询
   嵌套查询

   ````mysql
   -- 单行单列, 作为条件值, 使用 = != > < 等进行条件判断
   select
   字段列表
   from
   表名
   where
   字段名 = (子查询);
   
   -- 多行单列
   select
   字段列表
   from
   表名
   where
   字段名 in (子查询);
   
   -- 多行多列 作为虚拟表
   select
   字段列表
   from
   (子查询)
   where
   条件;
   ````


----

## 事务

````java
try {
    //用Connection对象开启事务
    connection.setAutoCommit(false);
    //执行sql语句
    //提交事务
    connection.commit();
} catch (Exception e) {
    //回滚事务
    connection.rollback();
}
````

----

## JDBC

- 连接数据库

  ````java
  //注册驱动 可省略
  Class.forName("com.mysql.cj.jdbc.Driver");
  
  //获取连接. useServerPrepStmts=true开启预编译. useSSL=false解决JDBC版本(低)与MySQL版本(高)不兼容的问题.
  String url = "jdbc:mysql://127.0.0.1:3306/db1?useServerPrepStmts=true&useSSL=false";
  String username = "root";
  String password = "1234";
  Connection conn = DriverManager.getConnection(url, username, password);
  
  //定义sql语句
  String sql = "...";
  ````

- 操作数据库

  1. sql语句中无?占位符.

     ````java
     //创建执行对象
     Statement stmt = conn.createStatement();
     //执行sql语句, 执行时需传入sql语句
     //(1)增删改, 返回int
     stmt.executeUpdate(sql);
     //(2)查, 返回ResultSet
     stmt.executeQuery(sql);
     ````

  2. sql语句中用?占位符替代了变量, 并对sql语句进行预编译.

     ````java
     //创建执行对象
     PreparedStatement pstmt = conn.prepareStatement(sql);
     //使用占位符的情况下,需要传入具体的值.parameterIndex为表的参数索引,从1开始.value为传入的值,Xxx对应值的具体数据类型.
     pstmt.setXxx(parameterIndex, value);
     //执行sql语句, 不需要传参
     pstmt.executeUpdate();
     pstmt.executeQuery();
     ````

  - 操作ResultSet

    ````java
    //登录操作
    ResultSet set = pstmt.executeQuery();
    if (set.next()) {
        System.out.println("登录成功");
    } else {
        System.out.println("登录失败");
    }
    
    //查询对象操作. 
    //(1)创建存储对象的容器
    ArrayList<Emp> list = new ArrayList<>();
    //(2)执行sql,返回结果集
    ResultSet set = stmt.executeQuery(sql);
    //(3)遍历结果集,如果不为空就创建对象,定义对象属性并存储到集合中
    while (set.next()) {
        Emp emp = new Emp();
        emp.setId(set.getInt(1));
        emp.setEname(set.getString(2));
        emp.setSalary(set.getInt(3));
        emp.setDept_id(set.getInt(4));
        list.add(emp);
    }
    ````

  - 配置druid

    ````java
    //导入druid的jar包
    //定义配置文件druid.properties
    
    //加载druid配置文件
    Properties prop = new Properties();
    prop.load(new FileInputStream("D:\\develop\\project\\JavaLearning\\JDBC\\src\\main\\java\\com\\java\\learning\\druid.properties"));
    
    //获取连接池对象
    DataSource dataSource = DruidDataSourceFactory.createDataSource(prop);
    
    //获取数据库连接Connection
    Connection conn = dataSource.getConnection();
    ````

    

----

# Mybatis

- 依赖

  ````xml
  <dependencies>
      <!-- mybatis -->
      <dependency>
          <groupId>org.mybatis</groupId>
          <artifactId>mybatis</artifactId>
          <version>3.5.5</version>
      </dependency>
  
      <!-- mysql驱动 -->
      <dependency>
          <groupId>mysql</groupId>
          <artifactId>mysql-connector-java</artifactId>
          <version>8.0.27</version>
      </dependency>
  
      <!-- junit单元测试 -->
      <dependency>
          <groupId>junit</groupId>
          <artifactId>junit</artifactId>
          <version>4.13.2</version>
          <scope>test</scope>
      </dependency>
  
      <!-- 添加slf4j日志api -->
      <dependency>
          <groupId>org.slf4j</groupId>
          <artifactId>slf4j-api</artifactId>
          <version>1.5.6</version>
      </dependency>
  
      <dependency>
          <groupId>ch.qos.logback</groupId>
          <artifactId>logback-classic</artifactId>
          <version>1.2.3</version>
      </dependency>
  
      <dependency>
          <groupId>ch.qos.logback</groupId>
          <artifactId>logback-core</artifactId>
          <version>1.2.3</version>
      </dependency>
  
      <dependency>
          <groupId>com.alibaba</groupId>
          <artifactId>druid</artifactId>
          <version>1.2.8</version>
      </dependency>
  </dependencies>
  ````



