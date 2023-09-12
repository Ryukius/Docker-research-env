# Docker-based data processing and analysis environment

**!! WIP !!**

## 前提条件

- Docker desktop
- [VSCode](https://code.visualstudio.com/)
  - Extension:  [Remote-Containers Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)

## セットアップ

※柳本さんの Setup & Workflow と同じ

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



## 参考

- [VSCode + Dockerでよりミニマルでポータブルな研究環境を](https://zenn.dev/nicetak/articles/vscode-docker-2023)

- [実践 Docker - ソフトウェアエンジニアの「Docker よくわからない」を終わりにする本](https://zenn.dev/suzuki_hoge/books/2022-03-docker-practice-8ae36c33424b59)



## 使用している Docker Image

- [rocker/geospatial](https://hub.docker.com/r/rocker/geospatial)
- (wip) [dataeditors/stata17](https://hub.docker.com/r/dataeditors/stata17)
- (wip) [jupyter/datascience-notebook](https://hub.docker.com/r/jupyter/datascience-notebook)
