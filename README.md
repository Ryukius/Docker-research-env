# Docker-based data processing and analysis environment

**!! WIP !!**

## 前提条件

- Docker desktop
- [VSCode](https://code.visualstudio.com/)
  - Extension:  [Remote-Containers Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)

## セットアップ

※下記引用の[柳本さんの記事](https://zenn.dev/nicetak/articles/vscode-docker-2023) のSetup & Workflow を参考にしていて、ほぼ同じ。

### 作成者

0. （初回だけ）Docker Volume を作成する。R と pip のパッケージの保管場所になる。

```{.shell}
docker volume create renv
docker volume create pip
```

1. レポジトリを VS Code で開く
   - `shift + cmd + P` で `Dev Containers: Open Folder in Container...` を選ぶ
2. Rプロジェクトを作成
   - (方法1) [`localhost:8787`](localhost:8787) から RStudio を開いて作成する
     → ただし、 Apple silicon の Mac だと rocker/geospatial (arm64 ベースが存在しないための不具合) との兼ね合いで不可。
   - テキストファイルから作成可能
   - ここをスキップしても、次の `renv::init()` はできる。いいかどうかは不明。
3. `renv::init()` を実行し、renv パッケージでのパッケージ管理を開始する
4. DVC をインストールする `pip install dvc dvc-gdrive`
5. DVC 環境を設定する
   - GoogleDrive でフォルダを作成し、ID をコピー
     - ※ID は、`https://drive.google.com/drive/folders/<ID>`という形になっている
     - ※僕の場合`Docker_project_data_storage`というフォルダ内に作成している
   - VS Code のターミナルから`dvc init && dvc remote add -d myremote gdrive://<ID>` を実

### 共同研究者

- このテンプレートを使用する（初回だけ）Docker Volume を作成する。R と pip のパッケージの保管場所になる。

```{.shell}
docker volume create renv
docker volume create pip
```

1. GitHubで管理者が作ったレポジトリをクローンする
2. レポジトリを VS Code で開く
   - `shift + cmd + P` で `Dev Containers: Open Folder in Container...` を選ぶ
   - VS code を使わずプロジェクトのフォルダに移動し、ターミナルから`docker compose up -d` としても良い
3. `renv::restore()` で、分析に使用されているパッケージをインストールする.
   - [`localhost:8787`](http://localhost:8787) から RStudio を開くか、VS code で Rターミナルを開くか、どちらかで実行
4. DVC をインストールする `pip install dvc dvc-gdrive`
5. `dvc pull` でデータをダウンロードする



### 作業中に実施すること（作成者・共同研究者どちらも）

- Rのパッケージを追加する際は、`renv::snapshot` を行い、使用したパッケージを保存する
- 作業を始める前に、Dockerコンテナを立ち上げたら`git pull` `dvc pull` を実施し、他の共同研究者が作業した内容を取り込む
- 作業が終わったら、GitHubへコードの変更をアップロードし、dvc でデータの変更をアップロードする
  - GitHub
    - `git add .`
    - `git commit -m "<作業した内容のメモ>"`
    - `git push`
  - dvc
    - `dvc push`



## Docker の内容説明

### Docker image 作成時に追加インストール

#### VS Code Extention

.devcontainer/devcontainer.json の "extensions" に記述

- GitHub copilot: 
- REditorSupport: 

#### R のパッケージ

- `renv`: パッケージマネージャー
- `languageserver`: Syntax highlightning とコード補完
  - [`languageserversetup`](https://github.com/jozefhajnala/languageserversetup/tree/master) を用いてインストールしている
- `httgd`: VS Code で、グラフをいい感じに出力してくれる
- `targets`: 変更のない部分をスキップして計算を実行してくれるパイプラインツール
  - [The {targets} R package user manual](https://books.ropensci.org/targets/)
  - [R のパイプラインツール targets を使う意義](https://terashim.com/posts/targets-r-pipeline/)
  - [R のパッケージ {targets} にコントリビュートした話](https://buildersbox.corp-sansan.com/entry/2022/12/17/000000)

## 参考

- [VSCode + Dockerでよりミニマルでポータブルな研究環境を](https://zenn.dev/nicetak/articles/vscode-docker-2023)

- [実践 Docker - ソフトウェアエンジニアの「Docker よくわからない」を終わりにする本](https://zenn.dev/suzuki_hoge/books/2022-03-docker-practice-8ae36c33424b59)
