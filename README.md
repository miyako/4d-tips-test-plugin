# 4d-tutorial-my-first-plugin
簡単なプラグインを作ってみましょう

## 概要

4Dのプラグインは，プラットフォーム標準の[ダイナミックリンクライブラリ](https://ja.wikipedia.org/wiki/ダイナミックリンクライブラリ)です。通常は[**C**](https://ja.wikipedia.org/wiki/C言語)で開発したソースコードを**Visual Studio**または**Xcode**の統合開発環境でコンパイルして作成しますが，ライブラリをコンパイルできる環境であれば，他のツールやプログラミング言語を使用しても構いません。

公式の[プラグイン開発キット](https://github.com/4d/4D-Plugin-SDK)には，下記のものが含まれています。

* 4D Plugin API - 公開されているエントリーポイントやパラメーターの組み合わせを関数にまとめたもの
* 4D Plugin Wizard - Xcode, Visual Studioのプロジェクトを生成するためのツール
* docs - v2003当時のドキュメント

プラグインの開発に必要なものは「4D Plugin API」だけであり，他はメンテナンスもあまりされていないようです。おそらく，ほとんどのプラグインデベロッパーは「4D Plugin API」以外は無視しているものと思われます。

## SDK

SDKに正式なバージョンコードは付けられてないないようです。便宜上，ファイルの日付で区別します。

|4D Plugin SDK|minimum 4D version|
|:-:|:-:|
|[21.7.13](https://github.com/miyako/4d-tutorial-my-first-plugin/tree/main/sdk/21.7.13)|v17|
