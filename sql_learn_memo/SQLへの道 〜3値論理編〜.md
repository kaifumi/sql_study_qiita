# 2値論理と3値論理

## 2値論理とは
命題の結果を真偽値すなわち真(TRUE)と偽(FALSE)の２つの値で表現しましょうってことです。

## 3値論理とは
3値論理とは、真(TRUE)と偽(FALSE)の2つの真理値に加え第3の値「不明(UNKNOWN)」という値を加えた３つ値で表現されます。
SQLはこの3値論理が採用されています。一旦ここでは、ふーんというレベルで理解しておきましょう。
 - TRUE：真
 - UNKNOWN：不明
 - FALSE：偽

# NULLとNULLの種類
SQL上では、NULLという表現は1つしかありませんが
厳密に言うと、NULLには「未知（Unknown）」と、「適用不能（Not Applicable, Inapplicable）」と２つの種類が存在します。

## 『未知（Unknown）』
では未知(Unknown)とは何でしょうか？未知(Unknown)とは現地点で知らないという事です。

そこにトランプ・カードがあるならば、マークや数字が記載されているのは明らかですが
現時点において、未だ知らない状況なので『未知(Unknown)』といえます。

このような状況もSQLの世界ではNULLで表現されます。

## 『適用不能（Not Applicable, Inapplicable）』
そしてもう1つ、SQLの世界でNULLと表現されるものに『適用不能（Not Applicable, Inapplicable）』があります。

今回のケースも「分からない」と状況でありますが、そもそもトランプ・カードに職歴という概念が存在しません。
聞かれたところで、適用が出来ない状況、『適用不能（Not Applicable, Inapplicable）』といえます。


### 整理
２種類のNULLを整理すると、
「未知」のNULLは現状は不明だが、「いずれ分かる可能性があるもの」
「適用不能」のNULLは「そもそも分かる可能性がないもの」。

SQLでは双方がNULLと表現されます。

ちなみに言うとNULLはNULLです。NULLという状況を示すラベル表記だと思ってください。

# NOTとANDとOR
# 3値論理におけるANDとORの優先順位
3値論理の図を覚えるのはなかなか大変です。
考え方のコツとして、3つの真理値の間に次のような優先順位があると考えましょう。
■ ANDの場合 ： false ＞ unknown ＞ true
■ ORの場合　： true ＞ unknown ＞ false

強い方が弱い方を打ち消します。

【例】
「true AND unknown」だと、unknownの方が強いので、結果はunknownになります。
「true OR unknown」だと、trueの方が強いので、結果はtrueになります。

「false AND unknown」だと、falseの方が強いので、結果はfalseになります。
「false OR unknown」だと、unknown方が強いので、結果はunknownになります。

この順位を覚えておけば、3値論理演算も計算がしやすくなります。

特に、ANDの演算にunknownが含まれた場合、
結果が絶対にtrueにならないという特徴をよく覚えておいてください。
