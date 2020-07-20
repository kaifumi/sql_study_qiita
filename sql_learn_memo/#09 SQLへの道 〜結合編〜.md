# テーブルの結合について
今回は、UsersテーブルのidとOrdersテーブルのuser_idを基に紐付けを行い、テーブルの結合を行います。

## 内部結合
まず、内部結合を説明します。

内部結合とは、２つのテーブルを結合しデータを取得する方法において、共通列が一致するレコード"のみ" 取得する方法が内部結合になります。

ここでは、内部結合を行うためのINNER JOIN句について解説します。

INNER JOIN句を使う為のSQLは、下記の通りとなります。

```
SELECT
  テーブルA.カラム1,
  テーブルB.カラム1,
  ......
FROM
  テーブルA
  INNER JOIN テーブルB ON テーブルA.カラム2 = テーブルB.カラム2
```

内部結合では、FROM句で指定したテーブルAと、結合するテーブルBをINNER JOIN句で指定します。

そして、テーブル同士の紐付け条件としてON句を利用し、共通列となるテーブルA.カラム2、テーブルB.カラム2を指定し紐付けを行う事でテーブルが結合されます。

サンプルデータを用いて実際にSQLを組むと、次の通りになります。

```
SELECT
  orders.order_code,
  users.name,
  users.name_kana,
  users.mail_address
FROM
  users
  INNER JOIN orders ON users.id = orders.user_id
```

このように、Users.idとOrders.user_idが一致するレコード以外は結合テーブルが作成された際に、除外されている事が解ります。

ベン図では、互いに一致しているレコードが結合テーブルの対象になる事を示しています。

# 外部結合
ここからは、外部結合を行う為のLEFT/RIGHT/FULL/CROSS JOIN句について解説します。

内部結合と外部結合の違いとして

内部結合　：　指定された共通列で、紐付いているレコード”のみ” で結合テーブルが作成される

外部結合　：　指定された共通列で、紐付いているレコード”以外” も結合テーブルとして作成される

といった点に違いが表れます。

## 左外部結合
LEFT JOIN句を使う為のSQLは、下記の通りとなります。

```
SELECT
  テーブルA.カラム1,
  テーブルB.カラム1,
  ......
FROM
  テーブルA
  LEFT OUTER(省略可) JOIN テーブルB ON テーブルA.カラム2 = テーブルB.カラム2
```

左外部結合では、FROM句で指定したテーブルAと、結合するテーブルBをLEFT JOIN句で指定します。

テーブル同士の紐付け条件は、内部結合と同様にON句を利用し、共通列となるテーブルA.カラム2、テーブルB.カラム2を指定し紐付けを行います。

サンプルデータを用いて実際にSQLを組むと、次の通りになります。

```
SELECT
  orders.order_code,
  orders.city,
  users.name,
  users.name_kana,
  users.mail_address
FROM
  users
  LEFT JOIN orders ON users.id = orders.user_id;
```
このように、Users.idとOrders.user_idが一致するレコード以外は結合テーブルが作成された際に、NULLとして扱われている事が解ります。

ベン図では、Usersテーブルを基として、互いに一致しているレコードは結合、一致していないレコードはNULLとして結合テーブルの対象になる事を示しています。

要は、結合元の情報はあるけど、結合してくるレコードがNULLになる

## 右外部結合
右外部結合では、

```
SELECT
  テーブルA.カラム1,
  テーブルB.カラム1,
  ......
FROM
  テーブルA
  RIGHT OUTER(省略可) JOIN テーブルB ON テーブルA.カラム2 = テーブルB.カラム2
```
このように記載します。

右外部結合では、左外部結合とは反対で、RIGHT JOIN句で指定したテーブルBを基に、FROM句で結合するテーブルAをで指定します。

テーブル同士の紐付け条件は、左外部結合と同様にON句を利用し、共通列となるテーブルA.カラム2、テーブルB.カラム2を指定し紐付けを行います。

サンプルデータを用いて実際にSQLを組むと、次の通りになります。

```
SELECT
  orders.order_code,
  orders.city,
  users.name,
  users.name_kana,
  users.mail_address
FROM
  users
  RIGHT JOIN orders ON users.id = orders.user_id;
```

このように、Orders.user_idとUsers.idが一致するレコード以外は結合テーブルが作成された際に、NULLとして扱われている事が解ります。

ベン図では、Ordersテーブルを基として、互いに一致しているレコードは結合され、一致していないレコードはNULLとして結合される事を示しています。

左外部結合は、LEFT JOIN句を中心に見て左のテーブル名を基として結合を行いますが、

右外部結合では、RIGHT JOIN句を中心に見て右のテーブル名を基として結合が行われますので、利用する際は注意が必要です。

## 完全外部結合
完全外部結合では、

```
SELECT
  テーブルA.カラム1,
  テーブルB.カラム1,
  ......
FROM
  テーブルA
  LEFT OUTER(省略可) JOIN テーブルB ON テーブルA.カラム2 = テーブルB.カラム2
UNION
SELECT
  テーブルA.カラム1,
  テーブルB.カラム1,
  ......
FROM
  テーブルA
  RIGHT OUTER(省略可) JOIN テーブルB ON テーブルA.カラム2 = テーブルB.カラム2
```

または、
```
SELECT
  テーブルA.カラム1,
  テーブルB.カラム1,
  ......
FROM
  テーブルA
  FULL OUTER(省略可) JOIN テーブルB ON テーブルA.カラム2 = テーブルB.カラム2
```

このように記載します。

データベースによっては、FULL JOINが利用出来ない事もあるので、利用しているデータベースに併せて使い分けて下さい。

完全外部結合は、サンプルのSQLを見て頂くと解る通り、左外部結合と右外部結合の機能を併せ持っています。

テーブル同士の紐付け条件は、左/右外部結合と同様にON句を利用し、共通列となるテーブルA.カラム2、テーブルB.カラム2を指定し紐付けを行います。

サンプルデータを用いて実際にSQLを組むと、次の通りになります。

（MySQLではFULL JOINが利用できない為、UNIONのSQLを使用した結果をサンプルとしています。）

```
SELECT
  orders.order_code,
  orders.city,
  users.name,
  users.name_kana,
  users.mail_address
FROM
  users
  LEFT JOIN orders ON users.id = orders.user_id
UNION
SELECT
  orders.order_code,
  orders.city,
  users.name,
  users.name_kana,
  users.mail_address
FROM
  users
  RIGHT JOIN orders ON users.id = orders.user_id;
```

完全外部結合を表で表すと

このように、Users.idとOrders.user_idが一致するレコードは結合テーブルとして作成され、一致しなかったレコードは結合テーブルが作成された際に、NULLとして扱われている事が解ります。

## クロス結合
クロス結合では、

```
SELECT
  テーブルA.カラム1,
  テーブルB.カラム1,
  ......
FROM
  テーブルA
  CROSS OUTER(省略可) JOIN テーブルB
```

このように記載します。

他の結合と違い、ON句の指定を行っていなくてもSQLは実行可能です。

そして、クロス結合を行う時はON句を指定しないケースが多いのではないかと思います。

（ON句を指定すると、共通列が一致したレコード”のみ”で結合テーブルが作成されるので、クロス結合で結合テーブルを作成する必要がありません）

```
SELECT
  orders.order_code,
  orders.city,
  users.name,
  users.name_kana,
  users.mail_address
FROM
  users
  CROSS JOIN orders;
```

このようにON句を指定していないので、FROM句のUsersテーブルを基にして、CROSS JOIN句のOrdersに登録されているレコード分（Usersレコード数 ｘ Ordersレコード数）を結合テーブルとして作成されます。

# 演習
 - UsersからOrdersへの左外部結合を行い、一致しなかったレコードを取得
```
SELECT
  orders.order_code,
  orders.city,
  users.name,
  users.name_kana,
  users.mail_address
FROM
  users
  LEFT JOIN orders ON users.id = orders.user_id;
```
 - Usersのid = 1のみを基にしてOrdersとの左外部結合を行い、全レコードを取得
```
SELECT
  orders.order_code,
  orders.city,
  users.name,
  users.name_kana,
  users.mail_address
FROM
  users
  LEFT JOIN orders ON users.id = 1;
```
 - UsersとOrdersで右外部結合を行い、Orders.user_idが10以上を条件にしてレコードを取得
```
SELECT
  orders.order_code,
  orders.city,
  users.name,
  users.name_kana,
  users.mail_address
FROM
  users
  RIGHT JOIN orders ON orders.user_id >= 10;
```
 - クロス結合にて、ON句を使用するパターンと使用しないパターンで発生する差異を確認
```
SELECT
  orders.order_code,
  orders.city,
  users.name,
  users.name_kana,
  users.mail_address
FROM
  users
  CROSS JOIN orders ON users.id = 1;
```

ONがあれば条件に合う分だけ表示する  
なければ全パターン表示する