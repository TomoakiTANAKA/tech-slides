---
marp: true
theme: none
paginate: true
---

<style>
img[alt~="center"] {
  display: block;
  margin: 0 auto;
}

</style>

# Unityで課金処理を実装する

2023.06.30 TomoakiTANAKA

---

<!--
_header: "**グループ勉強会資料** __Unityにおける課金処理（In-App Purchase）を実装する__"
_footer: "by [TomoakiTANAKA](tanaka1987＠gmail.com)"
-->

# 自己紹介

---

# 田中智章

![bg right](assets/images/tomioka_silk.png)

- 群馬県富岡市出身
- SEでキャリアを開始
  - 金融、製造etc
- 現在は株式会社DanRan
- 好きなもの
  - ウィスキー、LEGO、ぷよぷよetc

---

# 会社紹介

---



![bg](assets/images/danran.webp)

---

![bg right](./assets/images/akamaru-app.webp)

# 株式会社DanRan

## Vision
### こどもたちが自由に人生を歩む社会をつくる

## Products
### タッチであそぼ！あかまるどれかな？

---

# スキル紹介
## 相談にのれそうなスキル

---
<style scoped>
  table { 
    table-layout: fixed;
    width: 100%;
    display:table;
    font-size: 24px;
  }
</style>

# （相談にのれそうな）スキル紹介

|分類|技術スタック|備考|
|:---:|:---:|:---:|
|Web|Ruby on Rails / Next.js / WordPress / API開発|最近はそんなにやってない|
|アプリ|Unity（2Dゲーム）、Unity課金|今日話すテーマ|
|その他|機械学習のベース（数学）の話|大学で研究していたので|
|その他|エンジニア採用全般|エバーセンスでの実績あり|
|その他|BigQueryによる分析、SEO|非エンジニア向けかも|

---

# 本題

---

# Unityで課金処理を実装する

- 大雑把な実装の流れを説明します
  - C#のサンプルですが、雰囲気掴んでもらえたら
- iOS / Android / その他 つまづきを紹介します

---

# Unityで課金処理を実装する大まかな流れ

---

# レシート検証（正しい購入情報かどうかの確認）をサーバーで行う

---

# iOSやAndroid特有のつまずきポイント

---

# まとめ

---

# まとめ①

xxxxx

---

# ご清聴ありがとうございました

---

# 質疑応答

---

# おわり
