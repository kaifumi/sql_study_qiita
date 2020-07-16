# SQL 集合関数（SUM、MAX、MIN、AVG、COUNT）
集合関数とは、SQLに備わっている演算機能です。Excelある機能と類似しているので、馴染みのある方も多いかと思います。  
集計関数,集約関数と言ったりすることもあります。  
集合関数には主に次の5つがあります。

|関数名|意味|
|-|-|
|COUNT	|総数を求める|
|SUM	|総和を求める|
|MAX	|最大値を求める|
|MIN	|最小値を求める|
|AVG	|平均を求める|

# COUNT
countは総数です。「このテーブルのレコードは幾つなんだろう」「大分県生まれの方は何人いるのだろう」という際に利用します。

「このテーブルのレコードは幾つなんだろう」

```
select count(*) from users;
```
アスタリスク【*】は全てをあらわします。
users というテーブルには1000行あることがわかります。

「大分県生まれの方は何人いるのだろう」
```
select count(*) from users where birthplace = '大分県';
```

# SUM
sumは総和です。「合計金額はいくらだろう」といった際に利用します。
「合計金額はいくらだろう」

```
select sum(price) from lineitems;
```

【price】と言うカラムの総和なので
```
sum(price)
```
です。

当然ですが、計算できる値でなければダメです。

# MAX
maxは最大値です。
当然ですが、これも最大がある値でなければダメです。

```
select max(age) from users where birthplace = '大分県';
```

# MIN
minは最小値です。
当然ですが、これも最小がある値でなければ...

```
select count(*),max(age),min(age) from users where birthplace = '大分県' and gender_id = 0;
```
このように、集合関数同士同時に利用できます。

# AVG
avgは平均値です。
当然ですが、これも平均を取れる値で...

```
SELECT count(*) AS 総数,
       max(age) AS 最高齢,
       min(age) AS 最年少,
       avg(age) AS 平均年齢
FROM users
WHERE birthplace = '大分県';
```
どんどん長くなるため、わかりやすく改行しています。大文字の部分がありますが、もちろん小文字でも問題ありません。
【as】は名前をつけます。この場合はわかりやすくするためです。

これで基本編は終了です。

# 「ちょっと寄り道〜サブクエリ編〜」

## INを使う理由
自己考察  
whereなどでサブクエリを使う際によく用いられるINはカラムの特定の値を条件とするために、サブクエリとして使うために用いている。

## 大分県生まれ最高年齢購買合計金額
これやば
```
SELECT sum(price) AS 大分県生まれ最高年齢購買合計金額
FROM lineitems
WHERE order_id IN
    (SELECT id
     FROM orders
     WHERE user_id IN
         (SELECT id
          FROM users
          WHERE birthplace = '大分県'
            AND age IN
              (SELECT max(age)
               FROM users
               WHERE birthplace = '大分県')));
```

下から読めばいいんだな,,

まず末端から見ていきます。  
①

```
SELECT max(age)
FROM users
WHERE birthplace = '大分県'
```

上記に出てきたSQLです。  
大分県生まれの方の最高年齢を出しています。

徐々に範囲を広げます。  
②
```
SELECT id
FROM users
WHERE birthplace = '大分県'
  AND age IN
    (SELECT max(age)
     FROM users
     WHERE birthplace = '大分県')
```
大分県生まれの方の最高年齢の方のusersのidを出しています。  
上記の①がSQLの中に入っていることがわかります。

①の値がわかっているのでわかるかと思います。  
このSQLのwhereにも

WHERE birthplace = '大分県'  
があるのは、これが無いと  
①の年齢の人全員が対象になってしまうためです。

後はわかりますね。  
③
```
SELECT id
FROM orders
WHERE user_id IN
    (SELECT id
     FROM users
     WHERE birthplace = '大分県'
       AND age IN
         (SELECT max(age)
          FROM users
          WHERE birthplace = '大分県'))
```

これで大分県生まれ最高年齢の方のordersのidを出しています。  
これを繰り返せばいくらでも長く書くこともできます。

# 演習
 - 全顧客の最大の誕生日と最小の誕生日を取得
   - 全顧客のカウント
   - 誕生日カラムの最大と最小を出す
```
select max(birthday),min(birthday) from users where id in (select user_id from orders);
```
 - 1980年代生まれの方の注文合計数を取得
  - 1980年代生まれのuserを取得
  - そのuser_idをごとにcountで注文数を出す
```
select count(*) from orders where user_id in (select id from users where birthday LIKE "%1980%");
```
 - 20代の方の購買合計金額を取得
   - 20代の方の取得
   - そのuserの全てのオーダーを取得
   - そのオーダーごとの購買合計を取得
```
select sum(price) from lineitems where order_id in (select id from orders where user_id in (select id from users where age BETWEEN 20 AND 29));
```