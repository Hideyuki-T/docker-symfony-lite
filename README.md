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


### Symfonyリクエスト処理フロー
```
Browser
  ↓
Nginx → public/index.php
         └── app/public/index.php
             └─【入口】Symfony Runtime起動スクリプト
             └─ require vendor/autoload_runtime.php

```
```
Symfony Runtime
  ↓
App Kernel（アプリケーションの核）
         └── app/src/Kernel.php
             └─ extends Symfony\Component\HttpKernel\Kernel
             └─ use Symfony\Bundle\FrameworkBundle\Kernel\MicroKernelTrait
             └─ function configureRoutes() / configureContainer() がここに

```
```
Kernel::boot() / Kernel::handle(Request)
  ↓
HttpKernel に処理が移る
         └── vendor/symfony/http-kernel/HttpKernel.php
             └─ function handle(Request $request, $type, $catch)
             └─ calls $this->router->match($request)

```
```
ルーティング解析
  ↓
config/routes/
         ├── app/config/routes.yaml（YAMLベース）
         ├── あるいはアノテーション：
             app/src/Controller/◯◯Controller.php
             └─ #[Route('/', name: 'home')] など
         └── 読み込み設定は：
             app/config/routes.yaml にて resource 指定
```
```
コントローラーが実行される
  ↓
Controller
         └── app/src/Controller/WelcomeController.php（例）
             └─ public function index(): Response

```
```
レスポンス生成
  ↓
戻っていく流れ
         └── HttpKernel が Response を index.php に返却
             └── index.php が echo/return で出力
  ↓
Browser にHTML等が表示される

```


### Symfony 構成哲学
#### 「コードはすべて src/ に閉じ込めよ」設計思想が根幹にある。
<table>
<tr>
<th>役割</th><th>場所</th>
</tr>
<tr>
<th>フレームワークの骨組み・エントリーポイント</th><th>bin/, public/, config/, vendor/ など</th>
</tr>
<tr>
<th>アプリケーション本体</th><th>src/ に限定される</th>
</tr>
<tr>
</table>
<table>
<tr>
<th>Symfony 2〜3の頃まで</th><th>Symfony 4から</th>
</tr>
<tr>
<th>app/ ディレクトリがあった</th><th>より柔軟でモジュール的な設計を志向し、"src/に集約" する設計へと移行</th>
</tr>
<tr>
<th></th><th>Symfony Flex + PSR-4 によって
ディレクトリを「役割単位」に分け、
src/ をアプリコードのみに絞ることで、構造がより明確になった</th>
</tr>
</table>



<table>
<tr>
<th>質問</th><th>回答</th>
</tr>
<tr>
<th>Q1. SymfonyにはLaravelのようなウェルカムページはある？</th><th>いいえ、デフォルトではありません。ただし symfony new my_project --webapp で生成されるテンプレートには簡易なトップページが含まれます。必要であれば自作可能です。</th>
</tr>
<tr>
<th>Q2. Symfonyプロジェクトを src/ に置く構成はおかしい？</th><th>はい、混乱を招きます。Symfonyの中にも src/ が存在するため、外部に同名ディレクトリを作るとパスや意味の重複が起こります。</th>
</tr>
<tr>
<th>Q3. app/ ディレクトリに Symfony を配置した方が良い？</th><th>はい。Dockerなどで環境構築とコードを分離する際には、アプリケーション全体を app/ にまとめ、内部に src/ を保持する構成が最もわかりやすく、実務的です。</th>
</tr>
<tr>
<th>Q4. use App\Kernel とあるが、実体はどこ？</th><th>app/src/Kernel.php にあります。Composer のオートロード設定により App\ 名前空間は src/ を指すようにマッピングされているためです。</th>
</tr>
<tr>
<th>Q5. なぜ Composer で App\ → src/ とマッピングしている？</th><th>PSR-4 規格に基づき、名前空間とディレクトリ構造を一致させることで、コードの見通しと拡張性を高めるためです。</th>
</tr>
<tr>
<th>Q6. そもそもなぜ src/ にこだわるのか？</th><th>Symfony は「フレームワーク」と「アプリケーションロジック」を明確に分離する設計思想を持ち、貴様のコードは src/ に集約されるべきという美学があります。</th>
</tr>
<tr>
<th>Q7. Symfony のエントリーポイントである index.php からは、実際にどこに処理が飛んでいるのか？</th><th>index.php は Symfony Runtime により処理され、App\Kernel を返します。そこから HttpKernel を通じてリクエストが処理され、コントローラにルーティングされます。</th>
</tr>
<tr>
<th>Q8. public/index.php の return function (array $context) は何をしているのか？</th><th>Symfony Runtime に「アプリケーションの起動方法」を教えている。返されたクロージャ内で Kernel を生成し、Runtime がそれを実行してアプリケーションを起動します。</th>
</tr>
<tr>
<th>Q9. app/ 配下の構成（bin/, config/, public/ など）の責務は？</th><th>Symfonyの各役割に基づいて配置されており、bin/ はCLI用スクリプト、config/ は設定、public/ はWebルート、src/ は貴様のロジック、var/ はキャッシュ・ログ等、vendor/ は依存ライブラリです。</th>
</tr>
</table>