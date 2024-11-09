
# <font color="#00b050">BETWEEN ~ END </font>
- 特定の範囲内のデータを指定したいとき使う
- 指定した値の範囲を<font color="#ff0000">含む</font> ( 以下例は、5 <= rating <= 5 という意味)
```sql
WHERE
	rating BETWEEN 5 AND 10;
```

# <font color="#00b050">IN </font>
- 複数の条件を指定したいとき。membership condition 
- ANDを使って一つ一つ作成することもできるが、こっちのほうがきれい
```sql
WHERE
	original_language IN ('en', 'es', 'de');
```

# <font color="#00b050">LIKE </font>
- ワイルドカード ('%' または '\_' ) を使用し、パターンと一致するテキスト
 - % は、0文字以上の任意の文字列、 \_ は、任意の1文字を表す
```sql
SELECT
	*
FROM
	movies
WHERE
	title LIKE 'The%' -- The から始まるタイトル(長さ無視)の映画を全てもってくる
```

```sql
SELECT
	*
FROM
	movies
WHERE
	title LIKE '__ing' -- 任意の3文字+ing をタイトルに持つ映画を全てもってくる
```

# <font color="#00b050">CASE WHEN ~ </font>
- データを特定条件で分別したい時使う
- 主に他の SQL 関数( SUM や COUNT など)と組み合わせて使われることが多い
- フォーマットは基本　when (条件) THEN (結果) 　で、WHEN を追加してelse~if文のように多重して使うこともできる。
 - 一般的な分岐文と一緒に、条件の評価がすべてFALSE だったときの処理として、ELSE 句を使うことができる。
- ELSE 句は必須ではないが、条件に合致しない場合 column には NULL が入るため、NULL を扱いたくない場合はELSE 句を使うようにする
- END に続いて、AS 句を使って、クエリ実行結果の column 名を別名変更できる

```sql
-- rating 値の範囲によって異なる絵文字が入るようなTableを表示させる例

SELECT
	title,
	CASE WHEN rating >= 8 THEN
		'👍'
	WHEN rating <= 6 THEN
		'👎'
	ELSE
		'👀'
	END AS good_or_not
FROM
	movies;
```

# <font color="#00b050">ORDER BY </font>
- 特定の column を基準に、データのソートを行うとき使う
- column に続いて、ASC (昇順 ), DESC(降順) を入れて使う。デフォルトでは昇順
- 複数のorder by を指定でき、書いた順にソートの優先度が決まる
- NULLS 句を使って NULL 値を持つデータを頭・尾( FIRST, LAST )どちらかにまとめて配置することもできる
```sql
SELECT
	*
FROM
	movies
ORDER BY
	release_date DESC NULLS LAST,
	revenue DESC;	

/* ソートの優先度は書いた順に決まるため、上と下のクエリでは結果 (ソート) が異なる
ORDER BY
	revenue DESC,
	release_data DESC;
*/
```

# <font color="#00b050">LIMIT </font>
- 取得するデータの量を制限させたいとき使う
- 一度に多くのデータを取得することは、サーバーにもユーザー側にも負荷のかかる処理になってしまうので、LIMITを使ってデータの量を制限することがほぼ必須。

```sql
SELECT
	*
FROM
	movies
LIMIT 100;
 
```

# <font color="#00b050">OFFSET </font>
- 特定の位置以降に存在するデータを取得するとき使う
- 上のLIMITと組み合わせて、データを小分けして取得するようなロジックが、ほとんどのウェブサービスで用いられる
```sql
SELECT
	*
FROM
	movies
LIMIT 10  
OFFSET 10; -- movies から 10 以降(11~)の ROW を10個取得

/*
もしpagenation 等で、一度取得したデータより後ろのデータを取得したい場合は、以下のようにリクエストされるだろうという想定

LIMIT 10
OFFSET page * 10;
*/
```

>一見すると LIMIT の次に OFFSET が実行されるように見えるが、実際は
>
>FROM > WHERE > SELECT > <font color="#ff0000">OFFSET</font> > LIMIT 
>
>の順で実行される


# <font color="#00b050">WHERE </font>
- SELECT で取得するデータをフィルタリングしたい時に使われる
- 下の HAVING と似ているが、それぞれ実行されるタイミングが異なるため、注意が必要。

>WHEREではMIN, MAX, AVG, SUM, COUNT などの集約関数が<font color="#ff0000">使えない</font>

```SQL
SELECT
	*
FROM
	movies
WHERE
	rating IS NOT NULL
	AND release_date IS NOT NULL
```
# <font color="#00b050">GROUP BY </font>
- SELECTと一緒に使われ、同じデータを持つROWをグループ化する
	`ex) 監督別、年度別、ジャンル別など`

以下は、release_date 毎の平均 rating を求める例
```SQL
SELECT
	release_date,
	AVG(rating) AS avg_rating
FROM
	movies
WHERE
	rating IS NOT NULL
	AND release_date IS NOT NULL
GROUP BY
	release_date
ORDER BY
	avg_rating DESC;
```

# <font color="#00b050">HAVING </font>
- WHERE のようにROWをフィルタリングできる
- WHERE は<font color="#ff0000">集計される前の行に対し</font>フィルタリングを行い、HAVINGは<font color="#ff0000">集計結果に対し</font>フィルタリングが行われる
- WHEREとの違いは、実行されるタイミングが異なる。HAVINGはWHEREの次、またGROUP BY の次に実行される
- GROUP BY によってグループ化後の結果に対し、再度フィルタをかけるときはWHEREではなく、HAVINGを使って行う。
  クエリは FROM > WHERE > GROUP BY > SELECT の順で実行されるため、WHERE の実行段階では SELECT で宣言されるavg_rating にアクセスできないため。

```sql
SELECT -- 5
	release_date,
	round(AVG(rating),2) AS avg_rating
FROM -- 1
	movies
WHERE -- 2
	rating IS NOT NULL
	AND release_date IS NOT NULL
GROUP BY -- 3
	release_date
HAVING -- 4
	avg_rating > 6
ORDER BY --6
	avg_rating DESC;
```