# 第一章 SpringMVC介绍

- MVC设计模型：Model-View-Controller，模型-视图-控制器，是一种设计创建web应用程序表现层的模式
  - Model数据模型：JavaBean对象，用于封装数据
  - View视图：jsp或html，用于展示数据
  - Controller控制器：Servlet，用于处理用户交互即程序逻辑
- SpringMVC：基于Java的MVC设计模型的请求驱动类型的轻量级Web框架，通过一套注解能让简单的Java类成为控制器处理请求
- 与Struts2的区别
  - 核心控制器是Servlet和Filter
  - 基于方法和基于类

# 第二章 入门案例

1. 创建工程：选择maven的web骨架，在创建时加入键值对`archetypeCatalog-internal`

2. 完善目录结构：在main包下添加java包，并且右键选择`Make Directory as`

   ![image-20220405145942328](C:\Users\91494\AppData\Roaming\Typora\typora-user-images\image-20220405145942328.png)

3. `pom.xml`中导入依赖

   ```xml
   <!-- 版本锁定 -->
   <properties>
   	<spring.version>5.0.2.RELEASE</spring.version>
   </properties>
   
   <dependencies>
   	<dependency>
   		<groupId>org.springframework</groupId>
   		<artifactId>spring-context</artifactId>
   		<version>${spring.version}</version>
   	</dependency>
   	<dependency>
   		<groupId>org.springframework</groupId>
   		<artifactId>spring-web</artifactId>
   		<version>${spring.version}</version>
   	</dependency>
   	<dependency>
   		<groupId>org.springframework</groupId>
   		<artifactId>spring-webmvc</artifactId>
   		<version>${spring.version}</version>
   	</dependency>
   	<dependency>
   		<groupId>javax.servlet</groupId>
   		<artifactId>servlet-api</artifactId>
   		<version>2.5</version>
   		<scope>provided</scope>
   	</dependency>
   	<dependency>
   		<groupId>javax.servlet.jsp</groupId>
   		<artifactId>jsp-api</artifactId>
   		<version>2.0</version>
   		<scope>provided</scope>
   	</dependency>
   </dependencies>
   ```

4. 在`web.xml`中配置过滤器和核心控制器

   ```xml
   <filter>
   	<filter-name>characterEncodingFilter</filter-name>
   	<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
   	<init-param>
    		<param-name>encoding</param-name>
     		<param-value>UTF-8</param-value>
   	</init-param>
   </filter>
   <filter-mapping>
   	<filter-name>characterEncodingFilter</filter-name>
   	<url-pattern>/*</url-pattern>
   </filter-mapping>
   
   
   <servlet>
   	<servlet-name>dispatcherServlet</servlet-name>
   	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
       <!--设置配置文件位置-->
   	<init-param>
     		<param-name>contextConfigLocation</param-name>
     		<param-value>classpath:springmvc.xml</param-value>
   	</init-param>
       <!--服务器启动时创建-->
   	<load-on-startup>1</load-on-startup>
   </servlet>
   
   <servlet-mapping>
   	<servlet-name>dispatcherServlet</servlet-name>
       <!--控制所有路径-->
   	<url-pattern>/</url-pattern>
   </servlet-mapping>
   
   ```

5. resources下创建`springmvc.xml`配置文件，开启注解扫描

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:mvc="http://www.springframework.org/schema/mvc"
          xmlns:context="http://www.springframework.org/schema/context"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="
           http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/mvc
           http://www.springframework.org/schema/mvc/spring-mvc.xsd
           http://www.springframework.org/schema/context
           http://www.springframework.org/schema/context/spring-context.xsd">
   
       <!--开启注解扫描-->
       <context:component-scan base-package="com.zhouqiao"></context:component-scan>
   
       <!--配置视图解析器对象-->
       <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
           <!--设置页面存放位置以及后缀-->
           <property name="prefix" value="/WEB-INF/pages/"></property>
           <property name="suffix" value=".jsp"></property>
       </bean>
   
       <!--开启注解支持-->
       <mvc:annotation-driven></mvc:annotation-driven>
       
       <!-- 设置静态资源不过滤 -->
       <mvc:resources location="/css/" mapping="/css/**"/> <!-- 样式 -->
       <mvc:resources location="/images/" mapping="/images/**"/> <!-- 图片 -->
       <mvc:resources location="/js/" mapping="/js/**"/> <!-- javascript -->
   
   </beans>
   ```

6. 配置Tomcat服务器

6. 编写一个类使用`@Controller`注解标记为**控制器**，在方法上使用`@RequestMapping(path = "/hello")`注解

6. 控制器方法返回值为字符串，默认是要跳转的**页面名称**

9. 编写一个jsp页面，指定超链接为`@RequestMapping`注解的`path`参数

11. 在WEB-INF下创建一个`pages`目录，里面存放跳转页面

12. 在`springmvc.xml`中配置视图解析器

13. 开启注解支持


- 程序报错：Error : java 不支持发行版本5
  1. File -->Project Structure修改版本
  2. 左边Module中也要修改版本
  3. Settings-->Bulid, Execution,Deployment-->Java Compiler，**Target bytecode version**设为本地Java版本/可以在Default Settings中把**Project bytecode version** 一劳永逸地配置成本地Java版本

## 执行过程分析

1. 当启动Tomcat服务器时，因为配置了`load-on-startup`标签，所以会创建`DispatcherServlet`对象，并加载`springmvc.xml`配置文件
2. 开启了注解扫描，`HelloController`对象就会被创建
3. 从`index.jsp`发送请求，请求会先到达`DispatcherServlet`核心控制器，根据配置`@RequestMapping`注解找到执行的具体方法
4. 根据执行方法的返回值，再根据配置的视图解析器，去指定的目录下查找指定名称的jsp文件
5. Tomcat服务器渲染页面，做出响应

![image-20220326104034583](C:\Users\91494\AppData\Roaming\Typora\typora-user-images\image-20220326104034583.png)

## 详细过程(重点)

![image-20220326104655974](C:\Users\91494\AppData\Roaming\Typora\typora-user-images\image-20220326104655974.png)

- 前端控制器控制整个流程的执行，处理器映射器找到要执行的类和方法，处理器适配器用于适配所有控制器，去执行对应方法，视图解析器用于跳转页面
- 代码中显式配置了前端控制器和视图解析器，处理器映射器和处理器适配器通过`<mvc:annotation-driven>`实现自动加载

# 第三章 请求参数的绑定

- 绑定机制

  - 表单提交的数据都是key-value格式
  - SpringMVC的参数绑定过程是把表单提交的请求参数作为控制器中方法的参数进行绑定
  - 要求提交表单的name和参数的名称是相同的

- 支持的数据类型

  - 基本数据类型和字符串类型：提交表单的name和参数的名称是相同的，且区分大小写
  - 实体类型(JavaBean)
    - 提交表单的name和JavaBean中的属性名称要一致，执行方法直接传入对象即可，会自动创建对象并赋值
    - 如果一个JavaBean类中包含其他的引用类型，那么表单的name属性需要编写成`对象.属性`格式，例如address.name
  - 集合数据类型(List、Map集合等)
    - `userList[0].name   userMap("one").name`

- POST请求中文乱码

  - 设置filter进行过滤，位置在servlet之前

    ```xml
      <filter>
        <filter-name>characterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
          <param-name>encoding</param-name>
          <param-value>UTF-8</param-value>
        </init-param>
      </filter>
      <filter-mapping>
        <filter-name>characterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
      </filter-mapping>
    ```

- 自定义类型转换器

  - 表单提交的任何数据类型全部都是字符串类型，但是Spring框架内部会默认进行数据类型转换

  - 如果想自定义数据类型转换，可以实现`Converter`接口

    1. 自定义类型转换器

       ```java
       public class StringToDateConverter implements Converter<String, Date> {
           /**
            * 进行类型转换的方法
            */
           public Date convert(String source) {
       // 判断
               if(source == null) {
                   throw new RuntimeException("参数不能为空");
               }
               try {
                   DateFormat df = new SimpleDateFormat("yyyy-MM-dd");
       // 解析字符串
                   Date date = df.parse(source);
                   return date;
               } catch (Exception e) {
                   throw new RuntimeException("类型转换错误");
               }
           }
       }
       ```

    2. 在`springmvc.xml`中注册转换器

       ```xml
           <!-- 注册自定义类型转换器 -->
           <bean id="conversionService"
                 class="org.springframework.context.support.ConversionServiceFactoryBean">
               <property name="converters">
                   <set>
                       <bean class="cn.itcast.utils.StringToDateConverter"/>
                   </set>
               </property>
           </bean>
           <!-- 开启Spring对MVC注解的支持 -->
           <mvc:annotation-driven conversion-service="conversionService"/>
       
       ```

- 在控制器中使用原生的ServletAPI对象：只需要在控制器的方法参数定义`HttpServletRequest`和`HttpServletResponse`对象

# 第四章 常用注解

1. `@RequestMapping`：用于建立请求URL和处理方法之间的对应关系

   - 可以作用在**方法和类**上：作用在类上表示第一级的访问目录，作用在方法上表示第二级的访问目录
   - 路径可以不编写 / 表示应用的根目录开始
   - `${ pageContext.request.contextPath }`也可以省略不写，但是路径上不能写 /
   - 属性
     - `path/value`：指定请求路径的url
     - `method`：指定方法的请求方式，`RequestMethod.POST`
     - `params`：指定限制请求参数的条件
     - `headers`：发送的请求中必须包含的请求头

2. `@RequestParam`：将请求中指定名称的参数传递给控制器中的形参赋值，用于变量名与参数名不一致的情况，用于**形参**`(@RequestParam(value="username",required=false)String name)`

   - `required`属性表示必须传一个指定名称的参数

3. `@RequestBody`：获取POST方法请求体的内容，异步请求时使用，得到键值对，用于**形参**，表示参数是一个请求体

4. `@PathVariable`：用于绑定url中的占位符，如url中有`/delete/{id}`，`{id}`就是占位符

   - RESTful风格：为所有方法写同一个路径，根据**请求方式不同**调用不同方法，如果请求方式相同，则根据占位符来匹配方法，这样所有内容都在同一个文件里方便管理

   - 用于形参之前

     ```java
     @RequestMapping(path="/hello/{id}")
     public String sayHello(@PathVariable(value="id") String id) {
     	System.out.println(id);
     	return "success";
     }
     ```

5. `@RequestHeader`：获取指定请求头的值，用于形参上 `(@RequestHeader(value="Accept") String header)`

   - `value`属性表示请求头中的头名称

6. `@CookieValue`：获取指定cookie的名称的值，用于形参上 `sayHello(@CookieValue(value="JSESSIONID") String cookieValue)`

7. `@ModelAttribute`

   - 可以作用在**方法和参数**上：作用在方法上表示当前方法会在**控制器方法执行前**执行，作用在参数上表示获取指定的数据给参数赋值

   - 应用场景：当提交表单数据不是完整的实体数据时，保证没有提交的字段使用数据库原来的数据

   - 修饰的方法有返回值

     ```java
     @ModelAttribute
     public User showUser(String name) {
     	System.out.println("showUser执行了...");
     	// 模拟从数据库中查询对象
     	User user = new User();
     	user.setName("哈哈");
     	user.setPassword("123");
     	user.setMoney(100d);
     	return user;
     }
     
     // 在showUser之后执行
     @RequestMapping(path="/updateUser")
     public String updateUser(User user) {
     	System.out.println(user);
     	return "success";
     }
     ```

   - 修饰的方法没有返回值，结果存入Map集合中，其他方法从集合中取

     ```java
     @ModelAttribute
     public void showUser(String name,Map<String, User> map) {
     System.out.println("showUser执行了...");
     // 模拟从数据库中查询对象
     	User user = new User();
     	user.setName("哈哈");
     	user.setPassword("123");
     	user.setMoney(100d);
     	map.put("abc", user);
     }
     
     @RequestMapping(path="/updateUser")
     public String updateUser(@ModelAttribute(value="abc") User user) {
     	System.out.println(user);
     	return "success";
     }
     ```

8. `@SessionAttributes`：用于多次执行控制器方法间的参数共享

   ```java
   @Controller
   @RequestMapping(path = "/user")
   @SessionAttributes(value = {"username", "password", "age"}, types =
           {String.class, Integer.class}) // 把数据存入到session域对象中
   public class HelloController {
       
       // 向session中存入值
       @RequestMapping(path = "/save")
       public String save(Model model) {
           System.out.println("向session域中保存数据");
           model.addAttribute("username", "root");
           model.addAttribute("password", "123");
           model.addAttribute("age", 20);
           return "success";
       }
   
       
       // 从session中获取值
       @RequestMapping(path = "/find")
       public String find(ModelMap modelMap) {
           String username = (String) modelMap.get("username");
           String password = (String) modelMap.get("password");
           Integer age = (Integer) modelMap.get("age");
           System.out.println(username + " : " + password + " : " + age);
           return "success";
       }
   
       // 清除值
       @RequestMapping(path = "/delete")
       public String delete(SessionStatus status) {
           status.setComplete();
           return "success";
       }
   }
   ```

# 第五章 响应数据与结果视图

- 返回值分类

  - 字符串：Controller方法返回字符串可以指定逻辑视图的名称，视图解析器解析得到物理视图的地址

    ```java
    @Controller
    @RequestMapping("/user")
    public class UserController {
    /**
    * 请求参数的绑定
    */
    @RequestMapping(value="/initUpdate")
    public String initUpdate(Model model) {
    	// 模拟从数据库中查询的数据
    	User user = new User();
    	user.setUsername("张三");
    	user.setPassword("123");
    	user.setMoney(100d);
    	user.setBirthday(new Date());
    	model.addAttribute("user", user);
    	return "update";
    	}
    }
    ```

  - void：如果控制器的方法返回值编写成void，执行程序报404的异常，默认查找JSP页面没有找到，可以使用请求转发或者重定向跳转到指定的页面

    ```java
    @RequestMapping(value="/initAdd")
    public void initAdd(HttpServletRequest request,HttpServletResponse response) throws Exception {
    	System.out.println("请求转发或者重定向");
    	// 请求转发
    	// request.getRequestDispatcher("/WEB-INF/pages/add.jsp").forward(request, response);
    	// 重定向
    	// response.sendRedirect(request.getContextPath()+"/add2.jsp");
    	response.setCharacterEncoding("UTF-8");
    	response.setContentType("text/html;charset=UTF-8");
    	// 直接响应数据
    	response.getWriter().print("你好");
    	return;
    }
    ```

  - ModelAndView对象：Spring提供的一个对象，可以用来调整具体的JSP视图

    ```java
    @RequestMapping(value="/findAll")
    public ModelAndView findAll() throws Exception {
    ModelAndView mv = new ModelAndView();
    	// 跳转到list.jsp的页面
    	mv.setViewName("list");
    	// 模拟从数据库中查询所有的用户信息
    	List<User> users = new ArrayList<>();
    	User user1 = new User();
    	user1.setUsername("张三");
    	user1.setPassword("123");
    	User user2 = new User();
    	user2.setUsername("赵四");
    	user2.setPassword("456");
        users.add(user1);
    	users.add(user2);
    	// 添加对象
    	mv.addObject("users", users);
    	return mv;
    }
    ```

- Spring提供的转发与重定向

  - 通过关键字实现，其中重定向不需要加项目名称，底层实现

    ```java
    @RequestMapping("/delete")
    public String delete() throws Exception {
    	System.out.println("delete方法执行了...");
    	// return "forward:/WEB-INF/pages/success.jsp";
    	return "forward:/user/findAll";
    }
    
    @RequestMapping("/count")
    public String count() throws Exception {
    	System.out.println("count方法执行了...");
    	return "redirect:/add.jsp";
    	// return "redirect:/user/findAll";
    }
    ```

- ResponseBody响应json数据

  - 引入JQuery：在WEB-INF同级创建js文件夹，里面添加JQuery文件，在同级JSP页面中引入 `<script src="js/jquery.min.js"></script>`

  - `DispatcherServlet`会拦截到所有的资源，导致静态资源（img、css、js）也会被拦截到，从而不能被使用，因此需要配置不拦截静态资源，在`springmvc.xml`添加配置
    
    - `<mvc:resources>`标签配置不过滤
    
      ```xml
      <!-- 设置静态资源不过滤 -->
      <mvc:resources location="/css/" mapping="/css/**"/> <!-- 样式 -->
      <mvc:resources location="/images/" mapping="/images/**"/> <!-- 图片 -->
      <mvc:resources location="/js/" mapping="/js/**"/> <!-- javascript -->
      ```
    
  - 绑定单击事件
  
    ```jsp
    $(function(){
    	// 绑定点击事件
    	$("#btn").click(function(){
    		$.ajax({
    			url:"user/testJson",
    			contentType:"application/json;charset=UTF-8",
    			data:'{"addressName":"aa","addressNum":100}',
    			dataType:"json",
    			type:"post",
    			success:function(data){
    				alert(data);
    				alert(data.addressName);
    			}
    		});
    	});
    });
    ```
  
  - 使用`@RequestBody`获取请求体数据，把json的字符串转换成JavaBean的对象
  
    ```java
    @RequestMapping("/testJson")
    public void testJson(@RequestBody String body) {
    	System.out.println(body);
    }
    ```
  
  - 为了使Spring能够自动封装json数据为Bean对象，需要导入jackson相关jar包
  
    ```xml
    <dependency>
    	<groupId>com.fasterxml.jackson.core</groupId>
    	<artifactId>jackson-databind</artifactId>
    	<version>2.9.0</version>
    </dependency>
    <dependency>
    	<groupId>com.fasterxml.jackson.core</groupId>
    	<artifactId>jackson-core</artifactId>
    	<version>2.9.0</version>
    </dependency>
    <dependency>
    	<groupId>com.fasterxml.jackson.core</groupId>
    	<artifactId>jackson-annotations</artifactId>
    	<version>2.9.0</version>
    </dependency>
    ```
  
  - 使用`@ResponseBody`注解把JavaBean对象转换成json字符串直接响应，要求方法需要返回JavaBean的对象
  
    ```java
    @RequestMapping("/testJson")
    public @ResponseBody user testJson(@RequestBody User user) {
    	return user;
    }
    ```

# 第六章 SpringMVC实现文件上传

- 传统方式实现文件上传

  - 导入文件上传依赖的jar包

    ```xml
    <dependency>
    	<groupId>commons-fileupload</groupId>
    	<artifactId>commons-fileupload</artifactId>
    	<version>1.3.1</version>
    </dependency>
    <dependency>
    	<groupId>commons-io</groupId>
    	<artifactId>commons-io</artifactId>
    	<version>2.4</version>
    </dependency>
    ```

  - 编写文件上传的jsp页面

    ```jsp
    <h3>文件上传</h3>
    <form action="user/fileupload" method="post" enctype="multipart/form-data">
    	选择文件：<input type="file" name="upload"/><br/>
    	<input type="submit" value="上传文件"/>
    </form>
    ```

  - 编写文件上传的Controller控制器

    ```java
        @RequestMapping(value="/fileupload")
        public String fileupload(HttpServletRequest request) throws Exception {
    		// 先获取到要上传的文件目录
            String path = request.getSession().getServletContext().getRealPath("/uploads");
    		// 创建File对象，一会向该路径下上传文件
            File file = new File(path);
    		// 判断路径是否存在，如果不存在，创建该路径
            if(!file.exists()) {
                file.mkdirs();
            }
    		// 创建磁盘文件项工厂
            DiskFileItemFactory factory = new DiskFileItemFactory();
            ServletFileUpload fileUpload = new ServletFileUpload(factory);
    		// 解析request对象
            List<FileItem> list = fileUpload.parseRequest(request);
    		// 遍历
            for (FileItem fileItem : list) {
    		// 判断文件项是普通字段，还是上传的文件
                if(fileItem.isFormField()) {
                }else {
    				// 上传文件项
                    // 获取到上传文件的名称
                    String filename = fileItem.getName();
                    String uuid = UUID.randomUUID().toString().replaceAll("-", "").toUpperCase();
    				// 把文件的名称唯一化
    				filename = uuid+"_"+filename;
    				// 上传文件
                    fileItem.write(new File(file, filename));
    				// 删除临时文件
                    fileItem.delete();
                }
            }
            return "success";
        }
    
    ```

- SpringMVC实现文件上传

  - SpringMVC框架提供了`MultipartFile`对象，该对象表示上传的文件，要求变量名称必须和表单file标签的name属性名称相同

  - ```java
        @RequestMapping(value="/fileupload2")
        public String fileupload2(HttpServletRequest request,MultipartFile upload) throws Exception {
            System.out.println("SpringMVC方式的文件上传...");
    		// 先获取到要上传的文件目录
            String path = request.getSession().getServletContext().getRealPath("/uploads");
    		// 创建File对象，一会向该路径下上传文件
            File file = new File(path);
    		// 判断路径是否存在，如果不存在，创建该路径
            if(!file.exists()) {
                file.mkdirs();
            }
    		// 获取到上传文件的名称
            String filename = upload.getOriginalFilename();
            String uuid = UUID.randomUUID().toString().replaceAll("-", "").toUpperCase();
    		// 把文件的名称唯一化
            filename = uuid+"_"+filename;
    		// 上传文件
            upload.transferTo(new File(file,filename));
            return "success";
        }
    ```

  - ```xml
    <!-- 配置文件解析器对象，要求id名称必须是multipartResolver -->
    <bean id="multipartResolver"
    	class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
    	<property name="maxUploadSize" value="10485760"/>
    </bean>
    ```

  - ![image-20220327154058215](C:\Users\91494\AppData\Roaming\Typora\typora-user-images\image-20220327154058215.png)

- SpringMVC实现跨服务器文件上传

  - 导入jar包

    ```xml
    <dependency>
    	<groupId>com.sun.jersey</groupId>
    	<artifactId>jersey-core</artifactId>
    	<version>1.18.1</version>
    </dependency>
    <dependency>
    	<groupId>com.sun.jersey</groupId>
    	<artifactId>jersey-client</artifactId>
    	<version>1.18.1</version>
    </dependency>
    ```

  - 编写代码

    ```java
        @RequestMapping(value="/fileupload3")
        public String fileupload3(MultipartFile upload) throws Exception {
            System.out.println("SpringMVC跨服务器方式的文件上传...");
            // 定义图片服务器的请求路径
            String path = "http://localhost:9090/day02_springmvc5_02image/uploads/";
            // 获取到上传文件的名称
            String filename = upload.getOriginalFilename();
            String uuid = UUID.randomUUID().toString().replaceAll("-", "").toUpperCase();
            // 把文件的名称唯一化
            filename = uuid + "_" + filename;
            // 向图片服务器上传文件
            // 创建客户端对象
            Client client = Client.create();
            // 连接图片服务器
            WebResource webResource = client.resource(path + filename);
            // 上传文件
            webResource.put(upload.getBytes());
            return "success";
        }
    ```

# 第七章 SpringMVC的异常处理

- 异常处理思路：Controller调用service，service调用dao，异常都是向上抛出的，最终由DispatcherServlet找异常处理器进行异常的处理
- ![image-20220327160437000](C:\Users\91494\AppData\Roaming\Typora\typora-user-images\image-20220327160437000.png)

1. 自定义异常类

   ```java
   public class SysException extends Exception{
   	private static final long serialVersionUID = 4055945147128016300L;
   	// 异常提示信息
   	private String message;
   	public String getMessage() {
   		return message;
   	}	
   	public void setMessage(String message) {
   		this.message = message;
   	}
   	public SysException(String message) {
   		this.message = message;
   	}
   }
   ```

2. 控制器抛出自定义异常

3. 自定义异常处理器

   ```java
   public class SysExceptionResolver implements HandlerExceptionResolver{
   /**
   * 跳转到具体的错误页面的方法
   */
   	public ModelAndView resolveException(HttpServletRequest request, HttpServletResponse
   	response, Object handler, Exception ex) {
   		ex.printStackTrace();
   		SysException e = null;
   		// 获取到异常对象
   		if(ex instanceof SysException) {
   			e = (SysException) ex;
   		}else {
   			e = new SysException("请联系管理员");
   		}
   		ModelAndView mv = new ModelAndView();
   		// 存入错误的提示信息
   		mv.addObject("message", e.getMessage());
   		// 跳转的Jsp页面
   		mv.setViewName("error");
   		return mv;
   	}
   }
   ```

4. 配置异常处理器

   ```xml
   <!-- 配置异常处理器 -->
   <bean id="sysExceptionResolver" class="cn.itcast.exception.SysExceptionResolver"/>
   ```

# 第八章 SpringMVC中的拦截器

- 用于对**Controller**进行预处理和后处理的技术

- 可以定义拦截器链，将拦截器按着一定的顺序结成一条链，在访问被拦截的方法时，拦截器链中的拦截器会按着定义的顺序执行

- 拦截器和过滤器的功能比较类似，但也有区别

  - 过滤器是Servlet规范的一部分，任何框架都可以使用过滤器技术，而拦截器是SpringMVC框架**独有**的
  - 过滤器配置了/*，可以拦截任何资源，拦截器只会对**控制器中的方法**进行拦截，而不会去拦截静态资源

- 拦截器也是AOP思想的一种实现方式

- 想要自定义拦截器，需要实现`HandlerInterceptor`接口

- 自定义拦截器

  1. 创建类，实现`HandlerInterceptor`接口，重写需要的方法

     ```java
     public class MyInterceptor1 implements HandlerInterceptor{
     /**
     * controller方法执行前，进行拦截的方法
     * return true放行
     * return false拦截
     * 可以使用转发或者重定向直接跳转到指定的页面。
     */
     	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
     		throws Exception {
     			System.out.println("拦截器执行了...");
     			return true;
     	}
     }
     ```

  2. 在`springmvc.xml`中配置拦截器类

     ```xml
     <!-- 配置拦截器 -->
     <mvc:interceptors>
     	<mvc:interceptor>
     	<!-- 哪些方法进行拦截 -->
     		<mvc:mapping path="/user/*"/>
     		<!-- 哪些方法不进行拦截
     		<mvc:exclude-mapping path=""/>
     
     		<!-- 注册拦截器对象 -->
     		<bean class="cn.itcast.demo1.MyInterceptor1"/>
     	</mvc:interceptor>
     </mvc:interceptors>
     ```

- `HandlerInterceptor`接口中的方法

  1. `preHandle()`方法是`controller`方法执行前拦截的方法
     1. 可以使用request或者response跳转到指定的页面：`request.getRequestDispatcher("路径").forward(requser, response);`
     2. return true放行，执行下一个拦截器，如果没有拦截器，执行controller中的方法
     3. return false不放行，不会执行controller中的方法
  2. p`ostHandle()`是controller方法执行后执行的方法，在JSP视图执行前
     1. 可以使用request或者response跳转到指定的页面
     2. 如果指定了跳转的页面，那么controller方法跳转的页面将不会显示
  3. afterCompetion方法是在JSP执行后执行，request或者response不能再跳转页面了

- 配置多个拦截器：在`springmvc.xml`中按顺序配置即可