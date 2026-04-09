---
date: '2026-04-08T10:38:00-07:00'
draft: false
title: 'T-Code-Engine'
author: Kazuhiro F
tags:
    - T-Code-Engine
---

## 主な機能

T-Code-Engine では, ユーザー入力をまとめて変換する `BatchEngine` と, ユーザーによ
る入力と選択をインタラクティブに実行できる `InteractiveEngine` があります.

### `BatchEngine` の実行例

```scala
scala> import io.github.nicheapplab.tcodeengine._
scala> val engine = new BatchEngine with QwertyLayout
scala> engine.convert("hgjdkdhgjdhgjgjd;gjdkd;gjdja;g")
val res0: String = "で、ので、では、を、のを、とを"
```

`with QwertyLayout` の代わりに `with DvorakLayout` を指定することで, Dvorak キー
ボードで入力した文字列を日本語に変換することができます. また, **交ぜ書き変換**や**部首
合成**にも対応しています. 交ぜ書きは prefix を使用せずに, `BatchEngine.convert` で
変換した結果に対して, `BatchEngine.mixed.convert` を適用します. 2 つ目の引数は活
用の指定です.

```scala
scala> engine.mixed.convert("くわえ","る")
val res0: Array[String] = Array("加える")

scala> engine.mixed.convert("記しゃ")
val res1: Array[String] = Array("記者")
```

部首合成では, 基本となる二字の合成のほか, "jf" による再帰的合成にも対応しています.

```scala
scala> engine.combi.composite('上','七')
val res0: Option[Char] = Some('虍')

scala> engine.convert("jfjfibhtpd")
val res1: String = "劇"
```

### `InteractiveEngine` の実行例

```scala
scala> import io.github.nicheapplab.tcodeengine._
scala> val engine = new InteractiveEngine with QwertyLayout
scala> "fjyijstt".foreach(ie.put(_))
scala> ie.inflexRight()
scala> ie.convert()
scala> ie.selectCandidate(0)
scala> ie.commit()
val res0: String = "記者"
```

この例では, "fj"で交ぜ書き変換モードに入り, "記しゃ"を"記者"に変換しています.

- 例では短縮のために `foreach` を利用していますが, 実際に使用するときには各字を,
`ie.put('f')` のように 1 字ずつ入力することが想定されています.

- `InteractiveEngine.inflexRight()` や `InteractiveEngine.inflexLeft()` を実行する
ことで活用の位置を指定しています.

- 交ぜ書き変換の結果は
`InteractiveEngine.candidates` を参照し, 望ましい変換結果の index を指定します.

- `InteractiveEngine.commit()` を実行することで, 確定した変換を取り出すことがで
きます.

```scala
scala> val ie = new InteractiveEngine with QwertyLayout
scala> "fjjfpw.v.ddt".foreach(ie.put(_))
scala> ie.inflexLeft()
scala> ie.convert()
scala> ie.selectCandidates(0)
scala> "fjjeux".foreach(ie.put(_))
scala> ie.inflexRight()
scala> ie.convert()
scala> ie.selectCandidate(3)
scala> "kgjwjc".foreach(ie.put(_))
scala> ie.commit()
val res0: String = "相次ぐ火事により"
```

同様に, 交ぜ書きモードの中で部首合成を実行することができます. また, IME では変換
中の入力をリアルタイムでモニターすることが求められます. そのため
`InteractiveEngine` では, 変換中は確定した部分を
`InteractiveEngine.outputBuffer` , 未確定部分を `InteractiveEngine.buffer` に格
納しています.

## 開発情報

- sbt
```
libraryDependencies += "io.github.nicheapplab" %% "t-code-engine" % "0.4.3"
```
- [GitHub](https://github.com/NicheAppLab/T-Code-Engine)
- [API/0.4.3](/tcodeengine-0.4.3-api/index.html)
- [API/0.4.0](/tcodeengine-0.4.0-api/index.html)
