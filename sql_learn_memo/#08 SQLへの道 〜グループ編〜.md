# GROUP BY 便利
エンジニアは面倒臭がりです。  
これを全県分作れば結果が得られますが、実はこんなことしなくても一発で出る方法があります。
```
SELECT count(*),prefectural FROM orders GROUP BY prefectural;
```

# GROUP BY
group by  
は  
「〜ごと」  
という処理を行います。

上記の例では、全ての注文の「県ごと」の人数を出しています。  
この「県ごと」という部分が

```
GROUP BY prefectural;
```
の部分です。

基本構造は下記の形です。

```
SELECT 関数名(カラム名１),カラム名２ FROM テーブル名 GROUP BY カラム名２;
```

カラム名１が計算されるカラム  
カラム名２が「〜ごと」の部分のカラムです。

select部分のカラム名２はなくても動きますが、無いと困ります。  
どう困るかわからない方はやってみてください。

いくつか紹介して行きましょう。

# 例
 - 全ての顧客の年齢ごとの人数を調べる

```
select count(*) as 人数,age as 年齢 from users where age is not null group by age order by age;
```
年齢「ごと」の人数を出しています。

## IS NOT NULL
NULL以外のカラムを探すという意味になる

 - 出身地ごとに、性別ごとの最高齢と最年少を調べています。
```
SELECT max(age) AS 最高齢,
       min(age) AS 最年少,
       birthplace AS 出身地,
       (CASE
            WHEN gender_id = 0 THEN '男性'
            ELSE '女性'
        END) AS 性別
FROM users
WHERE age IS NOT NULL
GROUP BY birthplace,
         gender_id
ORDER BY birthplace,
         gender_id;
```

## group by 復習
〜ごとにと捉える。単位として考える。

ちなみに
```
(CASE
     WHEN gender_id = 0 THEN '男性'
     ELSE '女性'
END) AS 性別
```
の部分はcase文と呼ばれ、  
基本構文は

```
case when カラム名 = 値１ then AAA else BBB end
```

```
when カラム名 = 値１ then AAA
```
の部分は幾つでも増やすことができます。  
読んで字のごとくなのですが、この場合 gender_id が 0 の場合は「男性」それ以外の場合は「女性」を返します。

group by を使えば非常に便利なことがわかっていただけるかと思います。  
それをさらに便利にするのが having です。

# HAVING
```
select count(*) as 人数,age as 年齢 from users group by age order by age;
```

上記に出てきたこちらのSQLを用います。  
たとえば、この中で人数が25人以上の年齢を出したい場合にhavingが使えます。

```
SELECT count(*) AS 人数,
       age AS 年齢
FROM users
WHERE age IS NOT NULL
GROUP BY age
HAVING count(*) >= 25
ORDER BY age ;
```

having は集合関数の結果をもとに絞り込むことができます。  
基本構造は下記の形です。

```
SELECT 関数名(カラム名１),カラム名２ FROM テーブル名 GROUP BY カラム名２ HAVING 関数名(カラム名１);
```
# 例
下記3つの違い、わかりますか?

①

```
SELECT avg(age) AS 平均年齢,
       count(*) AS 人数,
       birthplace AS 出身地
FROM users
WHERE age IS NOT NULL
  AND age >= 40
GROUP BY birthplace ;
```

②
```
SELECT avg(age) AS 平均年齢,
       count(*) AS 人数,
       birthplace AS 出身地
FROM users
WHERE age IS NOT NULL
GROUP BY birthplace
HAVING avg(age) >= 40;
```

③
```
SELECT avg(age) AS 平均年齢,
       count(*) AS 人数,
       birthplace AS 出身地
FROM users
WHERE age IS NOT NULL
  AND age >= 40
GROUP BY birthplace
HAVING avg(age) >= 50;
```

①は出身地ごとの40歳以上の方の平均年齢と人数と出身地  
②は出身地ごとの平均年齢が40歳以上の平均年齢と人数と出身地  
③は出身地ごとの40歳以上の方の平均年齢が50歳以上の平均年齢と人数と出身地です。

こう書くと、日本語の方がややこしいですね。

# 「ちょっと寄り道〜基本数学編〜」
【新人教育 資料】第3章 SQLへの道 〜基本数学編〜
で出てきた基本数学を利用すれば各データをSQL上でこねくり回せます。色々遊んでみてください。  
例

```
SELECT count(*) AS 人数,
       max(age) AS 県内最高年齢,
       min(age) AS 県内最低年齢,
       avg(age) AS 県平均年齢,
       (max(age) - min(age)) AS 県内最大年齢差,
       (max(age) + min(age)) AS 県内最大年齢と最小年齢の和,
       (avg(age) * 2) AS 平均２倍,
       (max(age) + min(age)) - (avg(age) * 2) AS 県内最大年齢と最小年齢の和と平均の２倍の差,
       birthplace AS 出身地
FROM users
WHERE age IS NOT NULL
GROUP BY birthplace
ORDER BY 県内最大年齢と最小年齢の和と平均の２倍の差;
```

# 演習
- 血液型ごとにそれぞれ何人いるか情報を取得
```
select count(*) AS 人数,
       blood_type AS 血液型
from users
where blood_type is not null
group by blood_type
order by blood_type;
```
- 県ごとに最高齢と最年少の年齢差の情報を取得し、その差順に並べる
```
SELECT max(age) AS 最高齢,
       min(age) AS 最年少,
       (max(age)-min(age)) AS 最高齢と最年少の差,
       birthplace AS 出身地
FROM users
WHERE age IS NOT NULL
GROUP BY birthplace
ORDER BY 最高齢と最年少の差 DESC;
```