
## <font color="#0070c0">INSERT INTO ~~ VALUES</font>
- Table にデータを挿入する
- Table 名の後ろに () を入れて、データを挿入する Column を指定できる。ここで宣言されていなColumn には null が入る
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
