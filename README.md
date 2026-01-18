# Spring PetClinic サンプルアプリケーション [![Build Status](https://github.com/spring-projects/spring-petclinic/actions/workflows/maven-build.yml/badge.svg)](https://github.com/spring-projects/spring-petclinic/actions/workflows/maven-build.yml)[![Build Status](https://github.com/spring-projects/spring-petclinic/actions/workflows/gradle-build.yml/badge.svg)](https://github.com/spring-projects/spring-petclinic/actions/workflows/gradle-build.yml)

[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#https://github.com/spring-projects/spring-petclinic) [![Open in GitHub Codespaces](https://github.com/codespaces/badge.svg)](https://github.com/codespaces/new?hide_repo_select=true&ref=main&repo=7517918)

## いくつかの図で理解する Spring Petclinic アプリケーション

プレゼンテーションはこちら:
[Spring Petclinic Sample Application (レガシースライド)](https://speakerdeck.com/michaelisvy/spring-petclinic-sample-application?slide=20)

> **注意:** これらのスライドはレガシーなSpring Boot以前のバージョンのPetclinicを参照しており、現在のSpring Bootベースの実装を反映していない可能性があります。
> 最新の情報については、このリポジトリとそのドキュメントを参照してください。


## Petclinic をローカルで実行

Spring Petclinic は [Maven](https://spring.io/guides/gs/maven/) または [Gradle](https://spring.io/guides/gs/gradle/) を使用してビルドされた [Spring Boot](https://spring.io/guides/gs/spring-boot) アプリケーションです。
ビルドにはJava 17以降が必要で、アプリケーションはJava 17以降で実行できます。

まず、プロジェクトをローカルにクローンする必要があります:

```bash
git clone https://github.com/spring-projects/spring-petclinic.git
cd spring-petclinic
```
Mavenを使用している場合、コマンドラインで次のようにアプリケーションを起動できます:

```bash
./mvnw spring-boot:run
```
Gradleの場合、コマンドは次のようになります:

```bash
./gradlew bootRun
```

その後、<http://localhost:8080/> でPetclinicにアクセスできます。

<img width="1042" alt="petclinic-screenshot" src="https://cloud.githubusercontent.com/assets/838318/19727082/2aee6d6c-9b8e-11e6-81fe-e889a5ddfded.png">

もちろん、お気に入りのIDEでPetclinicを実行することもできます。
詳細は以下を参照してください。

## コンテナのビルド

このプロジェクトには `Dockerfile` はありません。Spring Bootビルドプラグインを使用して(dockerデーモンがある場合)コンテナイメージをビルドできます:

```bash
./mvnw spring-boot:build-image
```

## Spring Petclinicのバグを発見した場合や改善提案がある場合

課題トラッカーは[こちら](https://github.com/spring-projects/spring-petclinic/issues)で利用できます。

## データベース設定

デフォルト設定では、Petclinicは起動時にデータが投入されるインメモリデータベース(H2)を使用します。H2コンソールは `http://localhost:8080/h2-console` で公開されており、`jdbc:h2:mem:<uuid>` URLを使用してデータベースの内容を検査できます。UUIDは起動時にコンソールに出力されます。

永続的なデータベース設定が必要な場合は、MySQLとPostgreSQL用に同様の設定が提供されています。データベースタイプが変更されるたびに、アプリケーションは異なるプロファイルで実行する必要があることに注意してください: MySQLの場合は `spring.profiles.active=mysql`、PostgreSQLの場合は `spring.profiles.active=postgres` です。アクティブプロファイルの設定方法の詳細については、[Spring Bootドキュメント](https://docs.spring.io/spring-boot/how-to/properties-and-configuration.html#howto.properties-and-configuration.set-active-spring-profiles)を参照してください。

お使いのOSに適したインストーラーでMySQLまたはPostgreSQLをローカルで起動するか、dockerを使用できます:

```bash
docker run -e MYSQL_USER=petclinic -e MYSQL_PASSWORD=petclinic -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=petclinic -p 3306:3306 mysql:9.5
```

または

```bash
docker run -e POSTGRES_USER=petclinic -e POSTGRES_PASSWORD=petclinic -e POSTGRES_DB=petclinic -p 5432:5432 postgres:18.1
```

[MySQL](https://github.com/spring-projects/spring-petclinic/blob/main/src/main/resources/db/mysql/petclinic_db_setup_mysql.txt)
および[PostgreSQL](https://github.com/spring-projects/spring-petclinic/blob/main/src/main/resources/db/postgres/petclinic_db_setup_postgres.txt)についての詳細なドキュメントが提供されています。

通常の `docker` の代わりに、提供されている `docker-compose.yml` ファイルを使用してデータベースコンテナを起動することもできます。各サービスはSpringプロファイルにちなんで名前が付けられています:

```bash
docker compose up mysql
```

または

```bash
docker compose up postgres
```

## テストアプリケーション

開発時には、`PetClinicIntegrationTests`(デフォルトのH2データベースを使用し、Spring Boot Devtoolsも追加)、`MySqlTestApplication`、`PostgresIntegrationTests` の `main()` メソッドとして設定されたテストアプリケーションを使用することをお勧めします。これらは、IDEでアプリを実行して迅速なフィードバックを得られるように設定されており、同じクラスをそれぞれのデータベースに対する統合テストとして実行することもできます。MySQL統合テストはTestcontainersを使用してDockerコンテナでデータベースを起動し、Postgresテストも同様にDocker Composeを使用します。

## CSSのコンパイル

`src/main/resources/static/resources/css` に `petclinic.css` があります。これは `petclinic.scss` ソースから生成され、[Bootstrap](https://getbootstrap.com/) ライブラリと組み合わされています。`scss` に変更を加えたり、Bootstrapをアップグレードしたりする場合は、Mavenプロファイル "css" を使用してCSSリソースを再コンパイルする必要があります。例: `./mvnw package -P css`。GradleにはCSSをコンパイルするためのビルドプロファイルはありません。

## IDEでPetclinicを使用する

### 前提条件

システムに以下がインストールされている必要があります:

- Java 17以降(JREではなく完全なJDK)
- [Gitコマンドラインツール](https://help.github.com/articles/set-up-git)
- お気に入りのIDE
  - m2eプラグインを使用したEclipse。注: m2eが利用可能な場合、`Help -> About` ダイアログにm2アイコンがあります。m2eがない場合は、[こちら](https://www.eclipse.org/m2e/)のインストールプロセスに従ってください
  - [Spring Tools Suite](https://spring.io/tools) (STS)
  - [IntelliJ IDEA](https://www.jetbrains.com/idea/)
  - [VS Code](https://code.visualstudio.com)

### 手順

1. コマンドラインで次を実行:

    ```bash
    git clone https://github.com/spring-projects/spring-petclinic.git
    ```

1. EclipseまたはSTS内で:

    `File -> Import -> Maven -> Existing Maven project` を介してプロジェクトを開き、クローンしたリポジトリのルートディレクトリを選択します。

    次に、コマンドライン `./mvnw generate-resources` でビルドするか、Eclipseランチャー(プロジェクトを右クリックして `Run As -> Maven install`)を使用してCSSを生成します。アプリケーションのmainメソッドを右クリックして `Run As -> Java Application` を選択して実行します。

1. IntelliJ IDEA内で:

    メインメニューで `File -> Open` を選択し、Petclinicの[pom.xml](pom.xml)を選択します。`Open` ボタンをクリックします。

    - CSSファイルはMavenビルドから生成されます。コマンドライン `./mvnw generate-resources` でビルドするか、`spring-petclinic` プロジェクトを右クリックして `Maven -> Generates sources and Update Folders` を選択できます。

    - 最近のUltimateバージョンを使用している場合、`PetClinicApplication` という名前の実行構成が作成されているはずです。それ以外の場合は、`PetClinicApplication` メインクラスを右クリックして `Run 'PetClinicApplication'` を選択してアプリケーションを実行します。

1. Petclinicに移動

    ブラウザで[http://localhost:8080](http://localhost:8080)にアクセスします。

## 特定の情報をお探しですか?

|Spring Boot設定 | クラスまたはJavaプロパティファイル  |
|--------------------------|---|
|メインクラス | [PetClinicApplication](https://github.com/spring-projects/spring-petclinic/blob/main/src/main/java/org/springframework/samples/petclinic/PetClinicApplication.java) |
|プロパティファイル | [application.properties](https://github.com/spring-projects/spring-petclinic/blob/main/src/main/resources) |
|キャッシング | [CacheConfiguration](https://github.com/spring-projects/spring-petclinic/blob/main/src/main/java/org/springframework/samples/petclinic/system/CacheConfiguration.java) |

## 興味深いSpring Petclinicのブランチとフォーク

[spring-projects](https://github.com/spring-projects/spring-petclinic) GitHub組織のSpring Petclinic "main" ブランチは、Spring BootとThymeleafに基づく「正規」実装です。GitHub組織[spring-petclinic](https://github.com/spring-petclinic)には[かなり多くのフォーク](https://spring-petclinic.github.io/docs/forks.html)があります。Pet Clinicの実装に異なる技術スタックを使用することに興味がある場合は、そちらのコミュニティに参加してください。

## 他のオープンソースプロジェクトとのやり取り

Spring Petclinicアプリケーションに取り組む最も良い点の1つは、多くのオープンソースプロジェクトと直接連携する機会があることです。Spring、Spring Data、Bean Validation、さらにはEclipseなど、さまざまなトピックでバグを発見したり改善を提案したりしました。多くの場合、それらは数日で修正/実装されています。
以下はそのリストです:

| 名前 | 課題 |
|------|-------|
| Spring JDBC: NamedParameterJdbcTemplateの使用を簡素化 | [SPR-10256](https://github.com/spring-projects/spring-framework/issues/14889) および [SPR-10257](https://github.com/spring-projects/spring-framework/issues/14890) |
| Bean Validation / Hibernate Validator: Maven依存関係の簡素化と後方互換性 |[HV-790](https://hibernate.atlassian.net/browse/HV-790) および [HV-792](https://hibernate.atlassian.net/browse/HV-792) |
| Spring Data: JPQLクエリを使用する際の柔軟性の向上 | [DATAJPA-292](https://github.com/spring-projects/spring-data-jpa/issues/704) |

## 貢献

バグレポート、機能リクエスト、プルリクエストの送信には、[課題トラッカー](https://github.com/spring-projects/spring-petclinic/issues)が推奨されるチャネルです。

プルリクエストについては、一般的なテキストエディタで簡単に使用できるように、[editor config](.editorconfig)でエディタの設定が利用できます。詳細については<https://editorconfig.org>をお読みいただき、プラグインをダウンロードしてください。すべてのコミットには、貢献者がDeveloper Certificate of Originに同意していることを示すために、各コミットメッセージの最後に __Signed-off-by__ トレーラーを含める必要があります。
詳細については、ブログ記事[Hello DCO, Goodbye CLA: Simplifying Contributions to Spring](https://spring.io/blog/2025/01/06/hello-dco-goodbye-cla-simplifying-contributions-to-spring)を参照してください。

## ライセンス

Spring PetClinicサンプルアプリケーションは、[Apache License](https://www.apache.org/licenses/LICENSE-2.0)のバージョン2.0の下でリリースされています。