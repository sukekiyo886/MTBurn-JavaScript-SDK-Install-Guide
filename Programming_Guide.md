# 目次

- [In-Feed 広告](#infeed)
  - [Getting Started](#infeed/simple)
    - [広告枠の登録](#infeed/simple/adspot)
    - [広告テンプレートの入稿](#infeed/simple/template)
    - [広告タグの発行](#infeed/simple/tag)
  - [タグ側でのテンプレート実装](#infeed/start)
    - [広告枠の登録と設定](#infeed/start/adspot)
    - [テンプレートの記述](#infeed/start/template)
    - [広告挿入位置の指定](#infeed/start/position)
    - [広告のロード](#infeed/start/load)
  - [広告の追加ロード](#infeed/additional_load)
  - [広告タイトル・説明文の短縮](#infeed/title_desc_length)
  - [広告表示の高速化](#infeed/immediately_option)
  - [カスタム実装](#infeed/custom)
    - [インスタンスの作成](#infeed/custom/instance)
    - [広告リクエストの送信](#infeed/custom/load)
    - [広告案件情報の取り出し](#infeed/custom/getads)
    - [広告の表示](#infeed/custom/display)
    - [インプレッションの送信](#infeed/custom/imp)
    - [追加ロード](#infeed/custom/additional_load)
    - [ここまでの流れを踏まえた実装例](#infeed/custom/example)
    - [注意事項](#infeed/custom/caution)
  - [広告パラメータ](#infeed/parameter)
- [DFP連携](#dfp)
  - [非同期タグの場合](#dfp/async_tag)
  - [同期タグの場合](#dfp/sync_tag)
- [名前空間](#namespace)
- [よくある質問](#qa)
    - [コード中にあるInstreamはどういう意味ですか](#qa/instream)

<a name="infeed"></a>
# In-Feed 広告

<a name="infeed/simple"></a>
## Getting Started

<a name="infeed/simple/adspot"></a>
### 広告枠の登録

管理 UI から広告枠を登録します。事前に管理 UI へのアカウント開設が必要です。

<a name="infeed/simple/template"></a>
### 広告テンプレートの入稿

管理 UI から広告テンプレートを入稿します。以下のように `{{click_url}}` という記法でプレースホルダーを指定すると、その部分が広告素材に置き換えられ、広告として表示されます。

[使用できるプレースホルダーはこちらを参照してください。](#infeed/parameter)

```html
<div class="article">
  <div class="icon">
    <a href="{{click_url}}">
      <img src="{{main_image_url}}" />
    </a>
  </div>

  <div class="contents">
    <h3>{{title}}</h3>
    <p>【PR】{{description}}</p>
    <span class="source">Sponsored</span>
  </div>
</div>
```

<a name="infeed/simple/tag"></a>
### 広告タグの発行

管理 UI からタグを発行します。以下の様なタグが発行されるので、ページ内の広告を表示させたい位置に挿入します。

```html
<script type="text/javascript" src="http://js.mtburn.com/advs-instream.js"></script>
<div data-advs-adspot-id="広告枠ID" style="display:none">
<script type="text/javascript">MTBADVS.InStream.Default.run()</script>
</div>
```

<a name="infeed/start"></a>
## タグ側でのテンプレート実装

広告テンプレートをタグに記載することで、管理 UI に入稿せずに利用いただけます。実装がやや複雑になりますが、より細かくカスタマイズした実装が可能です。

- `head` タグに広告ユニットのテンプレートを記載します。クリエイティブ素材の URL や広告テキストなどの挿入位置をプレイスホルダで指定します。
In-Feed-Ads導入に際して、広告枠と隣接した位置に
枠が広告であることがユーザーにとって明らかに分かる文言を追加していただく必要があります。
（通常は「PR」もしくは「Sponsored」）

```html
<script type="text/advs-instream-template" data-adspot-id="MjQzOjIw">
<div class="article">
  <div class="icon">
    <a href="{{click_url}}">
      <img src="{{main_image_url}}" />
    </a>
  </div>

  <div class="contents">
    <h3>{{title}}</h3>
    <p>【PR】{{description}}</p>
    <span class="source">Sponsored</span>
  </div>
</div>
</script>
```

- 広告表示位置に適当な空要素を作成します。`data-advs-adspot-id` 要素に事前に払い出された ID を指定します。この要素は広告の DOM に置き換えられます。

```html
<div data-advs-adspot-id="MjQzOjIw" style="display:none"></div>
```

- 広告用スクリプトの呼び出しを行います。

```html
<script src="http://js.mtburn.com/advs-instream.js"></script>
<script>
MTBADVS.InStream.Default.run();
</script>
```

<a name="infeed/start/adspot"></a>
### 広告枠の登録と設定

事前に広告枠の登録を行います。以下の情報を設定し、広告枠 ID が払い出されます。

- 広告枠名
- 広告画像サイズ
- 広告案件数
- 掲載サイト URL (アプリの場合はストア URL)

広告枠は表示されるフォーマットごとにご登録ください。

<a name="infeed/start/template"></a>
### テンプレートの記述

テンプレートに広告ユニットの DOM 構造を記述します。広告データの挿入位置をプレイスホルダで指定します。

プレイスホルダは `{{parameter_name}}` という記法で記述します。

[利用できるパラメータはこちらを参照ください。](#infeed/parameter)

<a name="infeed/start/position"></a>
### 広告挿入位置の指定

広告挿入位置に空要素を作成してください。
`data-advs-adspot-id` 属性に払い出された広告枠 ID の指定が必須です。
広告がロードされると、この要素が広告の内容に置き換えられます。

要素には `display:none` スタイルを指定し、広告のロード失敗時などの際でもサイト表示に影響を与えないようにすることを推奨します。


```html
<div data-advs-adspot-id="MjQzOjIw" style="display:none"></div>
```

<a name="infeed/start/load"></a>
### 広告のロード

SDK の JavaScript コードのロードと処理の呼び出しを行います。画面のロード完了後に広告案件取得と表示を行います。

```html
<script src="http://js.mtburn.com/advs-instream.js"></script>
<script>
MTBADVS.InStream.Default.run();
</script>
```

<a name="infeed/additional_load"></a>
## 広告の追加ロード

フィード型のサイトなどでユーザーがサイト下部に到達した際に追加フィードを読み込むような UI の場合に、追加で広告ロードを行うことも可能です。

- フィードの追加読み込み時に挿入される DOM 上に、初期状態と同様の広告位置指定要素を含めていただきます。

```html
<div data-advs-adspot-id="MjQzOjIw" style="display:none"></div>
```

- フィード追加読み込み後に SDK の `MTBADVS.InStream.Default.reloadAds()` メソッドを呼び出します。呼び出し後に広告案件の追加取得と表示を行います。

```javascript
// 追加フィードの読み込み後に呼び出される関数の例
function onAdditionalFeedLoaded() {
    // ...

    // SDK の reloadAds メソッドを呼び出し
    MTBADVS.InStream.Default.reloadAds();
}
```

<a name="infeed/title_desc_length"></a>
## 広告タイトル・説明文の短縮

広告呼び出し時にオプションを追加することで、広告のタイトルや説明文のテキストを媒体様のサイトに合わせて短縮させることができます。

以下は説明文の長さを最大 30 文字にする例です。

```javascript
MTBADVS.InStream.Default.run({
    description_length: 30
});
```

説明文が30文字に収まるように、文の後半が切り取られます。また文末には三点リーダー `…` が自動的に付け加えられます。三点リーダーを含めた全体の長さが30文字になるよう調整されます。

指定できるオプションは以下です。

オプション名 | 説明 | 設定例 | 動作結果の例
--- | --- | --- | ---
title_length | タイトルの最大長を指定します | `5` | `テストタイトル` -> `テストタ…`
description_length | 説明文の最大長を指定します | `10` | `これはテスト説明文です` -> `これはテスト説明文…`

### コールバック関数による編集

またコールバック関数を渡すことで、媒体様の任意の方法で文字長の調整が可能です。コールバック関数は広告表示の直前に呼び出されます。引数として案件情報と設置箇所が渡されます。よって案件情報を任意に編集する処理を記述できます。また設置箇所に応じて、案件の文字数などを変動させることも可能です。コールバック関数は `before_render` オプションに渡します。

以下は、タイトルの先頭に `[PR]` という文字列を入れる。かつ、説明文を広告枠Aでは30文字、広告枠Bでは40文字に短縮し末尾に三点リーダーを付け加える例です。

```javascript
MTBADVS.InStream.Default.run({
    before_render: function(ad_info, placement_info) {
        // タイトルの先頭に `[PR]` を挿入
        ad_info.title = '[PR] ' + ad_info.title;

        // 広告枠ごとに説明文の最大長を設定する
        var desc_max_len = 30;
        if (placement_info.adspot_id === 'ADSPOT_A') {
            desc_max_len = 30;
        } else if (placement_info.adspot_id === 'ADSPOT_B') {
            desc_max_len = 40;
        }

        // 説明文がdesc_max_len文字数におさまるよう切り取り、末尾に三点リーダーを加える
        if (ad_info.description.length > desc_max_len) {
            ad_info.description = ad_info.description.substr(0, desc_max_len - 1) + '…';
        }

        // 編集結果を return する
        return ad_info;
    }
});
```

- `before_render` の第一引数には広告案件のオブジェクトが渡されます。オブジェクトが持つプロパティは [広告パラメータ](#infeed/parameter) を参照してください
- `before_render` の第二引数には設置位置情報のオブジェクトが渡されます。オブジェクトが持つプロパティは `adspot_id` (広告枠ID) です。
- コールバック関数は編集結果の案件情報オブジェクトを必ず `return` で返す必要があります。

<a name="infeed/immediately_option"></a>
## 広告表示の高速化

`immediately` オプションを追加することで、広告が表示されるまでの速度を速めることができます。

以下は高速化のオプションを有効にする例です。

```javascript
MTBADVS.InStream.Default.run({
    immediately: true
});
```

このオプションはデフォルトでは無効になっています。

`immediately` オプションが有効の場合、`MTBADVS.InStream.Default.run()` の呼び出し時に、即座に広告の読み込みと表示処理が開始されます。そのため、正しい順序で広告タグを貼る必要があります (広告テンプレートと広告挿入位置のタグの後に、広告呼び出しタグを設置する必要があります)。また M.T.Burn の広告が表示された後に動作する、媒体社様や外部の JavaScript の動作によって、表示が崩れる可能性があります。

`immediately` オプションが無効の場合、`MTBADVS.InStream.Default.run()` の呼び出し後、ページの読み込みが完了した後に、広告の読み込みと表示処理が行われます。そのため表示の崩れは起きにくくなりますが、オプション有効時に比べて、広告が表示されるまでの時間が長くなります。

<a name="infeed/custom"></a>
## カスタム実装

SDK の raw API を使用し、広告の呼び出しやレンダリングなどを任意のタイミングで行うことも可能です。以下に説明する API を用いて、媒体様による追加の実装が必要になります。

<a name="infeed/custom/instance"></a>
### インスタンスの作成

広告を管理するコントローラークラスのインスタンスを作成します。コンストラクタには広告枠 ID の指定が必須です。

```javascript
var ad_controller = new MTBADVS.InStream.AdController({ adspot_id: 'MjQzOjIw' });
```

<a name="infeed/custom/load"></a>
### 広告リクエストの送信

`loadAds()` メソッドで広告案件のリクエストを発行し、案件情報取得します。引数として完了後に呼び出されるコールバック関数を指定します。

コールバック関数の第一引数にはエラーオブジェクトが返されます。成功時には null となります。

```javascript
var on_ad_loaded = function(error) {
    if (error) {
        // エラー処理
    }

    // ...
};

ad_controller.loadAds(on_ad_loaded);
```

<a name="infeed/custom/getads"></a>
### 広告案件情報の取り出し

`loadAds()` でサーバより取得した案件情報は `getLoadedAds()` メソッドで取り出すことができます。

```javascript
var ads = ad_controller.getLoadedAds();
```

`getLoadedAds()` の返り値は次のような配列です。

```javascript
[
    {
        "title": "testAd",
        "description": "テスト広告です。",
        "click_url": "http://...",
        "main_image_url": "http://...",
        "icon_image_url": "http://...",
        "ad_id": 123,
    },
    ...
]
```

各パラメータの詳細は [広告パラメータの節](#infeed/parameter) を参照してください。

<a name="infeed/custom/display"></a>
### 広告の表示

`getLoadedAds()` で取り出した案件情報をもとに、広告を表示させます。表示処理は媒体様で実装していただく必要があります。

<a name="infeed/custom/imp"></a>
### インプレッションの送信

広告の表示後、必ず `notifyImp()` でインプレッションの通知をする必要があります。引数として表示した `ad_id` を渡します。

```javascript
var ad_id = ads[0].ad_id;
ad_controller.notifyImp(ad_id);
```

<a name="infeed/custom/additional_load"></a>
### 追加ロード

フィードをさらに読み込んだ場合など、追加で広告をロードする場合について。

`loadAd()` メソッドを呼び出すごとに、あたらしい広告案件を取得することができます。`getLoadedAds()` は表示済の案件も含め、これまでに取得した案件をすべて返します。

始めにインスタンス化したコントローラーのインスタンスを使いまわす必要があります。 ひとつのインスタンスを使い続けると、重複した案件が返されることはありません。

<a name="infeed/custom/example"></a>
### ここまでの流れを踏まえた実装例

```javascript
var ad_controller = new MTBADVS.InStream.AdController({ adspot_id: 'MjQzOjIw' });

var on_ad_loaded = function(error) {
    if (error) {
        // エラー処理
    }

    // 案件の取得
    var ads = ad_controller.getLoadedAds();

    ads.forEach(function(ad) {
        // 広告案件情報をもとに広告を表示 (媒体様実装)
        showAd(ad);

        // インプレッションの通知
        var ad_id = ad.ad_id;
        ad_controller.notifyImp(ad_id);
    });
};

ad_controller.loadAds(on_ad_loaded);
```

<a name="infeed/custom/caution"></a>
### 注意事項

- サーバから広告情報を取得はページの読み込みごとに行ってください。事前に取得したものをキャッシュし使いまわすことはご遠慮ください。
- サーバから取得できる広告案件は、より効果が高いと期待される順に並んでいます。上位の案件は画面上の上位に表示することを推奨します。

<a name="infeed/parameter"></a>
## 広告パラメータ

| パラメータ名 | 説明 | 例 |
| --- | --- | --- |
| title | タイトル文言 | `TestAd` |
| description | 説明・紹介文 | `テスト広告です。` |
| click_url | 広告クリック時の遷移先 URL | `http://click.mtburn.com/...` |
| icon_image_url | アイコン型の正方形画像の URL | `http://banner.dspcdn.com/...` |
| main_image_url | バナー型の矩形画像の URL | `http://banner.dspcdn.com/...` |
| ad_id | 広告案件の ID | `123` |

<a name="dfp"></a>
# DFP連携

DFP を利用した配信も可能です。Google サイト運営者タグの非同期タグと同期タグでそれぞれ実装方法が異なります。媒体様で実装されているタグの種類に応じて対応ください。

<a name="dfp/async_tag"></a>
## 非同期タグの場合

- 対象のオーダーにクリエイティブを追加します。クリエイティブのタイプは `サードパーティ` を選択します。
- クリエイティブとして M.T.Burn のタグを入稿します。
  - 通常の配信面同様に、広告ユニットのテンプレート、広告表示位置指定の空要素、広告用スクリプトの呼び出しのタグを入稿します
  - 加えて、表示のために必要なスタイルシート呼び出しなどを適宜入れて頂く必要があります。
  - DFP のクリックマクロを挿入します。正しく挿入することで、正確な画面遷移とクリック数計測ができます。

```html
<!-- css の読み込み等。媒体様によるカスタマイズが必要です -->
<link rel="stylesheet" type="text/css" href="http://yourodmain/sampale.css">

<!-- 広告ユニットのテンプレート -->
<script type="text/advs-instream-template" data-adspot-id="NzYzOjEyMg">
<div class="article">
  <div class="icon">
    <!-- [重要] dfp のクリックマクロを挿入し、直後に M.T.Burn のクリック URL をつけてください。 -->
    <a href="%%CLICK_URL_UNESC%%{{click_url}}" target="_blank">
      <img src="{{icon_image_url}}" />
    </a>
  </div>

  <div class="contents">
    <h3>{{title}}</h3>
    <p>{{description}}</p>
    <span class="source">Sponsored</span>
  </div>
</div>
</script>

<!-- 広告表示位置の指定 -->
<div data-advs-adspot-id="NzYzOjEyMg" style="display:none"></div>

<!-- 広告用スクリプトの呼び出し -->
<script src="https://js.mtburn.com/advs-instream.js"></script>
<script>
MTBADVS.InStream.Default.run({
    before_render:function(ad_info, placement_info) {
        // [重要] dfp マクロとの連携のため、必ずこの行の実装をお願い致します。
        ad_info.click_url = encodeURIComponent(ad_info.click_url);

        // 説明文の切り上げなどを媒体様に必要に応じて実装していただきます。
        // 広告枠の大きさが固定の場合、長い文字がはみ出す可能性があるため、切り上げ処理の実装を推奨します。
        var desc_max_len = 30;
        if (ad_info.description.length > desc_max_len) {
            ad_info.description = ad_info.description.substr(0, desc_max_len - 1) + '…';
        }

        return ad_info;
    }
});
</script>
```

iframe 内での広告表示となるため、通常のタグに加えてスタイルシートなどの読み込みが必要になります。

<a name="dfp/sync_tag"></a>
## 同期タグの場合

- 対象のオーダーにクリエイティブを追加します。クリエイティブのタイプは `サードパーティ` を選択します。
- クリエイティブとして M.T.Burn のタグを入稿します。
  - 通常の配信面同様に、広告ユニットのテンプレート、広告表示位置指定の空要素、広告用スクリプトの呼び出しのタグを入稿します
  - DFP のクリックマクロを挿入します。正しく挿入することで、正確な画面遷移とクリック数計測ができます。

```html
<!-- 広告ユニットのテンプレート -->
<script type="text/advs-instream-template" data-adspot-id="NzYzOjEyMg">
<div class="article">
  <div class="icon">
    <!-- [重要] dfp のクリックマクロを挿入し、直後に M.T.Burn のクリック URL をつけてください。 -->
    <a href="%%CLICK_URL_UNESC%%{{click_url}}" target="_blank">
      <img src="{{icon_image_url}}" />
    </a>
  </div>

  <div class="contents">
    <h3>{{title}}</h3>
    <p>{{description}}</p>
    <span class="source">Sponsored</span>
  </div>
</div>
</script>

<!-- 広告表示位置の指定 -->
<div data-advs-adspot-id="NzYzOjEyMg" style="display:none"></div>

<!-- 広告用スクリプトの呼び出し -->
<script src="https://js.mtburn.com/advs-instream.js"></script>
<script>
MTBADVS.InStream.Default.run({
    before_render:function(ad_info, placement_info) {
        // [重要] dfp マクロとの連携のため、必ずこの行の実装をお願い致します。
        ad_info.click_url = encodeURIComponent(ad_info.click_url);

        // 説明文の切り上げなどを媒体様に必要に応じて実装していただきます。
        // 広告枠の大きさが固定の場合、長い文字がはみ出す可能性があるため、切り上げ処理の実装を推奨します。
        var desc_max_len = 30;
        if (ad_info.description.length > desc_max_len) {
            ad_info.description = ad_info.description.substr(0, desc_max_len - 1) + '…';
        }

        return ad_info;
    }
});
</script>
```

非同期タグの場合に必要なスタイルシートなどの呼び出しが、同期タグの場合は不要です。

<a name="namespace"></a>
# 名前空間

`MTBADVS` 変数を `window` オブジェクト直下に作成します。この他のグローバル汚染は一切おこないません。

<a name="qa"></a>
#よくある質問

<a name="qa/instream"></a>
##コード中にあるInstreamはどういう意味ですか

このガイド中にある、`In-Feed` と同じ意味で用いられています
