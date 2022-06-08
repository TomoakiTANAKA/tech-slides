---
marp: false
theme: default
header: "**勉強会資料** __数千万PVをさばくためのインフラ構成について__"
footer: "by [TomoakiTANAKA](tanaka1987＠gmail.com)"
paginate: true
---

<style>
img[alt~="center"] {
  display: block;
  margin: 0 auto;
}

</style>

# 数千万PVをさばくためのインフラ構成について

## 2022/06/08 TomoakiTANAKA

---

# 自己紹介

---

# 田中智章

![bg right](assets/images/tomioka_silk.png)

- 富岡市出身
- SE としてキャリアを始め、現在は事業会社勤務
- 主に Web開発 を担当、時々アプリ開発（Rails / Flutter / Unity ← New）
- 好きなお酒はウィスキー全般、あと LEGO が好き

---

# 宣伝

---

# 会社/プロダクト紹介

---

# 株式会社エバーセンス

![bg right](./assets/images/company/eversense_logo.jpg)

## Vision

- 家族を幸せにすることで、笑顔溢れる社会をつくる。

## Products

- 妊婦さんへの情報提供をする「ninaru」
- 子育てに必要な情報や機能満載の育児アプリ「ninaru baby」

---

# 株式会社DanRan（だんらん）

![bg right](./assets/images/company/danran.png)

- エバーセンスとポプラ社のJVとして、2022年4月にできたばかりの会社
- 児童向けの知育アプリの開発等を行っています

## Products

- タッチであそぼ！あかまるどれかな？
- iOS / Android 版があるので小さなお子様やお孫さんがいらっしゃる方は是非

![タッチであそぼ！あかまるどれかな？](./assets/images/company/akamaru.png)

---

# アイスブレイク

- Q1. PVってなんのことでしょうか？（なんの略でしょうか）
- Q2. Webメディアってなんでしょうか？
- Q3. 月間PV 4,500万（@todo: 今いくつだ？）ってすごいのでしょうか？

是非スレッドに回答をお願いします

---

# アイスブレイク シンキングタイム

---

# アイスブレイク

- A1. PVはPageView（ページビュー）の略で、Webページの閲覧回数のこと
- A2. www（WorldWideWeb）上で、記事をまとめた読めるWebサイト（Webサービス）のこと
  - 例）Qiita, zenn
  - 例）マイナビニュース, ロケットニュース24
- A3. 月間PV 4,500万（@todo: 今いくつだ？）ってすごいのでしょうか？
  - 月間平均で 1,000pv/分 なので、結構すごいと思います
  - GoogleAnalyticsのリアルタイムユーザーでいうと、同時接続 3,000人 くらいです（でした）
  - もちろん上を見たらきりがないですが…
    - 例）Yahoo!ニュース 225億PV/月
    - 例）zozoタウン 5億PV/月

出典：
- [1つの記事で世の中が大きく変わる――1日の記事数約6000本、月間225億PVを数える「Yahoo!ニュース」のこれまでとこれから](https://about.yahoo.co.jp/hr/linotice/20200825.html)
- [通販新聞 【ZOZOテクノロジーズの久保田社長に聞く　「ウェア」の現状と成長戦略は?㊤】　中国でも「ウェア」が武器に、お手本コーデで差別化図る](https://www.tsuhanshimbun.com/products/article_detail.php?product_id=4763&_ssd=1#:~:text=%E3%82%BE%E3%82%BE%E5%AD%90%E4%BC%9A%E7%A4%BE%E3%81%AEZOZO%E3%83%86%E3%82%AF%E3%83%8E%E3%83%AD%E3%82%B8%E3%83%BC%E3%82%BA,%E3%81%AB%E6%8B%A1%E5%A4%A7%E3%81%97%E3%81%A6%E3%81%84%E3%82%8B%E3%80%82)

---

# 本日の勉強会について

---

# 本日の勉強会について

中規模「Webメディア」を効率的に運営する方法に関して、考えや知見をお話します。

あくまで田中個人の知見や経験によるものですので、取り扱いにはご注意ください。
掲載内容は私自身の見解であり、必ずしも所属する企業や組織の立場、戦略、意見を代表するものではありません。

---

## 話すこと

- 数百万〜数千万PVのWebメディアを、できるだけ安いインフラ構成でさばくための考え方
- 下記のシステム構成を前提にした話をします
  - サーバーサイドレンダリングを前提としたシステムにおける
  - AWSを前提としてインフラ構成（多分GCPやAzuleでも通用する考え）

## 話さないこと

- 数百万〜数千万PVのECサイト等、WebメディアではないWebサービスの大規模トラフィックをさばく技術
  - 例）EC, 不動産サイト, etc
- オンプレを前提としてインフラ環境における構成
- フロントエンドレンダリング全般
- 具体的なプライシングやインスタンス構成

---

## この話を聞いて得られそうなこと

- Webサービスの運用を見据えたインフラ構成やテクニックの理解
- テレビCMやインフルエンサーバズによる急なアクセス増加に対する心の安寧（個人差あり）


---

# 目次

---

# 目次

1. Webメディアの特徴と構成技術の分解
2. キャッシュを使ってアクセスをさばく（事例紹介）
3. まとめ

※ 組織の意見でなく、個人の意見です

---

# Webメディアの特徴

---

# Webメディアの特徴

- 誰が見てもほぼ同じ構成
  - 記事はだれが見てもほぼ同じ
  - 広告やレコメンドはユーザーによって異なる
- 技術的に分解すると…
  - 記事はだれが見てもほぼ同じ（HTML / CSS 部分）
  - 広告やレコメンドはユーザーによって異なる（JavaScript部分）
  - 読者にとっては、HTTPのGET中心の世界（データを取得するのが主）

--- 

# Webメディアの特徴（もう少し技術的に）

![](./assets/images/infra/default_1.png)

※ サーバーサイドレンダリングを前提にしています


- ①：ブラウザからWebサーバーへリクエストがはいる
- ②：サーバーは必要に応じて、DBへアクセス。データを取り出す
- ③：略
- ④：サーバーは、テンプレートエンジン等を使ってHTMLを作成、ブラウザに返す
- ⑤：ブラウザはHTMLを解釈、必要に応じてcssやJavaScriptをCDN問い合わせ
- ⑥：略
- ⑦：ブラウザが描画

---

# どこがボトルネックになるか？

ボトルネック
- お値段：（高い）DB（例：RDS）> Webサーバー（例：EC2）（安い）
- お値段：（高い）メモリ > HDD（安い）
- 転送速度：（高速）メモリ > HDD > ネットワーク（低速）

![](./assets/images/infra/speed.png)

出典
- [プログラマが知っておくべき、メモリ/ディスク/ネットワークの速度まとめ](https://qiita.com/awakia/items/c8ada6c8101efe2de561)


---

# ボトルネック解消をどのように考えるか？

- できる限りWebサーバー側でリクエストをさばく
- できる限りWebサーバーも、メモリやHDDを減らしたい（安くなるから）

```
ボトルネック
- お値段：DB（例：RDS）> Webサーバー（例：EC2）
- お値段：メモリ > HDD
- 転送速度：メモリ > HDD > ネットワーク
```

---

# ボトルネック解消をどのように考えるか？

![](./assets/images/infra/solution.png)

---

# キャッシュを使ってアクセスをさばく（事例紹介）

---

# キャッシュとは？

```
キャッシュは、CPUのバスやネットワークなど様々な情報伝達経路において、ある領域から他の領域へ情報を転送する際、その転送遅延を極力隠蔽し転送効率を向上するために考案された記憶階層の実現手段である。実装するシステムに応じてハードウェア・ソフトウェア双方の形態がある。
```

↓↓↓

- よく使うデータやファイルを、アクセスしやすいところにおいておく
  - 例）アクセスしやすい = 高速に取り出せる場所（メモリやローカルファイル）
- 逆にあまり使わないファイルは、必要なときに取り出す
  - 例）必要なときにネットワーク経由で取り寄せる

出典：
- [キャッシュ](https://ja.wikipedia.org/wiki/%E3%82%AD%E3%83%A3%E3%83%83%E3%82%B7%E3%83%A5_(%E3%82%B3%E3%83%B3%E3%83%94%E3%83%A5%E3%83%BC%E3%82%BF%E3%82%B7%E3%82%B9%E3%83%86%E3%83%A0))


---

# Webメディアによるキャッシュ戦略

- よく使うファイル
 - クエリなしのURLに対するHTMLファイル
 - CSSやJavaScriptなど、共通でアクセスするファイル
- あまり使わないファイル
  - クエリパラメータ付きのURL（検索やページネーション）
  - GET以外のリクエスト

---

# Webメディアによるキャッシュ戦略

 - クエリなしのURLに対するHTMLファイル 
  - => WebサーバーでHTMLファイルを事前作成（ファイルキャッシュ）
- CSSやJavaScriptなど、共通でアクセスするファイル
  - => CDNで高速アクセスできるようにしておく（S3 + CloudFront）

---

# ファイルキャッシュの運用事例

---

# 運用例 / 設定例（キャッシュの作成、削除）
## コーポレートサイトのようなアクセスが少ないサイトの場合

- キャッシュの作成
  - Webサーバーのデフォルト機能を使う（アクセスがあったらキャッシュを作成する）
- キャッシュの削除
  - 記事公開 / サイト全体の更新時（css変更など）
  - まとめてファイルを消す

まとめ
- 例えばWordPressには、まとめてファイルキャッシュを削除してくれるようなプラグインがあるのでそれを利用する

---

# 運用例 / 設定例（キャッシュの作成、削除）
## Webメディアのようにアクセスが多いサイトの場合

- キャッシュの作成
  - Webサーバーのデフォルト機能を使う（アクセスがあったらキャッシュを作成する）
- キャッシュの削除
  - 記事公開
    - => 記事のURL、トップページ、記事のカテゴリやタグページなど、関連ページを個別に削除
  - サイト全体の更新時
    - => URL一覧を取得して、数十〜数百ページ単位でキャッシュを作成

まとめ
- 常時アクセスが多いサイトだと、一度にファイル削除はキャッシュが薄くなるので危険
- キャッシュ生成や削除の部分は、基本手作り

---

# 運用例 / 設定例（キャッシュさせる / させない）

- とはいえ全部のファイルをキャッシュさせてしまうと運用上困る
  - 例）編集者が記事を更新したのに最新版が反映されない（確認できない）
  - 例）sitemapなど、リアルタイムでGoogleに伝えたいものが、古いままだと困る

@nginx/configure/cache
```bash
set $do_not_cache 0;

# WordPressの管理画面にログインすると付与されるcookieを持っていたらキャッシュなしにする
if ($http_cookie ~* "wordpress_(?!test_cookie)|wp-postpass_" ) {
    set $do_not_cache 1;
}

# GET以外はキャッシュなしにする
if ($request_method != GET) {
    set $do_not_cache 1;
}

# sitemapなど特定のURLはキャッシュなしにする
if ($request_uri ~* "(sitemap.xml") {
    set $do_not_cache 1;
}
```

---

# CDNの話

---


---

# まとめ


```ruby
    it 'youtube動画へのリンク形式が不正なときに無効な状態であること' do

      # 入力の定義
      invalid_urls = %w[https://cojicaji.jp
                        https://youtube.com
                        https://www.youtube.com/@hoge
                        https://www.youtube.com/user/こじかじ
                        https://www.youtube.com/channer/こじかじ]

      # すべての入力に対して処理を行う
      invalid_urls.each do |invalid_url|
        user_with_sns.youtube = invalid_url
        user_with_sns.valid?

        # 入力と出力の組み合わせを検証
        expect(user_with_sns.errors[:youtube]).to include('リンクの形式が誤っています')
      end
    end
```


---

# いつ / どれだけユニットテストを作成し実行するか？

---

## いつ、どうやって
- プルリクエスト作成時？リリース時？
- 手動？自動？

## どこまで
- カバレッジは？C0、C1?
- 全メソッド？一部のメソッド？
- ロジックのみ？UI部分も？


---

# ユニットテストする、しない
# シンプルな問だが考慮する変数は多い

---

# スピーカの意見

## いつ、どうやって
- プルリクエスト作成時にテストを用意しておき実行（手動）
- mainブランチへのマージ時にも実行（自動）

## どこまで
- カバレッジは気にしない。後述する複雑系のみ対応
- 基本ロジックのみでUIテストはしない
- コード解析やリファクタリング目的で作成することも

---

# いつ、どうやって、どこまで、に関する事例紹介

---

# 事例紹介

---

# 事例紹介（いつ、どうやって）
## 手動実行に関して
- ローカル環境を用意し「コマンド実行」できるようにしておく
- テストの実行方法を記載しておく（README、テストコード）
- テスト忘れが発生しないようチェックリスト（PULL_REQUEST_TEMPLATE.mdを利用）

## 自動実行に関して
- 簡易導入としてgit hooksを利用。push時にテスト
- 最近はGithub Actionsがメイン。無料枠が強力。
  - iOSアプリのビルドやストアへのアップロードにも利用している（余談）

---

例）手順を記載しておく

```ruby
# ↓↓↓ テストの実行方法を記述しておく
# run test command
# docker compose exec rails bin/rspec spec/models/user_spec.rb

require 'rails_helper'

RSpec.describe User, type: :model do
  # ...ユニットテストの中身...
end
```

---

例）Github Actions

```yaml
name: dart-analyzer

on:
  # masterやfeatureブランチにpushした時
  push:
    branches: [ 'master', 'feature/*' ]
    paths: ['**.dart', 'pubspec.*', 'analysis_options.yaml', '.github/workflows/dart-analyzer.yaml']

jobs:
  # テストジョブを走らせる（コード解析もセット）
  test:
    runs-on: ubuntu-latest
    timeout-minutes: 5
    steps:
    - uses: actions/checkout@v2
    - uses: subosito/flutter-action@v1
      with:
        flutter-version: 2.2.0
        channel: 'stable'
    - name: run-analyze
      run: |
        flutter pub get
        flutter test
        flutter analyze --fatal-warnings --no-fatal-infos
```

---

# 事例紹介（どこまで）
## カバレッジ100%目標にしない
- 計算が直感的でない（例：日付処理）や、入力パターンが多く手動でのテストがめんどくさいものを優先的に行う
- 品質が担保できれば良いので、ほぼ自明なものをわざわざテストしない

## ロジックに集中
- ロジックのテストに集中しUIのユニットテストはしない<br>（UIは見たほうが早いことも多く、テスト作成や維持するコストが重いので）
- ロジックに集中できるように、レイヤードアーキテクチャを意識。できる限りプレーンな言語だけでロジックを記載

---

例）日付計算（パターンは多岐に渡り、エッジケースも複雑なのでテスト化する）

```dart
// ロジック
int numberOfDayOfTheWeekInMonth() {
  // ある日付が7の周期で何回目の登場かをを計算
  final nth =  ((_value.day - 1) / 7).floor() + 1;
  return nth;
}
```
```dart
// test
void main() {
  group('ある月における何回目の曜日か', () {
      test('テストデータが第1水曜日であること', () {
        final result = calendarDate.numberOfDayOfTheWeekInMonth();
        expect(result, 1);
        expect(calendarDate.dayOfWeek, DayOfWeek.Wednesday);
      });
}
```

---

例）アプリのレビュー発火ロジック（起動100回を実施するのはめんどくさい。起動回数さえ正しければ〜という考えでロジックをテスト）

```dart
class ReviewLogic {
  bool shoudFired() {
    // 直接データを引っ張ってくると結合度が高くテストしにく
    final getLaunchTime = getgetLaunchTimeFromDataBase();
    return if getLaunchTime >= 100;
  }
}
↓↓↓
↓↓↓
class ReviewLogic {
  // リポジトリなどレイヤーをかませるとテストしやすい
  final _repository = UserPropertyRepository();
  bool shoudFired() {
    final getLaunchTime = _repository.getgetLaunchTime();
    return if getLaunchTime >= 100;
  }
}
```

---

# まとめ

---

# まとめ①（ユニットテストの考え方）
- 目的（品質担保）のするために、費用対効果の高い方法を取れば良い
  - 複雑な処理のエッジケースの確認
  - テスト自体がドキュメントになるので開発が持続するなら書く
  - など

---

# まとめ②（ユニットテストする / しない）

## する
- 入出力の組み合わせが多い場合
- コード解析やリファクタリングするときの前準備

## しない
- 目視で十分な部分なところ
- 入力に対する出力がシンプルな場合（例：ユーザー名に「さん」をつける、だけ）
- 行数が大きいメソッド（テストの前に、メソッドを分解）

---

# ご清聴ありがとうございました

---

# 付録

---

# 参考文献
- 影響を受けたり、役に立ったと思う書籍や資格
  - [テスト駆動開発](https://www.amazon.co.jp/dp/B077D2L69C)
    - ご存知t_wadaさんの翻訳本（KnetBeckの書籍としてもかなり有名）
  - [RSpecによるRailsテスト入門](https://leanpub.com/everydayrailsrspec-jp)
  - [JSTQB（ソフトウェアテストの資格）](http://jstqb.jp/)
    - ソフトウェア開発していればおおよそ経験する内容を網羅的にまとめている
    - 1〜3年目くらいにスキルの定着具合や抜け漏れないか確認する意味で

---

# 質疑応答

---

# おわり
