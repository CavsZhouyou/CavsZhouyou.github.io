---
title: 从零搭建Spring+SpringMVC+Mybatis
date: 2018-02-10 11:55:26
tags:
 - 后端
 - Spring
 - SpringMVC
 - Mybatis
categories: 后端
---

![从零搭建 Spring+SpringMVC+Mybatis 文章配图](http://p2p4htzmu.bkt.clouddn.com/peitu4.jpg)

## 写在前面

最近在写一个图片分享网站类型的小 demo，后端采用了 Spring+SpringMVC+Mybatis 来进行框架的搭建。因为一直写前端，对后端的一些实现一直不太了解，也导致在项目开发中后端运行出现了什么问题，我也不能及时察觉到。所以希望通过写这个小 demo，能够了解后端项目是如何创建、管理和实现的。所以先总结一下如何从零搭建 Spring+SpringMVC+Mybatis。

<!-- more -->

## 开发环境

* IntelliJ IDEA 2017.3.3 x64
* apache-maven-3.3.9
* apache-tomcat-9.0.4

具体下载方法及使用可以自行百度一下。

## 创建 Maven Web 项目

第一步我们需要创建一个基本的使用 Maven 管理 的 web 项目。

1. 打开 idea 之后，在左上角点击 file -> new -> project，在弹出的 new project 页面，选择 maven -> 勾选 Create from artifactype -> 选中 webapp（注意：这个地方有两个 app，选择 maven-archetype-webapp）, next 。如下图

    ![配图4-2](http://p2p4htzmu.bkt.clouddn.com/peitu4-2.png)

2. 接下来就是填写 groupId 和 archetypeId 了，点击 next。这里需要注意的是 GroupID 实际对应 JAVA 的包的结构，是 main 目录里 java 的目录结构。 ArtifactID 就是项目的唯一的标识符，实际对应项目的名称，就是项目根目录的名称。如下图

    ![配图4-3](http://p2p4htzmu.bkt.clouddn.com/peitu4-3.png)

3. 然后下一步，首先我们需要配置本地 maven 的安装路径，然后点击右侧的加号。如下图

    ![配图4-4](http://p2p4htzmu.bkt.clouddn.com/peitu4-4.png)

4. 输入对应的 name 和 value，然后点击 OK。点击 Next。这里需要注意 archetypeCatalog 表示插件使用的 archetype 元数据，不加这个参数时默认为 remote，local，即中央仓库 archetype 元数据，由于中央仓库的 archetype 太多了，所以导致很慢，指定 internal 来表示仅使用内部元数据。

    ![配图 4-5](http://p2p4htzmu.bkt.clouddn.com/peitu4-5.png)

5. 然后填写 project name 和 module name，并选择项目所在的路径。注意 module name 默认和 project name 一样的，但是如果这个项目只有这一个 module，不改无所谓的，如果有很多的 module，那肯定是修改一下。 填完点击 finish。如下图

    ![配图 4-6](http://p2p4htzmu.bkt.clouddn.com/peitu4-6.png)

6. 在创建过程中，这时候在 Idea 主窗口的右下角部分,我们可以看到一个滚动条在执行,说明我们的项目正在 build 中.右上角有一个提示框如下图所示:

    ![配图 4-7](http://p2p4htzmu.bkt.clouddn.com/peitu4-7.png)

    这个提示框大概意思是:Maven 项目需要被导入.我们选择 Enable Auto-Import(自动导入)

7. 当我们看见如下目录时，说明我们的项目已经创建好了。如下图

    ![配图 4-8](http://p2p4htzmu.bkt.clouddn.com/peitu4-8.png)

## 修改项目目录

创建完项目后，我们可以发现我们项目的目录不全，我们需要自己手动添加。

初始结构图如下:

![配图 4-9](http://p2p4htzmu.bkt.clouddn.com/peitu4-9.png)

修改后目录如下:

![配图 4-10](http://p2p4htzmu.bkt.clouddn.com/peitu4-10.png)

需要的主体目录对应解释:

```
src                            //源码、资源等文件的根目录
  -main                        //项目开发主要目录之一,可以放java代码和一些资源文件.
    -java                      //开发的主要的java代码存放目录
      -com.photoshareweb        //我的应用程序的包名
        -controller            //控制类代码
        -dao                   //mybatis-generator生成的dao映射类
        -entitys               //mybatis-generator生成的实体类
        -mapping               //mybatis-generator生成的映射文件
        -service               //服务器类代码
    -resources                 //开发中的主要的资源文件存放目录
    -webapp                    //web页面和其他web配置、资源文件存放目录
  -test                        //项目开发中的测试模块存放路径,包含java代码和资源文件.
    -java                      //测试代码存放目录
    -resources                 //测试资源文件存放目录
```

下面我们讲一下如何创建 main 下的 java 目录

1. 我们先右键点击 main 目录,接着选中 New→Directory,在弹出的对话框中输入 java。

2. 接着我们需要把 java 目录标记为源文目录。

    ![配图 4-11](http://p2p4htzmu.bkt.clouddn.com/peitu4-11.png)

3. 在 java 目录下创建包，这里有一个很容易尴尬的地方就是，有的情况下我们创建的目录不能自动识别，然后直接会生成一串。如果遇到这个问题，大家可以参考一下这个[解决办法](https://jingyan.baidu.com/article/e5c39bf5d8abc039d660335f.html)。

创建好相应目录后，一定要记得将该目录标记为相应的类型，如果是普通目录则不需要标记。

## 配置 pom.xml 文件

我们根据项目引入相应的资源库，我的具体配置如下:

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.photoshareweb</groupId>
    <artifactId>PhotoShareWeb</artifactId>
    <packaging>war</packaging>
    <version>1.0-SNAPSHOT</version>
    <name>PhotoShareWeb Maven Webapp</name>
    <url>http://maven.apache.org</url>
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>3.8.1</version>
            <scope>test</scope>
        </dependency>
        <!-- 1.日志 -->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>${log4j.version}</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>${slf4j.version}</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>${slf4j.version}</version>
        </dependency>


        <!-- 2.数据库 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>${mysql.version}</version>
            <scope>runtime</scope>
        </dependency>
        <!--druid==>阿里巴巴数据库连接池-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>${com.alibaba.druid.version}</version>
        </dependency>

        <!-- DAO: MyBatis -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>${com.mybatis.mybatis.version}</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>${com.mybatis.mybatis_spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis.generator</groupId>
            <artifactId>mybatis-generator-core</artifactId>
            <version>${mybatis-generator.version}</version>
        </dependency>

        <!-- 3.Servlet web -->
        <dependency>
            <groupId>taglibs</groupId>
            <artifactId>standard</artifactId>
            <version>1.1.2</version>
        </dependency>
        <dependency>
            <groupId>jstl</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>
        <!--json工具-->
        <dependency>
            <groupId>com.google.code.gson</groupId>
            <artifactId>gson</artifactId>
            <version>${com.google.gson.version}</version>
        </dependency>
        <!--Servlet版本设置-->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>${javax.servlet.version}</version>
        </dependency>

        <!-- 4.Spring -->
        <!-- 1)Spring核心 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>${org.springframework.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>${org.springframework.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${org.springframework.version}</version>
        </dependency>
        <!-- 2)Spring DAO层 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>${org.springframework.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>${org.springframework.version}</version>
        </dependency>
        <!-- 3)Spring web -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>${org.springframework.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>${org.springframework.version}</version>
        </dependency>
        <!-- 4)Spring test -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>${org.springframework.version}</version>
        </dependency>

        <!-- redis客户端:Jedis -->
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>${redis.clients.version}</version>
        </dependency>
        <dependency>
            <groupId>com.dyuproject.protostuff</groupId>
            <artifactId>protostuff-core</artifactId>
            <version>${com.dyuproject.protostuff.version}</version>
        </dependency>
        <dependency>
            <groupId>com.dyuproject.protostuff</groupId>
            <artifactId>protostuff-runtime</artifactId>
            <version>${com.dyuproject.protostuff.version}</version>
        </dependency>

        <!-- Map工具类 -->
        <dependency>
            <groupId>commons-collections</groupId>
            <artifactId>commons-collections</artifactId>
            <version>3.2.2</version>
        </dependency>

        <!--文件上传工具-->
        <dependency>
            <groupId>commons-fileupload</groupId>
            <artifactId>commons-fileupload</artifactId>
            <version>1.3.2</version>
        </dependency>
        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>2.5</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>RELEASE</version>
        </dependency>

    </dependencies>

    <build>
        <finalName>PhotoShareWeb</finalName>
        <plugins>
            <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>${mybatis-generator.version}</version>
                <!-- 数据库驱动  -->
                <dependencies>
                    <dependency>
                        <groupId>mysql</groupId>
                        <artifactId>mysql-connector-java</artifactId>
                        <version>${mysql.version}</version>
                    </dependency>
                </dependencies>
                <!-- 自动生成 -->
                <executions>
                    <execution>
                        <id>Generate MyBatis Artifacts</id>
                        <goals>
                            <goal>generate</goal>
                        </goals>
                        <configuration>
                            <!-- 指定文件位置好像不起作用，始终默认读取src/main/resources/generatorConfig.xml文件 -->
                            <configurationFile>src/main/resources/generatorConfig.xml</configurationFile>
                            <!--允许移动生成的文件-->
                            <verbose>true</verbose>
                            <!--允许覆盖生成的文件-->
                            <overwrite>true</overwrite>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.5</source>
                    <target>1.5</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
        </plugins>
        <resources>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                    <include>**/*.tld</include>
                </includes>
                <!-- 这里是false，用true会报 数据库连接 错误 -->
                <filtering>false</filtering>
            </resource>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                    <include>**/*.tld</include>
                </includes>
                <filtering>false</filtering>
            </resource>
        </resources>
    </build>

    <!-- 配置可变版本号,也就是常说的版本管理 （Spring、SpringMvc、Mybatis、Gson、Druid） -->
    <!-- 要针对某个依赖进行升级的时候只需要更改下面对应的版本号 -->
    <!-- 在上面使用版本号的时候需要用固定格式,如: ${包名.version} -->
    <properties>
        <slf4j.version>1.7.7</slf4j.version>
        <log4j.version>1.2.17</log4j.version>
        <org.apache.slf4j.log4j.version>1.5.6</org.apache.slf4j.log4j.version>
        <mysql.version>5.1.37</mysql.version>
        <com.alibaba.druid.version>1.0.25</com.alibaba.druid.version>
        <com.mybatis.mybatis.version>3.4.1</com.mybatis.mybatis.version>
        <com.mybatis.mybatis_spring.version>1.3.0</com.mybatis.mybatis_spring.version>
        <com.google.gson.version>2.7</com.google.gson.version>
        <javax.servlet.version>3.1.0</javax.servlet.version>
        <org.springframework.version>4.3.2.RELEASE</org.springframework.version>
        <redis.clients.version>2.7.3</redis.clients.version>
        <com.dyuproject.protostuff.version>1.0.8</com.dyuproject.protostuff.version>
        <developer.organization><![CDATA[scengine]]></developer.organization>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <mybatis-generator.version>1.3.2</mybatis-generator.version>
    </properties>

</project>
```

## 整合 Spring+SpringMVC+Mybatis

刚才我们已经配置好了基本的目录，也引入了相应的项目资源库，接下我们需要对 Spring+SpringMvc+Mybatis 进行整合。

我们先打开 webapp 目录下面的 WEB-INF 目录中的 web.xml 文件,web.xml 文件是整合 web 项目的配置中心.我们在 web.xml 中加入如下内容:

```xml
<!DOCTYPE web-app PUBLIC
"-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
"http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
                    http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
        version="3.1" metadata-complete="true">

    <!--默认的首页-->
    <welcome-file-list>
        <welcome-file>index.html</welcome-file>
        <welcome-file>index.htm</welcome-file>
        <welcome-file>index.jsp</welcome-file>
        <welcome-file>default.html</welcome-file>
        <welcome-file>default.htm</welcome-file>
        <welcome-file>default.jsp</welcome-file>
    </welcome-file-list>

<!-- 如果是用maven命令生成的xml，需要修改servlet版本为3.1 -->
    <!-- 配置DispatcherServlet -->
    <servlet>
        <display-name>MyDemo</display-name>    <!-- 项目名称 -->
        <servlet-name>mvc-dispatcher</servlet-name> <!-- mvc调度器 -->
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!-- 配置springMVC需要加载的配置文件
            spring-dao.xml,spring-service.xml,spring-web.xml
            Mybatis - > spring -> springmvc
        -->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring/spring-*.xml</param-value>
        </init-param>
    </servlet>
    <servlet-mapping>
        <servlet-name>mvc-dispatcher</servlet-name>
        <!-- 默认匹配所有的静态资源,此处配置出错,会产生错误500 -->
        <url-pattern>/js/*</url-pattern>
        <url-pattern>/css/*</url-pattern>
        <url-pattern>/images/*</url-pattern>
        <url-pattern>/fonts/*</url-pattern>
    </servlet-mapping>


    <!--druid ==> WEB方式监控配置-->
    <servlet>
        <servlet-name>DruidStatView</servlet-name>
        <servlet-class>com.alibaba.druid.support.http.StatViewServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>DruidStatView</servlet-name>
        <url-pattern>/druid/*</url-pattern>
    </servlet-mapping>
    <filter>
        <filter-name>druidWebStatFilter</filter-name>
        <filter-class>com.alibaba.druid.support.http.WebStatFilter</filter-class>
        <init-param>
            <param-name>exclusions</param-name>
            <param-value>/public/*,*.js,*.css,/druid*,*.jsp,*.swf</param-value>
        </init-param>
        <init-param>
            <param-name>principalSessionName</param-name>
            <param-value>sessionInfo</param-value>
        </init-param>
        <init-param>
            <param-name>profileEnable</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>druidWebStatFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <error-page>
        <error-code>404</error-code>
        <location>/static/view/404.html</location>
    </error-page>

</web-app>
```

在配置好 web.xml 文件后会报错，是因为我们有一些文件还没有创建。在 IDEA 中我们只需选中报错处，按下 Alt+Enter 即可生成相应的目录或文件。

* 我们首先需要在 src→main→resources 下创建一个 spring 目录，然后在 spring 目录下创建 spring 相关的控制文件。

在 spring 目录下创建 spring-dao.xml 文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd">
    <!-- 配置整合mybatis过程 -->
    <!-- 1.配置数据库相关参数properties的属性：${url} -->
    <!-- 使用数据库配置文件解耦 -->
    <context:property-placeholder location="classpath:jdbc.properties"/>

    <!-- 下面的druid配置都是基本配置,具体优化设置可以上网查询,也可以去github上面直接搜索druid -->
    <!-- 2.数据库连接池 -->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource"
          init-method="init" destroy-method="close">
        <!-- 配置连接池属性 -->
        <property name="driverClassName" value="${jdbc.driver}"/>
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>

        <!-- 配置初始化大小、最小、最大 -->
        <property name="initialSize" value="1" />
        <property name="minIdle" value="1" />
        <property name="maxActive" value="10" />

        <!-- 配置获取连接等待超时的时间 -->
        <property name="maxWait" value="10000" />

        <!-- 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒 -->
        <property name="timeBetweenEvictionRunsMillis" value="60000" />

        <!-- 配置一个连接在池中最小生存的时间，单位是毫秒 -->
        <property name="minEvictableIdleTimeMillis" value="300000" />

        <property name="testWhileIdle" value="true" />

        <!-- 这里建议配置为TRUE，防止取到的连接不可用 -->
        <property name="testOnBorrow" value="true" />
        <property name="testOnReturn" value="false" />

        <!-- 打开PSCache，并且指定每个连接上PSCache的大小 -->
        <property name="poolPreparedStatements" value="true" />
        <property name="maxPoolPreparedStatementPerConnectionSize"
                  value="20" />

        <!-- 这里配置提交方式，默认就是TRUE，可以不用配置 -->

        <property name="defaultAutoCommit" value="true" />

        <!-- 验证连接有效与否的SQL，不同的数据配置不同 -->
        <property name="validationQuery" value="select 1 " />
        <property name="filters" value="stat" />
        <property name="proxyFilters">
            <list>
                <ref bean="logFilter" />
            </list>
        </property>
    </bean>

    <!-- 3.配置SqlSessionFactory对象 -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!-- 注入数据库连接池 -->
        <property name="dataSource" ref="dataSource"/>
        <!-- 配置MyBaties全局配置文件:mybatis-config.xml -->
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
        <!-- 扫描entity包 使用别名 -->
        <!-- cn.acheng1314是我的应用程序的包名,你们需要使用你们自己的包名,也就是前面我们提到过的GroupId -->
        <property name="typeAliasesPackage" value="com.photoshareweb.entitys"/>
        <!-- 扫描sql配置文件:mapper需要的xml文件 -->
        <property name="mapperLocations" value="classpath:com/photoshareweb/mapping/*.xml"/>
    </bean>

    <!-- 4.配置扫描Dao接口包，动态实现Dao接口，注入到spring容器中 -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!-- 注入sqlSessionFactory -->
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
        <!-- 给出需要扫描Dao接口包 -->
        <property name="basePackage" value="com.photoshareweb.dao"/>
    </bean>

    <!-- 上面的druid的配置 -->
    <bean id="logFilter" class="com.alibaba.druid.filter.logging.Slf4jLogFilter">
        <property name="statementExecutableSqlLogEnable" value="false" />
    </bean>
</beans>
```

上面的配置中,肯定也会出现报错的情况,这时候我们只需要选中报错的地方按下 Alt+Enter 就能生成相关的资源.

在 spring 目录下创建 spring-service.xml 文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd
    http://www.springframework.org/schema/tx
    http://www.springframework.org/schema/tx/spring-tx.xsd">

    <!-- 扫描service包下所有使用注解的类型 -->
    <!-- com.photoshareweb为我们应用的包名,当然也是我们前面提到过的GroupId -->
    <context:component-scan base-package="com.photoshareweb.service" />

    <!-- 配置事务管理器 -->
    <bean id="transactionManager"
          class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <!-- 注入数据库连接池 -->
        <property name="dataSource" ref="dataSource" />
    </bean>

    <!-- 配置基于注解的声明式事务 -->
    <tx:annotation-driven transaction-manager="transactionManager" />
</beans>
```

在 spring 目录下创建 spring-web.xml 文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd
    http://www.springframework.org/schema/mvc
    http://www.springframework.org/schema/mvc/spring-mvc-3.0.xsd">
    <!-- 配置SpringMVC -->
    <!-- 1.开启SpringMVC注解模式 -->
    <!-- 简化配置：
        (1)自动注册DefaultAnootationHandlerMapping,AnotationMethodHandlerAdapter
        (2)提供一些列：数据绑定，数字和日期的format @NumberFormat, @DateTimeFormat, xml,json默认读写支持
    -->
    <mvc:annotation-driven/>
    <!-- 2.静态资源默认servlet配置
        (1)加入对静态资源的处理：js,gif,png
        (2)允许使用"/"做整体映射
     -->
    <mvc:resources mapping="/css/**" location="/static/css/" />
    <mvc:resources mapping="/images/**" location="/static/images/" />
    <mvc:resources mapping="/view/**" location="/static/view/" />
    <mvc:default-servlet-handler/>

    <!-- 3.配置jsp 显示ViewResolver -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="viewClass" value="org.springframework.web.servlet.view.JstlView"/>
        <property name="prefix" value="/WEB-INF/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

    <!-- 4.扫描web相关的bean配置 -->
    <context:component-scan base-package="com.photoshareweb">
        <!-- 制定扫包规则 ,只扫描使用@Controller注解的JAVA类 -->
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>

</beans>
```

* 现在我们会发现我们的 jdbc.properties 和 mybatis-config.xml 文件都是空的,我们需要继续写入内容。

jdbc.properties 是数据库连接的配置文件.如下:

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/photoshareweb
jdbc.username=数据库用户名
jdbc.password=数据库用户名对应的密码
```

上面的 jdbc.driver 为数据库连接的驱动,jdbc.url 为数据库的连接地址.

mybatis-config.xml 顾名思义是 mybatis 的配置文件,如下:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!-- 配置全局属性 -->
    <settings>
        <!-- 使用jdbc的getGeneratedKeys获取数据库自增主键值 -->
        <setting name="useGeneratedKeys" value="true" />

        <!-- 使用列别名替换列名 默认:true -->
        <setting name="useColumnLabel" value="true" />

        <!-- 开启驼峰命名转换:Table{create_time} -> Entity{createTime} -->
        <setting name="mapUnderscoreToCamelCase" value="true" />
    </settings>
</configuration>
```

* 我们还需要为日志记录添加一个配置文件，我们需要在资源目录下面添加一个默认的配置文件 log4j2.xml 即可，如下:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- status=debug 可以查看log4j的装配过程 -->
<configuration status="off" monitorInterval="1800">
    <properties>
        <!--日志目录-->
        <property name="LOG_HOME">/logs/webLog</property>
        <!-- 日志备份目录 -->
        <property name="BACKUP_HOME">{LOG_HOME}/backup</property>
        <property name="STAT_NAME">stat</property>
        <property name="SERVER_NAME">global</property>
    </properties>
    <appenders>
        <!-- 定义控制台输出 -->
        <Console name="Console" target="SYSTEM_OUT" follow="true">
            <PatternLayout pattern="%date{yyyy-MM-dd HH:mm:ss.SSS} %level [%thread][%file:%line] - %msg%n"/>
        </Console>
        <!-- 程序员调试日志 -->
        <RollingRandomAccessFile name="DevLog" fileName="${LOG_HOME}/${SERVER_NAME}"
                                 filePattern="${LOG_HOME}/${SERVER_NAME}.%d{yyyy-MM-dd-HH}.log">
            <PatternLayout pattern="%date{yyyy-MM-dd HH:mm:ss.SSS} %level [%thread][%file:%line] - %msg%n"/>
            <Policies>
                <TimeBasedTriggeringPolicy interval="1" modulate="true"/>
            </Policies>
        </RollingRandomAccessFile>
        <!-- 游戏产品数据分析日志 -->
        <RollingRandomAccessFile name="ProductLog"
                                 fileName="${LOG_HOME}/${SERVER_NAME}_${STAT_NAME}"
                                 filePattern="${LOG_HOME}/${SERVER_NAME}_${STAT_NAME}.%d{yyyy-MM-dd-HH}.log">
            <PatternLayout
                    pattern="%date{yyyy-MM-dd HH:mm:ss.SSS} %level [%thread][%file:%line] - %msg%n"/>
            <Policies>
                <TimeBasedTriggeringPolicy interval="1"
                                           modulate="true"/>
            </Policies>
        </RollingRandomAccessFile>
    </appenders>
    <loggers>
        <!-- 3rdparty Loggers -->
        <logger name="org.springframework.core" level="info">
        </logger>
        <logger name="org.springframework.beans" level="info">
        </logger>
        <logger name="org.springframework.context" level="info">
        </logger>
        <logger name="org.springframework.web" level="info">
        </logger>
        <logger name="org.jboss.netty" level="warn">
        </logger>
        <logger name="org.apache.http" level="warn">
        </logger>
        <logger name="com.mchange.v2" level="warn">
        </logger>
        <!-- Game Stat  logger -->
        <logger name="com.u9.global.service.log" level="info"
                additivity="false">
            <appender-ref ref="ProductLog"/>
        </logger>
        <!-- Root Logger -->
        <root level="DEBUG">
            <appender-ref ref="DevLog"/>
            <appender-ref ref="Console"/>
        </root>
    </loggers>
</configuration>
```

* 因为我们使用的是 mybatis-generator 来自动生成实体类和映射文件，因此我们还需要在资源目录下添加 mybatis_generator.properties 和 generatorConfig 两个文件。

mybatis_generator.properties 是 mybatis 连接数据库的文件，如下:

```properties
jdbc_driver=com.mysql.jdbc.Driver
jdbc_url=jdbc:mysql://localhost:3306/photoshareweb
jdbc_username=数据库用户名
jdbc_password=数据库用户名对应的密码

targetProject=src/main/java

modelPackage=com.photoshareweb.entitys
sqlMapperPackage=com.photoshareweb.mapping
daoMapperPackage=com.photoshareweb.dao
```

generatorConfig 是 mybatis-generator 的配置文件，如下:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<!-- 该配置文件说明插件需要如何生成，以及生成对应的包名，路径等信息。
      还有重要的就是我们要生成的实体类所对应的的表或者试图 -->
<generatorConfiguration>
    <properties resource="mybatis_generator.properties" />
    <context id="MBG" targetRuntime="MyBatis3" defaultModelType="conditional">
        <!-- 注意以下标签的顺序:property*,plugin*,commentGenerator?,jdbcConnection,
                javaTypeResolver?,javaModelGenerator,sqlMapGenerator?,
                javaClientGenerator?,table+ -->
        <!--<plugin type="org.mybatis.generator.plugins.EqualsHashCodePlugin" />-->
        <!--&lt;!&ndash; 这个插件给由MBG生成的Java模型对象增加了equals和hashCode方法 &ndash;&gt;-->
        <!--<plugin type="org.mybatis.generator.plugins.EqualsHashCodePlugin"/>-->
        <commentGenerator>
            <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
            <property name="suppressAllComments" value="false"/>
            <!-- 不希望生成的注释中包含时间戳 -->
            <property name="suppressDate" value="true" />
            <!-- 是否  自动为每一个生成的类创建一个构造方法-->
            <property name="constructorBased" value="false"/>
        </commentGenerator>
        <!-- 数据库连接 -->
        <jdbcConnection
                driverClass="${jdbc_driver}"
                connectionURL="${jdbc_url}"
                userId="${jdbc_username}"
                password="${jdbc_password}">
        </jdbcConnection>
        <!-- 指定生成的类型为java类型，避免数据库中number等类型字段 -->
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>
        <!-- 生成model模型，对应的包，存放位置可以指定具体的路径,如/ProjectName/src，也可以使用MAVEN来自动生成 -->
        <javaModelGenerator targetPackage="${modelPackage}" targetProject="${targetProject}">
            <!-- 在targetPackage的基础上，根据数据库的schema再生成一层package，最终生成的类放在这个package下，默认为false -->
            <property name="enableSubPackages" value="true"/>
            <!-- 设置是否在getter方法中，对String类型字段调用trim()方法 -->
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>
        <!--对应的xml mapper文件  -->
        <sqlMapGenerator targetPackage="${sqlMapperPackage}" targetProject="${targetProject}" >
            <property name="enableSubPackages" value="true"/>
        </sqlMapGenerator>
        <!-- 对应的dao接口 -->
        <javaClientGenerator type="XMLMAPPER" targetPackage="${daoMapperPackage}" targetProject="${targetProject}" >
            <property name="enableSubPackages" value="true"/>
        </javaClientGenerator>

        <!-- 表名对应生成的实体 -->
        <table tableName="webuser" domainObjectName="User"
         enableCountByExample="false" enableUpdateByExample="false"
        enableDeleteByExample="false" enableSelectByExample="false"
        selectByExampleQueryId="false" />
    </context>
</generatorConfiguration>
```

好啦，到这里具体的配置文件都搞定了！

## 配置 Tomcat

我们想要把项目跑起来，当然还需要配置 Tomcat 服务器啦！

1. 首先我们在主页面的点击右上角点击那个下拉框，再点击 Edit Configurations。

    ![配图4-13](http://p2p4htzmu.bkt.clouddn.com/peitu4-13.png)

2. 然后在弹出的对话框中点击+号，找到 Tomcat Server ，选择 Local.

    ![配图4-14](http://p2p4htzmu.bkt.clouddn.com/peitu4-14.png)

3. 在弹出的对话框中，选择 Deployment,点击右侧的+号，选择 Artifact。

    ![配图4-15](http://p2p4htzmu.bkt.clouddn.com/peitu4-15.png)

4. 在弹出的对话框中选择后缀为:exploded 的选项

    ![配图4-16](http://p2p4htzmu.bkt.clouddn.com/peitu4-16.png)

5. 最后回到 Server 选项卡，配置一下 Tomcat 的基本参数，最后点击 Ok 即可

    ![配图4-17](http://p2p4htzmu.bkt.clouddn.com/peitu4-17.png)

6. 回到主界面点击右上角的 Debug 按钮，如果出现以下界面，就说明服务器启动成功了。

    ![配图4-18](http://p2p4htzmu.bkt.clouddn.com/peitu4-18.png)

## 写在最后

好啦，我们一个最基本的基于 Spring+SpringMVC+Mybatis 搭建的 web 项目就建好了，希望我的总结也能够给你提供一点帮助。

**本篇文章纯属于个人的学习总结，如果文章中出现错误或不严谨的地方，希望大家能够指出，谢谢！**
