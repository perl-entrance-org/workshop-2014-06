# Perl入学式 第6回 Webアプリ編の資料

## 資料の見方

`slide.md`が資料本体です。

資料は Markdown 記法で書かれていますので、テキストエディタだけでなく、Markdownエディタなどでもご覧いただけます。

また、`revealup`に対応していますので、スライドとして閲覧も可能です。

    $ revealup serve slide.md

上記コマンドが動作しない場合は、以下の参考記事にインストール方法や使い方が書いてありますのでご覧ください。

### 参考記事
- [一瞬でクールなスライドがつくれる「App::revealup」をリリースしました - ゆーすけべー日記](http://yusuke.be/post/88914879289)

## 編集方法

`src`ディレクトリのなかに各章ごとのファイルがありますので、そちらを編集してください。

編集後には`build.pl`を実行して`slide.md`を生成してください。（Path::Tinyが必要です）

    $ perl build.pl

Perl入学式公式サイト： <http://www.perl-entrance.org>
