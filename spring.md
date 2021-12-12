# Spring框架

## 概述

1. Spring是轻量级的开源的JavaEE框架.
2. 可以解决企业应用开发的复杂性.
3. 包含两个核心部分: 
   - IoC: 控制反转, 把创建对象过程交由Spring管理.
   - AOP: 面向切面, 不修改源码进行功能增强.
4. 特点:
   - 方便解耦, 简化开发(IoC).
   - AOP编程支持.
   - 方便程序测试(Junit).
   - 方便和其他框架整合(MyBatis).
   - 方便事务操作.
   - 降低API开发难度(JDBC).

![image-20211206181910920](C:\Users\admin2\AppData\Roaming\Typora\typora-user-images\image-20211206181910920.png)

----

## 快速入门

1. 导入依赖(Core Container)

   ````xml
   <dependencies>
       <!-- https://mvnrepository.com/artifact/org.springframework/spring-beans -->
       <dependency>
           <groupId>org.springframework</groupId>
           <artifactId>spring-beans</artifactId>
           <version>5.3.13</version>
       </dependency>
   
       <!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
       <dependency>
           <groupId>org.springframework</groupId>
           <artifactId>spring-context</artifactId>
           <version>5.3.13</version>
       </dependency>
   
       <!-- https://mvnrepository.com/artifact/org.springframework/spring-core -->
       <dependency>
           <groupId>org.springframework</groupId>
           <artifactId>spring-core</artifactId>
           <version>5.3.13</version>
       </dependency>
   
       <!-- https://mvnrepository.com/artifact/org.springframework/spring-expression -->
       <dependency>
           <groupId>org.springframework</groupId>
           <artifactId>spring-expression</artifactId>
           <version>5.3.13</version>
       </dependency>
   
       <!-- https://mvnrepository.com/artifact/commons-logging/commons-logging -->
       <dependency>
           <groupId>commons-logging</groupId>
           <artifactId>commons-logging</artifactId>
           <version>1.2</version>
       </dependency>
   
   </dependencies>
   ````

2. 在resource创建bean.xml文件, 通过bean标签配置User对象创建

   ````xml
   <bean id="user" class="com.ht.spring5.User"></bean>
   ````

3. 创建User类

4. 测试

   ````java
   @Test
   public void addTest(){
       //1.加载spring配置文件
       ApplicationContext context = new ClassPathXmlApplicationContext("/config/bean.xml");
       //2.获取配置创建的对象
       User user = context.getBean("user",User.class);
       System.out.println(user);
       user.add();
   }
   ````

----

## IOC容器

控制反转, 把对象创建和对象之间的调用过程交给Spring管理.

使用IoC的目的: 降低耦合度. 上方入门案例就是IoC的实现.

### 底层原理

- xml解析, 工厂模式, 反射
  ![image-20211206205403695](C:\Users\admin2\AppData\Roaming\Typora\typora-user-images\image-20211206205403695.png)
  
  

### IoC接口

- IoC思想基于IoC容器, IoC容器底层就是对象工厂.
- Spring提供IoC容器的2种实现方式(接口):
  1. BeanFactory: IoC容器的基本实现方式, 一般供Spring内部使用. 加载配置文件时不创建对象, 而在获取或使用对象时才创建对象.
  2. ApplicationContext: BeanFactory接口的子接口, 提供更强大的功能, 一般供开发人员使用. 加载配置文件时就会将其中配置的类实例化.
- ApplicationContext接口中的实现类: 
  1. FileSystemXmlApplicationContext. 使用xml的全路径.
  2. ClassPathXmlApplicationContext. 使用xml的相对路径.
     

### IoC操作Bean管理

- Bean管理指2个操作: Spring创建对象, Spring注入属性.
- 2种实现方式: 基于xml, 基于注解.

#### 基于xml方式的Bean管理

##### 创建对象

````xml
<bean id="user" class="com.ht.spring5.User"></bean>
````

- 在Spring配置文件中, 使用bean标签, 标签中添加相应属性, 就可以实现对象创建.
- bean标签中使用的属性:
  - id: 唯一标识.
  - class: 类全路径(包类路径).
- 创建对象时默认执行**无参构造方法**, 如没有无参构造方法则会报错.

##### 注入属性(需要先创建对象)

- DI: 依赖注入(Dependency Injection)

  1. 使用无参构造 + set方法注入

     - 通过bean标签配置Book对象的创建和属性注入

       ````xml
       <bean id="book" class="com.ht.spring5.Book">
           <property name="bname" value="长恨歌"></property>
           <property name="bauthor" value="李白"></property>
       </bean>
       ````

     - 测试: 先创建对象, 再调用方法.

       ````java
       @Test
       public void showTest(){
           //1.加载spring配置文件
           ApplicationContext context = new ClassPathXmlApplicationContext("/config/bean1.xml");
           //2.获取配置创建的对象
           Book book = context.getBean("book",Book.class);
           System.out.println(book);
           book.show();
       }
       ````

  2. 使用有参构造方法注入

     - 通过bean标签配置Order对象的创建和属性注入.

       ````xml
       <bean id="order" class="com.ht.spring5.Order">
           <constructor-arg name="item" value="电脑"></constructor-arg>
           <constructor-arg name="price" value="5000"></constructor-arg>
       </bean>
       ````

     - 测试

       ````java
       @Test
       public void orderTest(){
           ApplicationContext context = new ClassPathXmlApplicationContext("/applicationContext1.xml");
           Order order = context.getBean("order", Order.class);
           System.out.println(order);
           order.show();
       }
       ````

     ![image-20211207131804227](C:\Users\admin2\AppData\Roaming\Typora\typora-user-images\image-20211207131804227.png)

##### 注入其他类型属性

1. 字面量

   1.1 空值

   ````xml
   <property name="bauthor">
       <null/>
   </property>
   ````

   1.2 属性包含特殊符号<>

   ````xml
   <!-- 1.把<>进行转义 &lt; &gt; -->
   <!-- 2.把带特殊符号的内容写到CDATA -->
   <property name="address">
       <value><![CDATA[<<南京>>]]></value>
   </property>
   ````

2. 外部bean注入

   2.1 创建两个类: service和dao. 在service中定义dao对象作为属性, 定义set方法设置dao对象, 并定义方法调用dao中的方法.

   ````java
   public class UserDaoImpl implements UserDao{
       @Override
       public void update() {
           System.out.println("UserDaoImpl中的update方法run...");
       }
   }
   
   public class UserService {
       private UserDaoImpl userDaoImpl;
   
       public void setUserDaoImpl(UserDaoImpl userDaoImpl) {
           this.userDaoImpl = userDaoImpl;
       }
   
       public void show(){
           System.out.println("UserService中的show方法run...");
           userDaoImpl.update();
       }
   }
   ````

   2.2 在spring配置文件中配置.

   ````xml
   <!--  通过bean标签配置UserDaoImpl对象的创建  -->
   <bean id="userDao" class="com.ht.spring5.dao.UserDaoImpl"></bean>
   <!--  通过bean标签配置UserService对象的创建, 和对象内部UserDaoImpl对象属性的创建. property中, name值与类中定义的属性名一致, ref值与该类的bean id一致  -->
   <bean id="userService" class="com.ht.spring5.service.UserService">
       <property name="userDaoImpl" ref="userDao"></property>
   </bean>
   ````

   2.3 测试.

   ````java
   @Test
   public void userServiceTest() {
       ApplicationContext context = new ClassPathXmlApplicationContext("/applicationContext1.xml");
       UserService userService = context.getBean(UserService.class);
       System.out.println(userService);
       userService.show();
   }
   ````

3. 内部bean和级联赋值(表之间的关系)

   ````xml
   <!-- 内部bean, 创建emp对象和其内部的dept对象 -->
   <bean id="emp" class="com.ht.spring5.bean.Emp">
       <property name="ename" value="mike"></property>
       <property name="gender" value="male"></property>
       <property name="dept">
           <bean id="dept" class="com.ht.spring5.bean.Dept">
               <property name="id" value="1"></property>
               <property name="dname" value="开发部"></property>
           </bean>
       </property>
   </bean>
   ````

4. 级联赋值, 需要在emp中创建dept的getter方法, 否则无法获取dept对象.

   ````xml
   <!-- 外部bean, 级联赋值, 创建emp对象, 引用内部的dept对象 -->
   <bean id="emp" class="com.ht.spring5.bean.Emp">
       <property name="ename" value="lucy"></property>
       <property name="gender" value="female"></property>
       <property name="dept" ref="dept"></property>
       <property name="dept.id" value="2"></property>
       <property name="dept.dname" value="财务部"></property>
   </bean>
   <bean id="dept" class="com.ht.spring5.bean.Dept"></bean>
   ````

5. 集合类型属性注入

   ````xml
   <bean id="stu" class="com.ht.spring5.demo.Stu">
       <!--  数组类型属性注入  -->
       <property name="array">
           <array>
               <value>arr1</value>
               <value>arr2</value>
           </array>
       </property>
       <!--  list类型属性注入  -->
       <property name="list">
           <list>
               <value>11</value>
               <value>22</value>
           </list>
       </property>
       <!--  map类型属性注入  -->
       <property name="map">
           <map>
               <entry key="1" value="map1"></entry>
               <entry key="2" value="map2"></entry>
           </map>
       </property>
       <!--  set类型属性注入  -->
       <property name="set">
           <set>
               <value>10</value>
               <value>20</value>
           </set>
       </property>
   </bean>
   ````

   - 在集合类型的属性中设置对象类型值

     ````xml
     <!--  创建course对象  -->
     <bean id="course1" class="com.ht.spring5.demo.Course">
         <property name="cname" value="数学课"></property>
     </bean>
     
     <bean id="course2" class="com.ht.spring5.demo.Course">
         <property name="cname" value="语文课"></property>
     </bean>
     
     <!-- 在集合类型的属性中引用这两个对象(id) -->
     <bean id="stu" class="com.ht.spring5.demo.Stu">
         <property name="courseList">
             <list>
                 <ref bean="course1"></ref>
                 <ref bean="course2"></ref>
             </list>
         </property>
     </bean>
     ````

   - 提取集合注入部分
     配置 xmlns:util 和 xsi:schemaLocation. 使用 util:list 定义集合, 并注入到类的属性.

     ````xml
     <?xml version="1.0" encoding="UTF-8"?>
     <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:util="http://www.springframework.org/schema/util"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd">
     
         <util:list id="courseList">
             <value>物理课</value>
             <value>化学课</value>
             <value>数学课</value>
         </util:list>
     
         <bean id="course1" class="com.ht.spring5.demo.Course1">
             <property name="list" ref="courseList"></property>
         </bean>
     </beans>
     ````

##### FactoryBean

1. Spring有两种bean, Bean和FactoryBean.

2. Bean: 配置文件中, 定义的bean类型就是返回类型.

3. FactoryBean: 定义的bean类型可以和返回类型不一致.
   3.1 创建类, 使这个类作为工厂bean, 实现FactoryBean接口.
   3.2 重写接口中的方法, 在实现的方法中定义返回的bean类型.

   ````java
   public class Mybean implements FactoryBean<Course> {
       @Override
       public Course getObject() throws Exception {
           Course course = new Course();
           course.setCname("体育课");
           return course;
       }
   }
   
   @Test
   public void factoryTest(){
       ApplicationContext context = new ClassPathXmlApplicationContext("/applicationContext5.xml");
       Course course = context.getBean("myBean", Course.class);
       System.out.println(course);
   }
   ````

   ````xml
   <bean id="myBean" class="com.ht.spring5.factorybean.Mybean"></bean>
   ````

##### Bean的作用域(单例/多例)

1. 在Spring中, 设置创建bean实例为单实例或多实例.
2. 在Spring中, 默认bean使单实例对象, 即只会创建一个对象.
3. 设置方法: 在Spring配置文件bean标签里, 使用属性**scope**. 其中singleton为默认值, 表示单实例对象, prototype表示多实例对象.
4. singleton单实例和prototype多实例的区别: 
   - singleton: 加载Spring配置文件时就创建单实例对象.
   - prototype: 调用getBean方法时才创建多实例对象.

##### Bean生命周期

- 生命周期: 从对象创建到对象销毁的过程.

- Bean生命周期

  1. 通过构造器创建Bean实例(无参构造).

  2. 为Bean的属性设置值, 和对其他Bean的引用(调用set方法).

     - 3.0 把Bean实例传递给Bean后置处理器的方法postProcessBeforeInitialization.

  3. 调用Bean的初始化方法(需要在Bean标签中配置: init-method="类中的初始化方法名").

     - 3.2 把Bean实例传递给Bean后置处理器的方法postProcessAfterInitialization.

  4. 使用Bean(已获取到对象).

  5. 容器关闭时, 手动调用Bean的销毁方法(需要在Bean标签中配置: destroy-method="类中的销毁方法名". 调用context对象的close方法, 则自动调用销毁方法, context对象需要强转为ClassPathXmlApplicationContext类).

     ````xml
     <bean id="book" class="com.ht.spring5.demo.Book" init-method="initMethod" destroy-method="destroyMethod">
         <property name="bname" value="悲惨世界"></property>
         <property name="bauthor" value="雨果"></property>
     </bean>
     <!-- 配置后置处理器, 作用于该xml文件中的所有bean -->
     <bean id="myBeanPost" class="com.ht.spring5.bean.MyBeanPost">
     ````

     ````java
     @Test
     public void lifestyleTest(){
         ApplicationContext context = new ClassPathXmlApplicationContext("/applicationContext6.xml");
         Book book = context.getBean("book", Book.class);
         System.out.println(book);
         ((ClassPathXmlApplicationContext)context).close();
     }
     ````

     ````java
     //后置处理器, 实现BeanPostProcessor接口, 重写2个方法
     public class MyBeanPost implements BeanPostProcessor {
         @Override
         public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
             System.out.println("初始化前的后置处理");
             return BeanPostProcessor.super.postProcessBeforeInitialization(bean, beanName);
         }
     
         @Override
         public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
             System.out.println("初始化后的后置处理");
             return BeanPostProcessor.super.postProcessAfterInitialization(bean, beanName);
         }
     }
     
     ````

##### xml自动装配

自动装配: 根据指定装配规则(属性名称或属性类型), Spring自动注入匹配的属性值.

方法: 使用bean标签的autowire属性. 包含2个值: byName(注入值bean的id 与 在类中作为属性的名称 一致), byType(多个同类型的bean会导致报错, 因为Spring不知道该注入哪一个).

##### xml外部属性装配(以Druid为例)

- 配置德鲁伊连接池.

  1. 导入jar包

     ````xml
     <dependency>
         <groupId>com.alibaba</groupId>
         <artifactId>druid</artifactId>
         <version>1.1.10</version>
     </dependency>
     <dependency>
         <groupId>mysql</groupId>
         <artifactId>mysql-connector-java</artifactId>
         <version>8.0.27</version>
     </dependency>
     ````

  2. 方式一: 直接配置连接池.

     ````xml
     <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
         <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"></property>
         <property name="url" value="jdbc:mysql://localhost:3306/db1"></property>
         <property name="username" value="root"></property>
         <property name="password" value="1234"></property>
     </bean>
     ````

  3. 方式二: 引入外部属性文件配置连接池.

     ````properties
     #创建外部properties文件, 写数据库信息
     prop.driverClass=com.mysql.cj.jdbc.Driver
     prop.url=jdbc:mysql://localhost:3306/db1
     prop.username=root
     prop.password=1234
     ````

     ````xml
     <!-- 把属性文件引入Spring配置文件中 -->
     <!-- 1.引入名称空间 xmlns:context 和 xsi:schemaLocation -->
     <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xmlns:context="http://www.springframework.org/schema/context"
            xsi:schemaLocation="http://www.springframework.org/schema/beans
            http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context
            http://www.springframework.org/schema/context/spring-context.xsd">
     <!-- 2.引入外部属性文件 -->
         <context:property-placeholder location="jdbc.properties"/>
     <!-- 3.配置连接池 -->
         <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
             <property name="driverClassName" value="${prop.driverClass}"></property>
             <property name="url" value="${prop.url}"></property>
             <property name="username" value="${prop.username}"></property>
             <property name="password" value="${prop.password}"></property>
         </bean>
     </beans>
     ````

     

#### 基于注解方式的Bean管理

##### 什么是注解

- 注解是代码的特殊标记, 格式: 注解名称(属性名=属性值, 属性名=属性值, ...).
- 作用位置: 类, 属性, 方法上方.
- 使用目的: 简化xml配置.

##### Spring针对Bean管理中对象的创建提供4种注解(均用来创建bean实例)

1. @Component: 普通类
2. @Service: Service层
3. @Controller: Web层
4. @Repository: Dao层

##### 创建对象

1. 引入jar包.

   ````xml
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-aop</artifactId>
       <version>5.3.13</version>
   </dependency>
   ````

2. 开启组件扫描

   ````xml
   <!-- 引入xmlns:context名称空间, 开启组件扫描 -->
   
   <!-- 方式1: 扫描多个包(默认过滤器, 扫描包中的全部内容):1.完整包名+用逗号隔开 2.扫描包上层目录 -->
   <context:component-scan base-package="com.ht.spring5"></context:component-scan>
   
   <!-- 方式2: 自定义过滤器, 扫描指定内容, 此处为只扫描@Component -->
   <context:component-scan base-package="com.ht.spring5.service" use-default-filters="false">
       <context:include-filter type="annotation" expression="org.springframework.stereotype.Component"/>
   </context:component-scan>
   
   <!-- 方式3: 自定义过滤器, 扫描除指定内容外的其他所有内容 -->
   <context:component-scan base-package="com.ht.spring5.service">
       <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Service"/>
   </context:component-scan>
   ````

3. 创建类

   ````java
   //在注解中value属性值可以省略, 默认值为类名, 首字母小写
   @Service(value = "stuService") //等同于<bean id="stuService" class="..."/>
   public class StuService {
   }
   ````

##### 注入属性

- @Autowired: 根据**对象属性类型**自动装配.

  1. 创建service和dao对象, 并在类上方添加创建对象注解(@Service和@Repository).

  2. 在service注入dao对象: 在service类添加dao类型属性, 并在属性上方使用注解(@Autowired).

     ````java
     //在注解中value属性值可以省略, 默认值为类名, 首字母小写
     @Service //等同于<bean id="stuService" class="..."/>
     public class StuService {
         @Autowired //使用注解 注入属性 不需要set方法 注解已封装
         private StuDao stuDao;
         public void test(){
             System.out.println("stuService tests...");
             stuDao.study();
         }
     }
     ````

- @Qualifier: 根据**对象属性名称**注入. **必须和@Autowired一起使用.**

  ````java
  @Service //等同于<bean id="stuService" class="..."/>
  public class StuService {
      //使用注解 注入属性 不需要set方法 注解已封装
      @Autowired //根据类型注入属性
      @Qualifier("stuDaoImpl2") //根据名称注入属性, 当StuDao接口有多个实现类时, 需要明确使用哪个实现类
      private StuDao stuDao;
  }
  ````

- @Resource: 根据**对象属性的类型或名称**注入. 
  注意: 该注解功能位于javax.annotation, 是java自带的扩展包, Spring官方更建议用Autowired和Qualifier.

  ````java
  @Resource(name = "stuDaoImpl2") //或 @Resource(实现类不唯一时报错)
  private StuDao stuDao;
  ````

- @Value: 注入**普通类型属性**.

  ````java
  @Value(value = "abc")
  private String sname;
  ````

##### 完全注解开发

- 使用配置类替代xml配置文件

  ````java
  @Configuration //声明配置类
  @ComponentScan(basePackages = {"com.ht.spring5"}) //设置组件扫描
  public class SpringConfig {
  }
  ````

- 测试类中加载配置类

  ````java
  ApplicationContext context = new AnnotationConfigApplicationContext(SpringConfig.class);
  ````

----

## AOP

### 概念

面向切面编程. 利用AOP对业务逻辑的各个部分进行隔离, 使得业务逻辑各部分之间的耦合度降低, 提高程序的可重用性和开发效率. 通俗讲就是**不通过修改源代码的方式, 在主干功能里添加新功能.** 下面是一个登录功能的例子.

![image-20211208164220891](C:\Users\admin2\AppData\Roaming\Typora\typora-user-images\image-20211208164220891.png)

### 术语

- 连接点: 可以被增强的方法.
- 切入点: 实际被增强的方法.
- 通知(增强): 实际增强的逻辑部分, 包括前置通知, 后置通知, 环绕通知, 异常通知, 最终通知.
- 切面(一个动作): 把通知应用到切入点的过程.

### 底层原理

#### 使用动态代理的2种情况

1. 有接口, 使用JDK动态代理.
   创建接口实现类代理对象, 增强类的方法.
2. 没有接口, 使用CGLIB动态代理.
   创建子类代理对象, 增强类的方法.

#### JDK动态代理

1. 创建接口和实现类(本体). 如speak和Zhangsan

2. 创建代理类, InvocationHandler接口的实现类, 定义对象属性, 重写invoke方法.

   ````java
   public class LawyerProxy implements InvocationHandler {
       //被代理的对象
       private Object object = new Object();
       public LawyerProxy(Object object){
           this.object = object;
       }
       /**
        * 对象一被创建, invoke方法就会被调用
        * @param proxy
        * @param method 需要被调用的被代理对象的方法
        * @param args method所需的参数列表
        * @return
        * @throws Throwable
        */
       @Override
       public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
           if (method.getName().equals("speak")){
               System.out.println("引用法律条文");
               method.invoke(object,args);
               System.out.println("道德层面谴责");
           }
           return null;
       }
   }
   ````

3. 测试: 调用Proxy中的静态方法newProxyInstance. 
   接收三个参数:

   - 类加载器. 如: 类名.class.getClassLoader()
   - 接口数组, 即本体实现的接口(本体: 被增强方法所在的类). 如: new Class[]{Speaker.class}
   - 代理对象, 即实现InvocationHandler接口的类的对象, 用于写增强的方法. 如: lawyerProxy

   ````java
   LawyerProxy lawyerProxy = new LawyerProxy(new Lisi());
   Speaker speaker1 = (Speaker) Proxy.newProxyInstance(Court.class.getClassLoader(), new Class[]{Speaker.class}, lawyerProxy);
   speaker1.speak("3次了");
   ````

   

### AOP操作

Spring框架一般基于AspectJ实现AOP操作. AspectJ不是Spring组成部分, 但一般AspectJ和Spring一起进行AOP操作.

2种基于AspectJ实现AOP操作: 1. 基于注解方式(常用) 2. 基于xml配置文件方式.

#### 准备阶段

##### 所需依赖

````xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aop</artifactId>
    <version>5.3.13</version>
</dependency>
<dependency>
    <groupId>cglib</groupId>
    <artifactId>cglib</artifactId>
    <version>3.3.0</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.springframework/spring-aspects -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aspects</artifactId>
    <version>5.3.13</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.7</version>
    <scope>runtime</scope>
</dependency>
<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjrt -->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjrt</artifactId>
    <version>1.9.7</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.springframework/spring-test -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>5.3.13</version>
    <scope>test</scope>
</dependency>
````

##### 切入点表达式

- 目的: 明确增强的对象(xx类中的xx方法)

- 语法结构: 
  execute( [权限修饰符 可以用*代表任意修饰符] [返回类型 可省略] [类全路径] [方法名称] ([方法的参数列表 可用..替代整个参数列表]) )
- 三种常用表达(星号 = *):
  1. 增强指定类的指定方法:
     execution(* com.ht.spring5.aop.annotation.User.add(..))
  2. 增强指定类的所有方法:
     execution(* com.ht.spring5.aop.annotation.User.星号(..))
  3. 增强指定包中所有类的所有方法:
     execution(* com.ht.spring5.aop.annotation.星号.星号(..))

#### 操作方式一: 基于AspectJ注解(常用)

1. 创建被增强类(本体), 定义方法.

   ````java
   //被增强类
   @Component
   public class User {
       public void add() {
           System.out.println("被增强方法");
       }
   }
   ````

   

2. 创建增强类, 编写增强逻辑. 创建不同方法代表不同通知类型.

   ````java
   //增强类
   @Component
   @Aspect  //生成代理对象
   public class UserProxy {
       //写增强逻辑
   }
   ````

   

3. 配置通知.

   - 在Spring配置文件中开启注解扫描(引入"context"名称空间).

   - 使用注解创建被增强类和增强类的对象.

   - 在增强类上添加注解@Aspect, 表示生成代理对象.

   - 在Spring配置文件中开启aspectj生成代理对象(需要引入"aop"名称空间).

     ````xml
     <?xml version="1.0" encoding="UTF-8"?>
     <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xmlns:context="http://www.springframework.org/schema/context"
            xmlns:aop="http://www.springframework.org/schema/aop"
            xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
     http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
     http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
         <!--  开启注解扫描  -->
         <context:component-scan base-package="com.ht.spring5.aop.annotation"></context:component-scan>
         <!--  开启aspectj生成代理对象  -->
         <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
     </beans>
     ````

     

4. 在增强类中, 在通知方法上方添加通知类型注解, 并使用切入点表达式配置.

   ````java
   //增强类
   @Component
   @Aspect  //生成代理对象
   public class UserProxy {
       //增强逻辑1: 前置通知
       @Before("execution(* com.ht.spring5.aop.annotation.User.add(..))")
       public void before() {
           System.out.println("前置通知");
       }
   
       //增强逻辑2: 后置通知/返回通知
       @AfterReturning("execution(* com.ht.spring5.aop.annotation.User.add(..))")
       public void afterReturning() {
           System.out.println("后置通知");
       }
   
       //增强逻辑3: 环绕通知
       @Around("execution(* com.ht.spring5.aop.annotation.User.add(..))")
       public void around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
           System.out.println("环绕前通知");
           proceedingJoinPoint.proceed();
           System.out.println("环绕后通知");
       }
   
       //增强逻辑4: 异常通知
       @AfterThrowing("execution(* com.ht.spring5.aop.annotation.User.add(..))")
       public void afterThrowing() {
           System.out.println("异常通知");
       }
   
       //增强逻辑5: 最终通知
       @After("execution(* com.ht.spring5.aop.annotation.User.add(..))")
       public void after() {
           System.out.println("最终通知");
       }
   }
   ````

   返回结果为:

   ![image-20211210142613332](C:\Users\admin2\AppData\Roaming\Typora\typora-user-images\image-20211210142613332.png)

5. 抽取相同切入点(表达式), 使用注解@Pointcut, 方法名能够被当初切入点表达式使用.

   ````java
   //抽取相同切入点
   @Pointcut("execution(* com.ht.spring5.aop.annotation.User.add(..))")
   public void pointcutDemo(){}
   
   //增强逻辑1: 前置通知
   @Before("pointcutDemo()")
   public void before() {
       System.out.println("前置通知");
   }
   ````

6. 设置增强类对象优先级(当多个类对同一个方法进行增强时)
   在增强类上方添加注解@Order(数字类型值), 数值从0开始, 数值越小优先级越高.

   ````java
   //增强类
   @Component //创建对象
   @Aspect  //生成代理对象
   @Order(1) //设置优先级
   public class UserProxy {...}
   ````

   

#### 操作方式二: 基于AspectJ配置文件

1. 创建增强类和被增强类, 并定义方法.

2. 在Spring配置文件中创建两个类的对象, 配置切入点.

   ````xml
   <!--  创建对象  -->
   <bean id="book" class="com.ht.spring5.aop.xmlconfig.Book"></bean>
   <bean id="bookProxy" class="com.ht.spring5.aop.xmlconfig.BookProxy"></bean>
   <!--  配置aop增强  -->
   <aop:config>
       <!--  配置切入点  -->
       <aop:pointcut id="pointcutDemo" expression="execution(* com.ht.spring5.aop.xmlconfig.Book.buy(..))"/>
       <!--  配置切面  -->
       <aop:aspect ref="bookProxy">
           <!--  增强作用到具体方法  -->
           <aop:before method="before" pointcut-ref="pointcutDemo"/>
       </aop:aspect>
   </aop:config>
   ````




#### 操作方式三: 完全注解开发

创建配置类, 不需要创建xml配置文件.

````java
@Configuration
@ComponentScan(basePackages = {"com.ht.spring5.aop.annotation"})
@EnableAspectJAutoProxy(proxyTargetClass = true)
public class ConfigAop {
}
````



----

## JdbcTemplate

Spring框架对JDBC进行封装, 使用JdbcTemplate实现对数据库的操作.

### 所需依赖

````xml
<!-- 操作数据库 -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.10</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.27</version>
</dependency>
<!-- 对jdbc的封装https://mvnrepository.com/artifact/org.springframework/spring-jdbc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.3.13</version>
</dependency>
<!-- 针对事务操作https://mvnrepository.com/artifact/org.springframework/spring-tx -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-tx</artifactId>
    <version>5.3.13</version>
</dependency>
<!-- 用于整合其他框架https://mvnrepository.com/artifact/org.springframework/spring-orm -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-orm</artifactId>
    <version>5.3.13</version>
</dependency>
````

### 准备工作

1. 在Spring配置文件中配置数据库连接池.

   ````xml
   <!--  创建JdbcTemplate对象, 注入数据源 dataSource  -->
   <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
       <property name="dataSource" ref="dataSource"></property>
   </bean>
   ````

2. 配置JdbcTemplate对象, 注入DataSource.

   ````xml
   <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
       <!--这里使用无参构造+JdbcTemplate的父类JdcbAccessor的set方法注入数据源,数据源是JdcbAccessor的私有属性-->
       <property name="dataSource" ref="dataSource"></property>
   </bean>
   ````

3. 配置开启注解扫描.

   ````xml
   <!--  开启注解扫描  -->
   <context:component-scan base-package="com.ht"></context:component-scan>
   ````

4. 创建dao类和service类, 在dao类注入jdbcTemplate对象, 在service类注入dao对象.

   ````java
   @Repository
   public class BookDaoImpl implements BookDao{
       //注入jdbcTemplate对象, 已在配置文件中创建
       @Autowired
       private JdbcTemplate jdbcTemplate;
   }
   
   @Service
   public class BookService {
       //注入dao
       @Autowired
       private BookDao bookDao;
   }
   ````

### 操作数据库

#### 添删改

均由jdbcTemplate.update(sql, args)实现.

1. 对应数据库创建实体类.

2. 编写dao和service. 

   - 在dao添加增删改方法, 在service调用dao的增删改方法. 
   - 在dao实现类中, 调用JdbcTemplate对象的update方法完成操作.
     update方法能够完成增删改操作, 有两个参数: sql语句和sql语句中使用的参数数组(可变参数).

   ````java
   @Repository
   public class BookDaoImpl implements BookDao{
       //注入jdbcTemplate对象, 已在配置文件中创建
       @Autowired
       private JdbcTemplate jdbcTemplate;
   
       @Override
       public void add(Book book) {
           String sql = "insert into tb_book values(?,?,?);";
           Object[] args = {book.getBookId(),book.getBookName(),book.getBookStatus()};
           int flag = jdbcTemplate.update(sql, args);
           System.out.println(flag);
       }
   }
   ````

3. 测试

   ````java
   @Test
   public void testJdbcTemplate(){
       ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
       BookService bookService = context.getBean("bookService", BookService.class);
       Book book = new Book(1,"一本书","在馆");
       bookService.addBook(book);
   }
   ````

#### 查询返回某个值

在dao实现类中, 由jdbcTemplate调用queryForObject方法实现. 该方法有2个参数: 

1. sql语句.
2. 需要返回的类型. 

以查询表中的总条目为例.

````java
@Repository
public class BookDaoImpl implements BookDao {
    //注入jdbcTemplate对象, 已在配置文件中创建
    @Autowired
    private JdbcTemplate jdbcTemplate;
    //重写dao接口的方法.
    @Override
    public Integer selectCount() {
    String sql = "select count(*) from tb_book;";
    Integer count = jdbcTemplate.queryForObject(sql,Integer.class);
    return count;
    }
}
````



#### 查询返回对象

在dao实现类中, 由jdbcTemplate调用queryForObject方法实现. 该方法有3个参数: 

1. sql语句.
2. 接口RowMapper的实现类实例, 该接口的实现类能够封装对象. 如BeanPropertyRowMapper, 使用格式: BeanPropertyRowMapper<返回对象的名称> (返回对象的类).
3. sql语句中使用的参数数组.

以查询表中的一条数据为例.

````java
@Override
public Book selectOne(Integer id) {
    String sql = "select * from tb_book where book_id = ?;";
    Book book = jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<Book>(Book.class), id);
    return book;
}
````



#### 查询返回集合

在dao实现类中, 由jdbcTemplate调用query方法实现. 该方法有2个参数: 

1. sql语句.
2. 接口RowMapper的实现类实例, 该接口的实现类能够封装对象. 如BeanPropertyRowMapper, 使用格式: BeanPropertyRowMapper<返回对象的名称> (返回对象的类).

以查询表中的所有数据为例.

````java
@Override
public List<Book> selectAll() {
    String sql = "select * from tb_book;";
    List<Book> books = jdbcTemplate.query(sql, new BeanPropertyRowMapper<Book>(Book.class));
    return books;
}
````



#### 批量增删改操作

操作表中多条记录. 在dao实现类中, 由jdbcTemplate调用batchUpdate方法实现.
该方法有2个参数: 

1. sql语句.
2. 装有Object数组的List集合, 每条数据都被封装到一个Object数组, 通过集合对多条数据进行操作.

````java
//添加多条数据
@Repository
public class BookDaoImpl implements BookDao {
    //注入jdbcTemplate对象, 已在配置文件中创建
    @Autowired
    private JdbcTemplate jdbcTemplate;
    @Override
    public int[] addBatch(List<Object[]> batchArgs) {
        String sql = "insert into tb_book values(?,?,?);";
        int[] arr = jdbcTemplate.batchUpdate(sql,batchArgs);
        return arr;
    }
}

public class TestJdbc01 {
    @Test
    public void testJdbcTemplate7() {
        ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
        BookService bookService = context.getBean("bookService", BookService.class);
        Object[] book1 = {"9", "水浒传", "借出"};
        Object[] book2 = {"10", "西游记", "借出"};
        Object[] book3 = {"11", "聊斋志异", "借出"};
        List<Object[]> args = new ArrayList<>();
        args.add(book1);
        args.add(book2);
        args.add(book3);
        int[] arr = bookService.addBatch(args);
        System.out.println(Arrays.toString(arr));
    }
}
````



----

## 事务管理

事务是数据库操作的最基本单元. 逻辑上, 一组操作要么成功, 如果有一个步骤失败, 则整组操作都失败. 
事务的四个特性(ACID): 原子性, 一致性, 隔离性, 持久性.

事务添加到JavaEE三次结构中的Service层(业务逻辑层).
事务管理操作的2种方式: 声明式事务管理(常用), 编程式事务管理(冗余, 不便于修改).

### 声明式事务管理(AOP原理)

2种方式: 基于注解(常用), 基于xml配置文件.

#### Spring事务管理API简介

提供PlatformTransactionManager接口(事务管理器), 该接口针对不同框架提供不同的实现类.

![image-20211211203840023](C:\Users\admin2\AppData\Roaming\Typora\typora-user-images\image-20211211203840023.png)

#### 注解声明式事务管理

##### 基本步骤

1. 在Spring配置文件中创建事务管理器, 注入数据源.

   ````xml
   <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
       <property name="dataSource" ref="dataSource"></property>
   </bean>
   ````

2. 开启事务注解.

   ````xml
   <!-- 2.1 Spring配置文件中引入名称空间tx -->
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xmlns:aop="http://www.springframework.org/schema/aop"
          xmlns:tx="http://www.springframework.org/schema/tx"
          xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd 
          http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd 
          http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd 
          http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">
       
   <!-- 2.2 开启事务注解, 配置事务管理器 -->
   <tx:annotation-driven transaction-manager="transactionManager"></tx:annotation-driven>
   ````

3. 添加事务注解@Transactional.

   - 为Service类中的1个方法添加事务: 在该方法上方添加注解.
   - 为Service类中的所有方法添加事务: 在Service类上方添加注解.

   ````java
   @Service
   @Transactional
   public class UserService {}
   ````

##### 参数配置

在Service类上方添加的@Transactional注解中, 可以配置事务相关参数:

1. propagation: 

   [事务传播行为]: https://blog.csdn.net/soonfly/article/details/70305683

   多事务方法被直接调用过程中, 事务的管理方式. 事务方法: 使数据库中的数据发生变化的操作(增删改).
   ![这里写图片描述](https://img-blog.csdn.net/20170420212829825?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc29vbmZseQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

2. isolation: 事务隔离级别. 
   

3. timeout: 超过时间. 

4. readOnly: 是否只读. 

5. rollbackFor: 回滚. 

6. noRollbackFor: 不回滚. 

#### xml配置文件声明式事务管理

----

## Spring5新特性



----

## 依赖汇总

````xml
<dependencies>
    <!-- https://mvnrepository.com/artifact/junit/junit -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>
    
    <!-- https://mvnrepository.com/artifact/org.springframework/spring-beans -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-beans</artifactId>
        <version>5.3.13</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.3.13</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/org.springframework/spring-core -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>5.3.13</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/org.springframework/spring-expression -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-expression</artifactId>
        <version>5.3.13</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/commons-logging/commons-logging -->
    <dependency>
        <groupId>commons-logging</groupId>
        <artifactId>commons-logging</artifactId>
        <version>1.2</version>
    </dependency>

    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aop</artifactId>
        <version>5.3.13</version>
    </dependency>
    <dependency>
        <groupId>cglib</groupId>
        <artifactId>cglib</artifactId>
        <version>3.3.0</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/org.springframework/spring-aspects -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aspects</artifactId>
        <version>5.3.13</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
    <dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>1.9.7</version>
        <scope>runtime</scope>
    </dependency>
    <!-- https://mvnrepository.com/artifact/org.aspectj/aspectjrt -->
    <dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjrt</artifactId>
        <version>1.9.7</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/org.springframework/spring-test -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-test</artifactId>
        <version>5.3.13</version>
        <scope>test</scope>
    </dependency>

    <!-- 操作数据库 -->
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid</artifactId>
        <version>1.1.10</version>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.27</version>
    </dependency>
    <!-- 对jdbc的封装https://mvnrepository.com/artifact/org.springframework/spring-jdbc -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-jdbc</artifactId>
        <version>5.3.13</version>
    </dependency>
    <!-- 针对事务操作https://mvnrepository.com/artifact/org.springframework/spring-tx -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-tx</artifactId>
        <version>5.3.13</version>
    </dependency>
    <!-- 用于整合其他框架https://mvnrepository.com/artifact/org.springframework/spring-orm -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-orm</artifactId>
        <version>5.3.13</version>
    </dependency>

</dependencies>
````



