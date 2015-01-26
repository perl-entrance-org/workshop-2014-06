# Mojolicious 入門

## 最初に
- この入門では、難しい表現を避けるために、厳密には正しくない事も書いてあります。
- この入門に書いていない沢山の引数やコマンドについては、本家サイトなどのリファレンスをご覧ください。

## 雛形を作る
```
$ mojo generate lite_app hello
```

- Mojoliciousをインストールすると、``mojo``というコマンドが使えるようになります。
- ``mojo generate lite_app``と入力すると、``Mojolicious::Lite``を使ったひな形を作成してくれます。
- ``hello``は作成するファイル名です。
- 現在のディレクトリに``hello``というファイルが作成されているか確認して下さい。

## 起動してみる
```
$ morbo hello
```

- Mojoliciousをインストールすると、``morbo``というコマンドも使えるようになります。
- ``morbo``は開発用のアプリケーションサーバーを起動してくれます。
- 画面上に``Server available at http://127.0.0.1:3000.``と表示されれば起動しています。
- Web ブラウザで ``http://127.0.0.1:3000`` にアクセスしてみましょう。

## コード解説（Line 1 - 2）
```
#!/usr/bin/env perl
use Mojolicious::Lite;
```

- ``Mojolicious::Lite`` は ``Mojolicious`` を簡単に使うためのモジュールです。
- `use Mojolicious::Lite;` とすることで、自動的に``strict``、``warnings``、``utf8``、``Perl 5.10 feature``が有効になります。

## コード解説（Line 1 - 2）
```
use strict;
use warnings;
use utf8;
use feature ':5.10';
```

- つまり、``use Mojolicious::Lite;``を書くだけで、ついでに上記のように書いているのと同じということです。

## コード解説（Line 4 - 5）
```
# Documentation browser under "/perldoc"
plugin 'PODRenderer';
```

- ``Mojolicious``では、機能を拡張するプラグインが利用できます。
- ここでは、``Mojolicious::Plugin::PODRenderer``を使用しています。
- ``http://127.0.0.1:3000/perldoc``にアクセスすると``Mojolicious``のPODを読むことができます。
- ［参考資料］
    - [Mojolicious::Plugin::PODRendererが便利 - Qiita](http://qiita.com/mozquito/items/1eabbb8ac7b1e516492f)

## コード解説（Line 7 - 10）
```
get '/' => sub {
  my $c = shift;
  $c->render(template => 'index');
};
```

- ウェブアプリケーションでは、URLごとに処理を変更できると便利です。
- このようなURLごとに処理を振り分ける機能のことを``router``や``dispatcher``と呼びます。
- ``Mojolicious::Lite``では、HTTPのGETリクエスト用の``router``として``get``という関数が用意されています。

## コード解説（Line 7 - 10）
```
get '/' => sub { ... };
```

- 見慣れない書き方ですが、これは、``get``という関数に、2つの引数を渡しているだけです。
- 一つ目の引数が``'/'``という文字列、二つ目の引数がコードリファレンスです。
- このように書くことで、HTTPのGETメソッドで``/``にアクセスした時の処理を``sub { ... }``に書くことができます。

## コード解説（Line 7 - 10）
```
my $c = shift;
$c->render(template => 'index');
```

- コードリファレンスの最初の行は、フレームワークのコントローラーを受け取っています。
- コントローラーには``render``というメソッドがあり、どのような出力をするのかを書くことができます。
- ここでは``index``のテンプレートを使用して出力するように書いています。

## コード解説
```
app->start;
```

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
