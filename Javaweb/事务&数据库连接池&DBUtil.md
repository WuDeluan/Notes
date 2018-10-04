# 事务&数据库连接池&DBUtil

## 事务
> Transaction 其实是指一组操作，里卖弄包含许多个单一的逻辑，只要有一个逻辑没有子hi习惯成功，那么就算失败，所有的数据就都回归到最初的状态。

### 使用命令行的方式演示事务
* 开启事务 `start transaction`
* 提交或回滚事务
    * commit : 提交事务
    * rollback : 回滚事务
1.关闭自动提交功能
![icon](https://github.com/WuDeluan/Notes/blob/master/picture/transaction01.PNG)
2.演示事务
![icon](https://github.com/WuDeluan/Notes/blob/master/picture/transaction02.PNG)

### 使用代码演示事务
> 代码里面的事务，主要是针对连接
* 步骤
    * 1.通过conn.setAutoCommit(flase)来关闭自动提交的设置
    * 2.提交事务  conn.commit();
    * 3.回滚事务  conn.rollback();
* 代码
```java
@Test
	public void testTransaction(){		
		Connection conn = null;
		PreparedStatement ps = null;
		ResultSet rs = null;
		try {
			conn = JDBCUtil.getConnection();
			
			//默认的连接，事务是自动提交的
			//关闭自动提交
			conn.setAutoCommit(false);
			
			//转账事务
			String sql = "update account set money = money - ? where id = ?";
			ps = conn.prepareStatement(sql);
			
			//扣钱 ，扣id为1的用户100块
			ps.setInt(1, 100);
			ps.setInt(2, 1);
			ps.executeUpdate();
			
			//加钱，给id为2的用户加100元
			ps.setInt(1, -100);
			ps.setInt(2, 2);
			ps.executeUpdate();
			
			//成功：提交事务
			conn.commit();
			
		} catch (SQLException e) {
			try {
				//失败: 回滚事务
				conn.rollback();
			} catch (SQLException e1) {
				e1.printStackTrace();
			}
			e.printStackTrace();
		}finally {
			JDBCUtil.close(rs, ps, conn);
		}		
	}
  ```
