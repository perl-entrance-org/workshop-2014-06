# Mojolicious 入門

## 雛形を作る

    $ mojo generate lite_app hello # cpanm でインストールした方
    $ perl -Ilib script/mojo generate lite_app hello # zipを解凍して準備した方

- Mojolicious を使った Web アプリケーションの簡単な雛形を作成します
    - Mojolicious の場合, `lite_app` 以外にも大規模な Web アプリケーション用の `app` という雛形が用意されています

## Web アプリケーションの起動

    $ morbo ./hello # cpanm でインストールした方
    $ perl -Ilib script/morbo ./hello # zipを解凍して準備した方

- Web ブラウザで `localhost:3000` にアクセスしてみましょう!

## コード解説

    #!/usr/bin/env perl
    use Mojolicious::Lite;

- `Mojolicious::Lite` は `Mojolicious` の Lite 版
    - `Mojolicious::Lite` は 1 つのスクリプトで複数のページを作成
    - `Mojolicious` は 1 ページ = 1 スクリプト
- 総ページ数の少ない(1〜2ページ程度), 簡単な Web アプリケーションであれば Lite で十分

## コード解説

    #!/usr/bin/env perl
    use Mojolicious::Lite;

- `use Mojolicious::Lite;` とすることで, 自動的に `use warnings` と `use strict` が書かれているのと同じ(有効な)状態になる

## コード解説
    get '/' => sub {
      my $self = shift;
      $self->render('index');
    };

- `get '/' => sub { ... };` は, get メソッドで `'/'` というURLにアクセスしたとき, sub 内の処理を行う
- 2 行目は「お約束」のようなもの
- 3 行目 はテンプレート `index` を使ってコンテンツを生成

## コード解説
    app->start;

- アプリケーションを実行します, という意味
- これも「お約束」のようなもの

## コード解説
    __DATA__

    @@ index.html.ep
    % layout 'default';
    % title 'Welcome';
    Welcome to the Mojolicious real-time web framework!

- `ep` は `embedded perl` の略称
- `layout 'default';` は次項で説明するレイアウトファイルの指定
- `title` は `default.html.ep` の `title` 変数, `Welcome to...` は `content` 変数に渡される

## コード解説
    @@ layouts/default.html.ep
    <!DOCTYPE html>
    <html>
      <head><title><%= title %></title></head>
      <body><%= content %></body>
    </html>

- `localhost:3000` のソースと見比べてみましょう
- `<%= xxx %>` がテンプレートの中で使用できる変数に相当する(詳しくは後述)

## 変数を渡す
    get '/' => sub {
      my $self = shift;
      $self->stash(title => 'Hello');
      $self->render('index');
    };
    % title 'Welcome'; # => 削除する

- `$self->stash` で, テンプレート内の変数に変数を渡せる

## 練習問題
    @@ profile.html.ep
    <html>
    <head><title><%= $name %>のプロフィール</title></head>
    <body style='padding: 30px;'>
      私の名前は<%= $name %>です.<br>
      趣味は<%= $hobby %>で, 好きなプログラミング言語は<%= $language %>です.
    </body>
    </html>

- このようなテンプレートを用意し, stash で `name`, `hobby`, `language` 変数に値を代入し, 自己紹介ページを作成しよう
    - `render` で profile テンプレートを指定しましょう

## テンプレートの中の特殊記号

    % if ($num1 == 1 ) { # %
    %= 'hoge';    # %=
        <% if ($num2 == 1 ) { %> # <% ... %>
            <%= $hoge %>         # <%= ... %>
        <% } %>
    % }

- `%`のように, テンプレートの中で特別な意味を持つ記号がある

## テンプレートの中の特殊記号

    % if ($num1 == 1 ) { # %
        ...
    % }

- `%`が先頭にある行は, その後ろのPerlのコードを実行する

## テンプレートの中の特殊記号

    %= 'hoge';    # %=

- `%=`が先頭にある行は, その後ろのPerlのコードを実行して, その結果をテンプレート内に直接埋め込む
    - 返り値を出力するので, `print 'hoge';`と書いてしまうと`1`になる!
    - `print`の実行結果は`1`なので...

## テンプレートの中の特殊記号
    <% if ($num2 == 1 ) { %> # <% ... %>
        <%= $hoge %>         # <%= ... %>
    <% } %>

- `<% ... %>`, `<%= ... %>`は, それぞれ`%`, `%=`をHTMLタグやテキストの中で使えるようにしたもの

## 練習問題
- `fizzbuzz`テンプレートを用意して, 1から100までのfizzbuzzを表示してみましょう
    - 但し, fizzbuzzの処理は全て`fizzbuzz`テンプレートの中に書くようにしましょう
- fizzbuzz
    - 3で割り切れる場合｢Fizz｣
    - 5で割り切れる場合｢Buzz｣
    - 3でも5でも割り切れる場合｢fizzbuzz｣
    - いずれも満たさない場合｢その数をそのまま｣
