---
layout: post
title: "Kaggle の Two-Stage Competition について"
description: ""
category: 
tags: []
---
{% include JB/setup %}

Kaggle の 2ステージコンペとは何かと 2ステージコンペで起きたふたつの事件を説明します。

## 2ステージコンペ

Kaggle のコンペでは教師データ付きの訓練データとテストデータが与えられ、
競技参加者は機械学習モデルを作って訓練データでモデルを学習して、モデルにテストデータを入力し予測した結果を提出します。
たとえば、日にちや天気、気温からお弁当や生鮮食品の需要予測とかです。

ある種の機械学習タスクは人間とAIが協力すると高スコアになれるものがあります。
たとえば、画像認識や音声認識とかです。
そのようなタスクではテストデータをすべて競技参加者に与えてしまうとチートできてしまうので
2ステージコンペを行います。

2ステージコンペではテストデータは２つ用意されます。
パブリックテストデータとプライベートテストデータです。
1st ステージ開始時にはパブリックテストデータのみが与えられます。
競技参加者は 1st ステージ期間中に予測した結果を何度でも提出でき、その得点はパブリックスコアボードに表示されます。
1st ステージ終了時には競技参加者は最終提出結果を出力した機械学習モデルのソースコードも提出します。
2nd ステージではプライベートテストデータが与えられ、参加者は 1st ステージで提出したプログラムを用いて予測した結果を提出します。
その得点はプライベートスコアボードに表示され、暫定の順位がつきます。

その後、賞金の出る入賞者のプログラムのみ確認が行われます。
同一のプログラムからプライベートとパブリックの両方のスコアが再現できるかを確認されます。
こうして、順位が確定します。

## 魚コンペの事例

ザ・ネイチャー・コンサーバンシー主催の船上のカメラ画像に写った魚の種類を当てるコンペです。
2ステージコンペでした。
どんな写真か見たければこのあたりから。

* [https://www.kaggle.com/c/the-nature-conservancy-fisheries-monitoring](https://www.kaggle.com/c/the-nature-conservancy-fisheries-monitoring)
* [https://www.kaggle.com/anokas/finding-boatids/notebook](https://www.kaggle.com/anokas/finding-boatids/notebook)

4/5 が 1st ステージの期限でソースコードの提出期限でした。
4/6 から 2nd ステージの開始で、
4/12 が 2nd ステージの期限でした。

そして事件が起きました。
プライベートスコアボード１位の Pinto さんが提出したプログラムがプライベートスコアを再現できませんでした。

* [https://www.kaggle.com/c/the-nature-conservancy-fisheries-monitoring/discussion/31607](https://www.kaggle.com/c/the-nature-conservancy-fisheries-monitoring/discussion/31607)

そこには Pinto さん自身のコメントがあります。
1st ステージの終了直前に大規模な変更を加えたら、前処理スクリプトのバージョンを間違えて提出したそうです。

出すべきものはパブリックスコアとプライベートスコアを再現できるひとつのプログラムなので言い訳になっていません。


## 細胞コンペの事例

次の事例は顕微鏡の画像から細胞の核を見つけるコンペです。
2ステージコンペでした。
画像はこのあたりから。

* [https://www.kaggle.com/mpware/stage1-eda-microscope-image-types-clustering/notebook](https://www.kaggle.com/mpware/stage1-eda-microscope-image-types-clustering/notebook)

1st ステージ終了時にソースコードを提出することを知らずに 2nd ステージ終了後に多くの参加者が失格にされてしまいました。
それ自体はルール通りでした。

そして、事件が起きました。
失格者の不満に押されて運営は失格者を復活してしまいました。

* [https://www.kaggle.com/c/data-science-bowl-2018/discussion/55228](https://www.kaggle.com/c/data-science-bowl-2018/discussion/55228)

理由は 1st ステージ終了時にソースを提出することが一部の参加者には曖昧だったからだそうです。

そして、不思議なことにパブリックスコアボードはクリアされています(2018/05/11 現時点で)。
パブリックスコアボードとプライベートスコアボードを比較されると不都合なことでもあるのでしょうか。

## 感想

入賞しなければチートし放題の2ステージコンペの仕組みや、
1stステージ終了後にソースコードを提出していなくても 2ndステージで提出できてしまうこと、
失格者が復活された対応、
最初知ったときはひどいの一言でした。

しかしながら、ソースコード確認作業の手間を考えると、
厳格なコンペを求めるならば参加費を有料にしなくてはなりません。

チートしても別に賞金がでるわけではないことを思うと、
そもそもの不幸の始まりは入賞できない人も楽しめるようにランキングやメダルのアイコンを与えたのが、
うまく行き過ぎて参加者がそれを不正してでも欲するようになったことです。

ベテランのひとも同じようなことを言ってます。

* [https://www.kaggle.com/c/data-science-bowl-2018/discussion/54575#314945](https://www.kaggle.com/c/data-science-bowl-2018/discussion/54575#314945)

Kaggle では世界のトップレベルのデータサイエンティストと
私のような機械学習を初めてまだ２ヶ月の初心者が同じコンペに参加する奇跡の空間です。
参加費が無料であり気軽に参加できることが、それが実現している大きな理由であると思います。
参加費無料でできることの限界を考えると、Kaggle はよくできていると思います。


## その他

kaggler-ja での会話がもとですが、
Slack での内容はすぐに流れてしまうので、まとめてみました。

final submission は２つ提出することなど省略しているため不正確な説明があります。

詳しくは[Two-Stage Competition FAQ](https://www.kaggle.com/two-stage-faq)、
なぜか現在は見れないので[その WayBackMachine](https://web.archive.org/web/20170725070603/https://www.kaggle.com/two-stage-faq)
を参照してください。
