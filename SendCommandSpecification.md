# 送信コマンド仕様

Firmware Version 2.3について記述

通信速度は115200bps 改行文字はCRLFが規定  
ターミナルソフトやAD7SGPR シリアル通信Webアプリより送信してください。

# 目次

- [送信コマンド仕様](#送信コマンド仕様)
- [目次](#目次)
- [送信コマンド一覧](#送信コマンド一覧)
- [各コマンド詳細仕様](#各コマンド詳細仕様)
  - [英数字・記号(ASCII)](#英数字記号ascii)
  - [@HEX](#hex)
  - [@CLR](#clr)
  - [@ANI](#ani)
  - [@7SG](#7sg)
  - [セグメントの個別制御について](#セグメントの個別制御について)
  - [エラーコード内容](#エラーコード内容)

<br><br>

# 送信コマンド一覧
コマンドを送信する際は余計なスペースなどを含めないでください。  
連続でデータを送信する場合は最低1ms以上の間隔を空けてください。  
確実に行うのであればAD7SGPRからのアンサーバックを確認してからの送信をおすすめします。

| コード | 機能 |
| --- | --- |
| 英数字・記号(ASCII) | 受信した英数字・記号を7セグディスプレイに表示します。(最大8桁まで、「.」は1つまで)              |
| @HEX[??*8桁]        | 16進数のデータでセグメントを個別に制御します。 |
| @CLR                | ディスプレイをクリアします。                                                                    |
| @ANI[数字0-2]       | ディスプレイのトランジションを設定します。<br>@ANI0 アニメなし<br>@ANI1 数字によるスロット風<br>@ANI2 セグメントの回転 |
| @7SG[数字0,1]       | ディスプレイの表示モードを設定します。<br>@7SG0 ディスプレイ消灯<br>@7SG1 ディスプレイ点灯<br>7セグの表示はされないだけでAD7SGPRから応答は返ってきます |
| @BRI[数字0-7]       | ディスプレイの輝度を設定します。@BRI の後に 0 から 7 の数字をつけて輝度を設定します。例：@BRI5     |
| @BRR                | 現在のディスプレイの輝度を返します。                                                            |


---
<br><br>

# 各コマンド詳細仕様

## 英数字・記号(ASCII)
[目次へ戻る](#目次)  
AD7SGPRに送信した文字列を7セグメントディスプレイに表示する  
(8桁まで、小数点は1つまで)  

| 送信コード | AD7SGPR返信コード| AD7SGPR7セグ表示 | 概要 |
| --- | --- | --: | --- |
| 123 | rxData:123| 123 |数字表示|
| abc | rxData:abc | abc |英字表示(一部英字・記号は不完全な状態で描画されます)|
| bto2023 | rxData:bto2023 | bto2023 |英数混合表示|
| 123456789 | rxData:123456789 | 23456789 |表示は8桁まで|
| 12.3 | rxData:12.3 | 12.3 |小数点は1つまで|
| 8.8.8.8.| rxData:8.8.8.8.<br>E1: Multiple dots | 23456789.0 | エラーコード:小数点は1つまで |

---

## @HEX
[目次へ戻る](#目次)  
セグメントの個別点灯の制御
字では表せないようなセグメントの点灯をサポート

| 送信コード | AD7SGPR返信コード| AD7SGPR7セグ表示 | 概要 |
| --- | --- | --: | --- |
| @HEXFFFFFFFFFFFFFFFF | rxData:@HEXFFFFFFFFFFFFFFFF | 8.8.8.8.8.8.8.8. |  |
| @HEX0000000000000080 |rxData:@HEX0000000000000080 |       .| |
| @HEXFF00000000000000| rxData:@HEXFF00000000000000 | 8.&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;||
|@HEX|rxData:@HEX<br>E3: Need 16 hex digits||エラーコード：@HEXのあとに続く文字が16桁ではない|

<details><summary>もっと詳しい解説</summary><div>

16桁の16進数でセグメントの点灯を制御します。  
2桁づつがそれぞれのセグメントへの指示となっており、特殊な点灯が可能です。

![セグメント割当](https://github.com/bit-trade-one/AD7SGPR-SCWA/assets/85532743/ef28a74a-37e0-4872-a17f-20ae382e3b21)

以下にセグメントの点灯を制御するための16進数と点灯内容を表にまとめました。

| @HEX用16進数 | セグメント点灯              |
| ------------ | --------------------------- |
| 00           | (無点灯)                    |
| 01           | a                           |
| 02           | b                           |
| 03           | a, b                        |
| 04           | c                           |
| 05           | a, c                        |
| 06           | b, c                        |
| 07           | a, b, c                     |
| 08           | d                           |
| 09           | a, d                        |
| 0A           | b, d                        |
| 0B           | a, b, d                     |
| 0C           | c, d                        |
| 0D           | a, c, d                     |
| 0E           | b, c, d                     |
| 0F           | a, b, c, d                  |
| 10           | e                           |
| 11           | a, e                        |
| 12           | b, e                        |
| 13           | a, b, e                     |
| 14           | c, e                        |
| 15           | a, c, e                     |
| 16           | b, c, e                     |
| 17           | a, b, c, e                  |
| 18           | d, e                        |
| 19           | a, d, e                     |
| 1A           | b, d, e                     |
| 1B           | a, b, d, e                  |
| 1C           | c, d, e                     |
| 1D           | a, c, d, e                  |
| 1E           | b, c, d, e                  |
| 1F           | a, b, c, d, e               |
| 20           | f                           |
| 21           | a, f                        |
| 22           | b, f                        |
| 23           | a, b, f                     |
| 24           | c, f                        |
| 25           | a, c, f                     |
| 26           | b, c, f                     |
| 27           | a, b, c, f                  |
| 28           | d, f                        |
| 29           | a, d, f                     |
| 2A           | b, d, f                     |
| 2B           | a, b, d, f                  |
| 2C           | c, d, f                     |
| 2D           | a, c, d, f                  |
| 2E           | b, c, d, f                  |
| 2F           | a, b, c, d, f               |
| 30           | e, f                        |
| 31           | a, e, f                     |
| 32           | b, e, f                     |
| 33           | a, b, e, f                  |
| 34           | c, e, f                     |
| 35           | a, c, e, f                  |
| 36           | b, c, e, f                  |
| 37           | a, b, c, e, f               |
| 38           | d, e, f                     |
| 39           | a, d, e, f                  |
| 3A           | b, d, e, f                  |
| 3B           | a, b, d, e, f               |
| 3C           | c, d, e, f                  |
| 3D           | a, c, d, e, f               |
| 3E           | b, c, d, e, f               |
| 3F           | a, b, c, d, e, f            |
| 40           | g                           |
| 41           | a, g                        |
| 42           | b, g                        |
| 43           | a, b, g                     |
| 44           | c, g                        |
| 45           | a, c, g                     |
| 46           | b, c, g                     |
| 47           | a, b, c, g                  |
| 48           | d, g                        |
| 49           | a, d, g                     |
| 4A           | b, d, g                     |
| 4B           | a, b, d, g                  |
| 4C           | c, d, g                     |
| 4D           | a, c, d, g                  |
| 4E           | b, c, d, g                  |
| 4F           | a, b, c, d, g               |
| 50           | e, g                        |
| 51           | a, e, g                     |
| 52           | b, e, g                     |
| 53           | a, b, e, g                  |
| 54           | c, e, g                     |
| 55           | a, c, e, g                  |
| 56           | b, c, e, g                  |
| 57           | a, b, c, e, g               |
| 58           | d, e, g                     |
| 59           | a, d, e, g                  |
| 5A           | b, d, e, g                  |
| 5B           | a, b, d, e, g               |
| 5C           | c, d, e, g                  |
| 5D           | a, c, d, e, g               |
| 5E           | b, c, d, e, g               |
| 5F           | a, b, c, d, e, g            |
| 60           | a, b, c, d, e               |
| 61           | a, b, c, d, e, f            |
| 62           | a, b, c, d, f               |
| 63           | a, b, c, d, e, g            |
| 64           | b, c, f, g                  |
| 65           | a, b, c, f, g               |
| 66           | b, c, e, f, g               |
| 67           | a, b, c, d, f, g            |
| 68           | c, d, e, f, g               |
| 69           | a, c, d, e, f, g            |
| 6A           | b, c, d, e, f, g            |
| 6B           | a, b, c, d, e, f, g         |
| 6C           | d, e, f, g                  |
| 6D           | a, d, e, f, g               |
| 6E           | b, d, e, f, g               |
| 6F           | a, b, d, e, f, g            |
| 70           | c, d, e, f, g               |
| 71           | a, c, d, e, f, g            |
| 72           | b, c, d, e, f, g            |
| 73           | a, b, c, d, e, f, g         |
| 74           | c, f, g                     |
| 75           | a, c, f, g                  |
| 76           | b, c, f, g                  |
| 77           | a, b, c, f, g               |
| 78           | d, f, g                     |
| 79           | a, d, f, g                  |
| 7A           | b, d, f, g                  |
| 7B           | a, b, d, f, g               |
| 7C           | c, d, f, g                  |
| 7D           | a, c, d, f, g               |
| 7E           | b, c, d, f, g               |
| 7F           | a, b, c, d, e, f, g         |
| 80           | D.P (小数点)                |
| 81           | a, D.P                      |
| 82           | b, D.P                      |
| 83           | a, b, D.P                   |
| 84           | c, D.P                      |
| 85           | a, c, D.P                   |
| 86           | b, c, D.P                   |
| 87           | a, b, c, D.P                |
| 88           | d, D.P                      |
| 89           | a, d, D.P                   |
| 8A           | b, d, D.P                   |
| 8B           | a, b, d, D.P                |
| 8C           | c, d, D.P                   |
| 8D           | a, c, d, D.P                |
| 8E           | b, c, d, D.P                |
| 8F           | a, b, c, d, D.P             |
| 90           | e, D.P                      |
| 91           | a, e, D.P                   |
| 92           | b, e, D.P                   |
| 93           | a, b, e, D.P                |
| 94           | c, e, D.P                   |
| 95           | a, c, e, D.P                |
| 96           | b, c, e, D.P                |
| 97           | a, b, c, e, D.P             |
| 98           | d, e, D.P                   |
| 99           | a, d, e, D.P                |
| 9A           | b, d, e, D.P                |
| 9B           | a, b, d, e, D.P             |
| 9C           | c, d, e, D.P                |
| 9D           | a, c, d, e, D.P             |
| 9E           | b, c, d, e, D.P             |
| 9F           | a, b, c, d, e, D.P          |
| A0           | f, D.P                      |
| A1           | a, f, D.P                   |
| A2           | b, f, D.P                   |
| A3           | a, b, f, D.P                |
| A4           | c, f, D.P                   |
| A5           | a, c, f, D.P                |
| A6           | b, c, f, D.P                |
| A7           | a, b, c, f, D.P             |
| A8           | d, f, D.P                   |
| A9           | a, d, f, D.P                |
| AA           | b, d, f, D.P                |
| AB           | a, b, d, f, D.P             |
| AC           | c, d, f, D.P                |
| AD           | a, c, d, f, D.P             |
| AE           | b, c, d, f, D.P             |
| AF           | a, b, c, d, f, D.P          |
| B0           | e, f, D.P                   |
| B1           | a, e, f, D.P                |
| B2           | b, e, f, D.P                |
| B3           | a, b, e, f, D.P             |
| B4           | c, e, f, D.P                |
| B5           | a, c, e, f, D.P             |
| B6           | b, c, e, f, D.P             |
| B7           | a, b, c, e, f, D.P          |
| B8           | d, e, f, D.P                |
| B9           | a, d, e, f, D.P             |
| BA           | b, d, e, f, D.P             |
| BB           | a, b, d, e, f, D.P          |
| BC           | c, d, e, f, D.P             |
| BD           | a, c, d, e, f, D.P          |
| BE           | b, c, d, e, f, D.P          |
| BF           | a, b, c, d, e, f, D.P       |
| C0           | g, D.P                      |
| C1           | a, g, D.P                   |
| C2           | b, g, D.P                   |
| C3           | a, b, g, D.P                |
| C4           | c, g, D.P                   |
| C5           | a, c, g, D.P                |
| C6           | b, c, g, D.P                |
| C7           | a, b, c, g, D.P             |
| C8           | d, g, D.P                   |
| C9           | a, d, g, D.P                |
| CA           | b, d, g, D.P                |
| CB           | a, b, d, g, D.P             |
| CC           | c, d, g, D.P                |
| CD           | a, c, d, g, D.P             |
| CE           | b, c, d, g, D.P             |
| CF           | a, b, c, d, g, D.P          |
| D0           | e, g, D.P                   |
| D1           | a, e, g, D.P                |
| D2           | b, e, g, D.P                |
| D3           | a, b, e, g, D.P             |
| D4           | c, e, g, D.P                |
| D5           | a, c, e, g, D.P             |
| D6           | b, c, e, g, D.P             |
| D7           | a, b, c, e, g, D.P          |
| D8           | d, e, g, D.P                |
| D9           | a, d, e, g, D.P             |
| DA           | b, d, e, g, D.P             |
| DB           | a, b, d, e, g, D.P          |
| DC           | c, d, e, g, D.P             |
| DD           | a, c, d, e, g, D.P          |
| DE           | b, c, d, e, g, D.P          |
| DF           | a, b, c, d, e, g, D.P       |
| E0           | f, g, D.P                   |
| E1           | a, f, g, D.P                |
| E2           | b, f, g, D.P                |
| E3           | a, b, f, g, D.P             |
| E4           | c, f, g, D.P                |
| E5           | a, c, f, g, D.P             |
| E6           | b, c, f, g, D.P             |
| E7           | a, b, c, f, g, D.P          |
| E8           | d, f, g, D.P                |
| E9           | a, d, f, g, D.P             |
| EA           | b, d, f, g, D.P             |
| EB           | a, b, d, f, g, D.P          |
| EC           | c, d, f, g, D.P             |
| ED           | a, c, d, f, g, D.P          |
| EE           | b, c, d, f, g, D.P          |
| EF           | a, b, c, d, f, g, D.P       |
| F0           | e, f, g, D.P                |
| F1           | a, e, f, g, D.P             |
| F2           | b, e, f, g, D.P             |
| F3           | a, b, e, f, g, D.P          |
| F4           | c, e, f, g, D.P             |
| F5           | a, c, e, f, g, D.P          |
| F6           | b, c, e, f, g, D.P          |
| F7           | a, b, c, e, f, g, D.P       |
| F8           | d, e, f, g, D.P             |
| F9           | a, d, e, f, g, D.P          |
| FA           | b, d, e, f, g, D.P          |
| FB           | a, b, d, e, f, g, D.P       |
| FC           | c, d, e, f, g, D.P          |
| FD           | a, c, d, e, f, g, D.P       |
| FE           | a, b, c, d, e, f, g, D.P    |
| FF           | a, b, c,d, e, f, g, D.P    |

</details><br>

---

## @CLR
[目次へ戻る](#目次)  
７セグメントディスプレイに表示された内容を消去する

| 送信コード | AD7SGPR返信コード| AD7SGPR7セグ表示 | 概要 |
| --- | --- | --: | --- |
|@CLR|rxData:@CLR Display clear|(消去)||


---

## @ANI
[目次へ戻る](#目次)  

トランジション（表示遷移時のアニメーション）の設定  

| 送信コード | AD7SGPR返信コード| AD7SGPR7セグ表示 | 概要 |
| --- | --- | --: | --- |
|@ANI0|rxData:@ANI0<br>No animation||トランジションをなしに設定(デフォルト)|
|@ANI1|rxData:@ANI1<br>Animation1||トランジションをパターン1に設定(数字によるスロット風)|
|@ANI2|rxData:@ANI2<br>Animation2||トランジションをパターン2に設定(セグメントの回転)|


---

## @7SG
[目次へ戻る](#目次)  

7セグメントディスプレイの表示モードを設定する

| 送信コード | AD7SGPR返信コード| AD7SGPR7セグ表示 | 概要 |
| --- | --- | --: | --- |
| @7SG0 | rxData:@7SG0<br>7sgOff | (裏面LED弱く点灯) | | 
| @7SG1 | rxData:@7SG1<br>7sgOn | (裏面LED強く点灯) | |


---

## @BRI
[目次へ戻る](#目次)  

| 送信コード | AD7SGPR返信コード| AD7SGPR7セグ表示 | 概要 |
| --- | --- | --: | --- |
| @BRI0 | rxData:@BRI0<br>brightness: 0 | (輝度0) | |
| @BRI1 | rxData:@BRI1<br>brightness: 1 | (輝度1) | |
| @BRI2 | rxData:@BRI2<br>brightness: 2 | (輝度2) | |
| @BRI3 | rxData:@BRI3<br>brightness: 3 | (輝度3) | |
| @BRI4 | rxData:@BRI4<br>Brightness: 4 | (輝度4) | |
| @BRI5 | rxData:@BRI5<br>Brightness: 5 | (輝度5) | |
| @BRI6 | rxData:@BRI6<br>Brightness: 6 | (輝度6) | |
| @BRI7 | rxData:@BRI7<br>Brightness: 7 | (輝度7) | |
| @BRI8 | rxData:@BRIx<br>E4: Brightness out of range || エラーコード:輝度が0-7で指定されていない|




---

## @BRR
[目次へ戻る](#目次)  

| 送信コード | AD7SGPR返信コード| AD7SGPR7セグ表示 | 概要 |
| --- | --- | --: | --- |
| @BRR |rxData:@BRR<br>brightness: ?|| ?は現在の輝度 |

---


## エラーコード一覧
[目次へ戻る](#目次)  

| エラーコード | エラー内容                                                        |
| ------------ | ----------------------------------------------------------------- |
| E0           | Over receive length<br>受信文字は20文字未満にしてください         |
| E1           | Multiple dots<br>ピリオドを1個以上存在させないでください           |
| E3           | Need 16 hex digits<br>16桁の16進数が必要                          |
| E4           | Brightness out of range<br>輝度が範囲外                           |
