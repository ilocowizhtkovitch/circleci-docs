---
layout: classic-docs
title: "CircleCI のビルド済み Docker イメージ"
short-title: "CircleCI のビルド済み Docker イメージ"
description: "CircleCI が提供する Docker イメージの一覧"
categories:
  - コンテナ化
order: 20
version:
  - Cloud
  - Server v3.x
  - Server v2.x
---

<div class="alert alert-warning" role="alert">
  <strong>プレフィックスが「 circleci/ 」のレガシーイメージは、 2021 年 12 月 31 日に<a href="https://discuss.circleci.com/t/legacy-convenience-image-deprecation/41034">サポートが終了</a></strong>しています。 ビルドを高速化するには、<a href="https://circleci.com/blog/announcing-our-next-generation-convenience-images-smaller-faster-more-deterministic/"> 次世代の CircleCI イメージ </a>を使ってプロジェクトをアップグレードしてください。
</div>

This document provides information about pre-built CircleCI images (convenience images) and a listing by language, service type, and tags.

* 目次
{:toc}

## 概要
{: #overview }
{:.no_toc}

CircleCI では、すぐに使える Docker イメージを多数提供しています。 These images are typically extensions of official Docker images, and include tools especially useful for CI/CD.

This document provides an overview of best practices when using a convenience image. Please note that we advise using the **next-generation** convenience images (these start `cimg/`) rather than **legacy images**, as explained below.

イメージを直接検索したい場合は、以下の場所から CircleCI イメージを閲覧することができます。

- 各次世代イメージのリポジトリへのリンクについては、[ディベロッパー ハブ](https://circleci.com/developer/ja/images/)を参照してください。
- Find all CircleCI pre-built images available on [Docker Hub](https://hub.docker.com/u/cimg).
- GitHub の `circleci-images` リポジトリには、[従来の Docker イメージそれぞれのソース コード](https://github.com/circleci/circleci-images)も用意しています。

__**注: ** CircleCI は、バグの修正または機能の強化のために、スケジュールに沿ってイメージに変更を加えることがあります。 こうした変更によって、CircleCI ジョブ内でのイメージの動作に影響が生じる可能性があります。 メンテナンスのスケジュールは、[Discuss ページで **convenience-images** タグを付けて事前に通知](https://discuss.circleci.com/tags/convenience-images)されますので、定期的にご確認ください。_

### 例
{: #examples }
{:.no_toc}

ビルド済み CircleCI Docker イメージのデモ アプリケーションでの使用例については、[チュートリアル]({{ site.baseurl }}/2.0/tutorials/)を参照してください。

## 次世代の CircleCI イメージ
{: #next-generation-convenience-images }

このセクションで紹介する次世代のコンビニエンス イメージは、CI、効率性、確定的動作を念頭に置いてゼロから設計されました。 注目ポイントは次のとおりです。

**スピンアップ時間の短縮** – Docker 的な言い方をすれば、次世代イメージは概してレイヤーがより少なく、より小さくなっています。 これらの新しいイメージを使用すると、ビルド開始時にイメージがすばやくダウンロードされると共に、イメージが既にホストにキャッシュされている可能性が高くなります。

**信頼性と安定性の向上** – 従来版は、アップストリームからの変更によってほぼ毎日再ビルドされるため、テストが十分に行われていない場合があります。 そのため、互換性が損なわれる変更が頻発してしまい、安定した確定的なビルドに最適な環境とは言えなくなっています。 次世代イメージは、セキュリティと致命的なバグについてのみ再ビルドされるため、より安定した確定的なイメージとなります。

### CircleCI のベースイメージ
{: #circleci-base-image }
`ベースイメージ` を使って設定すると、以下の例のようになります。

```yaml
  myjob:
    docker:
      - image: cimg/base:2021.04
```

これは必要最低限のものをインストールするように設計された、まったく新しい Ubuntu ベースのイメージです。 次世代版の CircleCI イメージは、すべてこのイメージがベースとなります。

**最適な用途**

汎用的なイメージを CircleCI で実行したり、Orb で使用したり、独自のカスタム Docker イメージのベースとして利用する必要がある場合に、このイメージをお使いください。

**関連資料**

このイメージの設定ファイルのサンプルは[デベロッパー ハブ](https://circleci.com/developer/ja/images/image/cimg/base)、ソース コードとドキュメントは [GitHub](https://github.com/CircleCI-Public/cimg-base) で入手できます。

以下の例では、上記の `ベースのイメージ`をベースにした、次世代のGoイメージを使用する方法を示しています。

```yaml
  myjob:
    docker:
      - image:  cimg/go:1.16
```

これは従来の CircleCI Go イメージ (`circleci/golang`) の後継となるものです。 Docker Hub の名前空間は `cimg` であることに留意してください。 他の言語の次世代イメージは、[以下](#next-gen-language-images)をご覧ください。


## ベストプラクティス
{: #best-practices }

以降のセクションで扱う次世代コンビニエンス イメージは、最新の Ubuntu LTS Docker イメージをベースにしており、言語またはサービスのベース ライブラリがインストールされています。したがって、可能な限り最も当てはまるイメージを使用することをお勧めします。 これで、いずれかのアップストリームイメージによってイメージに意図しない変更が組み込まれることを防止し、より決定論的にビルドを行うことができます。

したがって、アップストリームからの想定外の変更を防止するには、アップストリーム版の変更に伴ってそのイメージが変更されないよう、`cimg/ruby:2.4-node` と記述するのではなく、そのコンテナのさらに細かいバージョンを指定するようにタグを書き換えます。

たとえば、`cimg/ruby:2.4.10-node` のように、使用するイメージのバージョンを限定的に指定してください。 バージョンは CircleCI のすべての Docker イメージで指定できます。

また、使用するイメージを特定の SHA に至るまで指定することができます。 具体的には、`cimg/ruby:2.4.10-node` ではなく、`cimg/ruby@sha256:e4aa60a0a47363eca3bbbb066620f0a5967370f6469f6831ad52231c87ca9390` のように指定します。 これにより、変更を加える前に特定のイメージを好きなだけテストすることができます。


### イメージの指定に関する注意点
{: #notes-on-pinning-images }

<div class="alert alert-warning" role="alert">
SHA を長期的に使用することは推奨されません。 イメージの再ビルドを要する重大なバグやセキュリティ上の問題が見つかった場合、イメージにおけるパイプラインの依存関係が原因で、バグ修正やセキュリティ パッチ用の更新を取得できない可能性があります。
</div>

**注:** 従来のイメージを使用していてタグが指定されていない場合、Docker は `最新のタグ`を適用します。 `最新のタグ` が参照するのは、安定版の最新リリースのイメージです。 ただし、このタグは突然変わることもあるので、バージョンなどが明確なイメージタグを挿入するのがおすすめです。

**注:** Node.js バリアントの Docker イメージ (`-node` で終わるタグ) に対しては、Node.js の LTS リリースがプリインストールされています。 独自に特定のバージョンの Node.js/NPM を使用する場合は、`.circleci/config.yml` 内の `実行` ステップで設定できます。 Ruby イメージと共に特定のバージョンの Node.js をインストールする例については、以下を参照してください。

```yaml
version: 2.1

jobs:
  build:
    docker:
      - image: cimg/ruby:2.7.1-node
        auth:
          username: mydockerhub-user
          password: $DOCKERHUB_PASSWORD  # context / project UI env-var reference
    steps:
      - checkout
      - run:
          name: "Update Node.js and npm"
          command: |
            curl -sSL "https://nodejs.org/dist/v11.10.0/node-v11.10.0-linux-x64.tar.xz" | sudo tar --strip-components=2 -xJ -C /usr/local/bin/ node-v11.10.0-linux-x64/bin/node
            curl https://www.npmjs.com/install.sh | sudo bash
      - run:
          name: Check current version of node
          command: node -v
```

#### イメージ ID の確認方法
{: #finding-an-image-id }
{:.no_toc}

以下の手順で、Docker イメージの ID を確認してください。

1. CircleCI にアクセスし、そのイメージを使用した過去のビルドを表示します。
2. **[Spin up Environment (環境のスピンアップ)]** ステップをクリックします。
3. ログ内でそのイメージの **ダイジェスト** を確認します。
4. そこに記載されたイメージ ID を以下のようにイメージ名の末尾に付加します。

```
cimg/python@sha256:bdabda041f88d40d194c65f6a9e2a2e69ac5632db8ece657b15269700b0182cf
```

## イメージのタイプ
{: #image-types }

CircleCI イメージは、**言語**イメージと**サービス** イメージのいずれかのカテゴリに分類されます。 すべてのイメージは、`circleci` ユーザーをシステムユーザーとして追加します。 以下のセクションでは、利用可能な次世代および従来のイメージについて説明します。

### 次世代の言語イメージ
{: #next-gen-language-images }
{:.no_toc}

次世代言語イメージは、従来のものと同様、一般的なプログラミング言語に対応するコンビニエンス イメージであり、 関連する言語と[共通して使用されるツール](#pre-installed-tools)の両方が含まれます。 言語イメージを指定するときは、設定ファイル内の `docker` キー配下の最初の行に挿入します。したがって、ビルドの実行中はこれが[プライマリコンテナ]({{ site.baseurl }}/2.0/glossary/#primary-container){:target="_blank"}になります。

CircleCI は、以下の言語に対応する次世代イメージを開発しています。

- [Elixir](https://circleci.com/developer/images/image/cimg/elixir)
- [Go (Golang)](https://circleci.com/developer/images/image/cimg/go)
- [Node.js](https://circleci.com/developer/images/image/cimg/node)
- [OpenJDK (Java)](https://circleci.com/developer/images/image/cimg/openjdk)
- [PHP](https://circleci.com/developer/images/image/cimg/php)
- [Python](https://circleci.com/developer/images/image/cimg/python)
- [Ruby](https://circleci.com/developer/images/image/cimg/ruby)
- [Rust](https://circleci.com/developer/images/image/cimg/rust)

上記以外の言語のイメージが必要な場合は、CircleCI の[アイデア ボード](https://ideas.circleci.com/)でリクエストしてください。 まず、リクエストの前にアイデア ボード内を検索し、 同じアイデアがすでに投稿されている場合は、そのアイデアに投票してください。 まだ投稿されていなければ、カテゴリを 「イメージ」に設定してアイデアを投稿してください。 その後、そのアイデアを友人や同僚、フォーラム、その他のコミュニティに紹介して、票を集めることをお勧めします。

CircleCI では、獲得票数の多いアイデアほど、正式な開発を優先的に検討しています。

#### 次世代の言語イメージのバリアント
{: #next-gen-language-image-variants }
{:.no_toc}

CircleCI は、次世代言語イメージに対していくつかのバリアントを用意しています。 次世代イメージについては、イメージごとにそれぞれのバリアントを確認するようにしてください。 次世代イメージの `-browsers` バリアントは現在作成中です。 サポートされているバリアントの詳細については、[デベロッパー ハブ](https://circleci.com/developer/ja/images)でイメージの一覧を参照してください。

### 従来の言語イメージ
{: #legacy-language-images }
{:.no_toc}

従来の言語イメージは、一般的なプログラミング言語に対応するコンビニエンス イメージです。 よく使われる言語と[インストール済みツール](#pre-installed-tools)の両方を組み合わせたイメージとなっています。 言語イメージを指定するときは、設定ファイル内の `docker` キー配下の最初の行に挿入します。したがって、ビルドの実行中はこれが[プライマリコンテナ]({{ site.baseurl }}/2.0/glossary/#primary-container){:target="_blank"}になります。

CircleCI は、以下の言語に対応する従来のイメージを提供しています。

- [Android](#android)
- [Clojure](#clojure)
- [Elixir](#elixir)
- [Go (Golang)](#go-golang)
- [JRuby](#jruby)
- [Node.js](#nodejs)
- [OpenJDK (Java)](#openjdk)
- [PHP](#php)
- [Python](#python)
- [Ruby](#ruby)
- [Rust](#rust)

#### 次世代の言語イメージのバリアント
{: #language-image-variants }
{:.no_toc}

CircleCI は、言語イメージに対していくつかのバリアントを用意しています。 これらのバリアントを使用するには、以下のサフィックスの 1つをイメージタグの末尾に追加します。

- `-node`: 多言語対応の Node.js が含まれます。
- `-browsers`: Chrome、Firefox、OpenJDK v11、および GeckoDriver が含まれます。
- `-node-browsers`: `-node` バリアントと `-browsers` バリアントの組み合わせです。

例えば、`circleci/golang:1.9` イメージにブラウザーを追加する場合は、`circleci/golang:1.9-browsers` イメージを使用します。

### 次世代サービスイメージ
{: #next-gen-service-images }
{:.no_toc}

サービスイメージは、データベースなどのサービスに対応するコンビニエンスイメージです。 これらのイメージは言語イメージの**後に**リストし、セカンダリ サービス コンテナとして使用します。

- [Postgres](https://circleci.com/developer/images/image/cimg/postgres)
- [MySQL](https://circleci.com/developer/images/image/cimg/mysql)
- [MariaDB](https://circleci.com/developer/images/image/cimg/mariadb)
- [Redis](https://circleci.com/developer/images/image/cimg/redis)

### レガシーサービスイメージ
{: #legacy-service-images }
{:.no_toc}

CircleCI は、以下のサービスに対応する従来のイメージを提供しています。

- [buildpack-deps](#buildpack-deps)
- [DynamoDB](#dynamodb)
- [MariaDB](#mariadb)
- [MongoDB](#mongodb)
- [MySQL](#mysql)
- [PostgreSQL](#postgresql)
- [Redis](#redis)

#### サービス イメージのバリアント
{: #service-image-variant }
{:.no_toc}

CircleCI は、サービスイメージに対してバリアント 1つのみ用意しています。 RAM ボリュームを使用してビルドを高速化するには、サービスイメージタグの末尾に `-ram` サフィックスを追加します。

例えば、`circleci/postgres:9.5-postgis` イメージで RAM ボリュームを使用する場合は、`circleci/postgres:9.5-postgis-ram` イメージを使用します。

### 次世代サービスイメージ
{: #next-gen-service-images }
{:.no_toc}

CircleCI では、次世代版 CircleCI イメージの拡充に取り組んでいます。 使用可能な最新のサービス イメージについては、CircleCI の[デベロッパー ハブ](https://circleci.com/developer/ja/images/)を参照してください。

## プリインストール ツール
{: #pre-installed-tools }

すべてのコンビニエンス イメージは、`apt-get` と共にインストールされた追加ツールで拡張されています。

- `bzip2`
- `ca-certificates`
- `curl`
- `git`
- `gnupg`
- `gzip`
- `locales`
- `mercurial` (従来のイメージのみ)
- `net-tools`
- `netcat`
- `openssh-client`
- `parallel`
- `sudo`
- `tar`
- `unzip`
- `wget`
- `xvfb` (従来のイメージのみ)
- `zip`

特定の CircleCI イメージのバリアントにインストールされる特定のパッケージの特定のバージョンは、そのバリアントのベース イメージにインストールされている Linux ディストリビューション/バージョンのパッケージ ディレクトリに含まれるデフォルト バージョンに依存します。 従来の CircleCI コンビニエンス イメージは [Debian Jessie](https://packages.debian.org/jessie/) または [Stretch](https://packages.debian.org/stretch/) をベースにしていますが、次世代イメージ (`cimg`) は公式の [Ubuntu](https://packages.ubuntu.com) イメージを拡張したものです。 次世代イメージの詳細については、[デベロッパー ハブ](https://circleci.com/developer/ja/images/)を参照してください。 各イメージの変更履歴は、それぞれのリポジトリに掲載されています。

下記のパッケージは `curl` でインストールされます。

- [Docker クライアント](https://docs.docker.com/install/)
- [Docker Compose](https://docs.docker.com/compose/overview/)
- [dockerize](https://github.com/jwilder/dockerize)
- [jq](https://stedolan.github.io/jq/)


## 対象外のイメージ
{: #out-of-scope }

1. 上記一覧に記載されていないイメージは利用できません。 CircleCI イメージの提供プログラムが刷新されたため、現在のところ新しいイメージについての提案は受け付けていません。
1. 旧バージョンのソフトウェアは再ビルドされません。 アップストリーム イメージの特定のリリース (Node.js v8.1.0 など) 用のタグが作成されなくなった場合、CircleCI でもイメージの作成を終了します。 つまり、そのイメージ (`npm` など) に含まれる他のツールも更新されなくなります。
1. プレビュー、ベータ版、リリース候補を指定するイメージ タグには対応していません。 利用できることもありますが、これらのタグが原因となってCircleCI イメージのビルド システムに問題が発生しやすくなります。 特定の言語の非安定版リリースが必要な場合は、[Orb](https://circleci.com/ja/orbs/) またはカスタム Docker イメージからインストールすることをお勧めします。

## Legacy image tags by language
{: #legacy-image-tags-by-language }

**従来の**コンビニエンス イメージについて、最新のものを言語別に紹介します。

<div class="alert alert-warning" role="alert">
可能な限り次世代イメージを使用することをお勧めします。
最新の次世代版 CircleCI イメージの一覧と各イメージの内容の詳細については、<a href="https://circleci.com/developer/ja/">デベロッパー ハブ</a>を参照してください。
</div>

**注:** CircleCI は、[言語イメージのバリアント](#language-image-variants)および[サービス イメージのバリアント](#service-image-variant)以外の**従来のイメージ**に対して使用されるタグについては**管理していません**。 これらのタグは開発元が手がけるプロジェクトとして作成、メンテナンスされています。 似た名前のタグでも同じ内容のイメージとは限らないことにご注意ください。

{% assign images = site.data.docker-image-tags | sort %}
{% for image in images %}

### {{ image[1].name }}
{: # {{image1name}} }
{:.no_toc}

**リソース**

- [Docker Hub](https://hub.docker.com/r/circleci/{{ image[0] }}) - where this image is hosted as well as some useful instructions.

**使用方法:** config.yml の `docker:` に以下の行を追加します。

`- image: circleci/{{ image[0] }}:[TAG]`

**Recent Tags:**

See [the tag list for circleci/{{ image[0] }} on Docker Hub](https://hub.docker.com/r/circleci/{{ image[0] }})/tags?ordering=last_updated).

---

{% endfor %}

## 関連項目
{: #see-also }
{:.no_toc}

- プライベート リポジトリまたは Amazon ECR にあるイメージのビルドでの使用を承認する方法については、「[Docker の認証付きプルの使用]({{ site.baseurl }}/2.0/private-images/)」を参照してください。
- iOS 用の macOS イメージに関する詳細は、({{ site.baseurl }}/ja/2.0/testing-ios/) を参照してください。
- Docker イメージをビルドする方法については、「[Docker コマンドの実行手順]({{ site.baseurl }}/ja/2.0/building-docker-images/)」を参照してください。
