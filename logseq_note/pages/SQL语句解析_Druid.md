- druid 解析
	- 参考
		- https://developer.aliyun.com/article/1000273
		- https://cloud.tencent.com/developer/article/2310842
		- http://www.52xingchen.cn/detail/88
		- https://github.com/lulumengyi/Hive_SQL_AST
	- 解析sql
		- ```
		  // 第一种方式
		  SQLStatement statement = SQLUtils.parseSingleMysqlStatement("select name, age from users");
		  
		  // 第二种方式
		  SQLStatementParser parser = new MySqlStatementParser("select name, age from users");
		  SQLStatement statement = parser.parseStatement();
		  ```
	- Select语句
		- ```
		  SQLStatement statement = SQLUtils.parseSingleMysqlStatement("select name, age from users");
		  
		  // 解析后, 确定语句类型就可以遍历 AST 处理节点
		  SQLSelect select = ((SQLSelectStatement) statement).getSelect();
		  SQLSelectQueryBlock queryBlock = (SQLSelectQueryBlock) select.getQuery();
		  ```
	- 替换节点内容
		- ```
		  // 修改节点, name 字段包一个 COUNT() 函数
		  queryBlock.getSelectList().forEach(selectItem -> {
		      if (selectItem.toString().equalsIgnoreCase("name")) {
		          SQLAggregateExpr countExpr = new SQLAggregateExpr("COUNT");
		          countExpr.addArgument(new SQLIdentifierExpr("name"));
		          selectItem.setExpr(countExpr);
		      }
		  });
		  
		  // 输出改写后的 SQL
		  String modifiedSQL = SQLUtils.toSQLString(statement, JdbcConstants.MYSQL);
		  System.out.println("改写后的 SQL: " + modifiedSQL);
		  ```
	- 自定义 Visitor
		- ```
		  // 自定义 Visitor 进行复杂改写
		  // 将表名 users 改成 new_users
		  statement.accept(new MySqlASTVisitorAdapter() {
		      @Override
		      public boolean visit(SQLSelectQueryBlock x) {
		          // 示例：将表名改为新表名
		          if ("users".equalsIgnoreCase(x.getFrom().toString())) {
		              x.setFrom(new SQLIdentifierExpr("new_users"));
		          }
		          return super.visit(x);
		      }
		  });
		  ```