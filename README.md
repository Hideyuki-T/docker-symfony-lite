## 概要：
**Symfony**について知るために作成したシンプルなもの

## 技術一覧：
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat&logo=docker&logoColor=white)
![Symfony](https://img.shields.io/badge/Symfony-000000?style=flat&logo=symfony&logoColor=white)
![PHP](https://img.shields.io/badge/PHP-777BB4?style=flat&logo=php&logoColor=white)
![Composer](https://img.shields.io/badge/Composer-885630?style=flat&logo=composer&logoColor=white)
![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github&logoColor=white)
![Git](https://img.shields.io/badge/Git-F05032?style=flat&logo=git&logoColor=white)
![PhpStorm](https://img.shields.io/badge/PhpStorm-143?style=flat&logo=phpstorm&logoColor=white)


## 構造：
```
.
├── docker
│   ├── nginx
│   │   └── default.conf
│   └── php
│       └── Dockerfile
├── docker-compose.yml
├── README.md
└── app  
    ├── bin          //実行用スクリプトを格納
    │   └── console  //SymfonyのCLIコマンド実行の起点となるスクリプト
    ├── composer.json  //メタ情報を記述する構成ファイル
    ├── composer.lock  //環境間の再現性を担保
    ├── config  //アプリケーションの設定ファイル群を格納
    │   ├── bundles.php
    │   ├── packages
    │   ├── preload.php
    │   ├── routes
    │   ├── routes.yaml
    │   └── services.yaml
    ├── public         //ドキュメントルート（Webルート）として設定される
    │   └── index.php  //リクエストエントリーポイント
    ├── src
    │   ├── Controller
    │   └── Kernel.php
    ├── symfony.lock  //再インストール時の構成整合性を保証
    ├── var           //一時的なストレージ領域
    │   ├── cache
    │   └── log
    └── vendor  //全ての外部パッケージ（依存ライブラリ）を格納
        ├── autoload_runtime.php
        ├── autoload.php
        ├── bin
        ├── composer
        ├── psr
        └── symfony
```