1. 修改**pom.xml**文件，添加插件

   ```xml
   <plugin>
       <groupId>org.apache.maven.plugins</groupId>
       <artifactId>maven-assembly-plugin</artifactId>
       <configuration>
           <appendAssemblyId>false</appendAssemblyId>
           <descriptors>
               <!-- 读取assembly.xml文件 -->
               <descriptor>assembly.xml</descriptor>
           </descriptors>
           <!-- 输出在target/dist/目录下 -->
           <outputDirectory>${project.build.directory}/dist/</outputDirectory>
       </configuration>
       <executions>
           <execution>
               <id>make-assembly</id>
               <phase>package</phase>
               <goals>
                   <goal>single</goal>
               </goals>
           </execution>
       </executions>
   </plugin>
   <!-- 打包成jar包，并指定lib和resources文件夹位置 -->
   <plugin>
       <groupId>org.apache.maven.plugins</groupId>
       <artifactId>maven-jar-plugin</artifactId>
       <version>3.0.2</version>
       <configuration>
           <excludes>
               <!--以下文件将不被打入jar包中-->
               <exclude>**/bat/**</exclude>
               <exclude>application.yml</exclude>
           </excludes>
           <archive>
               <manifest>
                   <!-- 入口Main Class -->
                   <mainClass>club.xiaojiawei.ScriptApplication</mainClass>
                   <!-- 依赖的lib -->
                   <classpathPrefix>lib/</classpathPrefix>
                   <addClasspath>true</addClasspath>
               </manifest>
               <manifestEntries>
                   <Class-Path>resources/</Class-Path>
               </manifestEntries>
           </archive>
       </configuration>
   </plugin>
   ```

   

2. 创建**assembly.xml**文件，放入**pom.xml**里规定的位置

   ```xml
   <assembly xmlns="http://maven.apache.org/plugins/maven-assembly-plugin/assembly/1.1.2"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/plugins/maven-assembly-plugin/assembly/1.1.2 http://maven.apache.org/xsd/assembly-1.1.2.xsd">
   
       <id>app</id>
   
       <!--最终输出生成zip和tar.gz文件 -->
       <formats>
   <!--        <format>tar.gz</format>-->
           <format>zip</format>
       </formats>
   
       <includeBaseDirectory>false</includeBaseDirectory>
   
       <dependencySets>
           <dependencySet>
               <!--依赖包的输出目录-->
               <useProjectArtifact>false</useProjectArtifact>
               <outputDirectory>lib</outputDirectory>
               <unpack>false</unpack>
           </dependencySet>
       </dependencySets>
   
       <!--设置需要分离的文件-->
       <fileSets>
           <fileSet>
               <directory>${project.basedir}</directory>
               <outputDirectory/>
               <includes>
                   <include>README*</include>
                   <include>LICENSE*</include>
                   <include>logback-spring.xml</include>
                   <include>NOTICE*</include>
               </includes>
           </fileSet>
           <fileSet>
               <directory>${project.basedir}/src/main/resources</directory>
               <includes>
                   <include>application.yml</include>
               </includes>
               <outputDirectory/>
           </fileSet>
           <fileSet>
               <directory>${project.basedir}/src/main/resources/bat</directory>
               <includes>
                   <include>*.bat</include>
               </includes>
               <outputDirectory/>
           </fileSet>
           <fileSet>
               <directory>${project.build.directory}</directory>
               <outputDirectory/>
               <includes>
                   <include>*.jar</include>
               </includes>
           </fileSet>
       </fileSets>
   </assembly>
   ```

3. 利用Maven插件打包

   ![Screenshot_20231010_183656](https://gitee.com/zergqueen/images/raw/master/myblog/202310101838518.png)

