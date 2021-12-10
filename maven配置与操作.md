## Maven配置与操作

解决项目之间的依赖关系是其重要功能之一。每个Maven工程必须有一个pom文件。

### 一、本地配置

1. 进入官网下载最新版本 

   [下载地址]: https://maven.apache.org/download.cgi	"Downloading Apache Maven"

   Binary tar.gz archive一般适用于Linux，Binary zip archive一般适用于Windows。下载完成后解压到目标路径。

2. 环境变量配置

   1. 系统变量中新建变量MAVEN_HOME，值设置为Maven路径。
   2. 变量Path中添加  %MAVEN_HOME%\bin;  然后确定即可。

3. settings配置

   进入conf文件夹，在settings.xml中修改以下两点。

   1. 在localRepository注释下方添加本地仓库地址，本地仓库路径自定。

      ````xml
      <localRepository>D:\develop\soft\repository</localRepository> 
      ````

   2. 在mirrors标签内部添加远程仓库地址，以下为腾讯云仓库地址。

      ````xml
      	<mirror>
      		<id>nexus-tencentyun</id>
      		<mirrorOf>*</mirrorOf>
      		<name>Nexus tencentyun</name>
      		<url>http://mirrors.cloud.tencent.com/nexus/repository/maven-public/</url>
      	</mirror>
      	<mirror>
      		<id>maven-default-http-blocker</id>
      		<mirrorOf>external:http:*</mirrorOf>
      		<name>Pseudo repository to mirror external repositories initially using HTTP.</name>
      		<url>http://0.0.0.0/</url>
      		<blocked>true</blocked>
      	</mirror>
      ````

### 二、基于IDEA的配置

在settings中更新Maven路径。注意：每次新建工程和模块时都需要检查仓库地址是否正确。
![image-20211031101051003](C:\Users\admin2\AppData\Roaming\Typora\typora-user-images\image-20211031101051003.png)

### 三、基于IDEA的操作

1. 新建项目。新建完成后，可以将项目的src包删去，因为源码一般放在模块中。
   ![image-20211030225828937](C:\Users\admin2\AppData\Roaming\Typora\typora-user-images\image-20211030225828937.png)

2. pom文件。移除模块、增删依赖的操作可以在pom文件中执行，修改完源码后重载Maven项目即可。

3. 依赖包导入。

   1. [来源地址]: https://mvnrepository.com/	"MAVEN REPOSITORY"

   2. 在pom文件中的格式

      ````xml
          <dependencies>
              <!-- java.lang包的增强版本。https://mvnrepository.com/artifact/org.apache.commons/commons-lang3 -->
              <dependency>
                  <groupId>org.apache.commons</groupId>
                  <artifactId>commons-lang3</artifactId>
                  <version>3.12.0</version>
              </dependency>
      
              <!-- java单元测试框架，https://mvnrepository.com/artifact/junit/junit -->
              <dependency>
                  <groupId>junit</groupId>
                  <artifactId>junit</artifactId>
                  <version>4.13.2</version>
                  <scope>test</scope>
              </dependency>
          </dependencies>
      ````

      - java单元测试框架使用方法：在test-java包下新建测试类，定义用于测试的方法，方法上方@Test，不需要使用main方法，右键方法名可以单独run，也可以在Maven管理中使用test进行测试运行。