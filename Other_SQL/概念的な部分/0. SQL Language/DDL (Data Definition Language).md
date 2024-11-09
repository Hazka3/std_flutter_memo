
# <font color="#00b050">CREATE</font>

- テーブルを作成する
- テーブル名を書いて、括弧の中に column を追加する。
- すでに同名のテーブルが存在する場合はエラーとなる (テーブル自体の上書きはできない)
- constraint を使って、挿入されるデータ型や、データ範囲を制限させることができる

### <font color="#c3d69b">Constraints による型制限について</font>

- SQLite は基本的には型推論を伴うデータベースだが、STRICTモードにより、挿入されるデータの型を制限することができる
- **STRICTモード**を使用することで挿入されるデータの型を厳密に制限することもできる
  CREATE TABLE 文の末尾に <font color="#ff0000">STRICT</font> を追加して使う
	  `STRICT モードは SQlite 3.37.0 以降のみ対応`
-  宣言方法は RDBMS 共通で column 名 + Type で統一されている

```sqlite
CREATE TABLE movies ( -- SQLite での例
	title TEXT,
	released INTEGER,
	overview TEXT,
	rating REAL,
	director TEXT,
	poster BLOB
	/* 
	BLOB = Binary large object で、バイナリデータを保存できる
	ファイルサイズが大きい場合はパフォーマンスに影響を及ぼすことがあるため
	ファイル自体を追加するより、ファイルパスをTEXT型で保存するほうが一般的
	*/
)
STRICT;
```

> [!NOTE] Boolean Typeについて
> SQLite では、Bool 型は存在しない。
> Bool のように True / False を判定をしたい場合は、以下のような方法がある
>  -  データ型を INTEGER として宣言する (0 = False, 1 = True)
>  - column に特定の値 (0, 1) 以外が入らないように、CHECK 制約を使う
>    
>    (CHECK については以下を参照)


### <font color="#c3d69b">Constraints によるデータの範囲、入力制限について</font>

- 型制限をした上で、例えば文字数制限や数値の範囲など、値の範囲にも constraints を使って制限を設けることができる
- 型制限 constraints に続いて宣言する

```sqlite
  CREATE TABLE movies (
	title TEXT NOT NULL UNIQUE,
	released INTEGER NOT NULL CHECK (released > 0),
	overview TEXT NOT NULL CHECK (LENGTH(overview) < 100), --LENGTH関数をコール
	rating REAL NOT NULL CHECK (rating BETWEEN 0 AND 10),
	director TEXT,
	for_kids INTEGER NOT NULL DEFAULT 0 CHECK (for_kids IN(0,1))
)
STRICT;
	```


| <font color="#ffc000">Constraints</font>                  | <font color="#ffc000">Description</font>                                                                                                                                                                                                                                                                                                          |
| --------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| UNIQUE                                                    | column がユニークな値を持ち、重複しないようにすることができる<br>他のデータと重複するデータが挿入されようとした場合、エラーを返す                                                                                                                                                                                                                                                                            |
| DEFAULT                                                   | INSERT文で値が指定されなかった場合、デフォルトで格納される値を決められる                                                                                                                                                                                                                                                                                                           |
| NOT NULL                                                  | 値の null 型を許可しない                                                                                                                                                                                                                                                                                                                                   |
| CHECK                                                     | 条件ロジックを評価し、結果が true となる値のみ挿入するように挿入される値をチェックすることができる<br>条件内で関数を呼び出すこともできる                                                                                                                                                                                                                                                                         |
| PRIMARY KEY                                               | 対象 column を固有識別子(キー)として扱えるようになる。<br>INSERT文で値が指定されなかった場合も、デフォルトで値が格納される<br> (1, 2,,, とインクリメントされた値が入っていく)<br>    `詳しくは以下の Primary Key 項目を参照`                                                                                                                                                                                                       |
| AUTOINCREMENT<br><font color="#ff0000">(SQLite のみ)</font> | Primary Key は、データが削除されたあと、新しいデータが挿入されるとき再利用されることがあるが、AUTOINCREMENT を使うと、Primary Key の再利用を制限することができる。<br><br>最後に使われたKey の値は、AUTOINCREMENT を使うと自動生成されるsqlite_sequence という table で管理される<br><br>例えば、Key 2 のデータが削除され、新たにデータが挿入されるとき、Key 2のデータの有無に関わらず、2は既に使用されたことがあるため、Key 3 としてデータが挿入されるイメージ。<br><br>**Primary Key の auto increment 挙動は、RDBMS 毎に異なる** |

> [!NOTE] Constraints の順番について
> 型制限も制約 (Constraints)も、基本的には 型制限 → データ範囲の順で宣言する
> 
> `ex) title TEXT NOT NULL UNIQUE`
> 
> データ範囲のキーワードが先にくると、構文エラーとなる
> 
> `ex) title NOT NULL UNIQUE TEXT => syntax error`

### <font color="#c3d69b">Primary Key について</font>

- 各データ(Row) に対する固有識別子のようなもので、 immutable な値が Key として使われる。
- primary key には以下の2種類がある
	\- <font color="#ffc000">Natural Key</font>
		重複が想定されない、あるいはデータ間で固有な性質をもつことが想定される column を固有識別子として扱う方法。
		
	\- <font color="#ffc000">Surrogate Key</font>
		movie_id のように、他のcolumn に論理的な関係があるわけではなくcolumnを作成し、それを固有識別子として扱う方法。
		
	Natural Key を固有なものとして扱い続けること(不変性の保証)は難しいため、一般的には値が変更される可能性が少ない <span style="background:#fff88f">Surrogate Key</span>が使われる。

```sql
CREATE TABLE movies (-- Natural Key を使った movies テーブルの例
	movie_id INTEGER PRIMARY KEY NOT NULL,  
	title TEXT NOT NULL UNIQUE,
--以下省略

	
CREATE TABLE movies (-- Natural Key を使った movies テーブルの例
	title TEXT PRIMARY KEY NOT NULL, 
--以下省略
```



> [!NOTE] Constraints と RDBMS間の互換性
> データ型 (INT, TEXT など)は<span style="background:#fff88f">RDBMSによって呼び方が異なる</span>が、データ範囲や入力制限に関係する Constraints は <span style="background:#fff88f">基本的に RDBMS 間で共通して</span>使用される。

# <font color="#00b050">DROP</font>

- テーブルを削除する
- sql 文は基本的に確認アラートとかはないので注意が必要
- IF EXISTS を使って、テーブルが存在しない場合にもエラーを回避して処理を行うことができる

```sql
DROP TABLE IF EXISTS movies;
```

