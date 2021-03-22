+++
title = "Webサイトのセキュリティ診断ツール検証"
tags = ["セキュリティ"]
categories = ["SE"]
date = "2020-03-04"
description = "walti.io からみるWebセキュリティ診断ツールの調査"
menu = ""
banner = "banners/bunner_tech.jpg"
images = []
+++

<!--more-->

## Walti とは？  
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://walti.io/" data-iframely-url="//cdn.iframe.ly/4duEKyb?iframe=card-small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>  

そこそこ前からあるサービスですが、お値段が格安  
仕事柄果たしてその価値があるサービスなのか、どのレベルの診断ができるのかバックエンドで使っていると思われるツールから紐解いてみる  

## ポートスキャン
ポートスキャンに関しては何のツールを使っているかはわからない  
一般的には `namp` でしょうかね？  

例えば `nmap` コマンドを使えば下記のように開いてるポートやポートから情報も得られる  
下記はローカルのサーバからローカルの別サーバに対して実行しているので色々確認できるが、  
**自分の管理サーバ以外に行った場合は法的措置を取られることがあるので注意しましょう**  

```
Starting Nmap 7.70 ( https://nmap.org ) at 2020-03-04 15:38 JST
Nmap scan report for 10.96.84.106
Host is up (0.0062s latency).
Not shown: 997 closed ports
PORT     STATE SERVICE         VERSION
22/tcp   open  ssh             OpenSSH 8.0 (protocol 2.0)
| ssh-hostkey:
|   2048 37:1c:c1:6b:3a:2c:ba:90:3a:c9:ee:3b:84:8f:67:e0 (RSA)
|   256 35:67:ed:1c:9d:4f:68:10:b8:32:59:05:f7:f5:a0:97 (ECDSA)
|_  256 2d:aa:02:ec:24:52:e6:f9:9a:f0:71:41:80:6e:d7:05 (ED25519)
80/tcp   open  http            Apache httpd 2.4.37 ((Red Hat Enterprise Linux) mod_wsgi/4.6.4 Python/3.6)
| http-methods:
|_  Potentially risky methods: TRACE
| http-robots.txt: 1 disallowed entry
|_/
|_http-server-header: Apache/2.4.37 (Red Hat Enterprise Linux) mod_wsgi/4.6.4 Python/3.6
|_http-title: 
9090/tcp open  ssl/zeus-admin?
| fingerprint-strings:
|   GetRequest, HTTPOptions:
|     HTTP/1.1 400 Bad request
|     Content-Type: text/html; charset=utf8
|     Transfer-Encoding: chunked
|     X-DNS-Prefetch-Control: off
|     Referrer-Policy: no-referrer
|     X-Content-Type-Options: nosniff
|     <!DOCTYPE html>
|     <html>
|     <head>
|     <title>
|     request
|     </title>
|     <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
|     <meta name="viewport" content="width=device-width, initial-scale=1.0">
|     <style>
|     body {
|     margin: 0;
|     font-family: "Open Sans", Helvetica, Arial, sans-serif;
|     font-size: 12px;
|     line-height: 1.66666667;
|     color: #333333;
|     background-color: #f5f5f5;
|     border: 0;
|     vertical-align: middle;
|     font-weight: 300;
|     margin: 0 0 10px;
|     @font-face {
|_    font-family: '
| ssl-cert: Subject: commonName=localhost.localdomain/organizationName=c80d38dd2eb5421ea9ec22304996f3b9/countryName=US
| Subject Alternative Name: DNS:localhost.localdomain, DNS:localhost, IP Address:127.0.0.1
| Not valid before: 2019-05-16T11:50:26
|_Not valid after:  2029-07-03T04:30:26
|_ssl-date: ERROR: Script execution failed (use -d to debug)
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 183.27 seconds
```

## Nikto  
<i class="fas fa-external-link-alt"></i> [Nikto2](https://cirt.net/Nikto2)  
<i class="fas fa-external-link-alt"></i> [Github - sullo/nikto](https://github.com/sullo/nikto)  

Niktoの機能  

* SSL Support (Unix with OpenSSL or maybe Windows with ActiveState's Perl/NetSSL)  
* Full HTTP proxy support
* Checks for outdated server components
* Save reports in plain text, XML, HTML, NBE or CSV
* Template engine to easily customize reports
* Scan multiple ports on a server, or multiple servers via input file (including nmap output)
* LibWhisker's IDS encoding techniques
* Easily updated via command line
* Identifies installed software via headers, favicons and files
* Host authentication with Basic and NTLM
* Subdomain guessing
* Apache and cgiwrap username enumeration
* Mutation techniques to "fish" for content on web servers
* Scan tuning to include or exclude entire classes of vulnerability checks
* Guess credentials for authorization realms (including many default id/pw combos)
* Authorization guessing handles any directory, not just the root directory
* Enhanced false positive reduction via multiple methods: headers, page content, and content hashing
* Reports "unusual" headers seen
* Interactive status, pause and changes to verbosity settings
* Save full request/response for positive tests
* Replay saved positive requests
* Maximum execution time per target
* Auto-pause at a specified time
* Checks for common "parking" sites
* Logging to Metasploit
* Thorough documentation

2012年 くらいで更新は止まってるようだ  

### インストール  
epel 等からインストールできる  

```
# nikto -Version
---------------------------------------------------------------------------
Nikto Versions
---------------------------------------------------------------------------
File                               Version      Last Mod
-----------------------------      --------     ----------
Nikto main                         2.1.5
LibWhisker                         2.5
CHANGES.txt
db_404_strings                     2.003
db_content_search                  2.000
         2012-07-04                1.0
db_embedded                        2.004
db_favicon                         2.010
db_headers                         2.008
db_httpoptions                     2.002
db_multiple_index                  2.005
db_outdated                        2.017
db_parked_strings                  2.000
db_realms                          2.002
db_server_msgs                     2.006
db_subdomains                      2.006
db_tests                           2.019
db_variables                       2.004
nikto_apache_expect_xss.plugin     2.04
nikto_apacheusers.plugin           2.06
nikto_auth.plugin                  2.04
nikto_cgi.plugin                   2.06         2008-05-06
nikto_clientaccesspolicy.plugin    1.00
nikto_content_search.plugin        2.05
nikto_cookies.plugin               2.03
nikto_core.plugin                  2.1.5
nikto_dictionary_attack.plugin     2.04
nikto_embedded.plugin              2.07
nikto_favicon.plugin               2.09
nikto_fileops.plugin               1.00
nikto_headers.plugin               2.10
nikto_httpoptions.plugin           2.10
nikto_msgs.plugin                  2.07
nikto_multiple_index.plugin        2.03
nikto_outdated.plugin              2.09
nikto_parked.plugin                2.00
nikto_paths.plugin                 2.00
nikto_put_del_test.plugin          2.04
nikto_report_csv.plugin            2.06         2008-11-11
nikto_report_html.plugin           2.05         2009-07-20
nikto_report_msf.plugin            1.00
nikto_report_nbe.plugin            2.01
nikto_report_text.plugin           2.05         2008-11-11
nikto_report_xml.plugin            2.05         2009-07-20
nikto_robots.plugin                2.06
nikto_siebel.plugin                1.00         2011-01-03
nikto_ssl.plugin                   2.01
nikto_subdomain.plugin             2.01
nikto_tests.plugin                 2.04         2008-09-21
---------------------------------------------------------------------------
Module RPC::XML missing. Logging to Metasploit is disabled.
Module RPC::XML::Client missing. Logging to Metasploit is disabled.
Undefined subroutine &LW2::init_ssl_engine called at /var/lib/nikto/plugins/nikto_core.plugin line 2499.
```

データベースのアップデート  

```
# nikto -update
```

### スキャンの実行
素の Apache に対して実行してみる  
**自分の管理サーバ以外に行った場合は法的措置を取られることがあるので注意しましょう**  

```
# nikto -h 10.96.84.106
- Nikto v2.1.5
---------------------------------------------------------------------------
+ Target IP:          10.96.84.106
+ Target Hostname:    10.96.84.106
+ Target Port:        80
+ Start Time:         2020-03-04 19:21:20 (GMT9)
---------------------------------------------------------------------------
+ Server: Apache/2.4.37 (Red Hat Enterprise Linux) mod_wsgi/4.6.4 Python/3.6
+ Server leaks inodes via ETags, header found with file /, fields: 0x15737 0x58b06f5120010
+ The anti-clickjacking X-Frame-Options header is not present.
Use of each() on hash after insertion without resetting hash iterator results in undefined behavior, Perl interpreter: 0x99b150 at /usr/share/perl5/LW2.pm line 947.
Use of each() on hash after insertion without resetting hash iterator results in undefined behavior, Perl interpreter: 0x99b150 at /usr/share/perl5/LW2.pm line 947.
Use of each() on hash after insertion without resetting hash iterator results in undefined behavior, Perl interpreter: 0x99b150 at /usr/share/perl5/LW2.pm line 947.
Use of each() on hash after insertion without resetting hash iterator results in undefined behavior, Perl interpreter: 0x99b150 at /usr/share/perl5/LW2.pm line 947.
Use of each() on hash after insertion without resetting hash iterator results in undefined behavior, Perl interpreter: 0x99b150 at /usr/share/perl5/LW2.pm line 947.
Use of each() on hash after insertion without resetting hash iterator results in undefined behavior, Perl interpreter: 0x99b150 at /usr/share/perl5/LW2.pm line 947.
Use of each() on hash after insertion without resetting hash iterator results in undefined behavior, Perl interpreter: 0x99b150 at /usr/share/perl5/LW2.pm line 947.
Use of each() on hash after insertion without resetting hash iterator results in undefined behavior, Perl interpreter: 0x99b150 at /usr/share/perl5/LW2.pm line 947.
Use of each() on hash after insertion without resetting hash iterator results in undefined behavior, Perl interpreter: 0x99b150 at /usr/share/perl5/LW2.pm line 947.
Use of each() on hash after insertion without resetting hash iterator results in undefined behavior, Perl interpreter: 0x99b150 at /usr/share/perl5/LW2.pm line 947.
Use of each() on hash after insertion without resetting hash iterator results in undefined behavior, Perl interpreter: 0x99b150 at /usr/share/perl5/LW2.pm line 947.
Use of each() on hash after insertion without resetting hash iterator results in undefined behavior, Perl interpreter: 0x99b150 at /usr/share/perl5/LW2.pm line 947.
Use of each() on hash after insertion without resetting hash iterator results in undefined behavior, Perl interpreter: 0x99b150 at /usr/share/perl5/LW2.pm line 947.
Use of each() on hash after insertion without resetting hash iterator results in undefined behavior, Perl interpreter: 0x99b150 at /usr/share/perl5/LW2.pm line 947.
Use of each() on hash after insertion without resetting hash iterator results in undefined behavior, Perl interpreter: 0x99b150 at /usr/share/perl5/LW2.pm line 947.
Use of each() on hash after insertion without resetting hash iterator results in undefined behavior, Perl interpreter: 0x99b150 at /usr/share/perl5/LW2.pm line 947.
Use of each() on hash after insertion without resetting hash iterator results in undefined behavior, Perl interpreter: 0x99b150 at /usr/share/perl5/LW2.pm line 947.
Use of each() on hash after insertion without resetting hash iterator results in undefined behavior, Perl interpreter: 0x99b150 at /usr/share/perl5/LW2.pm line 947.
Use of each() on hash after insertion without resetting hash iterator results in undefined behavior, Perl interpreter: 0x99b150 at /usr/share/perl5/LW2.pm line 947.
Use of each() on hash after insertion without resetting hash iterator results in undefined behavior, Perl interpreter: 0x99b150 at /usr/share/perl5/LW2.pm line 947.
Use of each() on hash after insertion without resetting hash iterator results in undefined behavior, Perl interpreter: 0x99b150 at /usr/share/perl5/LW2.pm line 947.
Use of each() on hash after insertion without resetting hash iterator results in undefined behavior, Perl interpreter: 0x99b150 at /usr/share/perl5/LW2.pm line 947.
Use of each() on hash after insertion without resetting hash iterator results in undefined behavior, Perl interpreter: 0x99b150 at /usr/share/perl5/LW2.pm line 947.
Use of each() on hash after insertion without resetting hash iterator results in undefined behavior, Perl interpreter: 0x99b150 at /usr/share/perl5/LW2.pm line 947.
+ File/dir '/' in robots.txt returned a non-forbidden or redirect HTTP code (200)
+ "robots.txt" contains 3 entries which should be manually viewed.
+ Allowed HTTP Methods: OPTIONS, HEAD, GET, POST, TRACE
+ OSVDB-877: HTTP TRACE method is active, suggesting the host is vulnerable to XST
+ OSVDB-3268: /icons/: Directory indexing found.
+ OSVDB-3268: /images/: Directory indexing found.
+ OSVDB-3268: /images/?pattern=/etc/*&sort=name: Directory indexing found.
+ OSVDB-3233: /icons/README: Apache default file found.
+ 6544 items checked: 0 error(s) and 10 item(s) reported on remote host
+ End Time:           2020-03-04 19:22:21 (GMT9) (61 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```

なんかエラーが出てるけど何だろ・・・  
このOSに入ってる nikto が 2.1.5 で古いからかな・・・？  

とりあえず読み取れるレポートの内容を見てみると  

> <i class="fas fa-chevron-circle-right"></i> + Server: Apache/2.4.37 (Red Hat Enterprise Linux) mod_wsgi/4.6.4 Python/3.6  
> サーバーのバージョンが読み取れてしまっていること  
> <i class="fas fa-chevron-circle-right"></i> The anti-clickjacking X-Frame-Options header is not present.  
> X-Frame-Optionsヘッダーなどクリックジャンキング対策がされていないこと  
> <i class="fas fa-chevron-circle-right"></i> File/dir '/' in robots.txt returned a non-forbidden or redirect HTTP code (200)  
> <i class="fas fa-chevron-circle-right"></i> "robots.txt" contains 3 entries which should be manually viewed.  
> robot.txt に対する適切な挙動と配置がされていないこと  
> <i class="fas fa-chevron-circle-right"></i> Allowed HTTP Methods: OPTIONS, HEAD, GET, POST, TRACE  
> 許可されているメソッドが問題ないか？とか  
> <i class="fas fa-chevron-circle-right"></i> OSVDB-877: HTTP TRACE method is active, suggesting the host is vulnerable to XST
> HTTP TRACEメソッドはアクティブで、ホストがXSTに対して脆弱であること  
> <i class="fas fa-chevron-circle-right"></i> OSVDB-3268: /icons/: Directory indexing found.  
> <i class="fas fa-chevron-circle-right"></i> OSVDB-3268: /images/: Directory indexing found.  
> <i class="fas fa-chevron-circle-right"></i> OSVDB-3268: /images/?pattern=/etc/*&sort=name: Directory  
> <i class="fas fa-chevron-circle-right"></i> OSVDB-3233: /icons/README: Apache default file found.  
> 攻撃者が予測しやすいディレクトリが使われていたりすること  

という感じで一般的に気をつけた方が良いよという点がチェックされているような気がする  
テスト先の Apache アクセスログも見ていたけどよくあるアプリケーション（wordpressとか）に対するリクエストURLもあった  
でもちょっと足りなくない？素の Apache に対してであればもっと検知されると思っていたのでちょっとい意外  

## skipfish
<i class="fas fa-external-link-alt"></i> [Google Code - skipfish](https://code.google.com/archive/p/skipfish/downloads)  

Webアプリケーション向けの診断ツールらしい  
検知できるものは色々あるようだが大きな影響が考えられるものとして  

* サーバー側のSQL / PHPインジェクション（ブラインドベクトル、数値パラメーターを含む）。
* GETまたはPOSTパラメーターでの明示的なSQLのような構文。
* サーバー側のシェルコマンドインジェクション（ブラインドベクトルを含む）。
* サーバー側のXML / XPathインジェクション（ブラインドベクトルを含む）。
* フォーマット文字列の脆弱性。
* 整数オーバーフローの脆弱性。
* HTTP PUTを受け入れる場所。

などシステム侵害、データ侵害、などをチェックしてくれれるらしい  
更新は 2012年で止まってるようだ  

### インストール  
上記サイトより最新バージョンを入れておく  
※redhat 8/centos 8 には対応してないのか `idna.h` が足りないというコンパイルエラーが出る  
  色々ナレッジ調べてみても `libidn` 入れろと言われるが入っている  
  CentOS 系は `libidn-devel` が必要なんだが ver 8 系なさそう  

```
# wget https://storage.googleapis.com/google-code-archive-downloads/v2/code.google.com/skipfish/skipfish-2.10b.tgz  
# tar xf skipfish-2.10b.tgz
# cd skipfish-2.10b
# yum install pcre-devel openssl-devel linidn-devel
# make
```

### スキャンの実行
nikto みたいに Apache に対して実行しても大した結果は得られなさそうなので適当なアプリケーションに対して実行してみる  
**自分の管理サーバ以外に行った場合は法的措置を取られることがあるので注意しましょう**  

ブルートフォースアタック的なチェックもできるらしいが、辞書データ用意するのが面倒なのでそれ意外を見てみる

```
# ./skipfish -L -W /dev/null -o report http://localhost:3000
Welcome to skipfish. Here are some useful tips:

1) To abort the scan at any time, press Ctrl-C. A partial report will be written
   to the specified location. To view a list of currently scanned URLs, you can
   press space at any time during the scan.

2) Watch the number requests per second shown on the main screen. If this figure
   drops below 100-200, the scan will likely take a very long time.

3) The scanner does not auto-limit the scope of the scan; on complex sites, you
   may need to specify locations to exclude, or limit brute-force steps.

4) There are several new releases of the scanner every month. If you run into
   trouble, check for a newer version first, let the author know next.

More info: http://code.google.com/p/skipfish/wiki/KnownIssues

Press any key to continue (or wait 60 seconds)...
```

60秒経過するかエンターを押すとスキャンが開始する  
スキャン中の画面は下記の感じ  

```
skipfish version 2.10b by lcamtuf@google.com

  - 10.20.2.235 -

Scan statistics:

      Scan time : 0:39:56.605
  HTTP requests : 120686 (50.4/s), 2563366 kB in, 50594 kB out (1090.7 kB/s)
    Compression : 0 kB in, 0 kB out (0.0% gain)
    HTTP faults : 6 net errors, 0 proto errors, 0 retried, 0 drops
 TCP handshakes : 1324 total (92.3 req/conn)
     TCP faults : 0 failures, 6 timeouts, 0 purged
 External links : 21831 skipped
   Reqs pending : 1515

Database statistics:

         Pivots : 3395 total, 1399 done (41.21%)
    In progress : 1400 pending, 475 init, 91 attacks, 30 dict
  Missing nodes : 282 spotted
     Node types : 1 serv, 939 dir, 164 file, 0 pinfo, 1661 unkn, 630 par, 0 val
   Issues found : 1825 info, 8 warn, 806 low, 1 medium, 0 high impact
      Dict size : 0 words (0 new), 0 extensions, 0 candidates
     Signatures : 77 total

```

ページ数によるだろうけど結構時間がかかった  
40% くらいだけど `0 high impact` なのでわりと安心  
面倒だったので Ctr+C で強制終了してしまったが、レポートはちゃんと出力される  

<img src="/images/2020/se-security-tools/skipfish-01.png" />  

中身は結構細かく、詳細にテストもしてくれているよう  
どういうリクエストを送っているかもこのレポートから確認できるが、ちょっと項目にたいする説明が簡素すぎて何指摘してるかが親切ではない  

## O-Saft
OWSAP が提供している SSL 強度診断ツール  
<i class="fas fa-external-link-alt"></i> [OWSAP](https://owasp.org/)  
<i class="fas fa-external-link-alt"></i> [Github - OWSAP/O-Saft](https://github.com/OWASP/O-Saft)  

### インストール  
サイトからダウンロード  

```
# wget https://github.com/OWASP/O-Saft/archive/19.01.19.tar.gz
# tar xf O-Saft
# cd O-Saft
```

### スキャンの実行

```
# ./o-saft.pl +check yoursite.com
```

レポート内容  
長いので割愛するが、下記のように `!!Hint` という形でどの項目に問題があるかレポートしてくれる  

```
Target supports Secure Renegotiation:   yes
!!Hint: checks only if renegotiation is implemented serverside according RFC5746
Target supports Resumption:             yes
Target supports SRP:                    no ( )
Total number of check results 'yes':    69
Total number of check results 'no':     28
Total number of offered ciphers:        0
Total number of checked ciphers:        510
Certificate Chain Depth count:          0
Certificate Subject Altname count:      2
Certificate Wildcards count:            1
Certificate CPS size:                   87 bytes
Certificate CRL size:                   85 bytes
Certificate CRL data size:              0 bytes
Certificate OCSP size:                  24 bytes
Certificate OIDs size:                  0 bytes
Certificate Subject Altname size:       12 bytes
Certificate Chain size:                 0 bytes
Certificate Issuer size:                54 bytes
Certificate PEM (base64) size:          2187 bytes
Certificate PEM (binary) size:          1600 bytes
Certificate Public Key size:            2048 bits
Certificate Signature Key size:         2048 bits
Certificate Subject size:               59 bytes
Certificate Serial Number size:         16 bytes
```

他にも `o-saft --help` で確認すると様々なチェックオプションがあるみたいだけど基本的には `+check` で十分そう  

---

色々な診断サービスがあるけれど、格安の診断サービスを利用する場合、  
バックグラウンドのツールは何を使っているのか、そのツールの更新状況や使い勝手、診断結果レポート  
それらを知っていると導入の検討材料になるし、自分のサイトに対して適しているのかが判断できる  

なにより自分でできる力があるならそれでいいじゃんってなるしね  

{{< rawhtml >}}
<div style="border: dashed 1px #ccc;">
<a href="http://www.amazon.co.jp/exec/obidos/ASIN/4797393165/sinokyoufu-22/ref=nosim/" name="amazletlink" target="_blank"><img src="https://images-fe.ssl-images-amazon.com/images/I/513hw-04L8L._SL160_.jpg" alt="体系的に学ぶ 安全なWebアプリケーションの作り方 第2版 脆弱性が生まれる原理と対策の実践" style="border: none;" /></a>
</div>
{{< /rawhtml >}}
