# Mojolicious 入門

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
