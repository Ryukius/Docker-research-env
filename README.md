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

### 共同研究者のセットアップ手順

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



# 2. 作業中の手順

## 2.1. 作業開始時

1. レポジトリを VS Code で開く

   - `shift + cmd + P` で `Dev Containers: Open Folder in Container...` から、このプロジェクト（`GS_merger_DID` ）選ぶ

2. 他の共同研究者の作業を取り込む（bash ターミナルを使用）

   1. `git pull`: コードを取り込む
   2. `dvc pull`: データを取り込む

   - 必ずこの順番で行う。`git pull` でデータファイルの更新情報 (`data.dvc`) をアップデートし、その情報をもとに `dvc pull` でデータファイルをダウンロードしている

## 2.2. 作業の保存

- 作業が終わったら、GitHubへコードの変更をアップロードし、dvc でデータの変更をアップロードする。この作業は、VS code の bash ターミナルで行う。

### 2.2.1. Rのパッケージを追加（もしあれば）

- 新たなRのパッケージを追加する際は、R terminalより`renv::snapshot()` コマンドを実行し、使用したパッケージを保存する
  - これにより、共同研究者側にもパッケージ情報が反映される

### 2.2.2. データの更新（もしあれば）

1. `dvc add data/`: 追加したデータファイルを記録
2. `dvc push`: データファイルをGoogle Driveへ反映

### 2.2.3. コードの更新（必須）

1. `git add .`（全部のファイルの変更を追加）
   -  ※新しいデータの記録も、`data.dvc` ファイルを通じて git でバージョン管理される
   -  ※一部のファイルのみ `git add <file名>` （1つのファイルを変更する場合）
2. `git commit -m "<作業した内容のメモ>"`: 変更を記録
3. `git push`: コードとファイルの変更をGitHub へ反映

## 2.3. 作業を終える

1. VS Codeの左下の青い部分「開発コンテナー: GS_merger_DID @ desktop-linux」と表示されている部分をクリック
2. 上部に表示されるメニューから「リモート接続を終了する」を選ぶ
   - これで、コンテナも停止され、CPUやメモリは使用されなくなる

## トラブルシューティング

- Q. VS Code 上でファイルが反映されない
  - A. Docker コンテナを停止して VS Code を閉じてから、コンテナを再起動してください。

- Q. VS Code で Docker コンテナを再起動すると、R terminal が表示されない

  - A. R script から任意のコードを実行すると、再表示されるようになります


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

## Stata を使うとき

- `project.dta` は空のdtaファイルで、project 管理用。これをダブルクリックすることで、Stata が当該プロジェクトのディレクトリに移動してくれる。
  - doファイル内のディレクトリ指定を相対パスで記述可能になる
- なぜか、Dockerと紐付けたディレクトリでStataを使った後Docker環境を開くとバグるので、分けた方がいいかもしれない



## 参考

- [VSCode + Dockerでよりミニマルでポータブルな研究環境を](https://zenn.dev/nicetak/articles/vscode-docker-2023)

- [実践 Docker - ソフトウェアエンジニアの「Docker よくわからない」を終わりにする本](https://zenn.dev/suzuki_hoge/books/2022-03-docker-practice-8ae36c33424b59)
