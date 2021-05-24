+++
title = "ブラウザ表示をフルスクリーンで表示したい"
tags = ["Web"]
categories = ["SE"]
date = "2021-05-24"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

ちょろっと雑用でWebアプリをタブレットで表示時に、全画面表示させるために四苦八苦。  

<!--more-->

## 経緯
下記のようなブラウザで OpenCV.js で動くアプリを触る必要があり  

<div class="blogcardfu" style="width:auto;max-width:9999px;border:1px solid #E0E0E0;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFFFF;"><a href="https://yassi.hatenablog.com/entry/2020/12/25/000000" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:cdn-ak.f.st-hatena.com/images/fotolife/y/yassi-htn/20201224/20201224185410.png" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">JavaScriptで動体検知PWAを作ってみた【OpenCV.js】 - 知り得た履歴は水溜りへ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ぶぉんなたーれ！クリスマスはリベといっしょにパネトーネを食べたい人生だった…。 クリスマスといえばサンタさん。 その姿を一目見てやろうと夜更かしすると，サンタさんは来なくなります。 夜更かしは悪い子のすることなので。 悪い子にならずに，サンタさんの姿を確認したい… ということで，カメラに映る動くものを検知するサイトを作りました。 この記事は SLP KBIT Advent Calendar 2020 25日目の記事です。 adventar.org こんな感じ。 動作例 ここで動作します。 ただし，いまのところ(2020.12.25現在)，DesktopのfirefoxではA2HS(ホーム画面に…</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

こちらの記事を参考にさせていただきつつ触ってみたところ色々と課題が見えてきた。  

### 長時間の運用がきつい  
コードを参考にしつつ、検知結果の表示等不要部分を削除して試しに動かし始めてみると、端末のスペックによるのかもしれないが一定時間経つと **Out of memory** でブラウザが死ぬ。  
そのためとりあえずとして定期的にリロードをするように JS 側で処理を追加する。  
原因調査まではしてないので、もし詳しい方居たら教えて。。。  

### 同期・非同期問題？  
定期的にリロードで動作確認しているとどうにもまだアプリが停止するタイミングが有る。  
エラーを見ているとどうにも OpenCV.js とそれらの処理を書いてる main.js の読み込み、処理の同期非同期の関係なのか main.js 側で 「OpenCV.js の指定関数が見つからないよ！」とエラー出ている。え？ちゃんとロードしてるのに？  
確定では無いが、どうやら OpenCV.js をディスクキャッシュから呼び出している場合にこのような挙動になることがわかった。  
原因はわかんないけど。  
リロードしなければ良いんだけど、運用面やリロードは必要なので、ホスティングしているサーバ側でヘッダーにキャッシュしないように追加しておく。  
しかし OpenCV.js は重いのでできればキャッシュしてほしぃ。。。  

### Chrome for Android での全画面表示  
今回の利用端末は格安タブレットで Android さん。  
Chrome における全画面表示については下記 Dev サイトを見れば書いてあるのでそのまますればいい。  

<div class="blogcardfu" style="width:auto;max-width:9999px;border:1px solid #E0E0E0;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFFFF;"><a href="https://developers.google.com/web/fundamentals/native-hardware/fullscreen?hl=ja" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:developers.google.com/web/images/social-webfu-16x9.png" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">全画面表示にする &amp;nbsp;|&amp;nbsp; Web &amp;nbsp;|&amp;nbsp; Google Developers</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">全画面表示に移行します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

一番簡単なのは  

```js
document.documentElement.requestFullscreen();
```

等を JS でボタン設置して。。。とやれば良いんだけど、上記に上げてきたように定期的にリロードが必要。  
リロードされれば全画面は解除され、再度ボタンを押さなくてはならないし、  
かつ、フルスクリーン処理は現時点では**ユーザー側の操作なく自動的にはできない**とのことなので、この方法は取れない。  

次に目にとまるのはメタタグ。  

```html
<meta name="mobile-web-app-capable" content="yes">
```

これをメインのHTMLに記述し、端末側で対象のブラウザから**ホーム画面に追加する**してホームにショートカット作れば、開いたときに全画面になるし、リロードしても問題無い！
やったね！  

とはならず。  
この「ホーム画面に追加」、単純にショートカットを作るだけかと思ったらオフラインでも動くように全部キャッシュに乗せようとする。  
しかもこの時点では SW 関係の設定も入れてないし使ってないので、本当にハードキャッシュされてブラウザからキャッシュ削除するまでずっとそのまま。  

更新予定がないのであればそれでも良いけど、メンテナンスできないので却下だ。  

### ServiceWorker を使う
右往左往した結果、「全画面表示させる」「定期的にリロードさせる」「オンラインからコンテンツ更新できる」を満たそうとなると、PWA 化するしかなさそうだ。  

全画面表示用のメタタグはそのままに、  
とりあえず色んなサイトを参考にコピペでサクッと設定してみる。  
まず main.js に ServiceWorker 用のお決まりの定義と呼び出す ServiceWorker 用の設定 JS を書いておく。  

```js
// SW
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('sw.js')
  .then(
  function (registration) {
      if (typeof registration.update == 'function') {
          registration.update();
      }
  })
  .catch(function (error) {
      _c_("Error Log: " + error);
  });
}
```

ServiceWorker 側の記述は下記の通り。  
インストール設定のところは決り文句で、ServiceWorker が動き出してリクエストを処理する `fetch` 時にキャッシュの制御処理を入れておく。  
ServiceWorker 上でアプリを動かそうとすると、基本的には「ホーム画面に追加する」と同様にオフラインでも動けるよう全てキャシュしようとしてしまい、キャッシュされると OpenCV.js の動作が不安定になってしまうのでキャッシュはしないように `fetch` 処理時にキャッシュするかしないかをサイトドメインで制御させている。  

※そんな規模のアプリではないので、OpenCV.js だけキャッシュしなきゃ良いんだけど、ちょっと面倒だったので。  

またブラウザのように、ServiceWorker はHTTPヘッダーによるキャッシュ制御は見てないそうなので注意。  
キャッシュ制御は 今回のようにキャッシュ無効としてしまうと ServiceWorker の旨味を無くしてしまうことにもなるので注意と理解が必要なこともわかった。今回はスルーするけど。  

```
// キャッシュファイルの指定
var noCacheList = [
  "hogehoge.net",
  "localhost"
];

// インストール処理
self.addEventListener('install', function(e) {
  return;
});

// フェッチ時（何かしらの通信リクエスト）があったときの処理
self.addEventListener('fetch', function (e) {
  e.respondWith(
      caches.open(CACHE_NAME).then(function (cache) {
          return cache.match(e.request).then(function (response) {
              if (response) {
                  // e.requestに対するキャッシュが見つかったのでそれを返却
                  return response;
              } else {
                  // キャッシュが見つからなかったので取得
                  return fetch(e.request.clone()).then(function (response) {
                      //キャッシュしないドメイン名のリストに含まれていないかを確認する
                      var url = e.request.url;
                      var flg = true;
                      for(var cnt=0;cnt<noCacheList.length;cnt++){
                          if(url.indexOf(noCacheList[cnt])!=-1){
                              flg = false;
                          }
                      }
                      if(flg){
                          //キャッシュに追加する
                          cache.put(e.request, response.clone());
                      }else{
                          //リストにあったのでキャッシュ追加はしない。
                      }
                      // 取得したリソースを返却
                      return response;
                  });
              }
          });
      })
  );
});
```

これらを追加してホストして、ブラウザから同様に「ホーム画面に追加」して動作確認すると、全画面で表示され続け、  
定期リロードのタイミングでコンテンツが更新されることを確認できた。  

めでたしめでたし・・・かな？  
ちょっと暫定処理、いきあたりばったり対応ばかりだったので勉強しなきゃな。  
