## インデックス定義
- 大規模なテーブルに作成
- カーディナリティの高い列に作成。5パーセントに絞り込める(20種類)が目安。ただ、特定の値にデータが集中しているのはよくない。
- SQLのWHEREや結合条件で利用される列に作成
- レコードが1万以下の場合はほぼ効果がない

## インデックが効かなくなるケース
- インデックス列に演算をしている
- SQL関数を利用している
- IS NULLを利用している
- 否定を利用している
- ORを使っている(INならOK)
- LIKEを使っている(前方一致はOK)
- 暗黙の型変換を行なっている

## インデックスとは
- 検索、ソート、結合のスピードあげる
-

## インデックスの原則
- プライマリーキーにはデフォルトでつく
- インデックスの順番は前方一致で使われる

## インデックスの目安
- カーディナリティ5%くらいだといい


## 定義不要なインデックス
- 主キー
- 検索やソートにつかわれない
-

## 複合キーの順番の原則
- カーディナリティ高いもの
- 検索に利用されやすいもの
-

##
