# SpringMVC


## SpringMVC的核心是什么？

它的一个核心就是我们的控制反转，和我们的面向切面。

## 什么是MVC模式？

![MVC模式](/img/SpringMVC/1.png)

* MVC的全名是**Model View Controller**
* 是`模型(model)`－`视图(view)`－`控制器(controller)`的缩写，是一种软件设计典范。
* 它是用一种业务逻辑、数据与界面显示分离的方法来组织代码，将众多的业务逻辑聚集到一个部件里面，
* 在需要改进和个性化定制界面及用户交互的同时，不需要重新编写业务逻辑，达到减少编码的时间。
  <br>
  <br>

1. `M`即`model模型`是指模型表示业务规则。
在MVC的三个部件中，模型拥有最多的处理任务。被模型返回的数据是中立的，
模型与数据格式无关，这样一个模型能为多个视图提供数据，由于应用于模型的代码只需写一次就可以被多个视图重用，所以减少了代码的重复性。
2. `V`即`View视图`是指用户看到并与之交互的界面。
比如由html元素组成的网页界面，或者软件的客户端界面。
MVC的好处之一在于它能为应用程序处理很多不同的视图。
在视图中其实没有真正的处理发生，它只是作为一种输出数据并允许用户操纵的方式。
3. `C`即`controller控制器`是指控制器接受用户的输入并调用模型和视图去完成用户的需求，
控制器本身不输出任何东西和做任何处理。
它只是接收请求并决定调用哪个模型构件去处理请求，然后再确定用哪个视图来显示返回的数据。

## SpringMVC的执行流程？

* 请求处理流程就是，首先我们的用户，去发送请求到我们的一个前端控制器，然后我们的前端控制器呢，会根据我们请求信息，
比如说我们的URL，来去决定说到底是哪个页面的控制去进行处理，然后并把我们这个处理委托给他嘛，
我们以前的一个控制器的，一个控制逻辑部分嘛，然后还有我们的一个页面控制器，
当他接受到这个请求了之后，然后进行一些功能处理，然后我们首先我们需要去收集和绑定请求参数到一个对象，
然后去进行一个验证，再把这个命令对象委托给我们的业务对象，去进行我们的一个处理，
然后我们处理完毕的时候呢，返回一个我们的ModelAndView嘛，就是我们的数据模型跟视图，
然后前端控制器呢，回收我们的控制权，然后去根据我们的一个返回的逻辑，视图名嘛，
然后再去选择相对应的一个视图进行渲染，再把我们的一个模型数据呢，传入到我们这个，以便我们视图可以去渲染，
然后前端控制器再次回收我们的控制权，然后再去响应，返回给我们的用户。


![MVC执行流程](/img/SpringMVC/2.png)

## SpringMVC有哪些优点？

1. SpringMVC本身是与Spring框架结合而成的，它同时拥有Spring的优点(例如依赖注入DI和切面编程AOP等)。 
2. SpringMVc提供强大的约定大于配置的契约式编程支持，即提供一种软件设计范式，
减少软件开发人员做决定的次数，开发人员仅需规定应用中不符合约定的部分。 
3. 支持灵活的URL到页面控制器的映射。 
4. 可以方便地与其他视图技术(JSP、FreeMarker等)进行整合。
由于SpringMVC的模型数据往往是放置在Map数据结构中的，因此其可以很方便地被其他框架引用。 
5. 拥有十分简洁的异常处理机制。 
6. 可以十分灵活地实现数据验证、格式化和数据绑定机制，可以使用任意对象进行数据绑定操作。 
7. 支持RestFul风格。

## Spring MVC的主要组件？

1. 前端控制器：其作用是接收用户请求，然后给用户反馈结果。
它的作用相当于一个转发器或中央处理器，控制整个流程的执行，
对各个组件进行统一调度，以降低组件之间的耦合性，有利于组件之间的拓展。 
2. 处理器映射器：其作用是根据请求的URL路径，通过注解或者XML配置，寻找匹配的处理器信息。 
3. 处理器适配器：其作用是根据映射器处理器找到的处理器信息，按照特定规则执行相关的处理器（Handler）。 
4. 处理器：其作用是执行相关的请求处理逻辑，并返回相应的数据和视图信息，将其封装至ModelAndView对象中。 
5. 视图解析器：其作用是进行解析操作，
通过ModelAndView对象中的View信息将逻辑视图名解析成真正的视图View（如通过一个JSP路径返回一个真正的JSP页面）。 
6. 视图：View是一个接口，实现类支持不同的View类型（JSP、FreeMarker、Excel等）。

## SpringMVC和Struts2的区别有哪些?

1. SpringMVC的入口是一个Servlet，也就是前端控制器(DispatcherServlet)，
而Struts2的入口是一个Filter (StrutsPrepareAndExecuteFilter)。 
2. SpringMVC是基于方法开发(一个url对应一个方法)，请求参数传递到方法的形参，
可以设计为单例或多例(建议单例)。struts2是基于类开发，请求参数传递到类的成员属性，只能设计为多例。 
3. SpringMVC通过参数解析器将request请求内容解析，并给方法形参赋值，
将数据和视图封装成ModelAndView对象，最后又将ModelAndView中的模型数据通过reques域传输到页面。Jsp视图解析器默认使用JSTL。Struts2采用值栈存储请求和响应的数据，通过OGNL存取数据。

## SpringMVC怎么样设定重定向和请求转发？

### 请求转发与重定向的区别

* 请求转发在服务器端完成的；重定向是在客户端完成的。 
* 请求转发的速度快；重定向速度慢。 
* 请求转发的是同一次请求；重定向是两次不同请求。 
* 请求转发不会执行转发后的代码；重定向会执行重定向之后的代码。 
* 请求转发地址栏没有变化；重定向地址栏有变化。 
* 请求转发必须是在同一台服务器下完成；重定向可以在不同的服务器下完成。

### SpringMVC设定请求转发

在返回值前面加"forward:"

```java
@RequestParam("/login")
public String redirect(User user){
    if{
        //登录成功...
    }else{
        //登录失败，转发到登录页面
        return "forward:tologin";
    }
}
```

### SpringMVC设定重定向

在返回值前面加"redirect:"。例如我们在登录的时候，登录失败会重定向到登录页面。

```java
@RequestParam("/login")
public String redirect(User user){
    if{
        //登录成功...
    }else{
        //登录失败，重定向到登录页面
        return "redirect:tologin";
    }
}
```

## SpringMvc的控制器是不是单例模式,如果是,有什么问题,怎么解决？

* Controller是单例模式，在多线程访问的时候可能产生线程安全问题，不要使用同步，会影响程序性能。 
* 解决方案是在控制器里面不能编写成员属性。

## SpringMVC常用的注解有哪些？

### @Controller

`@Controller`用于标记在一个类上，使用它标记的类就是一个SpringMVC Controller对象。
处理器适配器将会扫描使用了该注解的类的方法，
并检测该方法是否使用了`@RequestMapping`注解。
`@Controller`只是定义了一个控制器类，
而使用`@RequestMapping`注解的方法才是真正处理请求的处理器。

### @RequsestMapping

`@RequestMapping`是一个用来处理请求地址映射的注解，可用于类或方法上。
用于类上，表示类中的所有响应请求的方法都是以该地址作为父路径。
返回值会通过视图解析器解析为实际的物理视图，
对于 InternalResourceViewResolver 视图解析器，
通过 prefix + returnValue + suffix 这样的方式得到实际的物理视图，
然后做转发操作。

```java
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/jsp/"/>
    <property name="suffix" value=".jsp"/>
</bean>
```

`@RequsestMapping`有如下6个属性

1. value：指定请求的实际地址。
2. method：指定请求的method类型， GET、POST、PUT、DELETE等。
3. consumes：指定处理请求的提交内容类型（Content-Type），例如application/json, text/html。
4. produces：指定返回的内容类型，仅当request请求头中的(Accept)类型中包含该指定类型才返回；
5. params：指定request中必须包含某些参数值是，才让该方法处理。
6. headers：指定request中必须包含某些指定的header值，才能让该方法处理请求。


### @ResponseBody

`@ResponseBody`把Java对象转化为json对象，这种方式用于Ajax异步请求，
返回的不是一个页面而是JSON格式的数据。

### @Valid

标志参数被`Hibernate-Validator校验框架`校验。

### @PathVariable

1. @PathVariable用于接收uri地址传过来的参数，Url中可以通过一个或多个{Xxx}占位符映射，
   通过@PathVariable可以绑定占位符参数到方法参数中，在RestFul接口风格中经常使用。
2. 例如：请求URL：http://localhost/user/21/张三/query
   (Long类型可以根据需求改变为String或int，SpringMVC会自动做转换)

```java
@RequestMapping("/user/{userId}/{userName}/query")
public User query(@PathVariable("userId") Long userId, @PathVariable("userName") String userName){
```

### @RequestParam

`@RequestParam`用于将请求参数映射到控制器方法的形参上，有如下三个属性

* value：参数名。
* required：是否必需，默认为true，表示请求参数中必须包含该参数，如果不包含抛出异常。
* defaultValue：默认参数值，如果设置了该值自动将required设置为false，
  如果参数中没有包含该参数则使用默认值。
* 示例：@RequestParam(value = "pageNum", required = false, defaultValue = "1")

### @ControllerAdvice

`@ControllerAdvice`标识一个类是全局异常处理类。

```java
@ControllerAdvice
public class ControllerTest {
    //全局异常处理类
}
```

### @ExceptionHandler

`@ExceptionHandler`标识一个方法为全局异常处理的方法。

```java
@ExceptionHandler
public void ExceptionHandler(){
    //全局异常处理逻辑...
}
```

## SpingMvc中的控制器的注解一般用那个,有没有别的注解可以替代？

* 一般使用@Controller注解标识控制器。 
* 也可以使用@RestController注解替代@Controller注解， 
@RestController相当于@ResponseBody＋@Controller，
表示控制器中所有的方法都返回JSON格式数据，一般不使用其他注解标识控制器。


## 如果在拦截请求中，想拦截get方式提交的方法,怎么配置？

1. 可以在@RequestMapping注解里面加上method=RequestMethod.GET。

```java
@RequestMapping(value="/toLogin",method = RequestMethod.GET)
public ModelAndView toLogin(){}
```
2. 可以使用@GetMapping注解。

```java
@GetMapping(value="/toLogin")
public ModelAndView toLogin(){}
```

## 怎样在控制器方法里面得到request或者session？

直接在控制器方法的形参中声明request，session，SpringMvc就会自动把它们注入。

```java
@RequestMapping("/login")
public ModelAndView login(HttpServletRequest request, HttpSession session){
    
        }
```

## 如果想在拦截的方法里面得到从前台传入的参数,怎么得到？

直接在控制器方法的形参里面声明这个参数就可以，但名字必须和传过来的参数名称一样，否则参数映射失败。


下面方法形参中的userId，就会接收从前端传来参数名称为userId的值。

```java
@RequestMapping("/deleteUser")
public void deleteUser(Long userId){
    //删除用户操作...
}
```

## 前台传入多个参数,并且这些参数都是一个对象的属性,怎么进行参数绑定？

直接在控制器方法的形参里面声明这个参数就可以，SpringMvc就会自动会请求参数赋值到这个对象的属性中。


下面方法形参中的user用来接收从前端传来的多个参数，参数名称需要和User实体类属性名称一致。

```java
@RequestMapping("/saveUser")
public void saveUser(User user){
    //保存用户操作...
}
```


```java
@Data
public class User {
    private Long userId;
    private String username;
    private String password;
    //...
}
```

## SpringMVC用什么对象从后台向前台传递数据的？

1. 使用Map、Model和ModelMap的方式，这种方式存储的数据是在request域中

```java
@RequestMapping("/getUser")
public String getUser(Map<String,Object> map,Model model,ModelMap modelMap){
    //1.放在map里  
    map.put("name", "xq");
    //2.放在model里，一般是使用这个
    model.addAttribute("habbit", "Play");
    //3.放在modelMap中 
    modelMap.addAttribute("city", "gd");
    modelMap.put("gender", "male");
    return "userDetail";
}
```

2. 使用request的方式

```java
@RequestMapping("/getUser")
public String getUser(Map<String,Object> map,Model model,ModelMap modelMap,HttpServletRequest request){
    //放在request里  
    request.setAttribute("user", userService.getUser());
    return "userDetail";
}
```

3. 使用ModelAndView

```java
@RequestMapping("/getUser")  
public ModelAndView getUser(ModelAndView modelAndView) {
    mav.addObject("user", userService.getUser());  
    mav.setViewName("userDetail");  
    return modelAndView;  
}  
```

## 怎么样把ModelMap里面的数据放入session里面？

在类上添加`@SessionAttributes`注解将指定的Model数据存储到session中。

### @SessionAttributes

1. 默认情况下Spring MVC将模型中的数据存储到request域中。
当一个请求结束后，数据就失效了。如果要跨页面使用。
那么需要使用到session。而@SessionAttributes注解就可以使得模型中的数据存储一份到session域中。 
2. @SessionAttributes只能定义在Class,interface enum上，
作用是将指定的Model中的键值对添加至session中，方便在一个会话中使用。

### @SessionAttributes参数

* names：这是一个字符串数组。里面应写需要存储到session中数据的名称。 
* types：根据指定参数的类型，将模型中对应类型的参数存储到session中。 
* value：其实和上面的names是一样的。

```java
@SessionAttributes(value={"names"},types={Integer.class})
@Controller
public class session{

    @RequestMapping("/session")
    public String session(Model model){
        model.addAttributes("names", Arrays.asList("caoyc","zhh","cjx"));
        model.addAttributes("age", 22);
        return "/session";
    }
}
```
在类上添加`@SessionAttributes`注解，并指定将names名称的Model数据存储到session域中，
以及将Integer类型的Model数据存储到session域中。

## SpringMVC中有个类把视图和数据都合并的一起的,叫什么？

它就是ModelAndView。

* 使用ModelAndView类存储处理完后的结果数据，以及显示该数据的视图。
从名字上看ModelAndView中的Model代表模型，View代表视图，从名字看就很好地解释了该类的作用。
Controller处理器调用模型层处理完用户请求后，把结果数据存储在该类的model属性中，
把要返回的视图信息存储在该类的view属性中，然后把ModelAndView返回给前端控制器。
前端控制器通过调用配置文件中定义的视图解析器，对该对象进行解析，最后把结果数据显示在指定的页面上。 
* 返回指定页面:
ModelAndView构造方法可以指定返回的页面名称。
也可以通过setViewName()方法跳转到指定的页面 。 

* 返回所需数值:
使用addObject()设置需要返回的值，
addObject()有几个不同参数的方法，可以默认和指定返回对象的名字。



