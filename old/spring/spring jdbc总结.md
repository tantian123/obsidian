JdbcTemplate
NamedParameterJdbcTemplate
SimpleJdbcInsert and SimpleJdbcCall
RDBMS Objects including MappingSqlQuery, SqlUpdate and StoredProcedure


Spring Framework的JDBC抽象框架由四个不同的包组成，分别是core、datasource、object和support。

org.springframework.jdbc.core包含JdbcTemplate类及其各种回调接口，以及各种相关类。一个名为org.springframework.jdbc.core.simple的子包含SimpleJdbcInsert和SimpleJdbcCall类。另一个名为org.springframework.jdbc.core.namedparam的子包含NamedParameterJdbcTemplate类和相关支持类。参见第14.2节“使用JDBC核心类控制基本的JDBC处理和错误处理”，第14.4节“JDBC批处理操作”和第14.5节“使用SimpleJdbc类简化JDBC操作”。

org.springframework.jdbc.datasource包含一个用于简化DataSource访问的实用类，以及各种简单的DataSource实现，可用于测试和在Java EE容器之外运行未修改的JDBC代码。一个名为org.springframework.jdbc.datasource.embedded的子包提供了使用HSQL和H2等Java数据库引擎创建内存数据库实例的支持。参见第14.3节“控制数据库连接”和第14.8节“嵌入式数据库支持”。

org.springframework.jdbc.object包含表示RDBMS查询、更新和存储过程的类，这些类是线程安全的可重用对象。参见第14.6节“将JDBC操作建模为Java对象”。尽管通过查询返回的对象与数据库“断开连接”，但这种方法是由JDO建模的。这种更高级别的JDBC抽象依赖于org.springframework.jdbc.core包中的较低级别抽象。

org.springframework.jdbc.support包提供了SQLException转换功能和一些实用类。在JDBC处理过程中抛出的异常被转换为org.springframework.dao包中定义的异常。这意味着使用Spring JDBC抽象层的代码不需要实现特定于JDBC或RDBMS的错误处理。所有转换的异常都是未检查异常，这使您可以选择捕获可以恢复的异常，同时允许其他异常传播给调用者。参见第14.2.3节“SQLExceptionTranslator”。




```
加载数据库驱动
获取数据库连接 通过URL 账号密码
定义sql
通过数据库连接 创建statement对象或者preparedStatement
通过statement对象执行sql语句 得到ResultSet对象
读取ResultSet对象 转换为JavaBean
关闭数据库连接 statement ResultSet


Class.forName("com.mysql.cj.jdbc.Driver");
Connection conn = DriverManager.getConnection(url,userName,passWord);
String sql = "select * from person where pname=?";

PreparedStatement ps = conn.prepareStatement(sql);

ps.setString(1, "itcoke");

ResultSet rs = ps.executeQuery(); 
while(rs.next()){ 
person = new Person(); 
person.setPid(rs.getLong("pid")); 
person.setPname(rs.getString("pname")); 
personList.add(person); 
}


如果是JdbcTemplate模板类 代码是
JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource); 
String sql = "SELECT COUNT(*) FROM employees WHERE department = ?"; 
int count = jdbcTemplate.queryForObject(sql, Integer.class, "IT");




```

为什么这样操作不行，需要另外的ORM框架：

因为每次都要创建连接 关闭连接 ，所以要使用数据库连接池
sql硬编码在代码中，如果sql修改，需要重新编译Java代码
从resultset中遍历结果集时，不利于维护
重复代码多
没有缓存


常用的数据库连接池
C3P0、DBCP、**Druid**


如何实现分页
```sql
 Oracle的分页依赖于ROWNUM这个伪列，ROWNUM主要作用就是产生行号
 
SELECT *FROM ( SELECT 列名,列名,ROWNUM rn FROM 表名 WHERE ROWNUM<=(currentPage*lineSize)) temp WHERE temp.rn>(currentPage-1)*lineSize;


 Mysql分页语法：
      @start---偏移量，不设置就是从0开始【也就是(currentPage-1)*lineSize】
      @length---长度，取多少行数据

SELECT *
    FROM 表名
    LIMIT [START], length;

```