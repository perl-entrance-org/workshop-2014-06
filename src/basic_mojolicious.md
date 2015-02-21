# Mojolicious 入門

## 最初に
- この入門では、難しい表現を避けるために、厳密には正しくない事も書いてあります。
- この入門に書いていない沢山の引数やコマンドについては、本家サイトなどのリファレンスをご覧ください。

## 雛形を作る
```
$ mojo generate lite_app hello.pl
```

- Mojoliciousをインストールすると、``mojo``というコマンドが使えるようになります。
- ``mojo generate lite_app``と入力すると、``Mojolicious::Lite``を使ったひな形を作成してくれます。
- ``hello``は作成するファイル名です。
- 現在のディレクトリに``hello``というファイルが作成されているか確認して下さい。

## 起動してみる
```
$ morbo hello.pl
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

## コード解説（Line 12）
```
app->start;
```

- ``Mojolicious::Lite``を使う場合は、コードの最後にこの命令文を書かなければいけません。

- 当面は「お約束」として覚えておけばよいでしょう。

## コード解説（Line 13 - 15）
```
__DATA__

@@ index.html.ep
```

- ``__DATA__``以降は文字通りデータとして使えます。Mojolicious::Liteでは、ここにテンプレートなどを書いておくことができます。
- ``Mojolicious::Lite``では、``@@ index.html.ep``と書いておくと、次に``@@``が出てくるまでの範囲を``index.html.ep``というファイルとして扱うことができます。このようにすれば、多くのファイルを必要とせずにプログラムを書くことができます。
- ``ep``は、Mojoliciousの標準的なテンプレート機能を使用するための拡張子です。拡張子を``ep``にすることで、テンプレートであることを示します。

## コード解説（Line 16 - 18）
```
% layout 'default';
% title 'Welcome';
Welcome to the Mojolicious real-time web framework!
```

- ``%``は、その行をPerlのコードとして実行したい時に書きます。ここでは出てきませんがHTMLタグのように``<% Perlのコード... %>``書くこともできます。
- Mojoliciousの標準的なテンプレートでは、このように書くとテンプレートの中でPerlのコードが実行できます。
- なお、``%``では、その行全体がPerlのコードとして扱われますが、``<% ... %>``の場合は、``...``の部分のみがPerlのコードとして扱われます。
- ``layout``はレイアウトを指定します。ここでは``default``を指定しています。（後ほど詳しく説明します）
- ``title``はタイトルを指定します。ここでは``Welcome``を指定しています。（後ほど詳しく説明します）
- それ以外の通常の文字列は、そのままHTMLとして表示されます。

## コード解説（Line 20）
```
@@ layouts/default.html.ep
```

- ``@@``が書いてあるので、これ以降は``layouts/default.html.ep``として扱われます。この文字列はパス扱いなので、``layouts``ディレクトリにある``default.html.ep``というファイルを示します。
- ``Mojolicious::Lite``では、``layouts``ディレクトリ内にレイアウト用のテンプレートを用意でき、``layout``コマンドで切り替えて使うことができます。
- 先のコードで``% layout 'default';``と指定しましたので、``layouts``ディレクトリにある``default.html.ep``をレイアウトとして使用することになります。

## コード解説（Line 21 - 25）
```
<!DOCTYPE html>
<html>
  <head><title><%= title %></title></head>
  <body><%= content %></body>
</html>
```

- ``<%= ... %>``（あるいは``%=``から始まる行）は、Perlのコードを実行するだけでなく、値を表示したい時に書きます。
- ``<%= title %>``は、先のコードで``% title 'Welcome';``と指定したので、``Welcome``と表示されます。
- ``title``は、テンプレート内で使える関数で、このようにHTMLファイルのタイトルを取得したり設定したりできる、便利な関数です。
- ``content``は、レイアウトテンプレート内で使える関数で、通常のテンプレートの中身を取得する関数です。

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
