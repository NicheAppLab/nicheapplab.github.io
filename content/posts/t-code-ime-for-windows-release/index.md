---
date: '2026-06-15T18:05:20-07:00'
title: 'T-Code IME for Windows リリースページ'
weight: 1
url: "posts/t-code-ime-for-windows-release/"
logical_breadcrumbs:
    - title: "T-Code"
      url: "/posts/t-code"
tags:
    - T-Code
    - T-Code IME for Windows
---
このページでは Windows 向け T-Code IME のリリース情報について記載しています.

## 動作デモ

T-Code IME はストロークを覚えることで, 前から後ろに一直線で文章が書けます.
{{< figure src="./input_kochi.gif" >}}

T-Code IME の交ぜ書き変換を使うと, 変換時の候補数を減らすことができます.
{{< figure src="./input_gohenkan.gif" >}}

## Winget によるインストール

インストールコマンドは x64, x86, arm64 共通です.
![WinGet Package Version](https://img.shields.io/winget/v/NicheAppLab.T-CodeIME?label=winget%3A%20T-CodeIME)

```powershell
winget install NicheAppLab.T-CodeIME
```

下記 x64 向け JRE 同梱版はx64アーキテクチャ限定です.
一般的な Intel/AMD CPUをお使いの方はこちらが便利です.
![WinGet Package Version](https://img.shields.io/winget/v/NicheAppLab.T-CodeIME-JRE?label=winget%3A%20T-CodeIME(JRE%20bundle))

```powershell
winget install NicheAppLab.T-CodeIME-JRE
```

## インストーラーの手動ダウンロード

[ダウンロードページ](https://github.com/NicheAppLab/T-Code-IME-for-Win/releases)

- x64-JRE: JRE 同梱, JRE の設定不要
- x64: 自前の JRE/JDK を使いたい方向け
- arm64: ARM CPU ご利用の方向け
- x86: 32 bit CPU のマシンや VM 内で IME を利用したい方向け

## JDK のインストール方法

x64 や arm64 の場合, Microsoft は各メジャーバージョンでリリースしています.

```powershell
winget install Microsoft.OpenJDK.25
```

## 環境設定

初回起動は T-Code IME Proxy を起動する必要があります.
PC 再起動していれば, 次回は自動で起動します.

JRE 同梱版以外では, Java の設定が必要です.
既に Java を使用していて, 環境が設定されている場合は不要です.
Java のインストールパスは種類やバージョンによって異なるため, 適宜編集してください.

コマンドプロンプト:
```
setx JAVA_HOME "C:\Program Files\Microsoft\jdk-25.0.3.9-hotspot"
setx Path "%Path%;%JAVA_HOME%\bin"
```

PowerShell:
```powershell
[System.Environment]::SetEnvironmentVariable("JAVA_HOME", "C:\Program Files\Microsoft\jdk-25.0.3.9-hotspot",[System.EnvironmentVariableTarget]::User)
$oldPath = [System.Environment]::GetEnvironmentVariable("Path", [System.EnvironmentVariableTarget]::User)
$newPath = "$oldPath;$env:JAVA_HOME\bin"
[System.Environment]::SetEnvironmentVariable("Path", $newPath, [System.EnvironmentVariableTarget]::User)
```

## ライセンス情報

T-Code IME for Windows は [ GPLv2 ライセンス ](https://raw.githubusercontent.com/NicheAppLab/T-Code-IME-for-Win/refs/heads/master/LICENSE.txt)です.

また, 使用しているライブラリのライセンスについては[ こちら ](https://raw.githubusercontent.com/NicheAppLab/T-Code-IME-for-Win/refs/heads/master/THIRD_PARTY_NOTICES.txt)をご確認ください.

## リリース履歴

### v0.2.1.0 (06/15/2026)

- 入力の一部が阻害されるバグを修正
- 変換候補表示の安定化

### v0.2.0.0 (06/13/2026)

- Microsoft C++ Redistributable, .NET Framework 10 Runtime を同梱するように変更
- 変換候補表示方法をコンポーズから ITfCandidateListUI に変更

### v0.1.1.0 (06/10/2026)

- メインのインストーラーを変更 (MSIX -> Inno)
- 使用レジストリの変更

### v0.1.0.0 (05/29/2026)

- 署名付きインストーラーの導入
