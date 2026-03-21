:::version
Ver 3.0 以降対応
:::

# Water プログラミングガイド：変数とデータ管理

このドキュメントでは、Waterにおける値の扱い方を基礎から上級者向けのメモリ管理まで解説します。

---

## 1. 基礎：変数を作ってみよう

Waterでは `set` 命令を使って、値を格納する「箱」を作成します。

### 変形型 (free)
入れたい値に合わせて、箱の形が勝手に変わる便利なタイプです。
`set free myValue`
`myValue = 10` (数値を入れる)
`myValue = "Hello"` (文字列を入れる)

### 固定型 (stay)
「この箱には整数しか入れない！」と決める、安全なタイプです。
`set stay myValue int` {{stay|型が指定されている固定型。指定外の型を入れようとするとエラーになります|}}とfreeを入れ替えることによって変形と固定を変更できます。

:::info
固定型の型を後から変えたいときは `stayto age string` のように命令します。
:::

---

## 2. 中級：複数の値をまとめる

### リスト (list)
出席名簿のように、順番に値を並べて管理します。
**作成**: `set list members`
**[span_7](start_span)追加**: `list members add "Ifuto"`
**一括追加**: `list members add ["Satou", "Water"]`
**変更**: `list members change 1 "NewName"`

:::info
`change` 命令の中で `CV` という言葉を使うと、「変更前の値」を利用して計算できます。
例：`list score change 0 CV + 10` （0番目のスコアに10足す）
:::

### 深層リスト (depthlist)
「レイヤー」という概念を使って、複雑なデータを整理します。
**作成**: `set depthlist worldData`
**操作**: {{deplist|深層リストに変更を加える命令。特定の住所を指定してアクセスします|/docs/deplist}}
**住所指定**: `deplist worldData add [1, 3] value "Tree"`
    これは「レイヤー1の中にある、3番目の場所」にデータを置くという意味です。

---

## 3. 上級：高度な計算とメモリ管理

### 連続計算 (math.compute)
数式が長くなるとき、スッキリ書くことができます。
`a = math.compute (x + 10) (cv / 5) (cv - 2)`
    1. `x + 10` を計算
    2. その結果(`CV`)を 5 で割る
    3. さらに 2 を引く

### 所有権 (Ownership)
Waterがプロ向けの高度な言語である理由の一つが、この{{所有権|データの持ち主を明確にすることで、メモリを自動で安全に片付ける仕組み|}}です。

1. **移動 (move)**: `move s1 to s2`
    データの中身を完全に `s2` へ渡します。スコープ{}している間、元の `s1` は使えなくなります。
2. **不変借用 (borrow)**: `borrow s1 as s2`
    ちょっと見せて」という状態です。中身は変えられません。
3. **可変借用 (borrow_mut)**: `borrow_mut s1 as s2`
    「預かって書き換えるね」という状態です。借りている間、他の人は触れません。

:::warning
スコープ（プログラムの区切り `{ }`）が終わると、役目を終えたデータは自動的にメモリから消去され、PCを重くしません。
:::

---

## 実践例：リストの合計値を出す

```water
set list prices int
list prices add [100, 200, 300]

set free total
total = math.compute (prices[0]) (cv + prices[1]) (cv + prices[2])
print(total)
```
