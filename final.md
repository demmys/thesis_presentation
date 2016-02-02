class: title

.long-title[
# Self-host化によるSwiftコンパイラの<br>ソースコード可読性の向上
]

環境情報学部4年 Arch

出水 厚輝 (demmy)

---

## 背景 (1/2)

.margin-top-small.margin-bottom-middle.center[
### 2015年12月 Swiftがオープンソース化
]

.padding-left-large.margin-left-large[
.margin-bottom-middle[
* 開発に関われるプログラマの数
* 開発スケジュールの適切さ
]

.padding-left-middle.margin-left-large[
.arrow-down[]
]

* 開発者のソフトウェアに対する関心
* プログラムの可読性
]

.inverted.margin-top-middle.center[
#### 開発スピードを決定する要因が変化
]

---

## 背景 (2/2)

.horizontal[
.center.image-middle[
![redmonk](img/redmonk.png)
]

* Swiftは人気言語であり多くの開発者の関心を充分に集めている
* 可読性はレビューなどで保たれているだけ
]

.center[
#### プロジェクトの活性化には可読性を向上する必要がある
]

---

## 本研究の目的

.vertical-center.center[
### Swiftコンパイラの可読性を向上する
]

---

## 可読性の要素 .font-tiny[ー 可読性とは]

.margin-top-tiny.center[
Swiftコンパイラの**可読性を向上**する
]

| 種類 | 要素の例 |
|:---:|:---:|
| 処理の複雑さ | データ構造、アルゴリズム、制御構造 |
| 説明の適切さ | コメント、名前付け、インデント |
| 知識の多さ | 読者の知識、手法の種類 |

特に処理の複雑さはレビューなどでは解消できず、可読性低下の主要因となる

.inverted.center[
#### 本研究では処理の複雑さに特に注目する
]

---

## 処理の複雑さを表す指標 .font-tiny[ー 可読性とは]

1980年代にソフトウェア・メトリクスとしてソフトウェアの複雑さを定量化する手法が盛んに研究された

| 手法 | 指標 |
|:---:|:---|
| Line of Code | ソースコードの行数 |
| Halstead Complexity Metrics | 演算子と被演算子の種類と数 |
| Cyclomatic Complexity Metric | 分岐の数 |
| Function Point | 入出力の数 |

.center[
対象とするソフトウェアによって有効な手法が異なる
]

---

## コンパイラにおける複雑さ (1/2) .font-tiny[ー 可読性とは]

* コンパイラは非常に巨大なソフトウェア
* コンパイラとして基本的な機能を担う箇所の複雑さが特に重要

.center[
基本的ないくつかのプログラムをコンパイルする時に<br>実行される部分だけを対象として複雑さを測定
]

.font-small[
| 手法 | 適合性 |
|:---:|:---:|
| .bold.highlight[Line of Code] | .bold.highlight[◎] |
| Halstead Complexity Metrics | △<br>(部分的なプログラムでは値が偏る) |
| Cyclomatic Complexity Metric | ×<br>(分岐が分解されてしまう) |
| Function Point | ×<br>(入出力はほとんどない) |
]

---

## コンパイラにおける複雑さ (2/2) .font-tiny[ー 可読性とは]

.margin-top-middle.center.image-middle[
![実行部分LOCの計測方法](img/loc_measurement.png)
]

.center[
#### 本研究では実行部分LOCが可読性を表すとする
]

---

## Swiftコンパイラの構成要素 .font-tiny[ー 検証の対象]

.margin-top-tiny.center[
**Swiftコンパイラ**の可読性を向上する
]

.margin-top-middle.image-width-max[
![Swiftコンパイラの構成](img/swift_compiler_process.png)
]

.margin-top-middle[
* 各処理ステップは異なる手法を用いて異なる処理を行う
* 一部のステップは他のミドルウェアに依存している
* 各処理ステップは前のステップの結果に依存している
]

.margin-top-middle.center[
#### 本研究では構文解析のみを対象とする
]

---

## 可読性を向上する手法

.horizontal[
.stuff-table.width-half.font-tiny[
| 順位 | 言語 | Self-host化したコンパイラ |
|:---:|:---:|:---:|
| 3 | C++ | clang, gcc |
| 4 | C# | .NET Compiler Platform |
| 5 | Python | PyPy |
| 7 | Visual Basic .NET | .NET Compiler Platform |
| 12 | Delphi/Object Pascal | Free Pascal |
| 17 | Groovy | groovy |
| 21 | D | DMD |
| 27 | Lisp | clisp |
| 28 | Ada | GNAT |
| 30 | Scala | scala |
]

.box[
.center.font-small[
TIOBE Programming Indexの30位中10個の言語がSelf-host化されている
]
* 新しく作られる汎用言語は既存のよく知られた汎用言語より表現力が高いことが多い
* ある構文をまさにその構文を用いて記述できる
* コンパイラの開発用に異なる言語を学ぶ必要がなくなる
]
]

.center[
#### 可読性の向上にSelf-host化が有効
]

---

## 本研究で行なったこと

.center.margin-top-large[
#### Self-host化によってSwiftコンパイラの可読性を<br>向上できることの検証
]

.margin-left-middle.margin-top-middle[
1. Self-host化したSwiftコンパイラの構文解析器を実装
2. 実装した構文解析器の実行部分LOCを測定
3. Apple社のSwiftコンパイラの構文解析器の実行部分LOCを測定
4. 各実行部分LOCを比較して考察
]

---

## 構文解析器の構成 .font-tiny[ー 実装の概要]

.margin-top-middle.horizontal[
.image-middle[
![構文解析器の構成](img/parser_structure.png)
]

.margin-left-small[
1. モジュール定義ファイルからライブラリ、ソースファイルからコンパイルするプログラムを読み込み
2. 字句解析器で各ファイルの文字列を字句に分解
3. 構文解析で各字句からASTを生成
]
]

---

## Self-host化した構文解析器の実装 .font-tiny[ー 実装の概要]

### TreeSwift

* オートマトンによる高級な字句解析
* LL(k)クラスの再帰構文解析
* テキストファイルベースのモジュール定義
* 文法から検知可能なエラーの検出

.image-width-max[
![TreeSwift](img/treeswift.png)
]

---

## 字句解析 .font-tiny[ー 実装の概要]

Swiftの字句は複雑な構造を持つ

.horizontal[
```cpp
1_000 // 数値区切り
"x: \(x)" // 文字列への式挿入
```

```cpp
i+++i // 二項演算子 +++
i++ + i // 後置演算子 ++
```
]

.horizontal[
.image-width-max[
![字句解析器の仕組み](img/lexical_analyzer.png)
]

字句解析器を各字句を解析する小さなオートマトンの集合として実装
]

.inverted.center[
#### 字句解析が構文解析より多くの処理を行うように実装
]

---

## 構文解析 .font-tiny[ー 実装の概要]

.center.image-small[
![構文解析手法](img/parser.png)
]

.horizontal[
.stuff-table.font-small.width-half[
| 解析手法 | 手作業での実装 | 自動生成 |
|:---:|:---:|:---:|
| 下向き | ○ | × |
| 上向き | × | ○ |
]

.margin-left-tiny[
* 曖昧な構文の解析を可能に
* Swiftで書き下せるように
]
]

.inverted.center[
#### Swiftと同じLL(k)クラスの再帰下降構文解析で実装
]

---

## モジュール定義 .font-tiny[ー 実装の概要]

* 標準ライブラリなどはテキストファイルによるモジュール定義ファイルによって読み込まれる
* 標準ライブラリは直接LLVM-IRで記述してDLL化

```cpp
public struct Int {
    public init (_builtinIntegerLiteral value: Builtin.Int64)
}
public func +(lhs: Int, rhs: Int) -> Int
```

```llvm
%_MS9TreeSwift3Int = type <{ i64 }>
define %_MS9TreeSwift3Int @_MO9TreeSwift1a_MS_MS9TreeSwift3Int_9TreeSwift3Int_MS9TreeSwift3Int(%_MS9TreeSwift3Int, %_MS9TreeSwift3Int) {
entry:
    %2 = extractvalue %_MS9TreeSwift3Int %0, 0
    %3 = extractvalue %_MS9TreeSwift3Int %1, 0
    %4 = add i64 %2, %3
    %5 = call %_MS9TreeSwift3Int @_MSI9TreeSwift3Int_LMT22_builtinIntegerLiteral7Builtin5Int64_MS9TreeSwift3Int(i64 %4)
    ret %_MS9TreeSwift3Int %5
}
```

---

## エラー検出 .font-tiny[ー 実装の概要]

* 文法から静的に検出できるエラーは全て検知
* エラー回復は未実装

```cpp
// 意味解析で型の不整合として検出
func noReturn() -> Int {
    return
}

// 現在のTreeSwiftでは検出不可
// (Apple社のSwiftではSIL解析で不整合として検出)
func notInitialized(f: Bool) {
    let x: Int
    if f {
        x = 10
    } else {
    }
    print(x)
}
```

---

## 評価方法 (1/2)

.font-small.inverted.center[
#### 2つの構文解析器の実行部分LOCを比較
]

.center.image-small[
![実行部分LOCの計測方法](img/loc_measurement.png)
]

### Apple社のSwiftコンパイラの条件

* swift-2.2-SNAPSHOT-2015-12-31-a を評価用に変更
* オプションを付けて構文解析だけを実行
* モジュール解析はファイル形式が異なるため除外

---

## 評価方法 (2/2)

.font-small[
| 対象プログラム | 行数 | 主な使用構文 |
|:---:|:---:|:---:|
| Simple Values | 25 | 変数、各種リテラル |
| Control Flow | 67 | 分岐、繰り返し、パターンマッチ |
| Functions and Closures | 69 | 関数、クロージャ |
| Objects and Classes | 82 | クラス |
| Enumerations and Structures | 62 | 列挙体、構造体 |
| Protocols and Extensions | 33 | 存在型、型拡張 |
| Generics | 25 | 全称型 |
]

.inverted.center[
#### チュートリアル中の7つのプログラムを対象とする
]

.footnote[
※ Trailing closureとString interpolationはTreeSwiftが未対応のため同等の構文に変更
]

---

## 評価上の懸念点

* .bold[構文解析以降の処理ステップが充分に実装されていない]
    * ASTにおける後のステップ用の準備処理がSwiftの実行部分LOCを増やす可能性がある
    * ファイルごとに計測結果を比較することで除外可能
* .bold[2つのコンパイラで字句解析の手法が異なる]
    * より単純なプログラムではTreeSwiftの実行部分LOCが高くなる可能性がある
    * 他のプログラムの例と比較することで特定可能
* .bold[TreeSwiftにはエラー回復が実装されていない]
    * エラーの発生しないプログラムを対象とするので、エラー回復を行うプログラムは計測対象に含まれない

---

## 評価結果と考察 (1/4)

.center[
各プログラムにおけるコンパイラ全体の実行部分LOC
]

.font-small[
| 対象プログラム | Swift | TreeSwift | Swift / TreeSwift |
|:---:|---:|---:|---:|
| Simple Values | 4188 | 1928 | 2.172 |
| Control Flow | 5347 | 2226 | 2.402 |
| Functions and Closures | 5819 | 2187 | 2.661 |
| Objects and Classes | 5937 | 2122 | 2.798 |
| Enumerations and Structures | 5762 | 2258 | 2.552 |
| Protocols and Extensions | 5598 | 2132 | 2.626 |
| Generics | 5887 | 2233 | 2.636 |
]

.center.inverted[
#### ASTにおける準備処理が影響している可能性がある
]

---

## 評価結果と考察 (2/4)

.horizontal[
.image-width-max[
.font-small.center.margin-bottom-small[
構文解析の本体を構成するファイル群
]
![構文解析ファイル群の実行部分LOC](img/parse_loc_result.png)
]

.image-width-max[
.font-small.center.margin-bottom-small[
ASTを構成するファイル群
]
![ASTファイル群の実行部分LOC](img/ast_loc_result.png)
]
]

AST関連部分では後ステップ用の処理が実行部分LOCを増加させている

.margin-top-middle.inverted.center[
#### 構文解析ファイル群の結果に注目する
]

---

## 評価結果と考察 (3/4)

.font-small.center[
構文解析ファイル群で再集計した実行部分LOC
]

.font-small[
| 対象プログラム | Swift | TreeSwift | Swiftからの減少率 |
|:---:|---:|---:|---:|
| Simple Values | 1263 | 1457 | -16.9% |
| Control Flow | 1816 | 1698 | 7.52% |
| Functions and Closures | 1982 | 1644 | 19.0% |
| Objects and Classes | 1970 | 1658 | 19.6% |
| Enumerations and Structures | 1886 | 1658 | 12.3% |
| Protocols and Extensions | 1851 | 1544 | 17.2% |
| Generics | 1934 | 1657 | 14.6% |
]

.center[
#### 平均して10.47%実行部分LOCが減少
]

---

## 評価結果と考察 (4/4)

### Simple Valuesにおける実行部分LOCの増加

* TreeSwiftは他の構文を想定した処理を字句解析で行っている
* 様々な字句を用いた単純な構文では比較して実行部分LOCが増加した

### 実行部分LOCの減少理由

* UTF-8文字の処理などプログラム中の要素を同じ構文で記述できる
* Swiftの多様なデータ構造とパターンマッチによる表現力の向上

.font-small[
| | Swift | TreeSwift |
|:---:|---:|---:|
| if文使用数の平均 | 398.429 | 209.163 |
| switch文使用数の平均 | 26.429 | 67.286 |
]

---

## 本研究の結論

.bold.vertical-center.center[
SwiftコンパイラをSelf-host化することによって<br>ソースコードの実行部分LOCを平均10.47%減少させる<br>可読性の向上を得られた
]
