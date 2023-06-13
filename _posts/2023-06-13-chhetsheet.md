public int insert(Goods goods) {
		int count = 0;
		Connection conn = null;
		PreparedStatement pstmt = null;

		try {
			conn = getConnection();
			//1. db接続
            
            //2. クエリの作成・実行
            
            //3. 返答をreturnする

        } catch (SQLException | ClassNotFoundException e) {
			e.printStackTrace();
			return count;
		} finally {
			try {
				if (pstmt != null) {
					pstmt.close();
				}
			} catch (SQLException e) {
				e.printStackTrace();
			}

			try {
				if (conn != null) {
					conn.close();
				}
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
	}
