# 初心者向け：Neo4jのご紹介

## グラフ型データベースとは

* データ間の関係性を表現できるデータベースのこと。

<pre>
[林田]─FRIENDS→[北沢]
</pre>

* 関係性から再帰的にデータを抽出できることが最大のメリット。
* ソーシャルネットワークだけでなく、たとえば以下のような関係性を表現し、分析することが可能。
 - 道路交通網：地点間の最短ルート探索
 - 製品のアッセンブリと仕入先：部品仕入先の最適化
 - 画面遷移情報：Webサイトの利用分析（ページ間リレーションシップ強弱の把握、最もよく辿られるパスの把握など）

## Neo4jの特徴

* Javaで書かれたオープンソースのグラフ型データベース。
* 開発元はNeo Technology社。
* ノード（単一データ）単位のACIDトランザクションが確保されている。（スケールしても同様）
* 可用性向上やリードレプリカ作成のため、マスタデータベースの複製が可能。ただし、データ分散は不可能（2.2.x）。
* 「Cypher（サイファー)」と呼ばれるデータ操作用クエリ言語が用意されており、クエリはトラバーサル（ある単位のクエリ実行結果をバケツリレーのように次のクエリのインプットに使うこと）で実行される。
* リッチなWebインターフェースからクエリ実行結果を見たり、クエリのチューニングをしたりすることができる。
* Webインターフェース以外にも、Neo4jシェルやREST API等、複数の方法でクエリ発行が可能。
* 導入事例が多く、グラフ型データベースの中で最も有名（だと思う）。


## Neo4jのデータモデル

* ノード：グラフデータの最小単位。
  - ラベル：ノードのグループ名。「キー:バリュー」形式。たとえば、「person:人」
  - 属性：ノードが持つ属性。json形式で指定できる。たとえば、「{name: "林田", sex: "男", age: "27"}」
  - 関係性：他ノードとの関係性。
* 関係性
  - タイプ：関係性の種類。たとえば、「(person:人 { name: "林田"})-[relations:FRIENDS]->(person:人 { name: "北沢"})」
  - 属性：関係性が持つ属性。たとえば、「relations.rating=5」


## Neo4jのインストールと起動

* http://neo4j.org/download から最新のアーカイブをダウンロード
* アーカイブを展開(exeをダウンロードした場合は実行)
* bin ディレクトリにあるスクリプトを実行
 - Linux/MacOS の場合：NEO4J_HOME/bin/neo4j start を実行
 - Windows の場合：%NEO4J_HOME%\bin\Neo4j.bat を実行


## ディレクトリ構成について（neo4j-community-2.2.1の場合）

* bin/：Neo4jのバイナリ群が格納されているディレクトリ。
  - Neo4j.bat：Neo4j本体。
  - Neo4jImport.bat：データインポート用。CSV形式やJSON形式で大量データのアップロードが可能。
  - Neo4jInstaller.bat：インストーラ。
  - Neo4jShell.bat：主にバッチ処理のために用意されているシェル（CUI）。
* conf/：設定ファイル群が格納されているディレクトリ。
  - custom-logback.xml：データベース用のログ設定ファイル。標準的なlogback(log4jの開発者が作った後継的な実装)オプションが設定可能。
  - neo4j-http-logging.xml：HTTP REST用のログ設定ファイル。標準的なlogbackオプションが設定可能。
  - neo4j-server.properties：実行時オプション設定ファイル。データベースファイルへのパスや、Web GUI用のポート番号等の設定が可能。
  - neo4j.properties：データベースのチューニングパラメータ。ページキャッシュメモリや、ログ保持期間等の設定が可能。
  - neo4j-wrapper.conf：起動／環境設定ファイル。JVMパラメータや、読み込むプロパティファイル等の設定が可能。
  - loggging.properties：java.util.loggingの設定ファイル。ログレベル等の設定が可能。
* data/：デフォルトのデータ格納ディレクトリ。
  - log/：デフォルトのログ格納ディレクトリ。
* lib/：コアライブラリ群が格納されているディレクトリ。中身は削除しないこと。
* plugins/：サーバサイドスクリプト等のプラグインを配置可能なディレクトリ。デフォルトでは何も格納されていない。配置したjarを適用するには、サーバ再起動が必要。
* system/：ランタイムライブラリやリソースが格納されたシステムディレクトリ。
* CHANGES.txt：リリースノート。
* LICENSE.txt：GNU GENERAL PUBLIC LICENSE, Version 3
* LICENSES.txt：Apache Software License, Version 2.0
* NOTICE.txt：ライセンス体系の説明。★ライセンス体系について今後要調査。
* README.txt：Neo4jの簡単な説明と、インストール～起動手順が載っている。
* UPGRADE.txt：バージョンアップグレードの手順説明（シングルインスタンスの場合）。HAクラスタ（エンタープライズ版で可能）を組んでいる場合の説明は載っていない。


## Webインターフェースの利用

1. http://localhost:7474 にアクセス（デフォルトのポートは7474）。
2. neo4j/neo4jでログイン
3. 新しいパスワードを設定
4. トップ画面が表示される
  - Neo4jマーク：インスタンスの情報を表示できる。
  - 星マーク：お気に入り項目。最初はここから操作を始める。
  - iマーク：リファレンスや操作ガイド、データサンプル等を見ることができる。


## Neo4jシェルの利用

1. bin ディレクトリにあるスクリプトを実行。
 - Linux/MacOS の場合：NEO4J_HOME/bin/neo4j-shell を実行
 - Windows の場合：%NEO4J_HOME%\bin\Neo4jShell.bat を実行


## REST APIの利用

1. http://localhost:7474/db/data/ のようなURLにアクセス。
2. ブラウザからやってみると認証エラーが返却されるが、Basic認証ヘッダを付加してGETリクエストを投げることで結果を取得可能。★今後REST APIを使ったアプリも作ってみたい。
  - REST APIのドキュメントは[こちら](http://neo4j.com/docs/milestone/rest-api.html)。


## Hello Worldしてみる（Webインターフェースを利用）

1. Webインターフェースにログイン
2. 星マーク＞General＞Create a node をクリック
3. 画面右側にCypherで書かれたクエリが表示される（クエリの意味はこの後の発表で解説予定？）
4. 画面右端の再生ボタンをクリックしてクエリ実行
5. 表形式で「Hello World」が返却される

<pre>
// Create a node
CREATE (n {name:"World"}) RETURN "hello", n.name
</pre>


## Neo4jを終了する

* プロセスを終了させればOK。
 - Linux/MacOS の場合：ctrl+C 等で終了させる
 - Windows の場合：起動中のjavaコンソールを閉じる


## データを初期化する

* data/ 以下の全データを削除すればOK。


##参考サイト

* [CL LAB Neo4j-グラフデータベースとは](http://www.creationline.com/lab/7168)
* [Neo4jで学ぶGraph DB入門](http://www.omotenashi-mind.com/index.php/Neo4j%E3%81%A7%E5%AD%A6%E3%81%B6Graph_DB%E5%85%A5%E9%96%80)
