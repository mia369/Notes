

---

# static关键字(p121开始)

- 特点
  1. 是一个修饰符, 用于修饰成员.
  2. 修饰的成员被所有对象所共享.
  3. 优先于对象存在, 因为static的成员在类的加载时就已经存在了.
  4. 修饰的成员多了一种调用方式, 即直接被类名所调用: 类名.静态成员.
  5. 修饰的数据是共享数据, 对象中存储的是特有数据.

- 成员变量和静态变量的区别

  |              | 成员变量                                     | 静态变量                                               |
  | ------------ | -------------------------------------------- | ------------------------------------------------------ |
  | 生命周期     | 随对象的创建而存在<br />随对象的被回收而释放 | 随类的加载而存在<br />随类的消失而消失                 |
  | 调用方式     | 只能被对象调用                               | 可以被对象调用, 也可以被类名调用                       |
  | 别名         | 实例变量                                     | 类变量                                                 |
  | 数据存储位置 | 在堆内存的对象中<br />所以也叫对象的特有数据 | 方法区(共享数据区)的静态区<br />所以也叫对象的共享数据 |

- 静态使用的注意事项
  1. 访问局限性: 静态方法只能访问静态成员(成员变量和成员函数). (非静态既可以访问静态, 也可以访问非静态).
  2. 静态方法中不可以使用this或super关键字.
  3. 主方法是静态的, 所以主方法只能调用静态方法(参照第一点).

- 主方法特殊之处

  1. 格式固定.

  2. 被JVM识别和调用.

  3. 解读: 

     | 关键字 | 意义                                        |
     | ------ | ------------------------------------------- |
     | public | 权限必须最大                                |
     | static | 不需要对象, JVM直接用主函数所属类名调用即可 |
     | void   | 没有具体返回值                              |

  - main: 函数名, 不是关键字, 只是一个JVM识别的固定的名字.

  - String[] args: 主函数的参数列表, 是一个数组类型的参数, 元素都是字符串类型.

- 静态什么时候用

  1. 静态变量
     - 当分析对象中所具备的成员变量的值都是相同的, 这时这个成员就可以被静态修饰.
     - 只要数据在对象中都是不同的, 就是对象的特有数据, 必须存储在对象中, 是非静态的.
     - 如果是相同的数据, 对象不需要做修改, 只需要使用即可, 不需要存储在对象中, 定义成静态.

  2. 静态函数

     - 内存角度: 函数是否用静态修饰, 就参考一点, 就是该函数功能是否有访问到对象中的特有数据. 如果访问到了, 就必须为非静态, 如果没访问到, 就可以是静态的.

     - 源代码角度: 该功能是否需要访问非静态的成员变量, 如果需要, 该功能就是非静态的, 如果不需要, 就可以将该功能定义成静态的.
     - 如果不需要访问非静态成员, 就可以将该功能定义成静态的. 当然, 也可以定义成非静态的, 但非静态需要被对象调用, 而仅创建对象调用非静态的没有访问特有数据的方法, 则该对象的创建是没有意义的.

---

# 代码块

- 静态代码块

  1. 随着类的加载而执行.
  2. 作用: 用于给类进行初始化, 不需要用类创建新对象, 只需要该类中的功能.
  3. 一般来说, 这种类中全是静态成员.

  ````java
      static {
          System.out.println("hahahaha");
      }
  ````

- 构造代码块: 可以给**所有对象**进行初始化的.

  ````java
      {
          System.out.println("people run");
      }
  ````

- 构造函数: 是给对应的对象进行**针对性的初始化.**

  ````java
      People(){
          name = "baby";
          cry();
      }
  ````

- 执行顺序: 静态代码块 -> (如果有对象) 构造代码块 -> 构造函数

---

# 文档注释

- 只有public类才能被文档化.
- 只有public和protected方法的注释才能被提取, private不会被提取, 可以只写注释.
- DOS命令: javadoc -d myhelp -author -version 类名.java

---

# 设计模式

对问题行之有效的解决方式, 其实是一种思想. 

1. 单例设计模式

   - 解决的问题: 保证一个类在内存中的**对象唯一性**.
   
   - 使用场景: 必须对于多个程序使用同一个配置信息对象时, 就需要保证该对象的唯一性.
   
   - 如何保证对象的唯一性?
     1. 不允许其他程序用new创建该类对象, 从而避免产生过多对象.
     2. 在该类中创建一个本类实例.
     3. 对外提供一个方法, 让其他程序可以获取该对象.

   - 步骤
   
     1. 私有化该类的构造函数.
     2. 通过new在本类中创建一个本类对象.
     3. 定义一个公有的方法, 将创建的对象返回.
   
     ````java
     //饿汉式, 一般开发时用的多
     class Single {//类一加载进来, 对象就已经存在
         //2.Single s通过new在本类中创建一个本类对象.
         //s是成员变量, getInstance是成员方法, 都为静态, 都可以被类名调用
         private static final Single s = new Single();
     
         //1.私有化该类的构造函数.
         private Single() {
     
         }
     
         //3.定义一个公有的方法, 将创建的对象返回.
         //可以加控制条件
         public static Single getInstance() {
             return s;
         }
     }
     
     //一般面试的时候用懒汉式
     //懒汉式//延迟加载形式
     class Single2 {//类加载进来时没有对象, 只有调用了getInstance方法时, 才会创建对象
         private static Single2 s = null;
     
         private Single2() {
     
         }
     
         public static Single2 getInstance() {
             if (s == null) {
                 s = new Single2();
             }
             return s;
         }
     }
     ````

     ![image-20210928212109513](C:\Users\admin2\AppData\Roaming\Typora\typora-user-images\image-20210928212109513.png)
   
   - 单例设计模式涉及多线程:
   
     1. 饿汉式无线程安全问题, 因为操作同一个共享数据的线程代码只有一条: return s;
   
     ````java
     class Single {
         private static final Single s = new Single();
         private Single () {}
         public static Single getInstance () {
             return s;
         }
     }
     ````
   
     2. 懒汉式有线程安全问题.
   
        ````java
        class Single {
            private static Single s = null;
            private Single () {}
            public static Single getInstance () {
                if (s == null) { //问题所在位置
                    s = new Single();
                }
                return s;
            }
        }
        ````
   
        - 解决方法一: 
   
          把getInstance方法定义为synchronized (注意getInstance方法为静态, 锁为该函数所属的字节码文件对象). 解决了线程安全问题, 但降低了运行效率, 因为明明只有一个对象, 然而每个线程都必须进入方法进行判断后, 才能获得对象地址.
   
        - 解决方法二: 
          用同步代码块解决线程安全问题 (注意getInstance方法为静态, 锁为该函数所属的字节码文件对象), 再用 if 判断解决效率问题.
   
          ````java
          class Single {
              private static Single s = null;
              private Single () {}
              public static Single getInstance () {
                  if (s == null) { //解决效率问题
                      synchronized (Single.class) { //解决线程安全问题
                      	if (s == null) { 
                      		s = new Single();
                 			}
                  	}
                  }
                  return s;
              }
          }
          ````
   
          

---

# 继承

- 继承的好处:
  1. 提高了代码的复用性.
  2. 让类与类之间产生了关系, 给多态提供了前提.

- 继承的弊端:

  1. 打破了封装性. 解决方法: final关键字 

- 单继承, 多继承, 多层继承:

  java中支持单继承和多层继承. 不直接支持多继承, 但对C++中的多继承机制进行改良.

  1. 单继承: 一个子类只能有一个直接父类.

  2. 多继承: 一个子类可以有多个直接父类(java中不允许, 进行改良), 不直接支持, 因为多个父类中的相同成员会产生调用的不确定性. 多继承在java中是通过"多实现"的方式来体现的.
  3. 多层继承(多重继承): A继承B, B继承C. 
     就会形成继承体系, 当要使用一个继承体系时, 
     - 查看该体系中的顶层类(基类/父类), 了解该体系的基本功能.
     - 创建体系中的最子类对象, 完成功能的使用.

- 什么时候定义继承?
  当类与类之间存在所属关系的时候, 就定义继承. xxx是yyy中的一种, xxx extends yyy.

- 在子父类中, 成员的特点体现:

  1. 成员变量

     - 同名变量的调用遵守的原则: 子类有就不找父类(同理, 局部有就不找成员). 原因是: 对内存空间而言, 本空间有就不到外面去找.
     - 当本类的成员和局部变量重名, 用this区分; 
       - 当子父类中的成员变量同名, 用super区分. 
       - this和super的区别: this代表一个本类对象的引用, super代表一个父类空间.
         ![image-20211019165010857](C:\Users\admin2\AppData\Roaming\Typora\typora-user-images\image-20211019165010857.png)

     - 子类中不具备父类的私有内容.

  2. 成员函数 
     - 重写/覆盖操作: 当子父类中出现成员函数**一模一样**的情况, 会运行子类的函数. 这是函数在子父类中的特性. 
     - 函数的两个特性:
       - 重载(overload): 同一个类中. 形参必须不一样, 与返回值类型无关.
       - 重写/覆盖(override): 子类中. 返回值类型/函数名/参数列表必须一样.
     - 重写的注意事项:
       - 子类方法覆盖父类方法时, 子类权限必须大于等于父类权限.
       - 静态只能覆盖静态, 或被静态覆盖.
     - 什么时候使用重写?
       - 当对一个类进行子类的拓展时, 子类需要保留父类的功能声明, 但是要定义子类中该功能的特有内容, 就使用重写操作完成.
  3. 构造函数
     - super():
       - 在子类构造对象时, 发现访问子类构造函数时, 父类也运行了. 
       - 原因是: 在子类的构造函数中第一行有一个默认的隐式语句--super().
         - this() 是指调用本类的构造函数.
         - super() 是指调用父类中的**空参**构造函数.
       - 如果父类中没有空参构造函数, 则必须在子类中用super(...)调用父类中的有参构造函数.
     - 子类的实例化过程
       - 子类中所有的构造函数默认都会访问父类中的空参数的构造函数.
       
       - 为什么子类实例化过程中要访问父类的构造函数?
         - 因为子类继承了父类, 获取了父类中的内容(属性), 所以在使用父类内容之前, 要先看父类是如何对自己的内容进行初始化的.
         - 所以子类在构造对象时, **必须**访问父类中的构造函数. 
           为了完成这个**必须**的动作, 就在子类的构造函数中加入super()语句. 如果父类中没有定义空参数构造函数, 那么子类的构造函数必须用super明确调用父类中的哪个构造函数. 同时子类构造函数中如果使用this()调用了本类构造函数时, super()就没有了, 因为super()和this()都只能定义在第一行, 所以只能有一个. 但是可以保证的是, 子类中肯定会有其他的构造函数访问父类的构造函数. 
         - 注意: super语句必须要定义在子类构造函数的第一行, 因为父类的初始化动作要先完成.
         - 通过super初始化父类内容时, 子类的成员变量并未显示初始化, 只有默认值. 等super初始化完毕后才进行子类的成员变量显示初始化
         
       - 一个对象实例化过程 Person p = new Person()
       
         1. JVM会读取指定路径下的Person.class文件, 并加载进内存, 并会先加载Person的父类(如果有直接父类的情况下).
         2. 在堆内存中开辟空间, 分配内存地址.
         3. 并在对象空间中, 对对象中的属性进行默认初始化.
         4. 调用对应的构造函数进行初始化.
         5. 在构造函数中, 第一行会先调用父类中的构造函数, 进行父类的初始化. //分水岭
         6. 父类的初始化完毕后, 再对子类的属性进行显示初始化.
         7. 再进行子类构造函数的特定初始化.
         8. 初始化完毕后, 将地址值赋值给引用变量(p)
       
         ![image-20211020231544505](C:\Users\admin2\AppData\Roaming\Typora\typora-user-images\image-20211020231544505.png)

---

# final关键字

- final可以修饰类: final修饰的类不可以被继承.

- final可以修饰方法: final修饰的方法不可以被重写(子类重写父类中的方法).

- final可以修饰变量: final修饰的变量是一个常量, 只能赋值一次.

  - 作用: 给常量取名, 增加可读性, 来说明这个数据代表什么.

  - 为什么要用final修饰变量: 
    程序中, 如果一个数据是固定的, 那么直接使用这个数据就可以了. 但是这样阅读性差, 所以给该数据起个名称. 而且这个变量名称的值不能变化, 所以加上final固定.

  - 命名规则: 全大写, 单词间用下划线隔开, 如PI, MY_PI.

  - final固定的是显示初始化值, 不固定默认初始化值.

    ````java
    final int x; // 报错
    static final int X = 7; // X的值固定不变, 所以一般用static修饰
    
    // public 全
    // static 不需要对象, 类名能直接访问
    // final 常量
    // 整个叫全局常量
    public static final double MY_PI = 3.14;
    // 全局变量
    public static double MY_PI = 3.14;
    ````

  ---

  ## 抽象类

  ````java
  abstract class 犬科{
      abstract void 吼叫();
  }
  ````

  

  - 特点:

    1. 方法只有声明而没有实现时, 该方法就是抽象方法, 需要被abstract修饰. 抽象方法必须定义在抽象类中, 该类必须也被abstract修饰.
    2. 抽象类不可以被实例化(不可以用new创建对象), 因为调用抽象方法没有意义--抽象方法没有方法体, 无法运行.
    3. 抽象类必须由其子类重写了所有的抽象方法后, 该子类才可以实例化. 否则, 这个子类还是抽象类.

  - 细节

    1. 抽象类中有构造函数吗?
       有, 用于给子类对象进行初始化.

    2. 抽象类可以不定义抽象方法吗?
       可以的. 目的是不让该类创建对象. AWT的适配器对象就是这种类. 通常这个类中的方法有方法体, 但是没有内容.

       ````java
       abstract class Demo{
           void show1(){}
           void show2(){}
       }
       ````

       
       
    3. abstract关键字不可以和哪些关键字共存?(非法组合)
    
       - private. abstract需要被重写, 但被private隐藏了无法重写.
       - static. 静态成员不需要对象, 可以直接用类名调用, 但是调用抽象方法是没有意义的(没有方法体).
       - final. abstract需要被重写, 但final要求不能被重写或继承

    4. 抽象类和一般类的异同点:
    
       - 同: 都是用来描述事物的, 都在内部定义了成员.
       - 异: 
         1. 一般类有足够的信息描述事物, 而抽象类描述事物的信息有可能不足.
         2. 一般类中不能定义抽象方法, 只能定义非抽象方法. 而抽象类中可定义抽象方法, 也可以定义非抽象方法.
         3. 一般类可以被实例化, 抽象类不能被实例化.
    
    5. 抽象类一定是个父类吗?
       是的. 因为需要子类重写其方法后才可以对子类实例化.

---

# 接口 interface

- java是一门单继承(只有一个父类, 保证调用的确定性), 多实现(保证了功能的拓展性) 的语言.
- 定义:
  接口是一个特殊的抽象类, 当一个抽象类中的方法都是抽象的, 那么这个抽象类就是接口. 接口方法都是抽象方法.  
- 定义接口的关键字:
  interface, 不是class.
- 接口中常见的成员, 这些成员都有固定的修饰符(可以不写全, 运行时自动补全).
  1. 全局常量: public static final 
  2. 抽象方法: public abstract (可以有返回值和参数, 但必须没有方法体)
  3. 由此得出结论, 接口中的成员都是公共权限.
- 类, 接口之间的关系:
  1. 类与接口之间是实现关系implements, 多实现.
  2. 类与类之间是继承关系extends, 只能单继承, 因为有方法体.
  3. 接口与接口之间是继承关系extends, 支持多继承, 因为没有方法体.
- 接口不可以实例化, 只能由实现了接口的子类并重写了接口中所有的抽象方法后, 该子类才可以实例化. 否则, 这个子类就是一个抽象类.
-  多实现机制(改良的多继承机制) 
  - 一个类可以实现多个接口.
    - 因为接口中的抽象方法没有方法体, 一个类要实现接口必须重写该方法, 重写后该方法就是确定的, 所以不会出现调用的不确定性. 如果多个接口中出现同名方法, 类对该方法重写后, 该方法就是确定的.
    - 而多继承中的方法有方法体, 如果出现同名方法, 子类调用方法时会出现不知道该调用哪个方法的情况.
  - 一个类在单继承的同时, 还可以多实现.

- 接口的特点:

  - 对外暴露的规则
  - 程序功能的扩展
  - 降低耦合性

- 接口和抽象类的异同

  - 相同点: 都是不断向上抽取而来的.
  - 不同点:
    1. 抽象类需要被继承, 单继承.
       接口需要被实现, 多实现.
    2. 抽象类中可以定义抽象和非抽象方法, 子类继承后, 可以直接使用非抽象方法.
       接口中只能定义抽象方法, 必须由子类实现.
    3. 抽象类的继承, 是is a关系, 在定义该体系的基本共性内容.
       接口的实现, 是like a关系, 在定义体系额外功能.

- 接口类型的引用指向的都是自己的子类对象

  ````java
  //暴露的规则
  public interface USB {
      void open();
      void close();
  }
  
  //实现规则
  //这些设备和电脑的耦合性降低了
  public class UPan implements USB{
      public void open(){
          System.out.println("upan open");
      }
  
      public void close(){
          System.out.println("upan close");
      }
  }
  
  public class BookPC {
      public static void main(String[] args) {
          //功能扩展了
          useUSB(new UPan());
      }
      //接口类型的引用, 用于接收(指向)接口的子类对象
      //使用规则
      public static void useUSB(USB u){
          u.open();
          u.close();
      }
  }
  ````



---

# 多态

- 一个对象对应着不同类型, 即父类或者接口的引用指向其子类的对象.

- 好处:

  - 提高了代码的扩展性, 前期定义的代码可以使用后期的内容.

- 局限性:

  - 前期定义的内容不能调用后期子类的**特有**内容.

- 前提:

  1. 必须有关系, 实现or继承.
  2. 要有重写.

- 转型/造型: 自始至终都是子类对象在做类型的变化.

  ````java
  //向上转型
  //猫对象提升为动物类型, 就无法访问猫类型的特有功能.
  //作用: 限制对特有功能的访问.
  Animal a = new Car();
  a.eat();
  //向下转型
  //作用: 调用子类中的特有方法.
  Cat c = (Cat) a;
  c.eat();
  c.catchMouse();
  //类型转换异常 ClassCastException
  Animal a1 = new Dog();
  Cat c1 = (Cat) a1; //狗不能转换成猫
  ````

  

- 类型判断 instanceof
  
  - 作用: 用于判断对象的具体类型, 只能用于引用数据类型判断, 通常在向下转型前用于健壮性的判断.
  - 用法: 对象 instanceof 类/接口
  
- 成员特点

  ````java
  Fu f = new Zi();
  System.out.println(f.num);
  f.show();
  ````

  
  
  1. 成员变量(调用父类的)
  
     - 编译时: 参考引用类型所属的类中的是否有调用的成员变量, 有, 编译通过; 没有, 编译失败.
     - 运行时: 参考引用类型所属的类中的是否有调用的成员变量, 并运行该所属类中的成员变量.
     - 简单说: 编译和运行都参考等号的左边**(引用类型)**. 
  2. 成员函数(非静态)
     - 编译时: 参考引用类型所属的类中的是否有调用的成员函数, 有, 编译通过; 没有, 编译失败.
     - 运行时: 参考的是对象所属的类中是否有调用的函数.
     - 简单说: 编译看左边**(引用类型)**, 运行看右边**(对象)**.
  3. 静态函数
     - 编译&运行时: 参考引用类型所属的类中的是否有调用的静态函数.
     - 简单说: 编译和运行都看左边**(引用类型)**.
     - 静态方法的调用其实不需要对象, 直接用类名调用即可.



---

# 内部类/内置类/嵌套类

- 将一个类定义在另一个类里面, 里面的那个类就是内部类.

- 访问特点:

  1. 内部类可以直接访问外部类中的成员.
  2. 外部类要访问内部类, 必须先建立内部类的对象.

- 访问方法:

  ````java
  //直接访问外部类中的内部类成员.
  Outer.Inner in = new Outer().new Inner();
  in.show();
  //如果内部类是静态的, 相当于一个外部类.
  Outer.Inner in = new Outer.Inner();
  //如果内部类是静态的, 成员是静态的. 如果内部类中定义了静态成员, 该内部类也必须是静态的.
  Outer.Inner.method();
  //内部类直接访问外部类中的成员: 持有外部类的引用即可
  Outer.this.num (外部类名.this.外部成员名)
  ````

  

- 使用场景:

  - 类的设计: 分析事物A时, 发现事物A的描述中还有事物B, 而且这个事物B还在访问被描述事物A的内容, 这时事物B就可以被定义成内部类.

- 内部类可以放在局部位置上, 只能访问局部中被final修饰的局部变量(常量), final已被jdk8省略.


## 匿名内部类

- 内部类的简写格式, 其实就是一个匿名的子类对象. 定义一个类的同时对其进行实例化.
- 使用前提:
  - 内部类必须继承或实现一个外部类或者接口.
- 格式:
  - new 父类or接口(){}
- 使用场景
  - 当函数参数是接口类型, 而且接口中的方法不超过三个, 就可以用匿名内部类作为实际参数进行传递.



---

# 异常类

- 用类将运行时发生的不正常情况进行描述与封装.

- 将正常流程代码和问题处理代码分离, 提高阅读性.

- 异常体系

  ![img](https://www.runoob.com/wp-content/uploads/2013/12/exception-hierarchy.png)

  - Error: JVM抛出的严重问题, 一般不可处理. 
  - Exception: 可处理.
    - 编译时异常: 编译时被检测出, 有针对性的处理. throws抛出的
    - 运行时异常: 由于调用者的操作使功能无法继续运行, 运行时强制停止程序, 让调用者修正代码.

- 自定义异常, 必须继承异常体系(Exception / RuntimeException), 从而具有可抛性

- 关键字throw/throws: 使类或对象具有可抛性.

  |          | throws                   | throw    |
  | -------- | ------------------------ | -------- |
  | 位置     | 函数上                   | 函数内   |
  | 抛出内容 | 异常类, 可多个, 逗号隔开 | 异常对象 |

- 异常处理的捕捉形式

  ````java
  try{
      //需要被检测异常的代码
  }
  catch(异常类 变量){ //该变量用于接收发生的异常对象
      //处理异常的代码
  }
  finally{
      //一定会被执行的代码
  }
  ````

  

- catch中常用方法

  - 异常对象.getMessage();
  - 异常对象.toString();
  - 异常对象.printStackTrace();

- 异常处理原则

  1. 函数内容如果抛出需要检测的异常, 那么函数上必须要声明. 否则必须在函数内用try/catch捕捉, 否则编译失败.
  2. 如果调用到了声明异常的函数, 要么try/catch要么throws, 否则编译失败.
  3. 功能内容可以解决时用catch. 解决不了时, 用throws, 让调用者解决.
  4. 一个功能如果抛出了多个异常, 那么调用时, 必须对应多个catch进行针对性处理. 内部有n个需要检测的异常, 就抛出n个, 就对应捕捉n个.

- finally代码块
  - 一定会被执行, 用于关闭/释放资源.
  - 链接数据库--查询(Exception)--关闭连接(finally)

- try catch finally代码块组合特点

  1. try + catch + finally

  2. try + catch (多个): 没有必要资源需要释放时. 多catch时, 父类异常要放在最下面, 否则编译失败. 接口中的方法不能声明异常, 只能在方法中用catch接收异常.

  3. try + finally: 异常无法直接catch处理, 但是资源需要关闭.

     ````java
     void show () { //throws Exception
         try {
             //开启资源
             throw new Exception();
         } finally {
             //关闭资源
         }
     }
     ````

- 需要看的信息
  1. 所属线程.
  2. 异常名称.
  3. 异常信息.
  4. 异常位置.


---

# Object类

- 常用方法:

  - equals(Object obj), 返回boolean. 一般会在类里覆盖此方法, 根据对象的特有内容, 建立判断对象是否相同的依据.

    ````java
        public boolean equals(Object obj){
            if (!(obj instanceof Person)) {
                throw new ClassCastException();
            }
            Person p = (Person) obj;
            return p.age == this.age;
        }
    ````

  - hashCode()

    ````java
    public int hashCode(){
        return age;
    }
    ````

  - toString()

    ````java
    public String toString(){
        return "Person: " + age;
    }
    ````

  - getClass(), 返回此Object的运行时类. 即获取当前对象所属的字节码文件对象(在堆中, 只加载一次, 然后可new无数个对象)
  - 开发时自定义类通常会重写equals(), hashCode(), toString()方法


---

# 包

````java
package packa;

public class DemoA{
	public void show(){
		System.out.println("demoa show run");
	}
}

package mypack;
import packa.DemoA; //导入了包中的DemoA类, 用到哪个类就导入哪个类.
import packa.*; //导入了packa包中所有的类, 但不导入包中的包

class PackageDemo{
	public static void main (String[] args) {
		/*packa.*/DemoA d = new /*packa.*/DemoA();
		d.show();
		System.out.println("Hello Package!");
	}
}
````

- DOS命令操作
  - javac -d . JarDemo.java : 把JarDemo编译到代码中指定的package "pack" 中.
  - java pack.JarDemo : 运行 "pack" 文件夹中的JarDemo.class文件.
  - jar -cvf pack.jar pack : 将 "pack" 文件夹压缩为 "pack.jar", 用 -cf 也可以, -xvf/-xf 为解压.
  - set classpath = ./pack.jar : 把classpath设置为jar包所在位置.
  - java pack.JarDemo : 在不解压的情况下, 可以直接运行jar包中的class文件, 其中 "pack" 为包中的文件夹.

- 权限修饰符

  |        | public | protected | (default) | private |
  | ------ | ------ | --------- | --------- | ------- |
  | 同类   | ok     | ok        | ok        | ok      |
  | 同包   | ok     | ok        | ok        |         |
  | 子类   | ok     | ok        |           |         |
  | 不同包 | ok     |           |           |         |





---

# 多线程

- 线程: 进程中一个负责程序执行的控制单元(执行路径), 一个进程中至少有一个线程.

- 多线程: 一个进程中可以有多个执行路径同时进行. 每个线程都有自己执行的任务. 目的是为了同时运行多部分代码.

- 好处: 解决了多部分同时运行的问题.

- 坏处: 线程太多容易导致低效率.

- JVM启动时就启动了多个线程, 至少有两个线程可以分析出来.

  1. 执行main函数的线程(主线程), 该线程的任务代码都定义在main函数中.
  2. 负责垃圾回收的线程.

- 创建线程

  - 目的: 开启一条执行路径, 去运行指定的代码, 并和其他代码实现同时运行. **运行的指定代码**就是这个执行路径的**任务**.

  - 任务定义的位置: 

    - JVM创建的**主线程任务**: 定义在主函数中. 
    - 自定义线程的任务: 定义在Thread类中的run方法中. 要继承Thread类并重写run方法.
      - 原因: Thread类用于描述线程, 线程需要任务, 所以Thread类是对任务的描述. 这个任务通过Thread类中的run方法体现, 即run方法就是封装自定义线程运行任务的函数, 即run方法中定义的就是线程要运行的任务代码. 

  - 线程的名称获取: 

    通过调用Thread类的getName方法获取, Thread-编号(从0开始). 主线程的名称为main. 调用Thread类的静态方法currentThread()获取当前运行的线程的名字.

  - 方式一: 继承Thread类.

    1. 定义一个类继承Thread类.
    2. **重写Thread类中的run方法**, run方法中就是需要运行的指定代码. 
    3. 直接创建Thread类的子类对象. 
    4. 调用start方法, 开启线程, JVM就会自动调用线程的任务run方法执行. **注意: 如果调用run方法, 则不会开启线程, 依旧是走的主线程.**

  - 方式二: 实现Runnable接口.

    - 步骤:
      1. 定义类实现Runnable接口, 该接口唯一的作用就是将线程的任务进行对象的封装.
      2. **重写接口中的run方法**, 将线程的任务代码封装到run方法中.
      3. 通过Thread类创建线程对象, 并将Runnable接口的子类对象作为Thread类的构造函数的参数进行传递. 为什么? 因为线程的任务都封装在Runnable接口子类对象的run方法中, 所以要在线程对象创建时就明确要运行的任务. 
      4. 调用线程对象的start方法开启线程.

    - 实现Runnable接口的好处:
      1. 将线程的任务从线程的子类中分离出来, 进行了单独的封装. 按照面向对象的思想将任务封装成对象.
      2. 避免了java单继承的局限性.

    ![image-20211026204626357](C:\Users\admin2\AppData\Roaming\Typora\typora-user-images\image-20211026204626357.png)

- 线程状态

  ![image-20211026170348116](C:\Users\admin2\AppData\Roaming\Typora\typora-user-images\image-20211026170348116.png)

  - CPU的执行资格: 可以被CPU处理, 在处理队列中, 但没有运行.
  - CPU的执行权: 正在被CPU处理.

- 线程安全问题产生的原因:

  - 前提:

  1. 多个线程在操作共享的数据.
  2. 操作同一个共享数据的线程代码有多条.

  - 过程: 

    当一个线程在执行操作共享数据的多条代码的过程中, 其他线程参与了运算, 就会导致线程安全问题的产生.

  ![image-20211026223023026](C:\Users\admin2\AppData\Roaming\Typora\typora-user-images\image-20211026223023026.png)
  - 解决方法:

    思路: 将多条操作共享数据的线程代码封装起来, 当有线程在执行这些代码的时候, 其他线程不可以参与运算. 只有当前线程将这些代码执行完毕后, 其他线程才可以参与运算.

    1. 同步代码块: 

       - 好处: 解决了线程安全问题.
       - 弊端: 相对降低了效率, 因为同步外的线程都会判断同步锁.
       - 前提: 同步中必须有多个线程并使用同一个锁, 即Object对象定义在类中方法外.
       - 锁: **任意的对象**, 可以是this, 也可以是所属的字节码文件对象. 如果锁是this, 可以简写成同步函数.

       ````java
       Object obj = new Object(); //obj必须为类的成员变量, 因为是唯一的一把锁, 由线程共用. 不可以是run的局部变量, 否则每个线程都会各自拥有一把锁.
       
       public void run () {
           synchronized (obj) /*对象/同步锁*/ {
         	  需要被同步的代码;
       	}
       }
       ````
       
    2. 同步函数:
    
       - 在函数上使用 synchronized 关键字.
       - 锁: **固定的this**, 不需要显示的写出.
    
       ````java
       public synchronized void sell () {
           需要被同步的代码;
       }
       ````
    
       
    
    3. 同步静态函数:
    
       - 锁: 该函数所属的**字节码文件对象**, 可以用 **对象/this.getClass()** 或 **当前类名.class** 获取.
    
       ````java
       public static synchronized void sell (t.getClass() /*Ticket.class*/) {
           需要被同步的代码;
       }
       ````
    
  - 死锁: 
  
    常见情景: 同步的嵌套, 下面是一个死锁程序实例.
  
    ````java
    public class DeadLockTest {
        public static void main(String[] args) {
            Test a = new Test(true);
            Test b = new Test(false);
            Thread t1 = new Thread(a);
            Thread t2 = new Thread(b);
            t1.start();
            t2.start();
        }
    }
    
    public class MyLock {
        public static final Object locka = new Object();
        public static final Object lockb = new Object();
    }
    
    public class Test implements Runnable {
        private boolean flag;
        Test(boolean flag) {
            this.flag = flag;
        }
        public void run() {
            if (flag) { //线程t1进入
                synchronized (MyLock.locka) {
                    System.out.println(Thread.currentThread().getName() + "if的A锁");
                    synchronized (MyLock.lockb) {
                        System.out.println(Thread.currentThread().getName() + "if的B锁");
                    }
                }
            } else { //线程t2进入
                synchronized (MyLock.lockb) {
                    System.out.println(Thread.currentThread().getName() + "else的B锁");
                    synchronized (MyLock.locka) {
                        System.out.println(Thread.currentThread().getName() + "else的A锁");
                    }
                }
            }
        }
    }
    ````
  
    
  
- 线程间通信
  多个线程在处理同一资源, 但是任务不同. 

  ````java
  //创建资源
  Resource r = new Resource();
  //创建任务
  Input input = new Input(r);
  Output output = new Output(r);
  //创建线程, 执行路径
  Thread t1 = new Thread(input);
  Thread t2 = new Thread(output);
  //开启线程
  t1.start();
  t2.start();
  ````

p219-232被跳过, 面试前需加强

---

# String类

- 特点

  1. 字符串对象一旦被初始化就不会被改变.

- [初始化方式]: https://blog.csdn.net/qq_42006801/article/details/115407170	"JDK1.8-直接赋值和new String()的区别及字符串常量池相关问题"

  1. 在字符串常量池中创建一个字符串, 并把地址赋给了引用变量. 在常量池中创建1个对象. 池中没有就创建, 有就直接用.

     ````java
     String s = "abc";
     ````

  2. 在堆内存中用new的方式创建一个子类对象, 对象在构造时接收了一个字符串对象, 在堆内存中创建1个或2个对象.

     ````java
     String s1 = new String("abc");
     s == s1; //false
     ````

     - equals() 方法
       比较字符串内容. String类中的equals重写了Object类中的equals, 建立了String类自己的判断字符串对象是否相同的依据. 

       ````java
       s.equals(s1); //true
       ````

  3. 构造方法

     `String(byte[] bytes)`

     `String(byte[] bytes,  int offset, int length)`

     `String(char[] value)`

     `String(char[] value,  int offset, int count)`

- 按照面向对象的思想对字符串进行功能分类.

  1. 获取

     1. **int length()** 获取字符串中字符的个数(长度). 

     2. **char charAt(int index)** 根据位置获取字符.

     3. **int indexOf(int ch) **根据字符获取在字符串中第一次出现的位置.
        **int indexOf(int ch, int fromIndex)** 从指定位置查找ch第一次出现的位置.

        **int indexOf(String str);**

        **int indexOf(String str, int fromIndex);**

        lastIndexOf (从后往前) 和 indexOf (从前往后) 使用方法相似.

        - 应用: 判断字符(串)是否存在, 不存在则返回 -1 .

     4. 获取子串.
        **String subString(int beginIndex, int endIndex)** 截下来的子串为: [开始位, 结束位).

        **String subString(int beginIndex)** 从指定位开始, 一直到最后.

  2. 转换
     1. **String[] split(String regex)** 切割为字符串数组.
     2. **char[] toCharArray()** 切割为字符数组.
     3. **byte[] getBytes()** 切割为字节数组.
     4. **String toUpperCase()** 字母转换成大写.
        **String toLowerCase()** 字母转换成小写.
     5. **String replace(char old, char new)** 替换字符.
        **String replace(String oldSubstring, String newSubstring)** 替换子串.
     6. **String trim()** 剪掉首位空格.
     7. **String concat(String s)** 连接字符串, 相当于+.
     
  3. 判断
     1. **boolean equals(Object obj)** 判断内容是否相同.
        **boolean equalsIgnoreCase(String s)** 判断内容是否相同, 忽略大小写.
     2. **boolean contains(String s)** 
        **boolean startWith(String s)** 
        **boolean endWith(String s)** 
     
  4. 比较
     **String compareTo(String s)** 按Unicode值比较字符串大小. 负数为小于, 正数为大于, 相等为0, 其实就是调用方法的string减去参数string的Unicode值.

## StringBuffer类

- 用于存储数据的容器.

- 特点: 

  1. 长度可变.
  2. 可存储不同类型的数据.
  3. 最终要转成字符串使用.
  4. 可以修改.

- 功能:

  1. 添加
     StringBuffer append(data)
     StringBuffer insert(index, data)

  2. 删除
     StringBuffer delete(start, end) 包头不包尾.

     ````java
     sb.delete(0, sb.length()); //清空缓冲区
     ````

     StringBuffer deleteCharAt(index) 删除指定位置的元素.

  3. 查找
     char charAt(index)
     int indexOf(string)
     int indexOf(string, fromIndex)
     int lastIndexOf(string)
     int lastIndexOf(string, fromIndex)
  4. 修改
     StringBuffer replace(start, end, string)
     void setCharAt(index, char)
     setLength(int length) 设置长度, 截短或加长
     reverse()

- **StringBuffer 与 StringBuilder**
  
  - jdk1.5后出现了Builder, 功能与Buffer一模一样. 
  - Buffer是线程同步的, 安全性高, 通常用于多线程.
  - Builder是线程不同步的, 效率高, 通常用于单线程.
  - jdk通常升级点: 简化书写; 提高效率; 提高安全性.

---

# 基本数据类型对象包装类

| 基本数据类型 | 包装类    |
| ------------ | --------- |
| byte         | Byte      |
| short        | Short     |
| int          | Integer   |
| long         | Long      |
| float        | Float     |
| double       | Double    |
| char         | Character |
| boolean      | Boolean   |

- [创建Integer对象的方法]: https://blog.csdn.net/AinUser/article/details/52416848	"java中Integer对象不同创建方法之间的区别"

  ````java
  // 方式一
  Integer a = new Integer(100);
  // 方式二
  Integer b = Integer.valueOf(100);
  // 方式三: 自动装箱
  Integer c = 100;
  c = c + 6; // c = new Integer(i.intValue() + 6); // i.intValue() 自动拆箱
  ````

  - 方式三也称为**自动装箱**

    - 好处: 简化书写.

    - 弊端: 比 int 多一个 null 值, 自动拆箱时可能会出现 NullPointerException. 所以在进行操作前, 需要对值进行判断.

    - 出现场合: 基本数据类型值**赋值**给引用数据类型时会出现自动装箱. 当基本数据类型和引用数据类型**做运算**时会出现自动拆箱.

    - jdk1.5以后, 如果自动装箱的是一个字节(-128~127), 那么该数据会被共享, 不会重新开辟空间.
    
      ````java
      Integer a = 127;
      Integer b = 127;
      System.out.println(a == b); // true
      Integer a = 128;
      Integer b = 128;
      System.out.println(a == b); // false
      ````

- 包装类对象主要用于基本数据类型和字符串之间的转换.

  - 基本数据类型 --> 字符串

    1. 基本数据类型 + "".

    2. 使用String类中的静态方法valueOf(基本类型数值).

       ````java
       String s = String.valueOf(10);
       ````

  - 字符串 --> 基本数据类型

    1. 使用包装类中的静态方法 xxx parseXxx("xxx类型的字符串"). 只有Characte类没有parse方法.

       ````java
       int i = Integer.parseInt("5");
       long l = Long.parseLong("3");
       boolean b = Boolean.parseBoolean("true");
       float f = Float.parseFloat("3.3");
       Double d = Double.parseDouble("3.3");
       ````

    2. 如果字符串被Integer进行对象的封装.
       使用非静态方法intValue()将Integer对象转换成基本数据类型的值.

       ````java
       Integer i = new Integer("11");
       int j = i.intValue();
       ````

    3. 使用Integer类中的静态方法valueOf(基本类型数值).

       ````java
       Integer i = Integer.valueOf("12");
       int j = Integer.valueOf("12");
       ````

       - Integer具备不同的进制体现.

         1. 十进制 --> 其他进制

            ````java
            String s1 = Integer.toBinaryString(60);
            String s2 = Integer.toOctalString(60);
            String s3 = Integer.toHexString(60); 
            ````

         2. 其他进制 --> 十进制

            ````java
            // 用二进制解析字符串"110", 返回十进制的值.
            int i = Integer.parseInt("110", 2);
            ````

----

# 集合类

![image-20211102165546110](C:\Users\admin2\AppData\Roaming\Typora\typora-user-images\image-20211102165546110.png)

- 对象用于封装特有数据, 对象多了需要存储, 如果对象的个数不确定, 就使用集合容器进行存储. **(对象类的数组也可以存储多个对象, 但是长度不可变, 如Person[]可以存储多个Person对象)**

- 特点

  1. 用于存储对象的容器. 
  2. 长度可变.
  3. 不可以存储基本数据类型值.

- 集合框架
  集合容器内部的数据结构不同, 有多种具体容器, 不断向上抽取, 就形成了集合框架. 

  

## 1. Collection接口 (框架的顶层)

### 1.1 常见方法

1. 添加
   boolean add(Object obj);
   boolean addAll(Collection coll);

2. 删除(会改变集合的长度)
   boolean remove(object obj);
   boolean removeAll(Collection coll); 删交集.
   void clear();

3. 判断
   boolean contains(Object obj);
   boolean containsAll(Collection coll);
   boolean isEmpty();

4. 获取
   int size();
   Iterator iterator(); 

   - 迭代器: 取出集合中元素的方式, 是实现了Iterator接口的每一个容器的内部对象. 
     该对象必须依赖于具体容器, 因为每一个容器的数据结构都不同, 所以该迭代器对象是在容器中进行内部实现的. 对于容器的使用者而言, 具体的实现不重要, 只要通过容器获取到该实现的迭代器对象即可, 也就是使用iterator方法. 
     Iterator接口就是对所有的Collection容器进行元素取出的公共接口.

   ````java
   //迭代器取元素的两种方式.
   //方式一, 循环结束后it消失, 不再占用内存空间.
   for (Iterator it = coll.iterator(); it.hasNext(); ) {
   	System.out.println(it.next());
   }
   //方式二, 循环结束后it还在内存中, 但指针已经走到集合末尾元素处.
   Iterator it = coll.iterator();
   	while (it.hasNext()) {
   		System.out.println(it.next());
   }
   ````

5. 其他
   boolean retainAll(Collection coll); 取交集. 
   Object[] toArray(); 将集合转成数组.

### 1.2 常用子类

#### 1.2.1 List接口

- 作用: 完成对元素的增删改查.

- 特点
  1. 有序 (存入和取出的顺序一致).
  2. 元素有索引 / 角标.
  3. 元素可重复.

- 特有的常见方法: (共性特点: 都可以操作角标)

  1. 添加
     void add(index, element);
     void add(index, collection);

  2. 删除
     Object remove(index);

  3.  修改
     Object set(index, element);

  4. 获取
     Object get(index);
     int indexOf(object);
     int lastIndexOf(object);
     List subList(from, to);

     ````java
     // List取元素的特有方式
     for (int i = 0; i < list.size(); i++) {
         System.out.println(list.get(i));
     }
     ````

- 在List类中, 当迭代器运行时, 不可以使用集合方法操作元素, 容易出现异常(ConcurrentModificationException). 应使用Iterator接口的**ListIterator子接口**完成在迭代中对元素进行的更多操作.

  ````java
  List list = new ArrayList();
  list.add("a1");
  list.add("a2");
  list.add("a3");
  
  // Iterator it = list.iterator(); CURD时容易抛异常.
  // 获取列表迭代器对象. 可以在迭代过程中完成对元素的CURD操作.
  // 注意: 只有List集合具备该迭代功能.
  ListIterator it = list.listIterator();
  
  while (it.hasNext()) {
      Object obj = it.next();
      if (obj.equals("a2")){
          it.add("a9");
      }
  }
  ````
  
- 常用实现类

  1. Vector

     - 内部是数组数据结构. 
     - 同步, 线程安全. API: 如不需要线程安全, 建议使用ArrayList代替Vector.
     - **增删查询都很慢.**

  2. ArrayList

     - 内部是数组数据结构. (数组本身就是一种数据结构, 特点: 1. 每个元素都有索引. 2. 连续存储)
     - 不同步, 线程不安全. 替代了Vector. 
     - **查询元素的速度很快.** (因为数组是一段连续的储存空间)
     - 内部存储的是对象的地址值, 不能接收基本数据类型.
     - **contains 和 remove 方法只基于 equals 方法进行判断.**
  
  3. LinkedList
  
     - 内部是双向链表数据结构.
     - 不同步. 
     - **增删元素的速度很快.** (因为链表是不连续的储存空间)
     - 常用方法
       - addFirst(); addLast(); 同 offerFirst(); offerLast();
       - **getFirst(); getLast(); removeFirst(); removeLast();** 如链表为空, 抛出NoSuchElementException. 
       - jdk1.6新增 **peekFirst(); peekLast(); pollFirst(); pollLast();** 如链表为空, 返回null.
  
  4. [三者比较]: https://blog.csdn.net/glpghz/article/details/107581333	"ArrayList和LinkedList、Vector的区别"
  
  5. 请使用LinkedList模拟一个堆栈或者队列数据结构.
  
     - 堆栈: 先进后出(压栈弹栈) First In Last Out (FILO).
  
     - 队列: 先进先出(水管) First In First Out (FIFO).
  
       ````java
       // 队列
       public class Queue {
           private LinkedList list;
           public Queue(){
               list = new LinkedList();
           }
       
           public void myAdd(Object obj){
               list.addLast(obj);	//从前往后添加
           }
       
           public Object myRemove(){
               return list.removeFirst();	//从前往后删除
           }
       
           public void myShow(){
               System.out.println(list);
           }
       
           public boolean isEmpty(){
               return list.isEmpty();
           }
       }
       ````



#### 1.2.2 Set接口

- 特点
  1. 元素不可以重复, 无序.
  2. 方法和Collection一致.
  3. 只能使用迭代器取出元素.
- 常用实现类
  1. HashSet
     - 内部数据结构是哈希表. 不同步.
     
       - 哈希表
     
         - 用于存储哈希算法算出的值, 能够根据元素自身的特点获取其在数组中的位置, 确定元素和其位置的对应关系, 优化查找, 性能稳定高效.
     
         - [哈希表确定元素是否相同]: https://blog.csdn.net/qq_19925065/article/details/52495453	"关于HashSet添加元素时的equals()方法和hashCode()方法"
     
           1. 判断两个元素的**哈希值**是否相同. 如果相同, 再判断两个对象的**内容**是否相同.
           2. 判断哈希值相同, 其实判断的是对象的hashCode方法. 判断内容相同, 用的是equals方法.
           3. 注意: 如果哈希值不同, 就不需要使用equals判断内容.
           4. **contains和 remove方法基于hashCode和equals方法进行判断.**
     
     - HashSet使用注意事项:
     
       1. 如果元素要存储到HashSet集合中, 必须重写hashCode和equals方法 (继承自Object类), 建立对象判断是否相同的依据, 以**针对元素的自身特点**保证其在HashSet中的唯一性. 这种情况下, 如果修改已经存入HashSet中的元素的属性, 其 hashCode 会自动改变. 
       2. 如果不重写, 使用的就是Object类的方法. 这种情况下, 如果修改已经存入HashSet中的元素的属性, 其hashCode不会改变. 
     
     - LinkedHashSet: 有序, 元素唯一.
  2. TreeSet

     - 内部数据结构是二叉树, 不同步. 对Set集合中的元素按照自然顺序进行排序, 对同类型数据(如字符串, 整数等)自动排序, 不需要重写方法.

     - 判断元素唯一性的方式: compareTo方法的返回值是否为0, 返回值0表示相同元素, 则不存.

     - 排序方式

       1. **让元素自身具备比较功能.** 元素的类必须实现Comparable接口, 并重写compareTo方法, 该方法在存储元素时被自动调用, 用于确定元素在集合中的位置. 

          ````java
          @Override
          public int compareTo(Object o) {
              Person p = (Person) o;
              //先比较年龄, 年龄相等再比较姓名.
              //用compareTo方法对字符串进行比较.
              int temp = this.getAge() - p.getAge();
              return temp == 0? this.getName().compareTo(p.getName()) : temp;
              //先比较姓名, 姓名相同再比较年龄.
              int temp = this.getName().compareTo(((Person) o).getName());
              return temp == 0? this.getAge() - ((Person) o).getAge() : temp;
          }
          ````

       2. **让集合自身具备比较功能.** 如果对象不具备或不能够使用按自然顺序的排序功能. 定义一个类实现Comparator接口, 重写compare方法, 将该类对象作为参数传递给TreeSet集合的构造函数.

          ````java
          public class TreeSetDemo1 {
              public static void main(String[] args) {
                  //使用比较器
                  Set set = new TreeSet(new ComparatorByName());
                  set.add(new Person("bb",21));
          		....
              }
          }
          
          public class ComparatorByName implements Comparator {
              @Override
              public int compare(Object o1, Object o2) {
                  Person p1 = (Person) o1;
                  Person p2 = (Person) o2;
                  int temp = p1.getName().compareTo(p2.getName());
                  return temp == 0? p1.getAge() - p2.getAge() : temp;
              }
          }
          ````

          - 注意: 在元素具备比较功能的情况下, 如果向容器中传入比较器, 会优先使用比较器进行排序, 因此, 比较器的使用更灵活, 使用场景更多.
            

### 1.3 集合选择技巧

- 判断元素是否唯一
  - 唯一: Set
    - 判断是否有序
      - 有序(自然顺序): TreeSet
      - 有序(和存储一致的顺序): LinkedHashSet
      - 无序: HashSet
  - 不唯一: List
    - 判断是否频繁增删
      - 频繁: LinkedList
      - 不频繁: ArrayList

| 前缀   | 数据结构 | 特点                                           |
| ------ | -------- | ---------------------------------------------- |
| Array  | 数组     | 查询快, 有角标                                 |
| Linked | 链表     | 增删快, 有add, get, remove + first, last的方法 |
| Hash   | 哈希表   | 元素需要覆盖hashCode和equals方法               |
| Tree   | 二叉树   | 接口Comparable或Comparator                     |



## 2. Map接口
### 2.1 特点

1. Map集合一次添加一对元素(key & value), 也叫**双列集合**. Collection集合一次添加一个元素, 也叫单列集合.
2. Map集合内部存储的是键值对.
3. Map集合必须保证键(key)的唯一性.

### 2.2 常用方法

1. 添加
   value put(key, value); 返回前一个和key关联的值, 如果没有值, 则返回null. 存相同键, 值会覆盖.  

2. 删除
   void clear(); 清空集合.
   value remove(key); 根据指定的key删除这个键值对.

3. 判断
   boolean containsKey(key);
   boolean constainsValue(value);
   boolean isEmpty();

4. 获取
   value get(key); 通过键获取值. 如果没有该键, 返回null. 可以通过返回null, 来判断是否包含指定键.
   int size(); 获取键值对的个数.

5. 迭代方法
   Map集合本身没有实现迭代器, 只能先转换成Set集合, 再使用迭代器.

   - 方法一: keySet()方法
     原理: 通过KeySet方法获取Map集合中所有的键所在的Set集合, 再通过Set的迭代器获取到每一个键, 再通过Map集合的get方法对每一个键获取其对应的值.

     ````java
     public static void getAllValue(Map<Integer, String> map) {
         //获取键的Set集合
         Set<Integer> keySet = map.keySet();
         //迭代keySet获取所有的值
         for (Iterator<Integer> it = keySet.iterator(); it.hasNext(); ) {
             Integer key = it.next();
             String value = map.get(key);
             System.out.println(value);
         }
     }
     ````

   - 方法二: entrySet()方法
     原理: 通过entrySet方法获取存有键和值的映射关系的Set集合, 该映射关系的类型是Map.Entry类型(结婚证). 注意, Entry是Map接口中的内部接口.

     ````java
     public static void getAllValue2(Map<Integer, String> map) {
         //获取存有映射关系的Set集合
         Set<Map.Entry<Integer, String>> entrySet = map.entrySet();
         for (Iterator<Map.Entry<Integer, String>> it = entrySet.iterator(); it.hasNext(); ) {
             Map.Entry<Integer, String> me = it.next();
             System.out.println(me.getKey() + ": " + me.getValue());
         }
     }
     ````

   - 方式三: values()方法

     ````java
     public static void getAllValue3(Map<Integer, String> map) {
         //获取存有所有值的Collection集合
         Collection<String> coll = map.values();
         for (Iterator<String> it = coll.iterator(); it.hasNext(); ) {
             System.out.println(it.next());
         }
     }
     ````

### 2.3 常用子类

1. Hashtable
   - 内部结构是哈希表, 同步.
   - 不允许null作为键或值.
   - 子类Properties: 用来存储键值对型的配置文件信息, 和IO技术相结合.
     - 特点
       1. 键和值都是字符串类型.
       2. 集合中的数据可以保存到流中, 或者从流中获取.
2. HashMap
   - 内部结构是哈希表, 不同步.
   - 允许null作为键和值.
   - 存入的对象类必须重写hashCode和equals方法.
3. TreeMap
   - 内部结构是二叉树, 不同步.
   - 可以对Map集合中的键进行排序.
     

## 3. 工具类

### 3.1 Collections

- 常用方法

  - sort() 排序

    ````java
    //1. 传入一个list.
    //泛型逻辑: 入参list是T类的, T类必须具备比较性, 才能进行排序, 所以T必须实现Comparable接口. 为了提高扩展性, 这个接口可以对T类的对象以及T的父类对象进行比较.
    static <T extends Comparable<? super T>> void sort(List<T> list) {}
    //2. 传入一个list和比较器.
    //泛型逻辑: 入参list是T类的, 比较器能够对T类的对象以及T的父类对象进行比较.
    static <T> void sort(List<T> list, Comparator<? super T> c) {}
    ````

  - swap() 换位
  - binarySearch() 
    对一个**自然升序排列**的List进行二分查找, 不可以对无序或逆序的List使用, 也不可以传入一个使List变成逆序的比较器.
  - reverseOrder() 返回一个逆序比较器.
  - replaceAll() 将集合中的指定元素替换为指定的元素.
  - fill() 将集合中的元素全部替换为指定的元素.
  - shuffle() 洗牌/打乱
  - synchronizedXxx() 返回线程安全的集合/列表/集/映射等, 原理是加锁.

### 3.2 Arrays

- 常用方法(全静态)

  - binarySearch()

  - copyOf()

  - copyOfRange()

  - equals() 比较两个数组是否相同

  - fill()

  - sort() 

  - toString() 源码

    ````java
    if (a == null)
        return "null";
    int iMax = a.length - 1;
    if (iMax == -1)
        return "[]";
    
    StringBuilder b = new StringBuilder();
    b.append('[');
    for (int i = 0; ; i++) { //中间省略条件判断, 提高了效率
        b.append(a[i]);
        if (i == iMax)
            return b.append(']').toString();
        b.append(", ");
    }
    ````

  - asList() 
    - 将数组转成**长度固定**的List集合, 
    - 好处: 可以使用集合的方法操作数组中的元素, 如查找, 判断, 修改.
    - 基本数据类型的数组转变为集合时, 会将该数组作为集合中的一个元素进行存储, 集合长度为1, 存储的是该数组的地址值. 而引用数据类型的数组转变为集合时, 直接将数组中的元素(引用对象)作为集合中的元素进行集合存储.
    - List集合长度固定. 因为数组的长度是固定的. 因此不可以使用集合的增删方法(add, remove, clear), 否则会发生UnsupportedOperationException.
  - 集合转数组: Collection接口中的toArray()方法
    - 好处: 可以对集合中元素操作的方法进行限定, 不允许增删操作.
    - 需要传入一个指定类型的数组. 
    - 入参数组的长度:
      - 如果长度小于集合的size, 则该方法会创建一个同类型并和集合相同size的数组.
      
      - 如果长度大于集合的size, 则该方法就会使用指定的数组, 存储集合中的元素, 其他位置默认为null.
      
      - 建议使用集合的size().
      
        ````java
        String[] s = list.toArray(new String[list.size()]);
        ````

----

# JDK1.5新特性

## 泛型

- jdk1.5新增的编译时期安全机制, 给编译器使用的技术, 确保了类型的安全. 

- 好处
  1. 将运行时的问题 ClassCastException 转到了编译时期.
  2. 避免了强制转换.
  
- <>的使用场景
  当操作的**引用**数据类型不确定的时候, 就使用<>. 将要操作的引用数据类型传入即可. <>其实就是一个用于接收具体引用数据类型的参数范围. 在程序中, 只要用到了带有<>的类或接口, 就要明确传入的具体引用数据类型. (不可以传基本数据类型)
  
- 泛型的擦除
  运行时会将泛型去掉, 生成的class文件中是不带泛型的.

- 泛型的补偿
  运行时, 通过获取元素的类型进行转换动作, 不需要使用者强制转换.

- (自定义)泛型类
  使用泛型来接收类中要操作的引用数据类型.

  - 使用场景
    当类中操作的引用数据类型不确定的时候, 就使用泛型来表示.

    ````java
    public class GenericDemo1 {
        public static void main(String[] args) {
            // 可以省略等号右侧的<Person>
            Tool<Person> tool = new Tool();
            tool.setObj(new Person("zhangsan",20));
            Person p = tool.getObj();
            System.out.println(p.getName() + "..." + p.getAge());
        }
    
        public static class Tool<E> {
            private E obj;
    
            public E getObj() {
                return obj;
            }
    
            public void setObj(E obj) {
                this.obj = obj;
            }
            //将泛型定义在方法上
            public <W> void show(W w){
                System.out.println(w);
            }
    		//当方法静态时, 不能访问类上定义的泛型, 如果静态方法需要使用泛型, 只能将泛型定义在方法上.
            public static <Y> void print(Y y){
                System.out.println(y);
            }
        }
    }
    ````

- 泛型接口
  将泛型定义在接口上.
- 泛型的通配符: ? (未知类型)
  1. 限定上限
     <? extends E> 接收E类型或者E的子类对象. 一般向集合存储元素时使用. 基于这种方式, 取出的对象都按照上限类型(父类)来运算, 不会出现类型安全隐患.
  2. 限定下线
     <? super E> 接收E类型或者E的父类对象. 一般从集合中取出元素时使用. 例如 TreeSet(Comparator<? super E> comparator)



## foreach语句

````java
//格式
//for (类型 变量 : Collection集合/数组) {}
for (String s : list) { //简化书写
    System.out.println(s);
}
````

- 和for的区别

  - for可以完成对语句的多次执行, 因为可以定义控制循环的增量和条件.
  - foreach是for的简化形式, 必须有对被遍历的目标. 该目标是数组或Collection单列集合.
  - 选择标准:
    如果遍历仅为获取数组中的元素, 可用foreach. 如果要对角标进行操作, 建议使用for.

- 遍历map集合的方法: 
  先将map转成单列set, 再用foreach或迭代器遍历.

  ````java
  for (Integer key : map.keySet()) {
      String value = map.get(key);
      System.out.println(key + ": " + value);
  }
  
  for (Map.Entry<Integer, String> me : map.entrySet()) {
      Integer key = me.getKey();
      String value = me.getValue();
      System.out.println(key + ": " + value);
  }
  ````



## 可变参数

- 定义: 适用于参数个数不确定且类型确定的情况, 方法接收的是数组的元素, 这些元素被自动封装成数组.
- 注意: 可变参数必须位于最后一项.
- 特点
  1. 只能出现在参数列表的最后.
  2. ...位于变量类型和变量名之间, 前后有无空格都可以. 
  3. 调用可变参数的方法时, 编译器为该可变参数隐含创建一个数组, 在方法体中以数组的形式访问可变参数.
- 提示
  1. 可变参数的核心是将参数封装为数组(arr).
  2. 什么元素都不传入时, arr中元素数量为0, 有地址值.
  3. 传入null时, arr为null, 无地址值.
  4. 可变参数方便调用者传入数量不确定的参数且不需要在前面非常不优雅的构造数组对象。

----

# System类

- 常见方法

  ````java
  //获取系统属性map集合
  Properties prop = System.getProperties();
  //获取指定的系统属性值
  public static final String LINE_SEPARATOR = System.lineSeparator();
  //自定义全局属性, 可应用于其他程序
  System.setProperty("xxx", "xxx");
  ````

----

# Runtime类

- 使用了单例设计模式. 体现在无构造方法, 不可以创建对象, 有静态方法getRuntime()返回一个Runtime对象.

  ````java
  Runtime r = Runtime.getRuntime();
  Process p = r.exec("D:\\tools\\notepad++\\notepad++.exe");
  Thread.sleep(5000);
  p.destroy();
  ````

----

# Math类

- 常用方法
  - ceil(): 返回大于参数的最小整数.
  - floor(): 返回小于参数的最大整数.
  - round(): 返回四舍五入的整数.
  - pow(a, b): 返回a的b次方.
  - random(): 返回[0, 1)之间的double.

----

# Date类和Calendar类

## 1. Date类

- 日期对象和毫秒值之间的转换.

  - 毫秒值 --> 日期对象

    1. 通过构造函数new Date(timeMillis).
    2. 通过setTime(timeMillis)方法设置.

    - 目的: 通过Date对象的方法对该日期中的各个字段(年月日)进行操作.

  - 日期对象 --> 毫秒值

    1. 通过Date对象的getTime()方法.

    - 目的: 对具体的数组做运算.
    
  - 格式化日期对象

    1. 日期对象 --> 日期格式字符串
       通过DateFormat类中的format方法.

       ````java
       //创建日期对象和日期格式对象. 日期格式为默认, 可用FULL/LONG/MEDIUM/SHORT指定格式
       Date date = new Date();
       DateFormat dateFormat = DateFormat.getDateTimeInstance(DateFormat.FULL, DateFormat.FULL);
       String s = dateFormat.format(date);
       //用SimpleDateFormat类自定义格式
       dateFormat = new SimpleDateFormat("yy---MM--dd");
       String s = dateFormat.format(date);
       ````

       - FULL: 2021年11月10日星期三 中国标准时间 上午10:40:17
       - LONG: 2021年11月10日 CST 上午10:40:44
       - MEDIUM: 2021年11月10日 上午10:41:12
       - SHORT: 2021/11/10 上午10:41

    2. 日期格式字符串 --> 日期对象
       通过DateFormat类中的parse方法.

       ````java
       //根据实际情况定义日期格式
       DateFormat dateFormat = new SimpleDateFormat("yy-MM-dd");
       Date date = dateFormat.parse("22-2-15");
       ````

## 2. Calendar类

- 抽象类, 用getInstance()获取对象. 

  ````java
  Calendar c = Calendar.getInstance();
  int year = c.get(Calendar.YEAR);
  //月字段的取值为0-11
  int month = c.get(Calendar.MONTH) + 1;
  int day = c.get(Calendar.DAY_OF_MONTH);
  ````

- 通过set()设置指定时间, 通过add()使时间发生偏移.

----

# IO流



- 换行符声明

  ````java
  public static final String LINE_SEPARATOR = System.lineSeparator();
  ````

- IO异常处理

  ````java
  FileWriter fw = null;
  try {
      fw = new FileWriter("Demo1.txt");
      fw.write("abc");
  } catch (IOException e) {
      System.out.println(e.toString());
  } finally {
      if (fw != null){
          try {
              fw.close();
          } catch (IOException e) {
              throw new RuntimeException("关闭失败");
          }
      }
  }
  ````

- 键盘录入

  ````java
  //键盘录入
  BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
  //        InputStream is = System.in;
  //        InputStreamReader isr = new InputStreamReader(is);
  //        BufferedReader br = new BufferedReader(isr);
  //控制台输出
  BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(System.out));
  //        OutputStream os = System.out;
  //        OutputStreamWriter osw = new OutputStreamWriter(os);
  //        BufferedWriter bw = new BufferedWriter(osw);
  String line;
  while ((line = br.readLine()) != null) {
      if ("over".equals(line)) {
          System.out.println("OVER");
          break;
      }
      bw.write(line.toUpperCase());
      bw.newLine();
      bw.flush();
  }
  ````



## 流的操作规律(确定使用哪个流对象)

1. 明确源和目的/汇.
   - 源: InputStream	Reader
   - 目的: OutputStream	Writer
2. 明确数据是否使纯文本数据.
   - 源--是纯文本: Reader
     源--非纯文本: InputStream
   - 目的--是纯文本: Writer
     目的--非纯文本: OutputStream
3. 明确具体的设备.
   - 硬盘: File
   - 键盘: System.in/out
   - 内存: 数组 
     1. 字节数组: ByteArrayInputSream/ByteArrayOutputStream
     2. 字符数组: CharArrayReader/CharArrayWriter
     3. 字符串: StringReader/StringWriter
   - 网络: Socket流
   
4. 是否需要额外功能(装饰类, 需要传入流对象).
   - 高效: 缓冲区Buffer.
   
   - 转换: 转换流InputStreamReader, OutputStreamWriter.
   
   - 操作对象/序列化: ObjectOutputStream, ObjectInputStream.
   
     - 将对象序列化/持久化ObjectOutputStream, 对象反序列化ObjectInputStream. 
   
     - 被序列化的对象必须实现Serializable接口(标记接口), 无抽象方法需要重写. 该接口用于给被序列化的类加入ID号, 标识类的版本信息, 判断类与对象是否为同一版本, ID号最好自己设置.
   
       ````java
       private static final long serialVersionUID = 34579493L;
       ````
   
     - 反序列化时需要确认是否有对象的class文件.
   
     - 被static和transient关键字修饰的数据不会被序列化
   
   - 操作基本数据类型: DataOutputStream, DataInputStream.



## 转换流

InputStreamReader: 字节到字符的桥梁, 解码.
OutputStreamWriter: 字符到字节的桥梁, 编码.

- FileWriter本质

  ````java
  //两句代码的功能是等同的
  FileWriter fw = new FileWriter("a.txt");
  OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream("a.txt"), "GBK");
  ````

  FileWriter 是按照默认码表来操作文件的便捷类, 是转换流指定了本机默认码表的体现. 且FileWriter作为转换流的子类对象, 可以方便文本的操作. 简单说, FileWriter = 操作文件的字节流 + 本机默认的编码表. 如果操作文本文件需要明确具体的编码表, 则必须使用转换流.

- 使用场景
  1. 源或目的对应的设备是字节流, 而操作的是文本数据, 可以使用转换流作为桥梁, 提高对文本操作的便捷性.
  2. 操作的文本设计具体的指定编码表,必须使用转换流.



## File类

- list()方法: 获取当前目录下的文件和文件夹的名称, 包括隐藏文件. 调用list方法的File对象中封装的必须是目录, 否则会发生空指针异常, 如果访问的是系统级目录, 也会发生空指针异常. 如果目录存在但是没有内容, 会返回一个长度为0的数组.



## PrintStream类和PrintWrite类

可以直接操作输入流和文件.

### PrintStream类

字节打印流

- 特点
  1. 提供了打印方法, 可对多种数据类型值进行打印, 并保持数据的表示形式.
  2. 不抛IO异常.
- 构造函数参数
  1. 字符串路径.
  2. File对象.
  3. 字节输出流 (, true).

- 常用方法
  1. print() 传入什么就打印什么, 其实就是把传入的数据先转成字符串, 再进行打印.
  2. write() 传入的数据只保留其二进制的最后八位, 前24位全部忽略.

### PrintWrite类

字符打印流

- 构造函数参数
  1. 字符串路径.
  2. File对象.
  3. 字节输出流 (, true). 使用println, printf, format方法时自动刷新流.
  4. 字符输出流 (, true). 同上.



## SequenceInputStream类

对多个流进行合并.

- 构造函数参数

  1. 2个字节输入流.

  2. 流的枚举.

     ```java
     //需求: 将文件数组中的文件对象传入序列流.
     //序列流能接收流的枚举, 所以要先遍历文件数组, 把每个文件与输入流先关联, 然后把输入流放入一个集合, 再通过Collections中的枚举方法将集合转为枚举对象, 再将该对象传入序列流/
     ArrayList<FileInputStream> list = new ArrayList<>();
     for (int i = 0; i < partFiles.length; i++) {
         list.add(new FileInputStream(partFiles[i]));
     }
     Enumeration<FileInputStream> en = Collections.enumeration(list);
     SequenceInputStream sis = new SequenceInputStream(en);
     ```

  

## RandomAccessFile类

- 特点
  1. 该类的实例支持读取和写入随机访问文件.
  2. 该对象内部维护了一个byte数组, 并可以通过指针操作数组中的元素.
  3. 可通过getFilePointer方法获取指针位置, 通过seek方法设置指针的位置.
  4. 本质是将字节输入流和输出流进行了封装.
  5. 该对象的源或者目的只能是文件.
- 构造函数参数
  1. 文件对象, 访问模式.
  2. 文件名, 访问模式.
- new对象时, 如果文件不存在, 则创建, 如果存在则不创建.

----

# 网络编程

## UDP协议

- 建立连接步骤
  - 发送
    1. DatagramSocket类创建发送对象.
    2. DatagramPacket类创建数据包, 用于存储需要发送的数据(字节数组), 目的ip地址, 目的端口.
    3. 用套接字对象的send方法发送数据包.
    4. 关闭套接字对象.
  - 接受
    1. DatagramSocket类创建接受对象, 需要明确端口.
    2. DatagramPacket类创建数据包, 用于存储将要被接受的数据(字节数组).
    3. 用套接字对象的receive方法接受数据后, 对数据包中的数据进行解析.
    4. 关闭套接字对象.



## 客户端与服务端交互

### 交互的三种方式

1. 地址栏输入url地址. method="get"
2. 超链接. get
3. 表单. get和post

----

# 反射

Java反射机制是指, 在运行状态中, 对于任意一个类(class文件), 都能够知道这个类的所有属性和方法; 对于任意一个对象, 都能够调用它的任意一个方法和属性; 这种动态获取信息以及动态调用对象的方法的功能称为Java语言的反射机制. 简单说, Java反射就是动态获取类中信息(类的解剖).

![image-20211209135308197](C:\Users\admin2\AppData\Roaming\Typora\typora-user-images\image-20211209135308197.png)

![image-20211209135231607](C:\Users\admin2\AppData\Roaming\Typora\typora-user-images\image-20211209135231607.png)











----



复习:

this是运行时真正发挥作用/执行操作的那部分 接口/类中的方法内容

抽象方法: 对事物的具体描述的抽象

匿名内部类: 在不定义具体的实现类的情况下, 通过创建一个接口的具体匿名子类, 来对接口的抽象方法进行具体实现.
