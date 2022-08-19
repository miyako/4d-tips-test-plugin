# 4d-tutorial-my-first-plugin
簡単なプラグインを作ってみましょう

## 概要

4Dのプラグインは，プラットフォーム標準の[ダイナミックリンクライブラリ](https://ja.wikipedia.org/wiki/ダイナミックリンクライブラリ)です。通常は[C](https://ja.wikipedia.org/wiki/C言語)で開発したソースコードをコンパイルして作成しますが，コンパイルしてライブラリを生成できるプログラミング言語であれば，[Go](https://ja.wikipedia.org/wiki/Go_(プログラミング言語))・[Swift](https://ja.wikipedia.org/wiki/Swift_(プログラミング言語))でも開発することができます。

公式の[プラグイン開発キット](https://github.com/4d/4D-Plugin-SDK)には，下記のものが含まれています。

* 4D Plugin API - 公開されているエントリーポイントやパラメーターの組み合わせを関数にまとめたもの
* 4D Plugin Wizard - Xcode, Visual Studioのプロジェクトを生成するためのツール
* docs - v2003当時のドキュメント

プラグインの開発に必要なものは「4D Plugin API」だけであり，他はメンテナンスもあまりされていないようです。おそらく，ほとんどのプラグインデベロッパーは「4D Plugin API」以外は無視しているものと思われます。
