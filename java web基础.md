## Servlet

1、解释
Servlet是运行在Web服务器或应用服务器上的程序,它是作为来自Web浏览器或其他HTTP客户端的请求和HTTP服务器上的数据库或应用程序之间的中间层。使用Servlet可以收集来自网页表单的用户输入，呈现来自数据库或者其他源的记录，还可以动态创建网页。本章内容详细讲解了web开发的相关内容以及servlet相关内容的配置使用,是JAVAEE开发的重中之重。

### 生命周期

![001f4cfd38894718afe46dbbcf0fdd1e](C:\Users\28032\Desktop\笔记\图片\001f4cfd38894718afe46dbbcf0fdd1e.png)

```
public class IndexServlet extends HttpServlet {
    @Override
    public void init(ServletConfig config) throws ServletException {
        System.out.println("初始化执行");
    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("----GET请求的时候执行-----");
    }

    @Override
    public void destroy() {
        System.out.println( "销毁时执行");
    }
}
```

![1f63123993fd4bcca4e083b394bd1efa](C:\Users\28032\Desktop\笔记\图片\1f63123993fd4bcca4e083b394bd1efa.png)

### Servlet路由

#### web.xml配置Servlet路由

```
<servlet>
        <servlet-name>index</servlet-name>			//定义路由的name
        <servlet-class>com.example.dayone.IndexServlet</servlet-class>		//载入路由的访问path
    </servlet>
    <servlet-mapping>
        <servlet-name>index</servlet-name>		//引用定义的路由name
        <url-pattern>/index</url-pattern>			//实现路由的name url_path
    </servlet-mapping>
```

#### 通过注解@WebServlet("/name")

```
@WebServlet(name = "helloServlet", value = "/hello-servlet")	

public class HelloServlet extends HttpServlet {
    private String message;

    public void init() {
        message = "Hello World!";
    }

    public void doGet(HttpServletRequest request, HttpServletResponse response) throws IOException {
        response.setContentType("text/html");

        // Hello
        PrintWriter out = response.getWriter();
        out.println("<html><body>");
        out.println("<h1>" + message + "</h1>");
        out.println("</body></html>");
    }

    public void destroy() {
    }
}
```

### 重写方法

1. ##### 快捷键 ctrl+o

2. ##### doGet 

3. ##### doPost 

4. ##### doDel 

5. ##### doPut ...方法

```
protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

// 从请求参数中获取名字数据
    String name = req.getParameter("name");			
    
    // 设置请求编码为UTF-8，以确保正确解析中文字符
    req.setCharacterEncoding("UTF-8");
    
    // 设置响应内容类型为text/html
    resp.setContentType("text/html");
    
    // 获取PrintWriter对象，用于向客户端发送响应数据
    PrintWriter out = resp.getWriter();		
    
    // 向客户端发送提示信息，表示这是通过POST提交的数据
    out.println("这是post提交的数据");
    
    // 向客户端发送从请求参数中获取的名字数据
    out.println(name);
    
    // 在服务器端打印名字数据到控制台
    System.out.println(name);
    
    // 刷新输出缓冲区，确保数据被及时发送到客户端
    out.flush();
    
    // 关闭PrintWriter，释放资源
    out.close();

```

------



## JAVA Web SQL

### 不安全的写法 JDBC SQL查询

Java Database connectivity): **由java提供,**用于访问数据库的统一API接口规范.[数据库驱动](https://so.csdn.net/so/search?q=数据库驱动&spm=1001.2101.3001.7020): 由各个数据库厂商提供,用于访问数据库的jar包(JDBC的具体实现),遵循JDBC接口,以便java程序员使用

#### 实现JDBC SQL流程:

1. 下载jar
2. 引用封装jar
3. 注册数据库驱动
4. 建立数据库连接
5. 创建Statement执行SQL
6. 结果ResultSet进行提取

```undefined
JDBC接口类
    DriverManager 注册驱动,获取数据库连接Connection
    Connection    数据库连接,获取传输器Statement
    Statement     传输器执行sql语句
    ResultSet     查询结果集合
 
```



##### 1.注册数据库驱动

[mysql-connector-java-5.1.47-bin.jar](https://repo1.maven.org/maven2/mysql/mysql-connector-java/5.1.47/mysql-connector-java-5.1.47.jar)

```cpp
首先,导入数据库驱动jar包(以MySQL为例,mysql-connector-java-5.1.47-bin.jar),然后注册驱动

// 方法一.导致MySql驱动被注册两次,还导致程序和具体驱动类绑定,切换数据库,需要修改java源码重新编译,不建议使用！
DriverManager.registerDriver(new Driver());

// 方法二.驱动类的静态代码块已经注册驱动,只需要加载驱动类即可
// 用反射加载,与驱动类字符串绑定,字符串可放在配置文件,切换数据库,无需改源码重新编译,只需要修改配置文件！    
Class.forName("com.mysql.jdbc.Driver");
```

##### 2.建立数据库连接

```dart
Connection connection = DriverManager.getConnection(url,user,password); 
    
url格式
    MySql:      jdbc:mysql://ip:3306/sid  (本机地址简写jdbc:mysql:///sid)
    Oracle:     jdbc:oracle:thin:@ip:1521:sid
    SqlServer:  jdbc:microsoft:sqlserver://ip:1433;DatabaseName=sid
    
参数(可选,user和password可以写在url参数中)
    ?user=lioil&password=***&useUnicode=true&characterEncoding=UTF-8
    
协议:子协议://ip地址:端口号/库名?参数1=值&参数2=值
String url = jdbc:mysql://localhost:3306/sid?useUnicode=true&characterEncoding=utf-8
```

##### 3.创建Statement执行SQL

```
//编写sql语句，并创建Statement对象
            String sql = "select * from user where username;
            Statement statement = connection.createStatement();
            ResultSet resultSet = statement.executeQuery(sql);
```

##### 4.结果ResultSet进行提取

```

            while (resultSet.next()){		
                Integer id =resultSet.getInt("id");			//获取整数列列 "id"
                String username = resultSet.getNString("username");		//获取字符串列 "username"
                String password = resultSet.getNString("password");
                System.out.println(username,password);
```

### 安全的写法 JDBC 预编译SQL查询 

```
package com.example.dayone;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.sql.*;

public class SafeInjectSqlServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String id = req.getParameter("id");
        try {
            Class.forName("com.mysql.jdbc.Driver");
            String url = "jdbc:mysql://127.0.0.1:3306/test";
            Connection connection = DriverManager.getConnection(url,"root","root");
            String sql = "select * from user where id=?";
            PreparedStatement preparedStatement = connection.prepareStatement(sql);
            preparedStatement.setString(1, "user");			//占位SQL中的?号 表名
            ResultSet resultSet = preparedStatement.executeQuery();		//执行SQL
            
        } catch (ClassNotFoundException e) {
            throw new RuntimeException(e);
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }
}




// 预编译写法
String safesql = "SELECT * FROM news WHERE id=?";
// 使用PreparedStatement
try (PreparedStatement preparedStatement = connection.prepareStatement(safesql)) {
    // 设置参数，防止SQL注入攻击
    preparedStatement.setString(1, s);

    // 执行查询
    ResultSet resultSet = preparedStatement.executeQuery();
```

------



## Filter 过滤器

![fc683fb3d1be4eadf485610b54e2bb23](C:\Users\28032\Desktop\笔记\图片\fc683fb3d1be4eadf485610b54e2bb23.png)

### 解释

客户端经过 监听器(Listener) 在到 过滤器(Filter) 最后到Servlet  如果 客户端的请求内容中有Filter中的逻辑 则不到到Servlet 否则放行在返回给客户端

Filter被称为过滤器，过滤器实际上就是对Web资源进行拦截，做一些处理后再交给下一个过滤器或Servlet处理，通常都是用来拦截request进行处理的，也可以对返回的 response进行拦截处理。开发人员利用filter技术，可以实现对所有Web资源的管理，例如实现权限访问控制、过滤敏感词汇、压缩响应信息等一些高级功能

### 创建过滤器的方法

1. 在对应的filter下创建XssFilter
2. 继承接口 Filter
3. 并实现Filter 接口中的所有方法
4. init 方法
5. destroy方法
6. doFilter 方法

```
init doFilter destroy
init(FilterConfig filterConfig):
该方法在过滤器被初始化时调用，只会执行一次。
用于执行一些初始化操作，例如获取配置信息等。

doFilter(ServletRequest request, ServletResponse response, FilterChain filterChain):
这是过滤器的主要方法，在每次请求被过滤时都会调用。
doFilter 方法中的 filterChain.doFilter(request, response) 表示继续执行过滤器链，如果没有更多的过滤器，最终将调用目标资源（例如 Servlet 或 JSP）。
如果在 doFilter 中不调用 filterChain.doFilter，则请求将被拦截，不会继续传递。

destroy():
该方法在过滤器被销毁时调用，只会执行一次。
用于执行一些清理工作，释放资源等。

```

### Filter触发

#### 通过web.xm的配置触发

```
 <servlet>
        <servlet-name>XssFilter</servlet-name>			
        <servlet-class>com.example.dayone.XssHttpServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>InjectSql</servlet-name>			
        <url-pattern>/xss</url-pattern>			//Filter 的触发url_path
    </servlet-mapping>
```

#### 通过注解的方法  @WebFilter

```
@WebFilter("/xss")
```

![image-20240228093653862](C:\Users\28032\AppData\Roaming\Typora\typora-user-images\image-20240228093653862.png)

![image-20240228093710179](C:\Users\28032\AppData\Roaming\Typora\typora-user-images\image-20240228093710179.png)

### Filter过滤器实现

```
package com.example.dayone.filter;

import javax.servlet.*;
import javax.servlet.annotation.WebFilter;
import javax.servlet.http.HttpServletRequest;
import java.io.IOException;
@WebFilter("/xss")
public class XssFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        Filter.super.init(filterConfig);
        System.out.println("开始过滤XSS");
    }

    @Override
    public void destroy() {
        Filter.super.destroy();
        System.out.println("XSS销毁");
    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("xss正在过滤");
        // 过滤代码就应该在放行前
        // 如果符合就放行，不符合就过滤（拦截）

        // XSS过滤 接受参数值 如果有攻击payload 就进行拦截
        // 接受参数值 如果没有攻击payload 就进行放行
        HttpServletRequest request = (HttpServletRequest) servletRequest;   //接受参数
        String code = request.getParameter("code");			//接受参数中code

        if (!code.contains("<script>")) { // 没有攻击payload		
            // 放行
            filterChain.doFilter(servletRequest, servletResponse);
        } else {
            System.out.println("存在XSS攻击");
            // 继续拦截
            // 这里可以根据需要添加拦截后的处理逻辑，例如记录日志、返回错误信息等**
        }
    }
}

```

#### 过滤器触发的File

```

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

@WebServlet("/xss")
public class XssHttpServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String name = req.getParameter("code");
        PrintWriter out =resp.getWriter();
        out.println(name);
        out.flush();
        out.close();

    }

}

```

------



## 监听器 Listener

### 认识监听器

是指专门用于对其他对象身上发生的事件或状态改变进行监听和相应处理的对象，当被监视的对象发生变化时，立即采取相应的行动。

**在web层面中就是监听java中的一些方法、请求等等，如果触发了请求中触发了监听器中的代码就会执行**









## 反射技术

Java反射(`Reflection`)是Java非常重要的动态特性，通过使用反射我们不仅可以获取到任何类的成员方法(`Methods`)、成员变量(`Fields`)、构造方法(`Constructors`)等信息，还可以动态创建Java类实例、调用任意的类方法、修改任意的类成员变量值等。Java反射机制是Java语言的动态性的重要体现，也是Java的各种框架底层实现的灵魂。

#### 什么是Java反射

参考：https://xz.aliyun.com/t/9117

Java 反射机制原理示意图：

![20210122163639-f253b862-5c8c-1](C:\Users\28032\Desktop\笔记\图片\20210122163639-f253b862-5c8c-1.png)

![dcec8416e6c84734945895ce7562a672](C:\Users\28032\Desktop\笔记\图片\dcec8416e6c84734945895ce7562a672.png)

#### 反射组成相关的类

反射机制相关操作一般位于java.lang.reflect包中

![20210122163927-56b70fca-5c8d-1](C:\Users\28032\Desktop\笔记\图片\20210122163927-56b70fca-5c8d-1.png)

#### java 反射的安全问题

![96a028501adb73497d91398929683b8b](C:\Users\28032\Desktop\笔记\图片\96a028501adb73497d91398929683b8b.png)

### Java-反射-Class对象类获取

1. ##### 创建一个User类，包含成员变量和成员方法，构造方法，便于获取反射所对应需要

```
public class Users {
    //成员变量
    public String name="xiaodi";
    public int age = 31;
    private String gender="man";
    protected String job="sec";

    //构造方法
    public Users(){
        //System.out.println("无参数");
    }

    public Users(String name){
        System.out.println("我的名字"+name);
    }

    private Users(String name,int age){
        System.out.println(name);
        System.out.println(age);
    }

    //成员方法
    public void userinfo(String name,int age,String gender,String job){
        this.job=job;
        this.age=age;
        this.name = name;
        this.gender=gender;
    }

    protected void users(String name,String gender){
        this.name = name;
        this.gender=gender;
        System.out.println("users成员方法："+name);
        System.out.println("users成员方法："+gender);
    }

}

```

![image-20240304162608914](C:\Users\28032\AppData\Roaming\Typora\typora-user-images\image-20240304162608914.png)

1. ##### 根据全限定类名：Class.forName(“全路径类名”)

2. ##### 根据类名：类名.class

3. ##### 根据对象：对象.getClass()

4. ##### 通过类加载器获得Class对象：//ClassLoader.getSystemClassLoader().loadClass(“全路径类名”);

ClassLoader clsload = ClassLoader.getSystemClassLoader();: 获取系统类加载器。这是获取应用程序中所有类的加载器。

- **`Class aClass2 = clsload.loadClass("com.example.reflectdemo1.User");`**: 使用 **`loadClass`** 方法加载名为 “.User” 的类。这是通过类的全限定名进行加载。

```
public class Test  {
    public static void main(String[] args) throws ClassNotFoundException {
        //1、根据全限定类名：Class.forName("全路径类名")
        Class aclass = Class.forName("Users");
        System.out.println(aclass);

        //2、根据类名：类名.class
        Class userClass = Users.class;
        System.out.println(userClass);

        //3、根据对象：对象.getClass()
        Users user= new Users();
        Class aClass1 = user.getClass();
        System.out.println(aClass1);

        //4、通过类加载器获得Class对象：//ClassLoader.getSystemClassLoader().loadClass("全路径类名");
        ClassLoader clsload=ClassLoader.getSystemClassLoader();
        Class aClass2 = clsload.loadClass("Users");
        System.out.println(aClass2);
    }
}


```

![image-20240304162010023](C:\Users\28032\AppData\Roaming\Typora\typora-user-images\image-20240304162010023.png)

#### Java-反射-Field成员变量类获取

![bed278c84d99c9782b366b8ea87b5901](C:\Users\28032\Desktop\笔记\图片\bed278c84d99c9782b366b8ea87b5901.png)

- #### 获取所有公共(public)成员变量对象的数组 Field [] getFields

```
import java.lang.reflect.Field;

public class Test  {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchFieldException {
        //1、根据全限定类名：Class.forName("全路径类名")
        Class aclass = Class.forName("Users");
        //获取所有公共(public)成员对象的数组
        Field [] field =aclass.getFields();

        //遍历数组
        for(Field field1:field){
            System.out.println(field1);
        }

    }
}


```

![image-20240304162656890](C:\Users\28032\AppData\Roaming\Typora\typora-user-images\image-20240304162656890.png)

![image-20240304162713209](C:\Users\28032\AppData\Roaming\Typora\typora-user-images\image-20240304162713209.png)

------

- ####  获取所有成员变量(public private protected)对象的数组   Field [] getDeclaredFields

```
import java.lang.reflect.Field;

public class Test  {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchFieldException {
        //1、根据全限定类名：Class.forName("全路径类名")
        Class aclass = Class.forName("Users");

        //获取所有成员(public private protected)对象的数组
        Field [] field =aclass.getDeclaredFields();
        //遍历数组
        for(Field field1:field){
            System.out.println(field1);
        }

    }
}


```

![image-20240304163312157](C:\Users\28032\AppData\Roaming\Typora\typora-user-images\image-20240304163312157.png)

![image-20240304163322429](C:\Users\28032\AppData\Roaming\Typora\typora-user-images\image-20240304163322429.png)

- #### 获取所有公共(public)单个成员变量对象 Field getField()

  ```
  import java.lang.reflect.Field;
  
  public class GetField {
      public static void main(String[] args) throws ClassNotFoundException, NoSuchFieldException {
          Class aclass = Class.forName("com.src.test.Users");
  
          //获取所有公共(public)成员变量对象 Field getField("属性")
          Field field =aclass.getDeclaredField("gender");
          System.out.println(field);
      }
  }
  
  ```

![image-20240304184212784](C:\Users\28032\AppData\Roaming\Typora\typora-user-images\image-20240304184212784.png)

![image-20240304184236510](C:\Users\28032\AppData\Roaming\Typora\typora-user-images\image-20240304184236510.png)

- #### 获取所有(public,private,protected)单个成员变量对象 Field getDeclaredField()

```
import java.lang.reflect.Field;

public class GetField {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchFieldException {
        Class aclass = Class.forName("com.src.test.Users");

        //获取所有(public,private,protected)成员变量对象 Field getDeclaredField()
        Field field =aclass.getDeclaredField("gender");
        System.out.println(field);
    }
}

```

![image-20240304184423047](C:\Users\28032\AppData\Roaming\Typora\typora-user-images\image-20240304184423047.png)

![image-20240304184434405](C:\Users\28032\AppData\Roaming\Typora\typora-user-images\image-20240304184434405.png)

------



#### Java-反射-Constructor构造方法获取

![5fcd7dc5a1961a27bbf5de3bd9885a9d](C:\Users\28032\Desktop\笔记\图片\5fcd7dc5a1961a27bbf5de3bd9885a9d.png)

- #### 获取所有公有**public**的构造方法对象数组 Constructor [] getConstructors

  ```
  import java.lang.reflect.Constructor;
  
  public class GetConstructor {
      public static void main(String[] args) throws ClassNotFoundException {
      
      	//获取所有公有 (public) 的构造方法对象数组
          Class aclass = Class.forName("com.src.test.Users");
          Constructor []  constructors = aclass.getConstructors();
          for (Constructor constructor1:constructors){
              System.out.println(constructor1);
          }
      }
  }
  
  ```

  

![image-20240305094203465](C:\Users\28032\AppData\Roaming\Typora\typora-user-images\image-20240305094203465.png)

- #### 获取所有 **public**,private,protected 的构造方法对象数组 Constructor [] getDeclaredConstructors

```
import java.lang.reflect.Constructor;

public class GetConstructor {
    public static void main(String[] args) throws ClassNotFoundException {
        Class aclass = Class.forName("com.src.test.Users");
        
        // 获取所有 public,private,protected 的构造方法对象数组
        Constructor []  constructors = aclass.getDeclaredConstructors();
        for (Constructor constructor1:constructors){
            System.out.println(constructor1);
        }
    }
}
```

![image-20240305094542188](C:\Users\28032\AppData\Roaming\Typora\typora-user-images\image-20240305094542188.png)

- #### 获取单个公有**public**的构造方法对象 Constructor [] getConstructor

```
import java.lang.reflect.Constructor;

public class GetConstructor {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchMethodException {
        Class aclass = Class.forName("com.src.test.Users");
        //默认不写就是调用无参数构造方法
        //Constructor constructors = aclass.getConstructor();
        //获取单个公有public的构造方法
        Constructor constructors = aclass.getConstructor(String.class);
        System.out.println(constructors);

    }
}

```

![image-20240305095052998](C:\Users\28032\AppData\Roaming\Typora\typora-user-images\image-20240305095052998.png)

- #### 获取单个所有,**public**,private,protected  的构造方法对象 Constructor [] getDeclaredConstructor

  ```
  import java.lang.reflect.Constructor;
  
  public class GetConstructor {
      public static void main(String[] args) throws ClassNotFoundException, NoSuchMethodException {
          Class aclass = Class.forName("com.src.test.Users");
          //默认不写就是调用无参数构造方法
          //Constructor constructors = aclass.getDeclaredConstructor();
          //获取单个公有public的构造方法
          Constructor constructors = aclass.getDeclaredConstructor(String.class,int.class);
          System.out.println(constructors);
  
      }
  }
  
  ```

  

![image-20240305095231684](C:\Users\28032\AppData\Roaming\Typora\typora-user-images\image-20240305095231684.png)

- #### 对构造方法进行操作

对构造方法进行操作(两个参数string，int)，**setAccessible(true)**临时开启对私有的访问，**newInstance** 使用构造方法创建对象，传递参数；

**newInstance** 方法：

**newInstance** 方法是 **java.lang.reflect.Constructor** 类的一个方法，用于创建新的类实例。
它通过调用类的构造方法来实例化对象。
**newInstance** 方法通常用于动态创建对象，尤其是在反射时，允许在运行时通过 **Constructor** 对象调用类的构造方法。
**setAccessible(true)** 方法：

**setAccessible** 方法是 **java.lang.reflect.AccessibleObject **类的一个方法，用于启用或禁用 Java 语言访问检查。
当 **setAccessible(true)** 被调用时，表示反射对象在使用时取消了访问权限检查，可以访问类的私有成员。
这通常用于访问那些受到访问控制限制的类的私有成员（字段、方法、构造方法等）。

```
import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;

public class GetConstructor {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchMethodException, InvocationTargetException, InstantiationException, IllegalAccessException {
        Class aclass = Class.forName("com.src.test.Users");
        Constructor constructors = aclass.getDeclaredConstructor(String.class,int.class);
        constructors.setAccessible(true);
        
        //等于 Users users = new Users("张三".18)
        constructors.newInstance("张三",18);

    }
}

```

![image-20240305100239318](C:\Users\28032\AppData\Roaming\Typora\typora-user-images\image-20240305100239318.png)

#### java-反射-Method成员方法类获取

![67f9ad7075c0fe380b110d7b15a1bcfa](C:\Users\28032\Desktop\笔记\图片\67f9ad7075c0fe380b110d7b15a1bcfa.png)

- #### ·获取所有公共 public 成员方法对象的数组 包括继承的 Method [] getMethods() 

  ```
  import java.lang.reflect.Method;
  
  public class GetMethod {
      public static void main(String[] args) throws ClassNotFoundException {
          Class aClass = Class.forName("com.src.test.Users");
  
          //获取所有公共 public 成员方法对象的数组 包括继承的
          Method [] methods = aClass.getMethods();
          for (Method method1:methods){
              System.out.println(method1);
          }
      }
  }
  
  ```

  ![image-20240305131356777](C:\Users\28032\AppData\Roaming\Typora\typora-user-images\image-20240305131356777.png)

- ##### 获取所有 public private protected 成员方法对象的数组 不包括继承的 Method [] getDeclaredMethods

  ```
  import java.lang.reflect.Method;
  
  public class GetMethod {
      public static void main(String[] args) throws ClassNotFoundException {
          Class aClass = Class.forName("com.src.test.Users");
  
          //获取所有 public private protected 成员方法对象的数组 不包括继承的
          Method [] methods = aClass.getDeclaredMethods();
          for (Method method1:methods){
              System.out.println(method1);
          }
      }
  }
  
  ```

![image-20240305131641308](C:\Users\28032\AppData\Roaming\Typora\typora-user-images\image-20240305131641308.png)

- 获取单个公有 public 成员方法对象的方法

```
import java.lang.reflect.Method;

public class GetMethod {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchMethodException {
        Class aClass = Class.forName("com.src.test.Users");

        //获取单个公有 public 成员方法对象的方法
        Method methods = aClass.getMethod("users", String.class, String.class);
        System.out.println(methods);

        }
    }


```

![image-20240305132802946](C:\Users\28032\AppData\Roaming\Typora\typora-user-images\image-20240305132802946.png)

- #### 获取单个 public private protected 成员方法对象的方法

  ```
  import java.lang.reflect.Method;
  
  public class GetMethod {
      public static void main(String[] args) throws ClassNotFoundException, NoSuchMethodException {
          Class aClass = Class.forName("com.src.test.Users");
  
          //获取单个公有 public 成员方法对象的方法
          Method methods = aClass.getDeclaredMethod("users", String.class, String.class);
          System.out.println(methods);
  
          }
      }
  
  
  ```

![image-20240305133022989](C:\Users\28032\AppData\Roaming\Typora\typora-user-images\image-20240305133022989.png)

- #### 执行成员方法

```
import com.src.test.Users;

import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

public class GetMethod {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchMethodException, InvocationTargetException, IllegalAccessException {
        Class aClass = Class.forName("com.src.test.Users");
        Users users = new Users();
        // 获取名为 "users" 的方法，该方法接受两个参数（String 和 String）
        Method users1 = aClass.getDeclaredMethod("users", String.class, String.class);
        // 使用反射调用 User 对象的 users 方法，传递参数 "xiaodigay" 和 "gay1"
        users1.invoke(users, "xiaodigay", "gay1");

        }
    }


```

![image-20240305134905100](C:\Users\28032\AppData\Roaming\Typora\typora-user-images\image-20240305134905100.png)

### Java-反射-不安全命令执行

```
import java.io.IOException;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

public class RuntimeExec {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchMethodException, InvocationTargetException, IllegalAccessException, IOException {

        //原生写法Runtime.getRuntime().exec("calc");
        //加载Runtime类
        Class aclass = Class.forName("java.lang.Runtime");
        //获取getRuntime成员方法
        Method method = aclass.getMethod("getRuntime");
        //获取exec成员方法
        Method method1 = aclass.getMethod("exec", String.class);
        //执行getRuntime成员方法
        Object o = method.invoke(aclass);
        //执行exec成员方法
        method1.invoke(o,"calc");
    }
}

```

![image-20240305134558031](C:\Users\28032\AppData\Roaming\Typora\typora-user-images\image-20240305134558031.png)

------



##### 参考

https://blog.csdn.net/qq_52173163/article/details/121110753

https://blog.csdn.net/wushangyu32335/article/details/136178879?spm=1001.2014.3001.5502

https://xz.aliyun.com/t/9117



