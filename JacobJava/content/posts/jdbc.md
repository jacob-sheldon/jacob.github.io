---
title: "JDBC Basic Tutorial"
date: 2022-10-22T19:09:24+08:00
draft: false
---

As description in Wikipedia, JDBC is the abbreviation of Java Database Connectivity. 

It's an application programming interface (API) written with Java, defining some interfaces to operate database.

There are a series of database systems, every database has its unique api, so we must write different code for a database system when we want to interact with it.
 It's will use us a lot of time to do this repeatable work, so horrible.

With JDBC, we can just use the apis of JDBC to operate a specified database. Every database provider will adapte the database to be operated by JDBC.

**JDBC is the middleman between applicaton developer and database provider.**

In [this oracle website](https://docs.oracle.com/javase/tutorial/jdbc/basics/index.html) you can find all about JDBC.

JDK and DBMS (dabase manager system) are the prior of starting to write the first line code. This blog is for JDBC, so you need install these by yourself. In following examples, I select MySQL as the DBMS, so I add mysql-connector-java.jar to my project.

The first step to operate database is connecting it. For doing this, we will use `Properties` to collect information DBMS need, and `DriverManager` class offered by `java.sql` package.

``` Java
public static Connection getConnection() throws Exception {
	Connection conn = null;
	Properties prop = new Properties();
	prop.put("user", "root");

	conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/test", prop);
	return conn;
}
```

It's so easy, but there are some little points need your attention:
- `jdbc:mysql://localhost:3306/test` is url, this format is fixed.
- A connection must be corresponded to a close, otherwise there will be a memory leak.

``` Java
public static void close(Connection conn) {
	try {
		if (conn != null)
			conn.close();
	} catch (SQLException e) {
		e.printStackTrace();
	}
}
```

After connection is establised, you can operate the database, for doing this, a statement is needed.

``` Java
Connection conn = null;
PreparedStatement ps = null;
try {
	String sql = "insert into users (name, email, birth) values (?, ?, ?)";
	conn = JDBC.getConnection();
	ps = conn.prepareStatement(sql);
	ps.setObject(1, "mike");
	ps.setObject(2, "mike@gmail.com");
	ps.setObject(3, "2003-10-31");
	ps.execute();
} catch (SQLException e) {
	e.printStackTrace();
} catch (Exception e) {
	e.printStackTrace();
} finally {
	JDBC.close(conn, ps);
}
```

The insert, delete, update opertion is similar, so i don't show it anymore. Query has a little bit different, and more difficult.

``` Java
Connection conn = null;
PreparedStatement ps = null;
try {
	conn = JDBC.getConnection();
	String sql = "select id, name, email, birth from users where id < ?";
	ps = conn.prepareStatement(sql);
	ps.setObject(1, 10);
	ResultSet ret = ps.executeQuery();
	while (ret.next()) {
		int id = ret.getInt(1);
		String name = ret.getString(2);
		String email = ret.getString(3);
		Date birth = ret.getDate("birth");
		System.out.print("id = " + id + "name = " + name + " email = " + email + " birth = " + birth);
	}
} catch (Exception e) {
	e.printStackTrace();
} finally {
	JDBC.close(conn, ps);
}
```

This is all about JDBC basic operation.