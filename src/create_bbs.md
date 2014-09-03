# 簡易 BBS の作成

## 雛形を作る
    $ mojo generate lite_app BBS

## PODRenderer
    # Documentation browser under "/perldoc" # 削除
    plugin 'PODRenderer'; # 削除

- PODRenderer は Mojolicious のプラグインで, perldoc を見れるようにするもの
- 削除する前に、[localhost:3000/perldoc](http://localhost:3000/perldoc) にアクセスしてみよう!

## FORM 作成
    Welcome to the Mojolicious real-time web framework! # 削除

- `index` テンプレートにある, もともとの文字列を削除する

## FORM 作成
    %= form_for '/' => begin
      %= text_field 'body'
      %= submit_button '投稿する'
    % end

- 削除したところに, フォームを出力するコードを書く
  - form\_for, text\_field, submit\_buttonなどは, Mojolicious のhelperという機能で定義されたPerlの関数(サブルーチン)

## FORM 作成
- ここまで出来たら, 保存してから, ブラウザをリロード (あるいは[http://localhost:3000](http://localhost:3000)にアクセス) してみましょう
- フォームから投稿しても画面上は何も変わりません
    - 次に, フォームから投稿された文字列を画面に表示するようにしてみよう

## GET
      %= submit_button '投稿する'
    % end
    <p><%= $entry %></p>

- まずは `index` テンプレートを上記のように変更する
- `<%= $entry %>` は, テンプレート内の変数

## GET
    get '/' => sub {
      my $self = shift;
      my $entry = $self->param('body'); # 追加
      $self->render('index');
    };

- form の情報を取得するために, 上記のように 1 行追加する
- `$self->param('body')` は フォームから投稿した `body` という名前の値を取得する

## GET
    get '/' => sub {
      my $self = shift;
      my $entry = $self->param('body');
      $self->stash(entry => $entry); # 追加
      $self->render('index');
    };

- 取得した情報をテンプレートに渡すため, `$self->stash(entry => $entry)` を挿入する
- `entry` に変数 `$entry` を渡したので, テンプレートで `$entry` が使用可能になる

## GET
- ここまで出来たら, 保存してからブラウザをリロード (あるいは[http://localhost:3000](http://localhost:3000)にアクセス) してみよう
- フォームに文字を入力して, ｢投稿する｣ボタンをクリックしてみよう!

## POST
- 先ほどのフォームは, HTTP でいうところのGETメソッドを利用してデータを送信していました
- GET でのリクエストは文字数の制限(おおよそ 2KB 程度)があるので、掲示板のような多くのデータを送信する必要がある場合は適当ではありません
  - このような場合, POST によるリクエストを行うとよいです

## POST
    @@ post.html.ep # 新しいテンプレートを用意する
    % layout 'default';
    % title '出力'; # タイトルを変更
    %= form_for '/post' => method => 'POST' => begin # 投稿先などを変更
      %= text_field 'body'
      %= submit_button '投稿する'
    % end
    <p><%= $entry %></p>

- `index.html.ep` の部分をコピーして, `post.html.ep` というテンプレートを作成する
- `form_for` に書いた `method => 'POST'` で, get ではなく post で送信するようになる

## POST
    @@ index.html.ep
    % layout 'default';
    % title '入力フォーム'; # タイトルを変更
    %= form_for '/post' => method => 'POST' => begin # 投稿先などを変更
      %= text_field 'body'
      %= submit_button '投稿する'
    % end

- `@@ index.html.ep` では, `$entry` を表示させないようにする
- その他, メソッドやタイトルも変更しておこう

## POST
    get '/' => sub {
      my $self = shift;
      $self->render('index');
    };

    post '/post' => sub {
      my $self = shift;
      my $entry = $self->param('body');
      $self->stash(entry => $entry);
      $self->render('post');
    };

- perl コードも変更しよう
  - 細々とした違いに注意!

## POST
- ここまで出来たら, 保存してから, ブラウザをリロード (あるいは[http://localhost:3000](http://localhost:3000)にアクセス) してみよう
- フォームに長い文字を入力して, ｢投稿する｣ボタンをクリックしてみよう!

## 記事を蓄える
- 記事を蓄えるための方法としては, いくつかの方法がある
    - DB (データベース) を利用
    - 外部ファイルに書き込み保存
- 今回は時間の制約上, データを蓄える方法として, **配列**を用いる
    - 言うまでもなく, 配列は Web アプリケーションが停止した時点で全てのデータが消えるので, 現実的ではない!
    - `MySQL`や`SQLite`などのデータベースを使うのがオススメです

## 記事を蓄える
    @@ index.html.ep
    % layout 'default';
    % title '入力フォーム';
    %= form_for '/post' => method => 'POST' => begin
      %= text_field 'body'
      %= submit_button '投稿する'
    % end
    % for my $entry (@{$entries}) {
        <p><%= $entry %></p>
    % }

- テンプレートに, 投稿済みの記事( `$entries` に格納されている )を表示するよう変更を加える

## 記事を蓄える
    my @entries = (); # 空の配列を宣言
    get '/' => sub {
      my $self = shift;
      $self->stash(entries => \@entries); # 配列のリファレンスをテンプレートに渡す
      $self->render('index');
    };

    post '/post' => sub {
      my $self = shift;
      my $entry = $self->param('body');
      push @entries, $entry; # 配列に格納
      $self->stash(entry => $entry);
      $self->render('post');
    };

## 記事を蓄える
- ここまで出来たら, 保存してから, ブラウザをリロード (あるいは[http://localhost:3000](http://localhost:3000)にアクセス) してみよう
- 文字の投稿をいくつか繰り返した後に, [http://localhost:3000](http://localhost:3000)にアクセスしてみよう!

## リダイレクト
- 別のページへ遷移(移動)するための機能
    - 今回の場合, `/post`で記事を投稿した後に記事を表示するページである`/`に戻るようにする
- Mojolicious ビルトインの `redirect_to` を使用すればよい

## redirect\_to
    post '/post' => sub {
      my $self = shift;
      my $entry = $self->param('body');
      push @entries, $entry;
      $self->stash(entry => $entry);
      $self->redirect_to('/'); # 追加
    };

- `redirect_to` を利用して, `/`へのページ遷移を追加する
  - `post`のテンプレートはもう必要ないので, 削除しても問題ない

## 最終問題

- これまで作成してきた簡易 BBS を, 改造してみましょう!
- 例えば...?
    - 名前/メールアドレスを入力/表示できるようにする...
    - メールアドレスが｢age｣であれば, 記事を`push`ではなく`unshift`する...
    - テンプレートを整理して, 見た目を綺麗にする...
        - Twitter Bootstrapを使ってみる...

## まとめ
- 非常に簡単ではありますが, BBS (のような) Web サービスを開発してみました
    - 時間の制約上, 紹介できなかった部分 (適切な記事の蓄え方など...) は紹介できませんでしたが, Perl を使った Web サービスの開発の基本的な流れは, このようになっています
    - 今日ここまで紹介してきた内容は, 基礎中の基礎です. ｢ Web サービスを作ろう!｣となると, やはりまだまだ挑戦しなければならない｢壁｣はいくつもあります
- その時困ったら, Perl入学式の資料や, スタッフを是非頼って下さい!
