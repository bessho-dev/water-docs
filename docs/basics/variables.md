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
* [span_0](start_span)`set free myValue`[span_0](end_span)
* [span_1](start_span)`myValue = 10` (数値を入れる)[span_1](end_span)
* [span_2](start_span)`myValue = "Hello"` (文字列を入れる)[span_2](end_span)

### 固定型 (stay)
「この箱には整数しか入れない！」と決める、安全なタイプです。
* [span_3](start_span)`set stay age int`[span_3](end_span)
* [span_4](start_span){{stay|型が指定されている固定型。指定外の型を入れようとするとエラーになります|/docs/stay}}[span_4](end_span)

:::info
[span_5](start_span)固定型の型を後から変えたいときは `stayto age string` のように命令します。[span_5](end_span)
:::

---

## 2. 中級：複数の値をまとめる

### リスト (list)
出席名簿のように、順番に値を並べて管理します。
* **[span_6](start_span)作成**: `set list members`[span_6](end_span)
* **[span_7](start_span)追加**: `list members add "Ifuto"`[span_7](end_span)
* **[span_8](start_span)一括追加**: `list members add ["Stelo", "Water"]`[span_8](end_span)
* **[span_9](start_span)変更**: `list members change 1 "NewName"`[span_9](end_span)

:::info
`change` 命令の中で `CV` という言葉を使うと、「変更前の値」を利用して計算できます。
[span_10](start_span)例：`list score change 0 CV + 10` （0番目のスコアに10足す）[span_10](end_span)
:::

### 深層リスト (depthlist)
「レイヤー」という概念を使って、複雑なデータを整理します。
* **[span_11](start_span)作成**: `set depthlist worldData`[span_11](end_span)
* **[span_12](start_span)操作**: {{deplist|深層リストに変更を加える命令。特定の住所を指定してアクセスします|/docs/deplist}}[span_12](end_span)
* **[span_13](start_span)住所指定**: `deplist worldData add [1, 3] value "Tree"`[span_13](end_span)
    * [span_14](start_span)これは「レイヤー1の中にある、3番目の場所」にデータを置くという意味です。[span_14](end_span)

---

## 3. 上級：高度な計算とメモリ管理

### 連続計算 (math.compute)
数式が長くなるとき、スッキリ書くことができます。
* [span_15](start_span)`a = math.compute (x + 10) (cv / 5) (cv - 2)`[span_15](end_span)
    * 1. `x + 10` を計算
    * 2. その結果(`CV`)を 5 で割る
    * 3. [span_16](start_span)さらに 2 を引く[span_16](end_span)

### 所有権 (Ownership)
[span_17](start_span)Waterがプロ向けの高度な言語である理由の一つが、この{{所有権|データの持ち主を明確にすることで、メモリを自動で安全に片付ける仕組み|/docs/ownership}}です。[span_17](end_span)

1. **[span_18](start_span)移動 (move)**: `move s1 to s2`[span_18](end_span)
    * [span_19](start_span)データの中身を完全に `s2` へ渡します。元の `s1` は使えなくなります。[span_19](end_span)
2. **[span_20](start_span)不変借用 (borrow)**: `borrow s1 as s2`[span_20](end_span)
    * [span_21](start_span)「ちょっと見せて」という状態です。中身は変えられません。[span_21](end_span)
3. **[span_22](start_span)可変借用 (borrow_mut)**: `borrow_mut s1 as s2`[span_22](end_span)
    * [span_23](start_span)「預かって書き換えるね」という状態です。借りている間、他の人は触れません。[span_23](end_span)

:::warning
[span_24](start_span)スコープ（プログラムの区切り `{ }`）が終わると、役目を終えたデータは自動的にメモリから消去され、PCを重くしません。[span_24](end_span)
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
