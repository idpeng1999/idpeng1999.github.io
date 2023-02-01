# Mybatis传参方式


[//]: # (![集合]&#40;/img/集合/img.png&#41;)

* 4种方式

## 顺序传参法(不建议使用，sql层表达不直观，且一旦顺序调整容易出错)

```java

public User selectUser(String name, int deptId);

<select id="selectUser" resultMap="UserResultMap">
    select * from user
    where user_name = #{0} and dept_id = #{1}
</select>

```

* `#{}`里面的数字代表你传入参数的顺序。

## @Param注解传参法

```java

public User selectUser(@Param("userName") String name, int @Param("deptId") deptId);

<select id="selectUser" resultMap="UserResultMap">
    select * from user
    where user_name = #{userName} and dept_id = #{deptId}
</select>

```

* #{}里面的名称对应的是注解 @Param括号里面修饰的名称

## Map传参法

```java

public User selectUser(Map<String, Object> params);

<select id="selectUser" parameterType="java.util.Map" resultMap="UserResultMap">
    select * from user
    where user_name = #{userName} and dept_id = #{deptId}
</select>

```

* `#{}`里面的名称对应的是 Map里面的key名称。

## Java Bean传参法

```java

public User selectUser(User params);

<select id="selectUser" parameterType="com.test.User" resultMap="UserResultMap">
    select * from user
    where user_name = #{userName} and dept_id = #{deptId}
</select>

```

* #{}里面的名称对应的是 User类里面的成员属性。 
* 需建一个实体类




