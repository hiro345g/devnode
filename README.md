# devnode

これは <https://github.com/devcontainers/images/tree/main/src/typescript-node> で公開されている mcr.microsoft.com/devcontainers/typescript-node:18-bullseye の Docker イメージをベースとする Node.js 用の開発環境を Dev Container 環境として用意したものです。
<https://hub.docker.com/r/hiro345g/devnode-node> で公開している Docker イメージをビルドするためのファイルも含まれています。

Dev Container については、開発が <https://github.com/devcontainers> でされていますので、こちらをご覧ください。

ここで用意している `docker-compose.yml` では、開発するアプリの Git リモートリポジトリを devnode コンテナーの `/home/node/repo` （つまり、`devnode:/home/node/repo`）へクローンして開発することを想定しています。

また、`devnode:/home/node/repo` は Docker ボリュームの devnode-node-repo-data をマウントして使うようになっています。他にも devnode-node-vscode-server-extensions という Docker ボリュームを使うようになっています。

## 必要なもの

devnode を動作をさせるには、Docker、Docker Compose、Visual Studio Code (VS Code) 、Dev Containers 拡張機能が必要です。

### Docker

- [Docker Engine](https://docs.docker.com/engine/)
- [Docker Compose](https://docs.docker.com/compose/)

これらは [Docker Desktop](https://docs.docker.com/desktop/) をインストールしてあれば使えます。
Linux では Docker Desktop をインストールしなくても Docker Engine と Docker Compose だけをインストールして使えます。
例えば、Ubuntu を使っているなら [Install Docker Engine on Ubuntu](https://docs.docker.com/engine/install/ubuntu/) を参照してインストールしておいてください。

### Visual Studio Code

- [Visual Studio Code](https://code.visualstudio.com/)
- [Dev Containers 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)

VS Code の拡張機能である Dev Containers を VS Code へインストールしておく必要があります。

### 動作確認済みの環境

次の環境で動作確認をしてあります。Windows や macOS でも動作するはずです。

```console
$ cat /etc/os-release
PRETTY_NAME="Ubuntu 22.04.1 LTS"
NAME="Ubuntu"
VERSION_ID="22.04"
VERSION="22.04.1 LTS (Jammy Jellyfish)"
VERSION_CODENAME=jammy
ID=ubuntu
ID_LIKE=debian
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
UBUNTU_CODENAME=jammy

$ docker version
Client: Docker Engine - Community
 Cloud integration: v1.0.29
 Version:           23.0.1
 API version:       1.42
 Go version:        go1.19.5
 Git commit:        a5ee5b1
 Built:             Thu Feb  9 19:47:01 2023
 OS/Arch:           linux/amd64
 Context:           default

Server: Docker Engine - Community
 Engine:
  Version:          23.0.1
  API version:      1.42 (minimum version 1.12)
  Go version:       go1.19.5
  Git commit:       bc3805a
  Built:            Thu Feb  9 19:47:01 2023
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.6.18
  GitCommit:        2456e983eb9e37e47538f59ea18f2043c9a73640
 runc:
  Version:          1.1.4
  GitCommit:        v1.1.4-0-g5fd4c4d
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0

$ docker compose version
Docker Compose version v2.15.1
```

## ファイルの構成

ファイルの構成は次のようになっています。

```text
devnode/
├── .devcontainer/ ... devnode を Dev Container として利用するときに使う
│   ├── devcontainer.json
│   └── docker-compose.yml
├── build_devcon/ ... devnode の Docker イメージをビルドするときに使う
│   ├── .devcontainer/
│   │   ├── Dockerfile
│   │   └── devcontainer.json
│   ├── build_npmexec.sh ... devnode の Docker イメージをビルドするためのスクリプト npm exec 版
│   └── build.sh ... devnode の Docker イメージをビルドするためのスクリプト
├── workspace_share/ ... Docker ホストとコンテナーとでファイルを共有するためのディレクトリー
│   └── .gitkeep
├── .gitignore
├── docker-compose.yml ... devnode を利用するときに使う
├── LICENSE ... ライセンス
├── README.md  ... このファイル
└── sample.env  ... .env ファイルのサンプル
```

この後、リポジトリをクローンもしくはアーカイブファイルを展開した `devnode` ディレクトリーのパスを `${DEVNODE_DIR}` と表現します。

## 使い方

起動の仕方は、次のどちらかを想定しています。

- devcontainer.json を使わずに通常のコンテナーとして起動
- devcontainer.json を使った Dev Container として起動

起動したら、`devnode:/home/node/repo` ディレクトリーへ開発したいアプリのリポジトリをクローンして使うことを想定しています。

慣れないうちは通常のコンテナーとして起動して使ってください。
慣れてきて、Node.js の開発専用で使うようになったら Dev Container として起動して使うのが良いです。

### devcontainer.json を使わずに通常のコンテナーとして起動

先に「ビルド」を参照して Docker イメージを作成してください。
また、「環境変数」を参考にして `.env` ファイルを用意してください。

それから、docker-compose.yml を使って devnode-node コンテナーを起動します。

```console
cd `${DEVNODE_DIR}`
docker compose up -d
```

VS Code の Docker 拡張機能画面で devnode-node コンテナーのコンテキストメニューで `Attach Visual Studio Code` を選択して、アタッチします。
すると devnode-node コンテナー用の VS Code の画面が開いて使えるようになります。

### devcontainer.json を使った Dev Container として起動

先に「ビルド」を参照して Docker イメージを作成してください。また、必要なら「環境変数」を参考にして `.env` ファイルを用意してください。

VS Code を起動してから、F1 キーを入力して VS Code のコマンドパレットを表示してます。入力欄へ「dev containers open」などと入力すると「Dev Containers: Open Folder in Container...」が選択肢に表示されます。これをクリックすると、フォルダーを選択する画面になるので `${DEVNODE_DIR}` を指定して開きます。

これで `${DEVNODE_DIR}/.devcontainer/devcontainer.json` の内容にしたがって、devnode コンテナーが Dev Container として起動します。
このとき、拡張機能なども追加されます。
それから、devnode コンテナー用の VS Code の画面となります。

つまり、開いている VS Code の画面が、そのまま devnode コンテナー用の VS Code の画面として開き直されます。
devnode コンテナーでは Docker ホストのファイルを間違えて操作しないように、`${DEVNODE_DIR}` は見えないようにしてあります。
慣れないうちは、`${DEVNODE_DIR}` が見える VS Code の画面もあった方がわかりやすいと思います。
こちらは、慣れてから使うようにすると良いでしょう。

## ビルド

最初にビルドが必要です。
Dev Container 環境を起動する度に自動でビルドを実行する必要はないので、ビルド作業を別にしてあります。
すでにビルド済みのものを Docker Hub で公開してあるので、それを使うのが簡単です。
自分でビルドをする場合はは次の2つの方法があります。

- VS Code を使う方法
- build.sh を使う方法

合計3つの方法があるので順番に説明します。

### Docker Hub で公開されているビルド済みのものを使う方法

Docker Hub で公開されているビルド済みのものをダウンロードしてタグをつけます。

```console
docker pull hiro345g/devnode-node:1.0
docker image tag hiro345g/devnode-node:1.0 devnode-node:1.0
```

### VS Code を使う方法

VS Code を起動してから、F1 キーを入力して VS Code のコマンドパレットを表示してます。入力欄へ「dev containers open」などと入力すると「Dev Containers: Open Folder in Container...」が選択肢に表示されます。これをクリックすると、フォルダーを選択する画面になるので `${DEVNODE_DIR}/build_devcon` を指定して開きます。

`vsc-build_devcon-` で始まる Docker イメージが作成されてコンテナーが起動します。
`vsc-build_devcon-` で始まる Docker イメージに `devnode-node:1.0` のタグをつけます。

例えば、次の例だと vsc-build_devcon-b3ed032a709b975173b2f2fcf5212c79-uid といったイメージが作成されたので、それに対して `devnode-node:1.0` のタグをつけています。

```console
$ docker container ls |grep vsc
351cab45fe6c   vsc-build_devcon-b3ed032a709b975173b2f2fcf5212c79-uid   （略）
$ docker tag vsc-build_devcon-b3ed032a709b975173b2f2fcf5212c79-uid devnode-node:1.0
```

### build.sh を使う方法

`${DEVNODE_DIR}/build_devcon/build.sh` スクリプトでビルドをするには、`npm` コマンド、`docker` コマンドが実行できる環境が必要です。

```console
cd ${DEVNODE_DIR}
npm install --global @devcontainers/cli
sh ./builde_devcon/build.sh
```

`@devcontainers/cli` をグローバルインストールせずに `npm exec` コマンドを使いたい場合は `${DEVNODE_DIR}/build_devcon/build_npmexec.sh` スクリプトの方を使います。

```console
cd ${DEVNODE_DIR}
sh ./builde_devcon/build_npmexec.sh
```

## 環境変数

コンテナーと Docker ホストとでファイルを手軽に参照したり転送したりできるように、`devnode:/share` をバインドマウントするようにしています。
Docker ホスト側で使用するディレクトリーを `SHARE_DIR` で指定します。
Docker ホスト側に存在するものを指定してください。
ここでは、あらかじめ `workspace_share` ディレクトリーを用意してあり、それを使っています。

これを変更することができるように、環境変数 `SHARE_DIR` を用意してあります。
次の例では `${DEVNODE_DIR}/share` ディレクトリーを作成して、それを使うようにしています。

```sh
cd ${DEVNODE_DIR}
mkdir share
echo 'SHARE_DIR=./share' > .env
```

`sample.env` も参考にしてください。
