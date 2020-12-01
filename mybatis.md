 # mybatis

## 1.mybatis 入门配置

### 1.1.pom.xml 配置

```xml
<dependency>
  <groupId>org.mybatis</groupId>
  <artifactId>mybatis</artifactId>
  <version>x.x.x</version> //3.3.0
</dependency>
<dependency>
   <groupId>mysql</groupId>
   <artifactId>mysql-connector-java</artifactId>
   <version>8.0.12</version>
   <scope>runtime</scope>
 </dependency>
```

### 1.2.mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <property name="driver" value="${driver}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${username}"/>
        <property name="password" value="${password}"/>
      </dataSource>
    </environment>
  </environments>
  <mappers>
    <mapper resource="org/mybatis/example/BlogMapper.xml"/>
  </mappers>
</configuration>
```

### 1.3.jdbc.properties

```properties
jdbc.driverClassName=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/booksystem?useSSL=FALSE&serverTimezone=UTC&allowPublicKeyRetrieval=true
jdbc.username=root
jdbc.password=123456
```

### 1.4.mapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="dao.UserDao">
    <select id="getUserById" resultType="po.User" parameterType="INTEGER">
        select * from user where id = #{id}
    </select>
    <insert id="insertUser" parameterType="po.User">
        insert into user value(#{id},#{name},#{password},#{detail})
    </insert>
</mapper>
```

### 1.5.maven加载mapper.xml

```xml
<build>
		<resources>
            <resource>
            	<!--填写主文件夹 -->
                <directory>src/main/java</directory>
                <includes>
                <!--使打包包含这2种配置文件 -->
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <!--不使用过滤器 -->
                <filtering>false</filtering>
            </resource>
        </resources>
	</build>
```

## 2.MyBatis配置详解

### 2.1 properties配置

```xml
<properties resource="org/mybatis/example/config.properties">
  <property name="username" value="dev_user"/>
  <property name="password" value="F2Fa3!33TYyg"/>
</properties> 配置文件中优先级大于properties中配置属性
```

### 2.2 设置（setting）

```xml
<settings>
  <setting name="cacheEnabled" value="true"/> #开启二级缓存
  <setting name="lazyLoadingEnabled" value="true"/> #延时加载
  <setting name="multipleResultSetsEnabled" value="true"/> #多种返回结果
  <setting name="useColumnLabel" value="true"/>   #列明对应属性
  <setting name="useGeneratedKeys" value="false"/>	
  <setting name="autoMappingBehavior" value="PARTIAL"/>
  <setting name="autoMappingUnknownColumnBehavior" value="WARNING"/>
  <setting name="defaultExecutorType" value="SIMPLE"/>
  <setting name="defaultStatementTimeout" value="25"/>
  <setting name="defaultFetchSize" value="100"/>
  <setting name="safeRowBoundsEnabled" value="false"/>
  <setting name="mapUnderscoreToCamelCase" value="false"/>
  <setting name="localCacheScope" value="SESSION"/>
  <setting name="jdbcTypeForNull" value="OTHER"/>
  <setting name="lazyLoadTriggerMethods" value="equals,clone,hashCode,toString"/>
</settings>
```

### 2.3 类型别名（typeAliases）

```xml
<typeAliases>
  <typeAlias alias="Author" type="domain.blog.Author"/>
  <typeAlias alias="Blog" type="domain.blog.Blog"/>
  <typeAlias alias="Comment" type="domain.blog.Comment"/>
  <typeAlias alias="Post" type="domain.blog.Post"/>
  <typeAlias alias="Section" type="domain.blog.Section"/>
  <typeAlias alias="Tag" type="domain.blog.Tag"/>
</typeAliases>
或者
<typeAliases>
  <package name="domain.blog"/>
</typeAliases>
```

### 2.4 插件（暂时不管）

### 2.5 环境配置

```xml
<environments default="development">
  <environment id="development">
    <transactionManager type="JDBC|MANAGED"> TransactionManager 表示mybatis的事务类型
        JDBC – 这个配置直接使用了 JDBC 的提交和回滚设施，它依赖从数据源获得的连接来管理事务作用域
        MANAGED现在基本不使用了，EJB用的技术
         如果你正在使用 Spring + MyBatis，则没有必要配置事务管理器，因为 Spring 模块会使用自带的管理器来覆盖前面的配置。
      <property name="..." value="..."/>
    </transactionManager>
    <dataSource type="POOLED|UNPOOLED|JNDI">
      <property name="driver" value="${driver}"/>
      <property name="url" value="${url}"/>
      <property name="username" value="${username}"/>
      <property name="password" value="${password}"/>
    </dataSource>
  </environment>
</environments>
```

其中环境可以有多套环境，每一套环境有一个ID，可在enviroments中default属性指定使用那个ID

## 3.xml映射器

### 3.1 顶级元素

- `cache` – 该命名空间的缓存配置。
- `cache-ref` – 引用其它命名空间的缓存配置。
- `resultMap` – 描述如何从数据库结果集中加载对象，是最复杂也是最强大的元素。
- `sql` – 可被其它语句引用的可重用语句块。
- `insert` – 映射插入语句。
- `update` – 映射更新语句。
- `delete` – 映射删除语句。
- `select` – 映射查询语句。

### 3.2.select(对应sql中的select语句)

```xml
<select
  id="selectPerson" //对应接口中的方法
  parameterType="int"    //入参 
  resultType="hashmap"     //返回参数
  resultMap="personResultMap" //返回参数，用一个在`resultMap` 中定义的map类
  flushCache="false"         //设置为true的话执行语句会将一级缓存和二级缓存清除
  useCache="true"			 //设置为true使用一级和二级缓存，将会导致本条语句的结果保存起来
  timeout="10"				//这个设置是在抛出异常之前，驱动程序等待数据库返回请求结果的秒数。默认值为未设置（unset）（依赖数据库驱动）
  fetchSize="256"       //这是一个给驱动的建议值，尝试让驱动程序每次批量返回的结果行数等于这个设置值。 默认值为未设置（unset）（依赖驱动）
  statementType="PREPARED"  可选 STATEMENT，PREPARED 或 CALLABLE。这会让 MyBatis 分别使用 Statement，PreparedStatement 或 CallableStatement，默认值：PREPARED。
  resultSetType="FORWARD_ONLY">
<select>
```

### 3.3 insert,update,delete

```xml
<insert
  id="insertAuthor"  
  parameterType="domain.blog.Author"  
  flushCache="true"  
  statementType="PREPARED"
  keyProperty=""
  keyColumn=""
  useGeneratedKeys=""	（仅适用于 insert 和 update）这会令 MyBatis 使用 JDBC 的 getGeneratedKeys 方法来取出由数据库内部生成的主键（比如：像 MySQL 和 SQL Server 这样的关系型数据库管理系统的自动递增字段），默认值：false
  timeout="20">

<update
  id="updateAuthor"
  parameterType="domain.blog.Author"
  flushCache="true"  // 默认为true
  statementType="PREPARED"
  timeout="20">

<delete
  id="deleteAuthor"
  parameterType="domain.blog.Author"
  flushCache="true"
  statementType="PREPARED"
  timeout="20">
```

  使用insert中的自动生成主键

 

```xml
 <insert id="insertUsers" parameterType="user1" useGeneratedKeys="true">
        insert into user(name,password,detail) value(#{name},#{password},#{detail})
    </insert>
```

### 3.4 ResultMap(懂就好)

### 3.5缓存

#### 3.5.1 一级缓存

```java
import dao.UserDao;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import po.User;
import java.io.InputStream;

public class TestCache {
    public static void main(String[] args) throws Exception {
        String resource = "mybatis-config.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        SqlSession sqlSession = sqlSessionFactory.openSession();
        UserDao userDao = sqlSession.getMapper(UserDao.class);
        User user1 = userDao.getUserById(1);
        user1.setName("wanghaofeng");
        user1.setPassword("682503");
        User user2 = userDao.getUserById(1);
        System.out.println(user2);
        // 当用同一个 sqlSession 使用sqlSession调用接口得到一个对象时，这个对象会被放在缓存中，即使改变这个对象的属性，缓存也不会消失，再次调用接口还是这个对象，比如下面结果返回true，此为一级缓存
        //一级缓存只适用于默认只支持select语句，不支持其他语句
        System.out.println(user1.equals(user2));
        userDao.updateUser(user1);  //update insert delete 会清空一级缓存，仅限sqlSession
       //sqlSession.clearCache(); // 可清空一级缓存
        User user3 = userDao.getUserById(1); 
        System.out.println(user3);
        System.out.println(user2.equals(user3));
    }
}
```

#### 3.5.2 二级缓存













