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
