PreparedStateent:

#why:

<1>.使用statement 拼写SQl语句，是非常困难的；
<2>.使用Statement 可以发生Sql 注入；

#what：
<1>他还是statement的子接口；
<2>可以传入带占位符的SQL，并且提供补充占位符的方法；

#how：
<1>创建PreparedStatement :<br>

 	String sql = "INTER INTO tablename VALUES(?,?,?,?)"
  	PreparedStatement ps = conn.preparedStatement(sql);
  	ps.setXxx(int index,Object val);//设置占位符
  	ps.executQuery();//或者executUpdate();
  	
  	
 只有一个
  