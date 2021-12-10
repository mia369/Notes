# Mybatis使用

## 依赖导入

```xml
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
</dependencies>
```

----

## 配置mybatis-config.xml

MyBatis核心配置文件, 替换连接信息, 解决硬编码问题

````xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

<!-- 类型别名&包扫描:使Mapper.xml中的mapper的resultType 和 resultMap中的type可以简写为接口名(忽略大小写),而不需要再带有包名 -->
    <typeAliases>
        <package name="com.ht.pojo"/>
    </typeAliases>

<!-- environments: 配置数据库连接环境信息,可以配置多个environment, 通过default属性进行切换 -->
    <environments default="development">
        <!-- 环境变量 -->
        <environment id="development">
            <!-- 事务管理器,会被更高级的spring框架接管 -->
            <transactionManager type="JDBC"/>
            <!-- 数据源,也会被spring接管 -->
            <dataSource type="POOLED">
                <!-- 数据库连接信息 -->
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql:///mybatis?useSSl=false"/>
                <property name="username" value="root"/>
                <property name="password" value="1234"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
<!-- 加载sql映射文件 -->
<!-- <mapper resource="com/ht/mapper/UserMapper.xml"/> -->
        <package name="com.ht.mapper"/>
    </mappers>
</configuration>
````

- 用包名加载sql映射文件需要将mapper.xml文件置于mapper字节码文件的同级目录下, 目录可安排如下

  ![image-20211125214718035](C:\Users\admin2\AppData\Roaming\Typora\typora-user-images\image-20211125214718035.png)

----

## 编写mapper.xml

1. 在pojo包中创建类, 用于封装每条数据.

2. 在mapper包中创建mapper接口, 用于定义方法.

   ````java
   public interface BrandMapper {
       List<Brand> selectAll();
   }
   ````

3. 在resources中的对应包下, 如下配置mapper.xml文件
   mapper.xml文件是**SQL映射文件, 统一管理sql语句, 解决硬编码问题**

   ````xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper
           PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   
   <!-- namespace 名称空间 -->
   <mapper namespace="com.ht.mapper.BrandMapper">
   
   <!-- 结果映射, 使用resultMap标签解决表列名和类属性名不一致的问题 -->
       <!-- id: 唯一标识  type: 映射的类型,支持别名 -->
       <resultMap id="brandResultMap" type="brand">
           <!-- id: 完成主键字段的映射	 result: 完成一般字段的映射
    			column: 表的列名		property: 实体类的属性名-->
           <id column="num" property="id"/>
           <result column="brand_name" property="brandName"/>
       </resultMap>
   
       <select id="selectAll" resultMap="brandResultMap">
           select *
           from tb_brand;
       </select>
   
       <!-- id是sql语句的唯一标识, resultType是返回结果的类型 -->
       <!--    <select id="selectAll" resultType="brand">
               select *
               from tb_brand;
           </select>-->
   </mapper>
   ````

   - 当数据库表的字段名称 和 实体类的属性名称 不一致时, 无法自动封装数据, 解决方法如下:
     1. 起别名: 在sql语句中给出现不一致的列名起别名. 缺点: 每次查询都要重新定义别名.
     2. sql片段. 缺点: 不灵活.
     3. resultMap(推荐)
        - 定义<resultMap>标签
        - 在<select>标签中使用resultMap属性替换resultType属性.

4. 根据需求增加sql语句

   - 参数占位符
     1. #{}: 执行sql时, 先替换为 ? , 再赋值, 无sql注入问题. 传参时使用.
     2. ${}: 拼接sql语句. 必存在sql注入问题. 对表名, 列名进行动态设置时使用.
   - parameterType: 用于设置参数类型, 一般省略.
   - sql语句中特殊字符处理(如 '<' ):
     1. 转义字符. 
     2. CDATA区: <![CDATA[内容]]>

   ````xml
   <!-- 查看详情 -->
   <select id="selectById" parameterType="brand" resultMap="brandResultMap">
       select *
       from tb_brand
       where id = #{id};
   </select>
   ````

----

## 参数封装

- 单个参数

  1. POJO类型: 直接使用, 属性名 和 参数占位符名称一致.
  2. Map集合: 直接使用, 键名 和 参数占位符名称一致.
  3. Collection: 封装为Map集合, 可用@Param注解替换Map集合中默认的arg键名.
     map.put("arg0", collection集合);
     map.put("collection", collection集合);
  4. List: 封装为Map集合, 可用@Param注解替换Map集合中默认的arg键名.
     map.put("arg0", list集合);
     map.put("collection", list集合);
     map.put("list", list集合);
  5. Array: 封装为Map集合, 可用@Param注解替换Map集合中默认的arg键名.
     map.put("arg0", 数组);
     map.put("array", 数组);
  6. 其他类型: 直接使用.

- 多个参数: 封装为Map集合, 可用@Param注解替换Map集合中默认的arg键名.

  map.put("arg0", 参数值1);
  map.put("param1", 参数值1);
  map.put("arg1", 参数值2);
  map.put("param2", 参数值2);
  -------------- @Param("username") ---------------
  map.put("username", 参数值1);
  map.put("param1", 参数值1);
  map.put("arg1", 参数值2);
  map.put("param2", 参数值2);

----

## 案例: 用户登录

![image-20211202132442180](C:\Users\admin2\AppData\Roaming\Typora\typora-user-images\image-20211202132442180.png)

![image-20211202132452532](C:\Users\admin2\AppData\Roaming\Typora\typora-user-images\image-20211202132452532.png)

## 用户注册

![image-20211202133210351](C:\Users\admin2\AppData\Roaming\Typora\typora-user-images\image-20211202133210351.png)
