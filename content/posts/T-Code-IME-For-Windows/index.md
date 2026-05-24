---
date: '2026-05-21T16:56:38-07:00'
title: 'T Code IME for Windows'
url: "/posts/t-code-ime-for-windows"
logical_breadcrumbs:
    - title: "T-Code"
      url: "/posts/t-code"
tags:
    - T-Code
    - T-Code IME for Windows
weight: 1
---
Android 用 T-Code IME として T-Code IME for Android をリリースする際に, エンジン
部分(T-Code Engine)を取り出し, 各プラットフォームで IME を提供するという構想があり
ました.

とはいえ, Windows 用の IME には Text Services Framework という C++ 用に提供され
たライブラリを使用するという制約上, Android 用と比較すると複雑な構成です.


![T-Code IME for Windows のコンポーネント図](./components.png)

- IME 本体である `TCode IME for Windows` は薄いラッパー
- バックグラウンドプロセスに `TCode Proxy`
- `TCode Proxy` は別の Java Thread (`Pekko Actor`) として `T-Code Engine` のサーバーを立てる
- そしてそれぞれ Windows Named Pipe と gPRC プロトコルを用いてローカルマシン上で通信する

T-Code IME for Android がこうした複雑な構成とならなかった理由は,ひとえに Android
アプリが Kotlin で書かれており, 同プロセス上に Scala で実装した T-Code Engine を
直接利用できたためでした.
