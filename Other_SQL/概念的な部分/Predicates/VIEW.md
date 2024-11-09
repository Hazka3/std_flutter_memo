
クエリのショートカットのようなもので、クエリを命名+packageして、繰り返し使用できるようになる。
反復使用が想定される長いクエリがある場合などに有用。

また、セキュリティリスクを下げる目的でも使用される。
ビューを利用することにより、実体のテーブルに格納されているユーザーに、直接見せたくないデータへのアクセスを制限することが可能になります。
さらに、不用意にデータを更新してしまうトラブルを防ぐことが出来るなど、実際にユーザーが必要なデータのみを抜粋したビューへのアクセスに制限することで、セキュリティを強化することにも繋がる。

ただ、ビューはテーブルと異なりインデックスを作成できないため、パフォーマンスに影響を及ぼすことがあるため、使用には注意する


クエリの頭に CREATE VIEW (命名) AS をつけて作成する
(恒例として、view の命名は v_ を接頭子としてつけることが多い)
```sql
CREATE VIEW v_flop_or_not AS SELECT -- CREATE VIEW OR REPLACE としてもいい
	CASE WHEN revenue < budget THEN
		'Flop'
	ELSE
		'Success'
	END AS flop_or_not,
	count(*) AS total_movies
FROM
	movies
WHERE
	budget IS NOT NULL
	AND revenue IS NOT NULL
GROUP BY
	flop_or_not;
```

これを実行させると、v_flop_or_not という view が作成され、今後 view として作成されたクエリを実行させたい場合は、新しく書いたりコピペする必要はなく、以下のようにview を呼び出すだけでよくなる(クエリの関数化のようなイメージ)。

```SQL
SELECT * FROM v_flop_or_not
```

もちろん他のSELECT句と同様に、以下のように、必要なColumnだけ出力させることもできる
```sql
SELECT total_movies FROM v_flop_or_not;
```

view リストを取得するクエリは、sqlite_master という内部クエリを参照して取得することができる
```sql
SELECT * FROM sqlite_master WHERE type='view';
```

sqlite_master  = sqlite のddlで生成可能なすべてのオブジェクト(table, view, index, contraint など) を管理するテーブル

VIEWの削除はDROP句を使う
```sql
DROP VIEW ビュー名; --DROP VIEW IF EXISTS としてもいい
```


> [!NOTE] VIEW は結果が保存されるわけではない
> 一見するとVIEW したクエリの結果を保存して、それをずっと流用しているように見えるが、VIEW を実行するたび内部ではクエリが実際実行されているため、DBのデータが変化するとVIEWの結果ももちろん変化する
