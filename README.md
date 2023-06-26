# AD7SGPR

![製品画像]()

(製品説明後日記載)  
シリアル通信で文字列を送るだけで値を表示できる７セグメントディスプレイ AD7SGPRのリポジトリです。   
[ターミナルソフト](#ターミナルソフトtera-termからの使い方)や[サンプルWEBApp](#値表示用サンプルweb-app)から7セグの表示を制御できます。   
本ドキュメントから各種App等AD7SGPRのサブリポジトリへ飛ぶことが出来ます。  

<br><br>

# 表示のために使うツール
## AD7SGPR表示用サンプルWEB App

※2023/06/21現在スマホからはご利用できません。 PC+Chromeよりご利用ください。  

- [AD7SGPR シリアル通信アプリ](https://bit-trade-one.github.io/AD7SGPR-SCWA/) [  [-使用方法]](https://bit-trade-one.github.io/AD7SGPR-SCWA/10manual.html)
- [YouTube登録者数取得App](https://bit-trade-one.github.io/AD7SGPR-youtubeCh/index.html) [  [-使用方法]](https://bit-trade-one.github.io/AD7SGPR-youtubeCh/10manual.html)
- [グーグルスプレッドシート取得App](https://bit-trade-one.github.io/AD7SGPR-GSS/) [  [-使用方法]](https://bit-trade-one.github.io/AD7SGPR-GSS/10manual.html)

## ターミナルソフト(Tera Term)からの使い方

- [詳細を読む](UseFromTerminal.md)

<br><br>

# 各部詳細
## 本体ボタン・コマンド設定について

AD7SGPRは本体ボタン操作と、シリアル経由のコマンドによる2つの設定方法があります。  
- [本体ボタンの詳細](BodyButtonSpecifications.md)  
- [コマンド設定詳細](SendCommandSpecification.md)  
<br><br>


# その他情報

## ファームウェアアップデート
- [アップデート手順](FirmwareUpdate.md)

## リンク
- [製品HPリンク](http://bit-trade-one.co.jp/)
- [Q&A](FAQ.md) 

## 使用例
- [(Qiita)東京の気温を現実の7セグで表示するWEBAppをGPT-4と作る](https://qiita.com/T-Oda-BTO/private/9eaa449083883b6a4b0d)

---
<br><br>

# エンジニアリング


## ハードウェアエンジニアリング情報
- [回路・PCB](https://github.com/bit-trade-one/AD7SGPR/tree/master/Schematics)(Assembly Desk License)

## ソフトウェアエンジニアリング情報(別リポジトリです)
- [ファームウェアリポジトリ](https://github.com/bit-trade-one/AD7SGPR-FW)(GNUv3.0)

- [AD7SGPR シリアル通信アプリリポジトリ](https://github.com/bit-trade-one/AD7SGPR-SCWA)(Assembly Desk License)
- [YouTube登録者数取得Appリポジトリ](https://github.com/bit-trade-one/AD7SGPR-youtubeCh)(Assembly Desk License)
- [グーグルスプレッドシート取得Appリポジトリ](https://github.com/bit-trade-one/AD7SGPR-GSS)(Assembly Desk License)
  


