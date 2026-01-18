---
title: "PHP×Tokyo January 2026 に参加しました" # 記事のタイトル
emoji: "🅿️" # アイキャッチとして使われる絵文字（1文字だけ）
type: "idea" # tech: 技術記事 / idea: アイデア記事
topics: ["php", "phpxtky", "laravel"]
published: true # 公開設定（falseにすると下書き）
---

## はじめに

1/17(土)に、「[PHP×Tokyo January 2026](https://phpxtky.connpass.com/event/377112/)」に参加しました。

![](/images/phpxtky_202601/phptky.jpg)
![](/images/phpxtky_202601/phptky_slide.jpg)


海外の方の参加も目立ち（全体の4割くらい？）、英語の発表もあり、直近のLaravelの海外からの波と熱を感じました。
一方で、同時翻訳のWEBサービスがあり、英語でも聞きやすかったです。

参加した感想や知見を書いていきます。

## オープニング

直近のトピックとして、以下3つが取り上げられた印象です。

* Laravel 2025 Advent Calendar
    - https://laravel.com/advent
* Claude Code Plugin
    - https://laravel-news.com/laravel-gets-a-claude-code-simplifier-plugin
* Livewire V4 のリリース
    - たぶんこの辺の話 https://laravel-news.com/everything-new-in-livewire-4

## 「PHP True Async RFCの概要と、コミュニティでの賛否や議論の経緯」by さかな (台湾から来日🇹🇼)

PHP RFCの[true_async](https://wiki.php.net/rfc/true_async)の提案者が、どのようにコミュニティで議論をしながら進めているか？という登壇内容でした。


Hypervelの話もありました。

https://x.com/__var/status/2012386543386976281?s=20

> Hypervelは、超高性能を実現するためのネイティブコルーチンサポートを持つLaravelスタイルのPHPフレームワークです。

初耳で調べてみたんですが、**[ベンチマーク](https://hypervel.org/docs/introduction#benchmark)でLaravel Octaneの構成の11～12倍近くのスコア** が出るっぽい。

ユースケースを勝手に想像すると、ソシャゲとかマイクロサービスとかで、普通のLaravelのパフォーマンスでは不十分な際に採用するんですかねー。。
英語で質問すると英語で返ってくるので、質問できなかった。

一方で、昔LumenとかLaravel派生形のフレームワークがあったけれど、いつの間にかなくなった・Octaneに成り代わった印象があり、HypervelはLaravelのアップデート頻度と歩調が合うのか？が、個人的に気になっています。

## 「MCP For Everybody: Deep Dive into Core MCP Concepts」by Bijaya Prasad Kuikel

MCP全般の仕組みについての登壇でした。
stdioやHTTPでMCPサーバがやり取りする仕組みや、実際のMCP側のtoolsなどの実装の話でした。

あとは、PHP×Tokyo ということもあり、Laravel MCPでMCP作れるよねーの話もありました。

## 「Introduction to Laravel × DDD × Vibe Coding」by Takayuki Suzuki

カンボさんがDDDでVibe Codingした話をされていました。
実際のフェーズの定義とAI・人間の役割分担の定義の話が印象的でした！

https://x.com/kanbo0605/status/2012408490019492191?s=20

## 「New Features in Swoole 6.1」by Albert Chen (台湾から来日🇹🇼)

Swoole6.1の新機能の話で、String/Array/Streamをそのままオブジェクトライク(Laravelのコレクションライク)に操作できるのが印象的でした。


https://x.com/__var/status/2012406733746933811?s=20

Hypervelの開発や、先述のtrue_sync への興味の話もあり、台湾のPHPコミュニティは並列処理が好きなのかな？とか、コミュニティの興味関心の分野が国ごとに違うであればおもしろ～～～と思いました。


## [Laravel Live Japan 2026](https://laravellive.jp/ja)の最新情報

https://x.com/__var/status/2012410868105924777?s=20

* **スポンサー募集中** とのこと！
* **学割・団体割のチケット** もあるとのこと！
* 登壇者募集中とのこと！
    - 1/31まで募集中
    - LT枠はまた別枠で募集するとのこと

## 最後に

楽しかったです。

懇親会でNightwatchをチラっと聞いたのですが、Laravel公式というのもあり、**Laravelのdispatch Eventまわりのトレーシング** も強みらしいとのこと伺い、Event周りが多いプロダクトだと有用そうです。
試してみたいですね～～

あとは、LaravelとNightwatchとHypervelのステッカーをもらいました。
会社の人にも配ります。

https://x.com/__var/status/2012399516830343183?s=20

さっそくPCに貼りました。

https://x.com/__var/status/2012380075312697544?s=20

楽しかったです！

https://x.com/albert_cht/status/2012497777478209988?s=20


