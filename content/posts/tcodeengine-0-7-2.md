---
date: '2026-05-08T10:49:32-07:00'
title: 'T-Code-Engine 0.7.2'
url: "/posts/tcodeengine-0-7-2/"
logical_breadcrumbs:
    - title: "T-Code"
      url: "/posts/t-code/"
    - title: "T-Code Engine"
      url: "/posts/tcodeengine/"
weight: 2
tags:
    - T-Code-Engine
---
t-code-engine 0.7.2 をリリースしました.
前回のリリースからオブジェクトの API が変更になりました.

主な変更点は以下のものです.
- エンジン種別の追加
- 辞書ファイル/SQLite データベースの利用
- 部首合成機能の終端指定

## エンジンの種類

T-Code-Engine では, ユーザー入力をまとめて変換する `BatchEngine` と, ユーザーに
よる入力と選択をインタラクティブに実行できる `InteractiveEngine` があります. こ
の種別とは別に, Zip ファイル辞書から SQLite database を生成しこれを使用する
`SQLite` と, Zip ファイル辞書を直接参照する `Archived` の種別があります. まとめる
と, エンジンの選択肢は以下の通りです.

- `SQLiteInteractiveEngine` (T-Code IME for Android で使用中)
- `ArchivedInteractiveEngine`
- `SQLiteBatchEngine`
- `ArchivedBatchEngine`

### `Archived` の用例

`Archived` では, uPickle を利用して msgpack 化し Zip 書庫にまとめた辞書ファイル
(`src/main/resources/tcode_dict.zip`)を使用します. 辞書データはインスタンスを作
成した際にメモリ上に展開されます.

```scala
val engine = new ArchivedInteractiveEngine with QwertyLayout
```

### `SQLite` の用例

一方 Anroid 上の IME はこのエンジンインスタンスの頻度が高く, 書庫ファイルへの頻繁
なアクセスは, IME を要求するたびに ロード時間を増加させるという欠点(IME ラグと呼
ばれます)があります. そのため, T-Code IME for Android では `SQLite` 版を使
用することで, Zip 書庫の読み込みを初回のみに限定し, アプリ専用のストレージに
database ファイルを作成し, IME の機能をコールする際にデータベース問合せを実行しま
す.

`SQLite` 版エンジンインスタンスは以下のようなコードで生成します. この例は
`src/test/scala/InteractiveEngineFixture.scala`からの抜粋です. テスト用のため,
一時ファイルにデータベースファイルを配置しています.

```scala
val tcode_tbl_path = System.getProperty("java.io.tempdir") ++ "/.t-code-engine/tcode_tbl.db"
val mazegaki_path = System.getProperty("java.io.tempdir") ++ "/.t-code-engine/mazegaki.db"
val bushu_path = System.getProperty("java.io.tempdir") ++ "/.t-code-engine/bushu.db"
val jdbc_prefix = "jdbc:sqlite"

Class.forName("org.sqlite.JDBC")

engine = new SQLiteInteractiveEngine(jdbc_prefix, tcode_tbl_path, mazegaki_path, bushu_path) with QwertyLayout
```

Android 等では, SQLite データベースにアクセスするための jdbc がデスクトップとは異
なり, 専用のネイティブ実装を使用するため, コンストラクタ引数 `jdbc_prefix`には
`jdbc:sqlite` ではなく, `jdbc:sqldroid`を指定することに留意が必要です.

次節では, 変換用の API について説明しますが, `Archived` と `SQLite` 版に変換フロー上の差異はありません.

### `InteractiveEngine` の実行例

`InteractiveEngine` ではエンジンインスタンス(例では`ie`)にユーザー入力を`put()`メソッドで渡します.

```scala
scala> "fjyijstt".foreach(ie.put(_))
scala> ie.inflexRight()
scala> ie.convert()
scala> ie.selectCandidate(0)
scala> ie.commit()
val res0: String = "記者"
```

この例では, "fj"で交ぜ書き変換モードに入り, "記しゃ"を"記者"に変換しています.

- 例では短縮のために `foreach` を利用していますが, 実際に使用するときには各字を
IME のイベントハンドラで`ie.put('f')` のように 1 字ずつ入力することが想定されてい
ます.

- `inflexRight()` および `inflexLeft()` を実行する
ことで活用の位置を指定しています.

- 交ぜ書き変換の結果は`candidates` を参照し, 望ましい変換結果の index を指定しま
す.

- `commit()` を実行することで, 確定した変換を取り出すことができます.

### 部首合成の終端指定

```scala
scala> "fjjfpw.v".foreach(ie.put(_))
scala> ie.convert()
scala> ".ddt".foreach(ie.put(_))
scala> ie.inflexLeft()
scala> ie.convert()
scala> ie.selectCandidate(0)
scala> "fjjeux".foreach(ie.put(_))
scala> ie.inflexRight()
scala> ie.convert()
scala> ie.selectCandidate(3)
scala> "kgjwjc".foreach(ie.put(_))
scala> val res = ie.commit()
```

これまで通り, 交ぜ書きモードの中で部首合成を実行することができます. 今リリースでは, 部
首合成区間に対し, 部首合成を`convert()`を呼び出して合成を確定させる必要がありま
す(変更の理由[部首合成機能の終端指定]({{< ref "convert-in-kanjicomposition" >}})).

変換中は確定した部分を`InteractiveEngine.outputBuffer` , 未確定部分を
`InteractiveEngine.buffer` に格納しています. IME の実装においては, これらの内部
変数を参照することになるでしょう.

## 開発情報

- sbt
```
libraryDependencies += "io.github.nicheapplab" %% "tcodeengine" % "0.7.2"
```
- [GitHub](https://github.com/NicheAppLab/T-Code-Engine)
- [API/0.7.2](/tcodeengine-0.7.2-api/index.html)
