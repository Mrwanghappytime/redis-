# maven的使用

### 1.maven命令

1. **-v查询Maven版本**
2. **compile:编译**
3. **test：测试项目**
4. **package:打包，将项目打成jar包**
5. **clean：删除target文件夹**
6. **install：安装，将当前项目放到MAVEN的本地仓库中，供其他项目使用**

### 2.maven中的定义

- **聚合**

  1. **什么是聚合**

     **将多个项目同时运行就是聚合**

  2. **如何实现聚合**

     **只需在pom中作如下配置**

     ```xml
     
     <modules>
         <module>web-connection-pool</module>
         <module>web-java-crawler</module>
     </modules>
     ```

- **继承**

  1. **什么是继承**

     ​	**在聚合多个项目时，如果这些被聚合的项目中需要引入相同的Jar，那么可以将这些Jar写入父pom中，各个子项目继承该pom即可。**

  2. **如何实现继承**

     - **父pom文件**

       ```xml
       <dependencyManagement>
           <dependencies>
                 <dependency>
                   <groupId>cn.missbe.web.search</groupId>
                   <artifactId>resource-search</artifactId>
                   <packaging>pom</packaging>
                   <version>1.0-SNAPSHOT</version>
                 </dependency> 
           </dependencies>
       </dependencyManagement>
       ```

     - **子pom配置**

       ```xml
       
       <parent>
             <groupId>父pom所在项目的groupId</groupId>
             <artifactId>父pom所在项目的artifactId</artifactId>
             <version>父pom所在项目的版本号</version>
       </parent>
        <parent>
             <artifactId>resource-search</artifactId>
             <groupId>cn.missbe.web.search</groupId>
             <version>1.0-SNAPSHOT</version>
       </parent>
       ```

       

  

  