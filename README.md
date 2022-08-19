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
|[20.4.5](https://github.com/miyako/4d-tutorial-my-first-plugin/tree/main/sdk/20.4.5)|v14|

SDKの本体は`4DPluginAPI.c`というソースファイルです。ファイルには`FourDPackex`という名前の関数をエクスポートしており，これが`main`に相当するエントリーポイントとなっています。

4Dは，プラグインをロードすると`FourDPackex`にコールバックのアドレスを渡します。プラグインは，このアドレスをグローバル変数`gCall4D`に代入します。ほとんどすべてのAPIは，このアドレスをコールする仕組みになっています。いつでも，どんなスレッドでも`gCall4D`がコールできるというわけでなく，4Dから呼び出されれたタイミング，つまりプラグインコマンドまたはプラグインエリアイベントのコンテキストでなければ，APIを使用することはできません。

上述した最低バージョンは，大体の目安です。v11以降，SDK側の基本的な仕様は変更されておらず，新しいものほどAPIの数が多くなっています。言い換えるならば，新しいAPIを使用しなければ，下位バージョン向けのプラグインを開発することもできます。ただし，プラグインはプラットフォームSDK（WindowsまたはmacOS）ともリンクしているので，下位バージョンの4Dでロードできるようにするためには，コンパイラーやリンカーのフラグを対象に合わせて調整する必要があります。

## 4D Plugin Wizard

GUIでプラグインのコマンド名やパラメーターの組み合わせを設定し，Xcode, Visual Studioのプロジェクトを生成するためのツールですが，前述したように必須ではなく，この記事では使用しません。

## Template

[4d-plugin-template](https://github.com/miyako/4d-plugin-template)をダウンロードまたはクローンします。

`{project}/test/Project/test.4DProject`プロジェクトを開きます。

`generate_project`メソッドを開きます。

```4d
$params:=New object
$params.PRODUCT_NAME:="My First Plugin"
$params.PRODUCT_VERSION:="1.0.0"
$params.AUTHOR:=Current system user
$params.CREATE_DATE:=Current date
$params.COPYRIGHT_YEAR:=Year of(Current date)

generate_project_source($params)
generate_project_vs($params)
generate_project_xcode($params)
generate_project_plugin_stub($params)
```

* `PRODUCT_NAME`: プラグイン・ソースコード・プロジェクトなどの名称になる任意の文字列です。

* `PRODUCT_VERSION`: *Info.plist*に書き込まれるバージョンコードです。通常は[Semantic Versioning 2.0.0](https://semver.org)で管理します。

* `AUTHOR`: ソースコードのコメント文および*Info.plist*に書き込まれる任意の文字列です。

---

*manifest.json*ファイルを開きます。

```json
{
    "name": "My First Plugin",
    "id": 20000,
    "commands": [
        {
            "theme": "Theme 1",
            "syntax": "My First Plugin Command:J",
            "threadSafe": true
        }
    ]
}
```

`name`: エクスプローラーに表示されるプラグイン名です。

`id`: [PLUGIN LIST](https://doc.4d.com/4Dv19R5/4D/19-R5/PLUGIN-LIST.301-5830974.ja.html)から返されるプラグイン番号です。`15001`から`32000`の間を設定します。この範囲のプラグイン番号を設定した場合，プラグインは名前で識別されます。`15000`以下の値は4D純正プラグインのために予約されており，プラグインは名前で識別されます。

`commands.theme`: エクスプローラーに表示されるプラグインコマンドの分類名です。

`commands.syntax`: プラグインのコマンド名およびシンタックスです（後述）。

`commands.threadSafe`: コマンドがプリエンプティブモードに対応していれば`true`に設定します。v16以前では効果がありません。APIの対応状況はSDKの*EntryPoints.h*ファイルで確認することができます。`threadSafe`と記述されているAPIは対応しています。その他，グローバル変数に対する排他的アクセスをmutexで保護し，スレッドセーフなプラットフォームAPIだけを使用するなど，C言語のレベルでもプリエンプティブモードに対応する必要があります。

---

```c
#ifndef PLUGIN_MY_FIRST_PLUGIN_H
#define PLUGIN_MY_FIRST_PLUGIN_H

#include "4DPluginAPI.h"

#pragma mark -

void My_First_Plugin_Command(PA_PluginParameters params);

#endif /* PLUGIN_MY_FIRST_PLUGIN_H */
```

```
#include "4DPlugin-My-First-Plugin.h"

#pragma mark -

void PluginMain(PA_long32 selector, PA_PluginParameters params) {
    
	try
	{
        switch(selector)
        {
			// --- My First Plugin
            
			case 1 :
				My_First_Plugin_Command(params);
				break;

        }

	}
	catch(...)
	{

	}
}

#pragma mark -

void My_First_Plugin_Command(PA_PluginParameters params) {

}
```
