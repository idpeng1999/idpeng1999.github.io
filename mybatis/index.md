# Mybatis

## Mybatis的xml,xml这个映射文件，和mybatis内部数据结构之间的这个映射关系是什么样的？

* 这个映射关系，就是我们的Mybatis,他就是把我们的这个所有的xml的一个配置信息都封装到一个重量级对象里面，
  这个重量级对象就是封装到我们的Configguration的内部，然后在我们的这个xml映射文件中呢，
  我们的parameterMap呢，这个标签会被我们解析成我们的parameterMap对象，然后他这里的每一个子元素，
  都会被我们解析，解析成我们的ParameterMapping对象，然后我们返回的结果级嘛，我们的<ResultMap>嘛，
  这个标签，会被我们解析成一个ResultMap一个对象，他这里面的每个子元素啊，都会被我们解析成ResultMapping一个对象，
  所以他这些标签的话，就是每一个我们的增删改查嘛，我们的select、insert、update、delete这些标签呢，
  都会被我们解析成我们的MappedStatement这么一个对象，然后我们标签内的这些sql,会被我们解析成BoundSql 的Sql对象。

## Mybatis是如何进行分页的？分页插件的原理是什么？

* 这个是比较常用的一个功能，就是我们的Mybatis是使用我们的这个RowBounds对象去进行一个分页的，
  他主要是针对我们的ResultSet一个结果级，去执行的一个内存分页，所以他这个其实不是一个物理分页，
  这个可以在我们sql里面去直接写带有这个物理分页的参数，然后去完成我们的物理分页功能，也可以呢使用我们的一个分页插件，
  这个也是比较常用的，来去实现我们的一个物理分页，那就是我们在用这个分页插件，他的基本原理，
  其实就是使用我们Mybatis里面提供给我们的这些插件啊这些接口，然后去实现我们的一个自定义插件，
  然后我们在这个插件的拦截方法内，去拦截我们这个待执行的sql语句嘛，那我们就重写sql语句，
  然后根据像我们的一个dalect方言，也可以去添加这个对应的这些物理分页语句，和我们的物理分页的一些参数，这些原理就这样。

  
  `pageHelper`

## mybatis和mybatis plus有啥区别？

* MyBatis前身是iBatis，是java的持久层框架，可以使用XML或者注解进行映射和配置，
通过将参数映射到配置的SQL最终解析为执行的SQL语句，查询后将SQl结果集映射成java对象返回。 
* MyBatis-plus相当于是MyBatis的增强工具，例如内置 Sql 注入剥离器，预防了Sql注入攻击，
也支持代码生成，内置了分页插件、性能分析插件和全局拦截插件。

## mybatis是怎么和数据库交互的？

* 通过dao层进入到mapper，然后用id去关联的。Resulttype(锐肉太不)是它所在的路径。还有用到resultMap。
如果是使用的mybatis-Plus，我们可以直接调save等方法，不用手写sql，会用plus封装好的方法直接查询数据。
当使用resultType做SQL语句返回结果类型处理时，对于SQL语句查询出的字段在相应的路径中必须有和它相同的字段对应，
而resultType中的内容就是在本项目中的位置。当使用resultMap做SQL语句返回结果类型处理时，
通常需要在mapper.xml中定义resultMap进行路径和相应表字段的对应。

* 常用的sql标签有test，if，choose，when等

## #{}和${}的区别是什么？

* ${}:是变量占位符，它可以用于标签属性值和 sql 内部，属于静态文本替换，比如${driver}会被静态替换为com.mysql.jdbc.Driver。 
* 而#{}:是 sql 的参数占位符，MyBatis 会将 sql 中的#{}替换为?号，然后会将参数替换 
* 另外使用 #{} 可以有效的防止SQL注入，提高系统安全性。


