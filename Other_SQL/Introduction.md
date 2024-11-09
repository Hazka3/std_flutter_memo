

# <span style="background:#d3f8b6">SQLとは</span>

SQL (Structured Query Language) とは、データベース管理システム (RDBMS - Relational data base management system) のデータを管理するため設計されたプログラミング言語で、多くのDB関連プログラムがSQLを標準として採択している。

SQLは以下のように更に細分化することができる。
-  [[DDL (Data Definition Language)]]
	データではなく、DBそのものの構造を作成・変更・削除に用いられる命令
		`ex) CREATE, DROP, RENAME

-  <font color="#0070c0">DML (Data Manipulation Language)</font>
	DB内のデータに対する操作を行う命令で、さらに update と query でカテゴリーを分けることができる。 <font color="#d83931">query 命令は、 SELECT のみが該当する</font>
		`ex) SELECT, INSERT, UPDATE, DELETE 

- #### <font color="#0070c0">TCL (Transaction Control Language)</font>
	DB内でのトランザクションの開始や終了に使用する命令
		`ex) COMMIT, ROLLBACK, SAVEPOINT`

- <font color="#0070c0">DCL (Data Control Language)</font>
	DBに対するアクセス権の制御や、DBオブジェクトを管理する時に使用される命令
		`ex) GRANT, REVOKE`



# <span style="background:#d3f8b6">共通するルール的なもの</span>
-  注釈の入れ方
	single line → --{comment}
	multi line → /\* {comment} \*/ 

- 大文字小文字の区別
	大・小区分はされず、SELECT と select で処理は一緒

- クエリ最後には基本的にはセミコロン ";" をつける