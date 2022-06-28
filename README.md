# mybatis
1
1.
Persistence: persistence is the preocess to put the data of program between persistence situation to instantaneous situation
1.2. persistence layer: complete persostence work layer.

2.mybatic program:
build--> import mybatis---> code ---> test
-1build a normal maven project
-2.remove src dir
-3.import maven dependency
<!--   mysql     -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.47</version>
        </dependency>
<!--       mybatis -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.2</version>
        </dependency>
<!--       junit -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>

2.2 create a module
code mybatis core config file
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="hsp"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="org/mybatis/example/BlogMapper.xml"/>
    </mappers>
</configuration>

code mybatis tool class

2.4 test
solve the file in the resources and java dir cannot be read by maven
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\
<build>
        <resources>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
        </resources>
    </build>
\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\

build mybatis steps:
1.util package
package com.qilun.utils;
//sqlSession Factory  --> sqlSession
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import java.io.IOException;
import java.io.InputStream;
public class MybatisUtils {
    private static SqlSessionFactory sqlSessionFactory;
    static {
        try {//first step of using mybatis.get Session factory obj
            String resource = "mybatis-config.xml";//remind the xml
            InputStream inputStream = Resources.getResourceAsStream(resource);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    //since we have sqlSessionFactory, we can get SqlSession instance
    //sqlSession include all methods of operating db
    public static SqlSession getSqlSession(){
        SqlSession sqlSession = sqlSessionFactory.openSession();
        return sqlSession;
    }
}

2.config xml in the resources dir
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="hsp"/>
            </dataSource>
        </environment>
    </environments>
<!--    each mapper.xml need -->
    <mappers>
        <mapper resource="com/qilun/dao/UserMapper.xml"/>
    </mappers>
</configuration>

3.pojo obj class
package com.qilun.pojo;

public class User {
    private int id;
    private String name;
    private String pwd;

    public User() {
    }

    public User(int id, String name, String pwd) {
        this.id = id;
        this.name = name;
        this.pwd = pwd;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPwd() {
        return pwd;
    }

    public void setPwd(String pwd) {
        this.pwd = pwd;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", pwd='" + pwd + '\'' +
                '}';
    }
}

4.interface in dao
package com.qilun.dao;

import com.qilun.pojo.User;

import java.util.List;

public interface UserDao {
    List<User> getUserList();
}


5.xml link to the interface
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--name space log a mapper interface-->
<mapper namespace="com.qilun.dao.UserDao">
<!--    select -->
    <select id="getUserList" resultType="com.qilun.pojo.User">
        select * from mybatis.user
    </select>
</mapper>
6.test
package com.qilun.dao;

import com.qilun.pojo.User;
import com.qilun.utils.MybatisUtils;
import org.apache.ibatis.session.SqlSession;
import org.junit.Test;

import java.util.List;

public class UserDaoTest {
    @Test
    public void test(){
        //get sqlSession obj
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        try {
            //execute sql
            UserDao userDao = sqlSession.getMapper(UserDao.class);
            List<User> userList = userDao.getUserList();
            for (User user : userList) {
                System.out.println(user);
            }

        }catch (Exception e){
            e.printStackTrace();
        }finally {
            //close
            sqlSession.close();
        }


    }
}

3.CRUD
        
        1.name space
        the namespace of the package should be same with the Dao/mapper
        2.select selece language:
        id: same with the method name of the name space.
        resultType: sql return result
        add, delete and updat need commit;

4.MAP
        when too many attibutes in the map, we need to use map.

5.like research:
	-1.when executing java code, use % %
	-2.use % in the sql 

  4.config search
	1.core config file: configuration:properties  settings  typealiases
	typeHandlers objectFactory plugins environments mappers
	

	2.mybatis can have many environments, but each time can only use one environment
        
        
 properties: we can use properties to import config file db.properties
	in xml, all the files must have ordered queue
	1.code a db properties file
	2.import outside file, out config file is priority first.
<properties resource="db.properties">

    </properties>

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
--------------------------------------------------------
typealiases : set a short name for the java type
<!--alias one-->
    <typeAliases>
        <typeAlias type="com.qilun.pojo.User" alias="User"/>
    </typeAliases>
-----------------------------------------

 LOG:
STDOUT_LOGGING:
	^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
	<!--    settings-->
    <settings>
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>
	^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
LOG4J:

1.import dependency        
      <dependencies>
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
    </dependencies>
2.Use config file to control:
create log4j.properties in the resources dir
	
7.limit page:
reduce the amount of executing data
	
8.










































        
        
        
        
        
