
# <font color="#00b050">INSERT</font>


- Table にデータを挿入する
  
- <font color="#ffc000">INTO 句</font>を使って、データを挿入するテーブルを指定する
  
- Table 名の後ろに () を入れて、データを挿入する Column を指定できる。ここで宣言されないcolumn には null が入る
  
- keyword に入れる値が String  の場合は、シングルクォーテーション ' で囲う

```sql
INSERT INTO movies (title, rating, released) VALUES
('TLOTR Ⅲ', 10, 1999), 
('The Godfather Ⅱ', 9, 1999);

/*movies は以下のような Column を持つ
CREATE TABLE movies (
	title,
	released,
	overview,
	rating,
	director
);
*/
```


> [!NOTE] フィールドを指定しないやり方は非推奨
> - Value の順番をすべて覚えている必要があるため
> - 一部の Column の値が不明な場合、マニュアルで null を入力する必要があるため
>   
> ```sql
> INSERT INTO movies  VALUES
('The Godfather', 1980, 'The best movie in the world', 10, 'F.F.C'),
('1984', 1983, 'good', 10, 'dont know');
> ```


# <font color="#00b050">UPDATE</font>


- 既存のデータの値を更新するとき使う
  
- 値の変更は <font color="#ffc000">SET 句</font>を使って行うことができる
  
- WHERE 句を使わないと、<span style="background:#fff88f">テーブル上の全てのデータに対し</span>クエリが実行されてしまうため、実行スコープを常に意識する

```sql
UPDATE movies SET rating = rating + 2 WHERE title = 'The Lord of The Rings';
UPDATE movies SET director = 'Unkown' WHERE director IS NULL;
```

> [!NOTE] null は値ではないことに注意
> null が使われないようにしたい時など、null な column すべてに値を格納したい場合は、 {columnName} = null ではなく {columnName} IS NULL を使う。
> 
null は値ではなく一種の状態を表すものと考える。
ちなみに null = null 論理式は常に <font color="#d83931">false</font> となる


# <font color="#00b050">DELETE </font>


- 指定したデータを削除したいときに使う
  
- <font color="#ffc000">FROM 句</font>を使って、データを削除するテーブルを指定する
  
- UPDATEと同様、<span style="background:#fff88f">デフォルトではテーブル全体に対し</span>クエリが実行されてしまうので、実行スコープを常に意識する

```sql
DELETE FROM movies WHERE movie_id = 3;
```


# <font color="#00b050">SELECT</font>


- 条件に当てはまるデータを取得したいときに使う
  
- 条件には、値を返すものであれば何を入れてもいい。
  関数、または演算子を使うこともできるし、その両方を組み合わせて使うこともできる
	  `アスタリスク '＊' を使って、テーブルの全てのデータを以てくることもできる`
  
- <font color="#ffc000">FROM 句</font>を使って、クエリを実行させるテーブルを指定することができる
  
- SELECT クエリによって取得された結果は、Table のような構造を持っている。
  指定した各条件が column 名となっている。
	  `SELECT => Table を返すという認識でいいかも`
  
- <font color="#ffc000">AS句</font> を使って column 名を別名変更できる
- CASE ~ WHEN ~ THEN ~ END 句を使って、値それぞれに対し、


```sql
SELECT
	REPLACE(title, ': Part One', 'I') AS title, -- 文字列を置き換えて表示する関数
	rating * 2 AS double_rating,
	UPPER(overview) AS overview_upp -- 対象columnを大文字に変換して表示する関数
FROM
	movies
WHERE
	release_date < 2024;
```


> [!NOTE] SELECT 文の論理的な意味での処理順序
> SELECT 文は、一見すると書かれている順に実行されるように感じるが、実際は実行順序という概念がある。
> 上の例では FROM が先に実行され、続いて WHERE, SELECT の順で実行される。
> 
> <font color="#ff0000">実際の実行順序の話ではないことに注意。実際にCPUで計算する順序やファイルアクセスの処理順序についてはRDBMSによって異なったりなどして、決まりがない</font>
> 
> `誤) (条件) と一致するデータを (テーブル) から抽出`
> 
> `正) (テーブル) から (条件) と一致するデータを抽出` <= FROM 句の処理が先
