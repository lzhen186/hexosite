---
title: MySQL巩固第三天
date: 2021-12-21T06:46:06.009Z
---
1. 用户管理

   ```sql
   --当前用户
   select user()
   --刷新权限
   flush privileges
   --创建新用户
   create user kuangshen identified by '123456'

   --用户重命名
   rename user kuangshen to kuangshen2
   --用户设密码
   set password for kuangshen2 = password('111111')
   --删除用户
   drop user kuangshen2
   --用户设访问权限
   grant all privileges on *.* to kuangshen
   --显示用户的权限
   show grants for root@localhost;
   show grants;
   --删除用户的权限
   revoke all privileges, grant option from kuangshen
   ```
2. 三大范式

   > 不合规范的表设计会导致的问题：
   >
   > 信息重复、更新重复、插入重复、删除重复

   * 第一范式

     所有字段值都是不可分解的原子值
   * 第二范式

     需要确保数据库表中的每一列都和主键相关
   * 第三范式

     每一列数据都和主键直接相关，而不能间接相关

     `🔗:`<https://www.cnblogs.com/linjiqin/archive/2012/04/01/2428695.html>``
3. JDBC

   1. 查询的例子

      ```java
      //要连接的数据库URL
      String url = "jdbc:mysql://localhost:3306/jdbcStudy?useUnicode=true&characterEncoding=utf8&useSSL=true"; //连接的数据库时使用的用户名
      String username = "root"; //连接的数据库时使用的密码
      String password = "111111";

      Connection conn = null;
      Statement st = null;
      ResultSet rs = null;
      try {
          //1.加载驱动
          Class.forName("com.mysql.cj.jdbc.Driver");//推荐使用这种方式来加载驱动
          //2.获取与数据库的链接
          conn = DriverManager.getConnection(url, username, password);
          //3.获取用于向数据库发送sql语句的statement
          st = conn.createStatement();

          String sql = "select id,name,password,email,birthday from users";
          //4.向数据库发sql,并获取代表结果集的resultset
          rs = st.executeQuery(sql); //executeUpdate更新
          //5.取出结果集的数据
          while(rs.next()) {
              System.out.println("id=" + rs.getObject("id"));
              System.out.println("name=" + rs.getObject("name"));
              System.out.println("password=" + rs.getObject("password"));
              System.out.println("email=" + rs.getObject("email"));
              System.out.println("birthday=" + rs.getObject("birthday"));
          }


      } catch (ClassNotFoundException e) {
          e.printStackTrace();
      } catch (SQLException e) {
          e.printStackTrace();
      }finally {
          try {
              //6.关闭链接，释放资源
              rs.close();
              st.close();
              conn.close();
          } catch (SQLException e) {
              e.printStackTrace();
          }
      }
      ```
   2. 防止注入用

      ```java
      ......
      String sql  = "select * from users where name= ? and password = ?";
      st = conn.prepareStatement(sql);
      st.setString(1,'zhangsan');
      st.setString(2,'123456');
      rs = st.executeQuery();
      while (rs.next()){
          System.out.println(rs.getString("name"));
      }
      ......
      ```
   3.  事务

      ```java
      conn.setAutoCommit(false);
      //多个prepareStatement语句....//
      conn.commit();
      //....
      //捕获里   
      conn.rollback();
      ```
   4. C3P0数据源

      ```java
      ComboPooledDataSource dataSource = new ComboPooledDataSource();
      Connection conn = null;
      PreparedStatement ps = null;
      ResultSet rs = null;
      try {
          dataSource.setDriverClass("com.mysql.cj.jdbc.Driver");
          dataSource.setUser("root");
          dataSource.setPassword("111111");
          dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/jdbcStudy?characterEncoding=utf8&useSSL=true&useUnicode=true");
          dataSource.setInitialPoolSize(0);
          dataSource.setMinPoolSize(5);
          dataSource.setMaxPoolSize(10);

          conn = dataSource.getConnection();
          String sql = "select * from users";
          ps = conn.prepareStatement(sql);
          rs = ps.executeQuery();
          while(rs.next()){
              System.out.println(rs.getString("name"));
          }
      } catch (PropertyVetoException e) {
          e.printStackTrace();
      } catch (SQLException e) {
          e.printStackTrace();
      } finally {
          try {
              rs.close();
              ps.close();
              conn.close();
              dataSource.close();
          } catch (SQLException e) {
              e.printStackTrace();
          }
      }
      ```