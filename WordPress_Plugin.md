# 目次
- [WordPressプラグイン「Advertising Manager」のインストール](#install)
  - [「Advertising Manager」をインストールする](#install1)
  - [「Advertising Manager」を有効にする](#install2)
- [「Advertising Manager」の設定](#setup)
  - [テンプレートの編集](#setup1)
	- [追加手順](#setup1-1)
  - [「Adveritising Manager」の広告枠設定](#setup2)
	- [広告テンプレートの設定](#setup2-1)
	- [インフィード広告の表示](#setup2-2)


WordPress にプラグインを用いてインフィード広告を導入する方法を説明します。


<a name="install"></a>
# WordPressプラグイン「Advertising Manager」のインストール

<a name="install1"></a>
## 「Advertising Manager」をインストールする

一般的なやり方は下記の3通りになります。

- WordPress管理画面上でプラグイン検索してインストールする方法
  1. WordPress管理画面の「プラグイン＞新規追加」で「advertising-manager」を検索する
  1. Scott Switzer作の「Advertising Manager」を「いますぐインストール」でインストールする
![インストール方法その1](Install_SDK_Guide_Images/install1.png)

- プラグインのZIPファイルを指定してインストールする方法
  1. [WordPressプラグイン「Advertising Manager」](https://wordpress.org/plugins/advertising-manager/)からプラグインのZIPファイルをダウンロードする
  1. WordPress管理画面の「プラグイン＞新規追加」で「プラグインのアップロード」を選択する
  1. プラグインのZIPファイルを指定して「いますぐインストール」でインストールする
![インストール方法その2](Install_SDK_Guide_Images/install2.png)

- サーバに直接アップする方法
  1. [WordPressプラグイン「Advertising Manager」](https://wordpress.org/plugins/advertising-manager/)からプラグインのZIPファイルをダウンロードする
  1. ZIPファイルを WordPressがインストールされているディレクトリの `wp-contents/plugins`ディレクトリにアップする
  1. ZIPファイルを解凍する
![インストール方法その3](Install_SDK_Guide_Images/install3.png)


<a name="install2"></a>
## 「Advertising Manager」を有効にする
- WordPress管理画面の「プラグイン＞インストール済みプラグイン」で「宣伝マネージャ」(Advertising Manager)を「有効」にしてください。
![有効化](Install_SDK_Guide_Images/activate.png)


<a name="setup"></a>
# 「Advertising Manager」の設定

<a name="setup1"></a>
## テンプレートの編集
フィード一覧ページのフィード一覧部分にコードを追加します。

フィード一覧ページのテンプレートは、一般的(WordPressインストール初期状態)では下記の3つになります。

- メインインデックステンプレート (``index.php``)
- 検索結果テンプレート (``search.php``)
- アーカイブテンプレート (``archive.php``)
![テンプレート](Install_SDK_Guide_Images/template1.png)


<a name="setup1-1"></a>
### 追加手順
- WordPress管理画面の「外観＞テーマの編集」で、編集するテンプレート(``index.php``や``search.php``、``archive.php``)を選ぶ
- 下記を参考にコードを追加してください(``advman_ad``で始まる2行)
- 「ファイルを更新」を押してテンプレートの編集を終了してください

**編集前:**
```php
	// Start the Loop.
	while ( have_posts() ) : the_post();

		/*
		 * Include the Post-Format-specific template for the content.
		 * If you want to override this in a child theme, then include a file
		 * called content-___.php (where ___ is the Post Format name) and that will be used instead.
		 */
		get_template_part( 'content', get_post_format() );

	// End the loop.
	endwhile;
```

**編集後:**
```php
	// Start the Loop.
	while ( have_posts() ) : the_post();

		/*
		 * Include the Post-Format-specific template for the content.
		 * If you want to override this in a child theme, then include a file
		 * called content-___.php (where ___ is the Post Format name) and that will be used instead.
		 */
		get_template_part( 'content', get_post_format() );

		advman_ad('ADVS-' . (is_null($advs) ? ($advs = 1) : ++$advs));	// ←この行を追加

	// End the loop.
	endwhile;
	advman_ad('ADVS');													// ←この行を追加
```
![テンプレートの編集](Install_SDK_Guide_Images/template2.png)


<a name="setup2"></a>
## 「Adveritising Manager」の広告枠設定

<a name="setup2-1"></a>
### 広告テンプレートの設定
- 広告枠のテンプレート(HTML)を準備してください。媒体様ごとに異なるテンプレートが必要です。詳しくは [こちら](https://github.com/mtburn/MTBurn-JavaScript-SDK-Install-Guide/blob/master/Programming_Guide.md#user-content-%E3%83%86%E3%83%B3%E3%83%97%E3%83%AC%E3%83%BC%E3%83%88%E3%81%AE%E8%A8%98%E8%BF%B0) を参照ください。

**例:**
```html
<div class="article">
	<div class="icon">
		<a href="{{click_url}}"><img src="{{icon_image_url}}"></a>
	</div>
	<div class="contents">
		<h3>{{title}}</h3>
		<p>{{description}}</p>
		<span class="source">Sponsored</span>
	</div>
</div>
```

テンプレート内で使うことができるパラメータは [こちら](https://github.com/mtburn/MTBurn-JavaScript-SDK-Install-Guide/blob/master/Programming_Guide.md#user-content-%E5%BA%83%E5%91%8A%E3%83%91%E3%83%A9%E3%83%A1%E3%83%BC%E3%82%BF) を参照ください。

- WordPress管理画面の「広告＞新規作成」で、下記のコードを貼り付けて「インポート」してください。

```html
<script type="text/advs-instream-template" data-adspot-id="広告枠ID">
ここに先ほど準備した広告テンプレートを貼り付けてください
</script>
<script src="//js.mtburn.com/advs-instream.js"></script>
<script>MTBADVS.InStream.Default.run();</script>
```
![広告設定1](Install_SDK_Guide_Images/ad_setup1.png)

※ 「**ここに先ほど準備した広告テンプレートを貼り付けてください**」部分に、さきほど準備いただいた広告テンプレートを貼り付けてください。また広告を表示する際の文字数などは、この時点での設定が必要です。詳しくは [こちら](https://github.com/mtburn/MTBurn-JavaScript-SDK-Install-Guide/blob/master/Programming_Guide.md#user-content-%E5%BA%83%E5%91%8A%E3%82%BF%E3%82%A4%E3%83%88%E3%83%AB%E8%AA%AC%E6%98%8E%E6%96%87%E3%81%AE%E7%9F%AD%E7%B8%AE) を参照ください。

※「**広告枠ID**」の部分には、管理画面で発行した広告枠 ID を入力してください。

![広告枠ID](Install_SDK_Guide_Images/adspot_id.png)

- 「HTML広告の設定を編集」画面になります。
  1. ``HTML``を``ADVS``に変更してください
  2. 「アカウント詳細＞Max Ads Per Page」を「1」に変更してください
  3. 「保存」を押して登録を完了してください
![広告設定2](Install_SDK_Guide_Images/ad_setup2.png)


<a name="setup2-2"></a>
### インフィード広告の表示
- WordPress管理画面の「広告＞新規作成」で、下記のコードを貼り付けて「インポート」してください。

```html
<div data-advs-adspot-id="広告枠ID" style="display:none"></div>
```

※ 「**広告枠ID**」の部分には、管理画面で発行した広告枠 ID を入力してください。

![広告設定1](Install_SDK_Guide_Images/infeed1.png)

- 「HTML広告の設定を編集」画面になります。
  1. ``HTML``を``ADVS-<表示位置>``に変更してください。 <表示位置>番目のフィードの次にインフィード広告が表示されます
  2. 「保存」を押して登録を完了してください
![広告設定2](Install_SDK_Guide_Images/infeed2.png)


フィード一覧に、さらにインフィード広告を表示する場合は、[インフィード広告の表示](#インフィード広告の表示)を必要個数分実行してください。


**これで設定は完了です。**

変更したテンプレートの画面で、インフィード広告が表示されるかご確認ください。
