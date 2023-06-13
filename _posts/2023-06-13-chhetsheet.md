---
title: Cheetsheet
author: kan00n
date: 2023-06-12 00:00:00 +0900
categories: [java]
tags: [java]
math: true
mermaid: truet7we

---

# Cheetsheet

##　注意事項

- 変数名は`<val>`って書いてます
- 

## java servlet

### import類

```java
import java.io.IOException;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
```

### requestの値の取得

```java
		// 入力パラメータをデコードする文字コード指定
		request.setCharacterEncoding("UTF-8");

		// リクエストパラメータの取得 -> String型
		String id = request.getParameter(<val>);
		// リクエストパラメータの取得 -> Int型
		String id = Integer.parseInt(request.getParameter(<val>));
```

### エラー処理

#### 空かどうかの判定

```java
if(id.isEmpty()){
    // <id>が空の時
    return true;
} 
// <id>が空でない時
return false;
```

#### 文字列に合うかどうかの判定

```java
//if(id.matches("\\d+")){
if(id.matches(<正規表現>)){
    // <正規表現>と同じ時
    return true;
} 
// <正規表現>と異なる時
return false;
```

### javaの変数をjspに渡すとき

- <val1>の変数をjsp用の変数<val2>として利用する時

```java
//request.setAttribute("goods", goods);
request.setAttribute("<val2>", <val1>);
```

### jspに処理の流すとき

- <url>にjpsのアドレスを指定して送る

```java
		// コンテキストオブジェクトの参照
		ServletContext sc = getServletContext();

		// リクエストのフォワード
		RequestDispatcher rd = sc.getRequestDispatcher(<url>);
		rd.forward(request, response);
	}
```



## Java Beans

### 基本形

まずは指示に従って欲しいが、一応基本形を書くと

- メンバ変数は `private`で宣言
- 引数に対応したsetterとgetterを`public`で作る
- (コンストラクタは空とすべてのものを作る)

```java
public class SeasonBean implements Serializable {

    //変数定義
	private int month; 		// 月

	// 引数なしのコンストラクタ
	public SeasonBean() {
	}

	// アクセサメソッド(getter)
	public int getMonth() {
		return month;
	}
	// アクセサメソッド(setter) 
	public void setMonth(int month){
		this.month = month;
	}
```

### エラー付きのsetter

- メゾットに`throws + <エラーの名前>`を追加することで `throw new`でエラーとして返すことができる

```java
	public void setMonth(int month) throws NumberFormatException{
		if (month < 1 || month > 12) {
			throw new NumberFormatException();
		}
		this.month = month;
	}
```

## Java BeanDAO

### SQL用のimport類

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
//ArrayList用のインポート
import java.util.ArrayList;
```

### 共通するsql文の土台部分と取り出しの格納ArrayList

- 修飾子として`private static final`がつく

- ?はそこに文字や数字が後から入る

```java
// 商品テーブルから抽出した商品リスト
	private ArrayList<Goods> list = new ArrayList<Goods>();

// 商品テーブルから全件参照するSQL
	private static final String SQL_SELECTALL_GOODS = "select * from goods";

	// 商品テーブルから商品名のキーワード検索するSQL
	private static final String SQL_SELECT_BY_NAME_GOODS = "select * from goods where name like ? ORDER BY name";
```

### javaとsqlをつなぐおまじない

- <データベースの名前>にはデータベースの名前を書く
  - `dbmvc`なら`~localhost:3306/dbmvccharacterEncoding=~?`って感じで

```java
	public Connection getConnection() throws SQLException, ClassNotFoundException {
		Connection conn = null;

		Class.forName("com.mysql.cj.jdbc.Driver");
		conn = DriverManager.getConnection(
				"jdbc:mysql://localhost:3306/<データベースの名前>?characterEncoding=utf8&useSSL=false&serverTimezone=GMT%2B9:00&rewriteBatchedStatements=true&allowPublicKeyRetrieval=true",
				"root", "mysql");

		return conn;
	}
```

### SQLからBeansに格納するメゾット

#### 説明

```java
	private void readResultSet(ResultSet rs) throws SQLException {
        // listの中身を空にする
		list.clear();
		while (rs.next()) {
            //Beansのインスタンス作成
			//Goods goods = new Goods();
            <Beans> <beans> = new <Beans>();
			//rsからbeansへの値の受け渡し
            //goods.setId(rs.getInt("id"));
			//goods.setName(rs.getString("name"));
			//goods.setPrice(rs.getInt("price"));
            
            //Intで受け取るならこれ
            <beans>.<set○○>(re.getInt("<table name>"))
			//Stringで受け取るならこれ
            <beans>.<set○○>(re.getString("<table name>"))
			//listに追加する
            list.add(goods);
		}
	}

	private void readResultSet(ResultSet rs) throws SQLException {
		list.clear();
		while (rs.next()) {
			<Beansのクラスの名前> goods = new Goods();
			goods.setId(rs.getInt("id"));
			goods.setName(rs.getString("name"));
			goods.setPrice(rs.getInt("price"));
			list.add(goods);
		}
	}
```

#### chhetsheet

```java
	private void readResultSet(ResultSet rs) throws SQLException {
		list.clear();
		while (rs.next()) {
            //rsからBeansに移し替え
            
            //リスト追加
		}
	}
```





### 全選択のsql文のメゾット

#### 説明

```java
public ArrayList<Goods> selectAll() {
		Connection conn = null;
		PreparedStatement pstmt = null;
		ResultSet rs = null;
		try {
			conn = getConnection();
            //pstmt = conn.prepareStatement(SQL_SELECTALL_GOODS);
			pstmt = conn.prepareStatement(<SQL文の選択>);
			rs = pstmt.executeQuery();
            //reからの情報の取り出し(上で説明済み)
			readResultSet(rs);
            <取り出してAllay Listに格納するjavaコード>
			return list;
		} catch (SQLException | ClassNotFoundException e) {
			e.printStackTrace();
			return null;
		} finally {
			try {
				if (rs != null) {
					rs.close();
				}
			} catch (SQLException e) {
				e.printStackTrace();
			}
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
```

#### cheetsheet

```java
public ArrayList<Goods> selectAll() {
		Connection conn = null;
		PreparedStatement pstmt = null;
		ResultSet rs = null;
		try {
			//1. db接続
            
            //2. クエリの作成・実行
            
            //3. 返答をパースして配列に格納
            
            //4　returnで配列を返す           
			
		} catch (SQLException | ClassNotFoundException e) {
			e.printStackTrace();
			return null;
		} finally {
			try {
				if (rs != null) {
					rs.close();
				}
			} catch (SQLException e) {
				e.printStackTrace();
			}
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
```



## Id（主キー選択）のSQL文

#### 説明

```java
public Goods selectById(int id) {
		Goods goods = null;
		Connection conn = null;
		PreparedStatement pstmt = null;
		ResultSet rs = null;

		try {
			conn = getConnection();
			//pstmt = conn.prepareStatement(SQL_SELECT_BY_ID_GOODS);
            pstmt = conn.prepareStatement(<SQL文の選択>);
			pstmt.setInt(1, id);
			rs = pstmt.executeQuery();
            //reからの情報の取り出し(上で説明済み)
			readResultSet(rs);

			//商品IDの検索結果があれば、商品オブジェクトを取得する
            //ID検索なので結果は0or1しかないので0しかない
			if (list.size() > 0) {
				goods = list.get(0);
			}
			return goods;
		} catch (SQLException | ClassNotFoundException e) {
			e.printStackTrace();
			return null;
		} finally {
			try {
				if (rs != null) {
					rs.close();
				}
			} catch (SQLException e) {
				e.printStackTrace();
			}
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
```

#### cheetshhet

```java
public Goods selectById(int id) {
		Goods goods = null;
		Connection conn = null;
		PreparedStatement pstmt = null;
		ResultSet rs = null;

		try {
			//1. db接続
            
            //2. クエリの作成・実行
            
            //3. 返答をパースして配列に格納
            
            //4.1　returnで配列の0番目があれば返す
            //4.2　ないならNULLを返す
			
        } catch (SQLException | ClassNotFoundException e) {
			e.printStackTrace();
			return null;
		} finally {
			try {
				if (rs != null) {
					rs.close();
				}
			} catch (SQLException e) {
				e.printStackTrace();
			}
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
```

### SQLに追加する文

#### 説明

```java
public int insert(Goods goods) {
		int count = 0;
		Connection conn = null;
		PreparedStatement pstmt = null;

		try {
			conn = getConnection();
            
			//pstmt = conn.prepareStatement(SQL_INSERT_GOODS);
			pstmt = conn.prepareStatement(<SQL文の選択>);
            //SQL文の?の箇所を埋める
            //pstmt.setInt(1, goods.getId());
			//pstmt.setString(2, goods.getName());
			//pstmt.setInt(3, goods.getPrice());
            //SQLのINTやCHAR・VARCHARに対応する
            //INTなら以下の感じ
            pstmt.setInt(1, goods.getId());
            //Stringなら以下の感じ
            pstmt.setString(1, goods.getName());
            
            //クエリの発行してSQLをアップデート
			count = pstmt.executeUpdate();
            //正常に動作すると1が帰り、異常だと0になる
			return count;
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
```

#### cheetsheet

```java
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
```





