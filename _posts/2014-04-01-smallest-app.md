---
layout: post
unique_identifier:
 - 'http:/example.jp/bookid_in_url'
 - 'BookID'
 - 'URL'
title: '一番小さなRailsアプリづくり'
creator: 'igaiga'
date: '2014-04-01'
categories:
---

TODO:図にキャプション入れる

# 一番小さなRailsアプリづくり

ここではできるだけ小さい構成のRailsアプリを作ってみます。Railsアプリがどのように動作するのかの説明と、Railsが作るファイルがどのような役割なのか、機能と関連づけて説明していきます。

## 一番小さなRailsアプリをつくる

### アプリの作成

今回も最初にアプリを作ります。ブラウザに"Hello world!"と表示させるアプリです。前の章で作成した my_web_apps の下に新しいアプリを作ってみましょう。ターミナルを起動して以下のコマンドを打ちます。

{% highlight bash %}
cd my_web_apps
rails new helloworld
{% endhighlight %}

{% highlight console %}
$ rails new helloworld
      create
... (略)
Your bundle is complete!
Use `bundle show [gemname]` to see where a bundled gem is installed.
         run  bundle exec spring binstub --all
* bin/rake: spring inserted
* bin/rails: spring inserted
{% endhighlight %}

次に、前の章と同じように以下のコマンドを実行してみましょう。

{% highlight bash %}
cd helloworld
rails s
{% endhighlight %}
{% highlight console %}
$ rails s
=> Booting WEBrick
=> Rails 4.1.0 application starting in development on http://0.0.0.0:3000
=> Run `rails server -h` for more startup options
=> Notice: server is listening on all interfaces (0.0.0.0). Consider using 127.0.0.1 (--binding option)
=> Ctrl-C to shutdown server
[2014-05-05 10:22:56] INFO  WEBrick 1.3.1
[2014-05-05 10:22:56] INFO  ruby 2.1.1 (2014-02-24) [x86_64-darwin13.0]
[2014-05-05 10:22:56] INFO  WEBrick::HTTPServer#start: pid=30940 port=3000
{% endhighlight %}

ブラウザを起動して以下のURLを入力してアクセスしてみます。

* http://localhost:3000

![welcome rails]({{ site.url }}/assets/my-first-web-app/welcome_rails.png)

前の章と同じように動作しています。ここで実行した ```rails s``` コマンドの s は server の略で、省略した s でも、省略せずに server でも、同じように動作します。

TODO: コラム：rails s を停止した場合のアクセス。ポート重複時のアクセス。

### rails g コマンドでページを作る

ひきつづき、以下のコマンドを入力してみましょう。rails server が起動している場合は、Ctrl-c (controlキーを押しながらcキー)で終了してからコマンドを打ってください。

{% highlight bash %}
rails g controller hello index
{% endhighlight %}
{% highlight console %}
$ rails g controller hello index
      create  app/controllers/hello_controller.rb
       route  get 'hello/index'
      invoke  erb
      create    app/views/hello
      create    app/views/hello/index.html.erb
      invoke  test_unit
      create    test/controllers/hello_controller_test.rb
      invoke  helper
      create    app/helpers/hello_helper.rb
      invoke    test_unit
      create      test/helpers/hello_helper_test.rb
      invoke  assets
      invoke    coffee
      create      app/assets/javascripts/hello.js.coffee
      invoke    scss
      create      app/assets/stylesheets/hello.css.scss
{% endhighlight %}

再びrails server を起動させましょう。
{% highlight bash %}
rails s
{% endhighlight %}
{% highlight console %}
$ rails s
=> Booting WEBrick
=> Rails 4.1.0 application starting in development on http://0.0.0.0:3000
=> Run `rails server -h` for more startup options
=> Notice: server is listening on all interfaces (0.0.0.0). Consider using 127.0.0.1 (--binding option)
=> Ctrl-C to shutdown server
[2014-05-05 10:22:56] INFO  WEBrick 1.3.1
[2014-05-05 10:22:56] INFO  ruby 2.1.1 (2014-02-24) [x86_64-darwin13.0]
[2014-05-05 10:22:56] INFO  WEBrick::HTTPServer#start: pid=30940 port=3000
{% endhighlight %}

ブラウザを使い、以下のURLへアクセスします。

* http://localhost:3000/hello/index

![entries]({{site_url}}/assets/smallest-app/hello_index.png)

この画面が出れば、ここまで意図通りに動作しています。さきほど実行した rails g コマンドはこのページ、/hello/index を作るものでした。どのような仕組みで動作しているかは後ほどまた説明しますので、今は先にこのページに"Hello world!"と表示させてみることにします。

```app/views/hello/index.html.erb``` ファイルをエディタで開いてみてください。以下のような内容になっています。

{% highlight erb %}
<h1>Hello#index</h1>
<p>Find me in app/views/hello/index.html.erb</p>
{% endhighlight %}

これを以下のように変更して、ブラウザで同じURLへアクセスしてみてください。(rails s は起動したままで大丈夫です。もしも rails s を一度終了していた場合は、rails s コマンドでもう一度起動してからアクセスしてください。)

{% highlight erb %}
<p>Hello world!</p>
{% endhighlight %}

![entries]({{site_url}}/assets/smallest-app/helloworld.png)

"Hello world!"の文字が表示されましたか？これで一番小さなRailsアプリが完成しました。それでは次は、このアプリがどのように動作しているのかを見ていきましょう。

## Webアプリはどのように動作しているか

ここで、みなさんが普段ブラウザからつかっているWebアプリがどのように動作しているかを見てみましょう。アドレス入力欄にURLを入力してエンターキーを押すと、「リクエスト」がURL先のサーバへ向けて飛んでいきます。たとえば ```http://cookpad.com/``` と入力した場合は、クックパッドのサーバへ向けてリクエストが飛んでいきます。リクエストは追って説明していきますが、ざっくりとは「そのページを見たいという要求（リクエスト）」とイメージしてもらえばOKです。

![entries]({{site_url}}/assets/smallest-app/request.png)

Webサーバ上で動作しているWebアプリはリクエストを受け取ると、「レスポンス」としてHTMLで書かれたテキストを作ってブラウザへ返します。レスポンスは「Webアプリが返してきた情報群（HTMLで書かれた表示するの情報を含む）」とイメージできます。HTMLは HyperText Markup Language のことで、Webページを記述するための言語です。ブラウザはHTMLを解釈して、私たちの見易い、そしていつも見慣れたWebページを表示します。

![entries]({{site_url}}/assets/smallest-app/response.png)

HTMLはブラウザからも見ることができます。Chromeの場合は、どこかのサイト(たとえば ```http://cookpad.com/``` ) へアクセスしたあと、右クリックメニューから「ページのソースを表示」を選ぶとHTMLで書かれたそのページを閲覧することができます。

![entries]({{site_url}}/assets/smallest-app/right_click.png)

図 : 右クリックしてHTMLを表示する

![entries]({{site_url}}/assets/smallest-app/html.png)

図 : HTML(抜粋)

ここまで説明してきたのが、ブラウザの大きな仕事2つです。TODO：★ここから

##TODO？:コラム bin/rails