# SQL基本操作
## SQLの4大命令
標準SQL規格では大きく以下の3つが定義されています。

 - データ定義言語(CREATE,DROP,ALTER等)
 - データ操作言語(INSERT,UPDATE,DELETE,SELECT)
 - データ制御言語(GRANT,REVOKE,SET TRANSACTION,BEGIN,COMMIT,ROLLBACK,SAVEPOINT,LOCK)  

今回はデータ操作言語に該当するSQLの4大命令について勉強していきましょう。

## 4大命令
|命令|説明|文法|
|---|---|----|
|INSERT|データを追加する|INSERT INTO テーブル名(カラム名1,カラム名2,...) VALUES(値1,値2,...);|
|SELECT|データを参照する|SELECT カラム名1,カラム名2,... FROM テーブル名 [WHERE 絞込条件];|
|UPDATE|データを更新する|UPDATE テーブル名 SET カラム名1=値1 [カラム名2=値2 ...] [WHERE 絞込条件];|
|DELETE|データを削除する|DELETE FROM テーブル名 [WHERE 絞込条件];|


## MySQLへの接続
```
$ mysql -uroot -p
```

## CREATE DATABASE
RMDBSの1つであるMySQLの中にデータベースを作っていきましょう。  
テーブルなどの、実際のデータだけでなく、DBを管理するためのデータ等も含まれる
1つのデータのセットだと思ってください。
```
CREATE DATABASE studydb CHARACTER SET utf8;
```

## 利用するデータベースを選択する
```
use studydb;
```

## CREATE TABLE
学習用に使うテーブルを作成していきましょう。
|id|name|sex|birth_day|
|--|--|--|--|
|1|taro|male|1992-11-30|
|2|hanako|female|1993-01-14|

今回学習するために利用するテーブルは上記のように作りたいため  
以下のコマンドでテーブルを作成しましょう。

```
CREATE TABLE members
(
    id          serial primary key,
    name        VARCHAR(40) not null,
    sex         VARCHAR(40) not null,
    birth_day   date
);
```

## テーブルの一覧を確認する。
```
show tables;
```

# 基本操作
## INSERT文
```
INSERT INTO members (name,sex,birth_day)
VALUES ('tarou', 'male', '1992-11-30');

INSERT INTO members (name,sex,birth_day)
VALUES ('hanako', 'femal','1993-01-14');
```

## SELECT文
レコードを作成したので、今度は検索してみましょう。  
SELECT文は一番使う頻度が多く、かつ検索方法もたくさんあるのですが、ここでは基本中の基本だけ。

## 全件全列を参照する
```
SELECT * FROM members; -- 全件検索
```

## 全件 name列だけを参照する
```
SELECT name FROM members; -- 全件検索(名前だけ見たい)
```

## 全件 特定の条件に一致するデータだけを参照する
```
SELECT * FROM members WHERE name = 'tarou'; -- 名前が"tarou"のレコードを検索
```

## UPDATE文
## 全件特定のカラムを更新
```
--membersのレコードのnameをすべて'jirou'に更新する
UPDATE members SET name = 'jirou';
```

## 絞込条件に該当するデータの特定のカラムを更新
```
--membersのレコードでidが1のレコードのname,birth_dayを更新
UPDATE members SET name = 'saburo', birth_day = '2015-03-11' WHERE id = 1;
```

## DELETE文
## 特定条件に該当するデータを削除
```
--idが1のレコードを削除する
DELETE FROM members WHERE id = 1;
```

## 全件データを削除
```
--membersのレコード全てを削除する
DELETE FROM members;
```

# 小話1 CRUD図(表)
システム開発をしていて、ドキュメントを作っている会社だと、属人化を未然に防いだり、影響調査のコスト圧縮のために、CRUD図という図を作成したりします。  
このCRUD図とは、SQLの４大命令に即した形で「Create」、「Read」、「Update」、「Delete」の操作がどのテーブルに対して行われているかを画面(機能やユースケース)ごとに記載する資料です。  
|省略形(意味)|SQL|説明|
|--|--|--|
|C(Create)|INSERT|データを作成する|
|R(Read)|SELECT|データを参照する|
|U(Update)|UPDATE|データを更新する|
|D(Delete)|DELETE|データを削除する|

# 小話2 ACID
## データを守るための基本的なルール "ACID"
システム開発において、データベースをそれ単体で使うということは、あまり考えにくいですね。通常他のシステム(たとえばWebアプリケーションなど)と連携して使用することが多いです。データベースには顧客情報などの重要な情報が保存されています。  
ではそんなとき、みなさんがデータベースに期待する、性能/機能とはなんでしょう。

もしかしたら...  
突発的なサーバーダウンが発生するかもしれません。  
ハードが壊れて、そもそもサーバーを起動することすらできなくなるかも。  
Aさんがあるデータを更新しているときに、Bさんも同一データを更新しているかもしれません。

どんなことが起きたとしても、大切なデータは守りたいです。  
結局、「データを守る」ことがみんなが共通に期待する性能ではないでしょうか。  
そして、「データを守る」ために"ACID"というルールがあります

## ACID
以下の特徴の頭文字をつなげてACIDと言います

 - 原子性 (ATOMICITY)
 - 一貫性 (CONSISTENCY)
 - 独立性 (ISOLATION)
 - 永続性 (DURABILITY)

言葉は難しいので、はじめはそうなんだと聞き流しておきましょう。

## トランザクション
標準SQL規格にもある「データ制御言語」の１つにトランザクションというものがあります。

## 「処理のまとまり」としてのトランザクション
トランザクションを説明するときに、よく例に出されるのが銀行口座の例です。  
Aさんの口座から、Bさんの口座に100万円を振り込む処理を例に考えてみましょう。

これを実現するには、下記のような２つのステップが必要になります。  
STEP1 => Aさんの口座から100万円を引く  
STEP2 => Bさんの口座に100万円を足す

別にSTEP2=>STEP1の順で処理してもいいと思いますが、少なくとも、  
STEP1とSTEP2は両方とも実行完了するか、両方とも実行完了されない という条件が満たされていないと、口座間の計算があわなくて大変です。  
Aさん口座から100万円引いたのにも関わらず、その時点でサーバーダウンして、Bさんの口座に100万円が振り込まれなかったら...考えるだけで恐ろしいです。

この STEP1とSTEP2は両方とも実行完了するか、両方とも実行完了されない という性質を 原子性 (ATOMICITY)といいます。

ではこのSTEP1とSTEP2はセットで実行したいということ(トランザクション)を、RDBMSはどうやって認識するのでしょうか。  
これはデータベースを使う人が明示的に指定してあげる必要があります。データベースが勝手に判断して、ここからここまでがトランザクションだ!と判断することはありません。  
今回の預金口座の例では、下記のような感じになります。

```
CREATE TABLE bank_accounts
(
    id          serial primary key,
    name        VARCHAR(40) not null,
    balance      bigint
);

INSERT INTO bank_accounts (name,balance)
VALUES ('A', 1000);

INSERT INTO bank_accounts (name,balance)
VALUES ('B', 900);

SELECT * FROM bank_accounts;-- 確認

 id | name | balance
----+------+---------
  1 | A    |    1000
  2 | B    |     900
(2 rows)


--トランザクションはクライアントが明示的に宣言する必要がある
BEGIN; --トランザクションの開始宣言
  UPDATE bank_accounts SET balance = ( balance - 100) WHERE name = 'A'; --STEP1
  <!-- 途中でexitしてみたけど、値保存されてなかった。やるね。 -->
  UPDATE bank_accounts SET balance = ( balance + 100) WHERE name = 'B'; --STEP2
COMMIT; --トランザクションの終了宣言

SELECT * FROM bank_accounts;-- 確認

 id | name | balance
----+------+---------
  1 | A    |     900
  2 | B    |    1000
(2 rows)

```

## 「データの復旧の単位」としてのトランザクション
トランザクションには、なにかしら障害が発生した際の「データの復旧の単位」としての一面もあります。  
RDBMSは、障害発生前に終了しているトランザクションの結果は保証する。言い換えれば、障害発生時にトランザクションが終了していないものに関しては、保存してくれません。

この 障害発生前に終了しているトランザクションの結果は保証する という性質を  
永続性 (DURABILITY) といいます

### トランザクションと同時実行
データベースを一人で使えればいいのですが、複数のユーザーが同時並行でデータベースにアクセスするのが通常です。  
同一データを、AさんBさん二人が更新しようとしているとき、どのようにしたらデータの整合性を保証できるでしょうか。  
AさんとBさんがお互いを気遣い合って、相手方が終わってから処理をかけるジェントルマンならいいのですが...  
そんなこと普段意識していませんよね?この辺は、RDBMSが裏で働いていて、うまい具合に複数処理をスケジューリングして、整合性を保証してくれています。  
この性質を 独立性 (ISOLATION) といいます  
ひとつひとつのトランザクションは、その他の処理からは独立しているよってことですね。

最後の 一貫性 (CONSISTENCY) というのはレコードへのnot null, default =,primary_key などの制約に関して、トランザクションの前後で整合性をとるよって意味です。

RMDBSとしては、多くがこんな特徴があるよと覚えておいてください。