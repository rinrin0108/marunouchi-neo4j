#第一回丸の内Neo4j勉強会(CRUD操作の方)


##### 文責 : Takumi Kitazawa

間違いや指摘事項がある場合は[ここ](https://www.facebook.com/takumi.kitazawa.3)にこっそり教えてください。


##1.導入

基本的に公式からtarを落として来て使えばよい。使えばよい。

現行の最新版は2.2.1。→勉強会ではこれ使います。

Java7以降が必要なので事前に入れておくこと。

***
###1.1 Linux/Mac OS X
* ダウンロードしたtarを展開してできたディレクトリ内の実行ファイルを使って起動(2.2.1)


      $bin/neo4j start

* 起動後ブラウザからlocalhost://7474に接続
***
###1.2 Mac OS X(Homebrew)

* brewコマンドでインストール(stable2.2.1、devel2.3.0)。
(~~めんどくさくてやってない~~)

⇒確認の後、記載。多分コマンドは下記

      $brew install neo4j

* コマンドで起動


      neo4j start

***

###1.3 Windows

バイナリを展開して実行。多分。~~(めんどくさくてやってない。)~~

⇒確認の後、記載。

***

###1.4 Docker
* tpires/neo4j辺りを引っ張ってくる。  

***

##2. 基本的な情報とか操作方法とか
####2.1 初起動時

* user名/passwordを求められるので、
記載があるようにneo4j/neo4jを入力してログイン。

* パスワードを変更。

* チュートリアル等が表示されるのでお好みで実施を。

***

####2.2 基本的な情報とか操作方法とか

* 画面上部のコンソールにCypherを打ち込むことで操作する。
  * shift+Enterで改行が可能
  * 実行すると画面中央のタイムライン的な何かにその結果が表示される。


* 画面左のメニューからも操作やマニュアルの参照等が可能
  * 一番上のメニュー(ノードっぽいの)
    * 現在登録されているlabel、relation、property、DBのパスの参照が出来る。それぞれのlabelやrelationを押すことでそのリストなどをタイムラインに表示できる。
  * 上から二番目のメニュー(お気に入りっぽいの)
    * 基本コマンドの雛形やREST API、現在の設定などをタイムラインに
    表示できる。
    * グラフのスタイルシートやCSVでのデータインポートが可能らしい。(未実施)
    * タイムラインのお気に入りっぽいボタンを押すことでこのメニュー内にクエリを保存出来る。
    * お気に入りディレクトリの作成などもできる。
  * 上から三番目のメニュー(インフォメーションっぽいの)
    * 公式マニュアルへのリンクがある他、操作ガイドやヘルプなどをタイムラインに表示できる。
  * 一番下のメニュー(連絡先っぽいの)
    * 連絡先、ライセンス情報、などなどなどなど。


* 画面中央のタイムライン
  * 問い合わせ結果のグラフ画面は、何も無い所をdrag&dropすることでぐりぐり動かせる。
ノードもdrag&dropでぐりぐり動かせる。楽しい。
  * タイムライン左のGraphボタン、Rowsボタンでクエリ結果の見方をグラフ⇔テキストに切り替えることができる。
  * ノードにカーソルを合わせるとプロパティ情報が参照できる。
  * ノードダブルクリックすると隣接ノードが飛び出る。(問い合わせ結果自体は変化しない)
  * 右上のボタンでフルスクリーンにしたり問い合わせ結果をダウンロード出来たりする。
  * グラフ上部に表示されたノードのラベルや、リレーションのタイプを押すと、
  ノードやリレーションの矢印の大きさを変えるメニューが表示される。

***
##3. CRUD操作

* SQLで馴染みのあるLIMIT、ORDER BY、DISTINCTとかは使える。
[公式ガイドのその辺り](http://neo4j.com/docs/stable/query-read.html)を参照のこと。

***
###[Create]RUD
####★ノードの作成
#####CREATE ( ノード識別子 {プロパティ})

※識別子は指定しなくてもOK

[識別子無しの例]

    CREATE ({name:'hoge'})

[プロパティを複数指定する例]

    CREATE ({name:'hoge',type:'ghost'})


[ノード識別子有りの例]

    CREATE (hoge{name:hoge})
※ノード識別子は永続的に付けられるわけではないので、上記の例単体では意味が
無い。(後述のリレーションの作成や、値をRETURNしたい時などに利用する。)

####★リレーションの作成
#####CREATE (ノード識別子)-[リレーション名]->(ノード識別子)

※既存のノードに対してリレーションの作成をする場合、ノード識別子をマッピングする必要があることに注意

[ノードの生成と同時にリレーションを作成する場合]

    CREATE (hoge {name:'hoge'}),(fuga {name:'fuga'}),(hoge)-[:KNOWS]->(fuga),(fuga)-[:KNOWS]->(hoge)

※(hoge {name:'hoge'})、(fuga{name:'fuga'})の文にて、作成したノード({name:hoge})、({name:fuga})をhoge、fugaにマッピングしている。

下記コマンドを別々に実行すると、二つ目のコマンドでは新しい
(プロパティ無しの)ノード2つとそのリレーションが作成される。

    CREATE (hoge {name:'hoge'}),(fuga {name:'fuga'})

    CREATE (hoge)-[:KNOWS]->(fuga),(fuga)-[:KNOWS]->(hoge)

[既存のnodeに対してrelationを生成する場合の例]

    MATCH a,b WHERE a.name='hoge' AND b.name='fuga' CREATE (a)-[:KNOWS]->(b)
※探索結果をマッピングした後、リレーションの作成を行っている。


***
###C[Read]UD
####★全てのnode、relationを探索

    MATCH n RETURN n

※(~~Webコンソール上で遊んでるだけなら~~)よく使う。

※※問い合わせ結果が大量になりそうな場合はLIMITを付けること。


####★特定条件に一致したノード・リレーションのみを探索
#####MATCH ノード識別子 WHERE 条件 RETURN 識別子
または
#####MATCH ノード識別子orリレーション識別子を用いた条件 RETURN ノード識別子

[nameプロパティの値がhogeのノードを探索]

    MATCH a WHERE a.name='hoge' RETURN a
    または
    MATCH ( a{name:'hoge'} ) RETURN a

[nameプロパティの値がhoge、fugaのノードの関係を探索]

    MATCH (a{name:'hoge'}),(b{name:'fuga'}),(a)-[r]->(b) RETURN r
※ブラウザでこれを問い合わせた結果をRowsの方で見ると何故かrがemptyになっている。。。
ちなみにシェル上で叩くと下記のような結果が返ってくる。

|r|

| :KNOWS[24]{} |

####★開始ノードを指定した探索

※過去にはSTARTを使っていたが今は過去のインデックスを使わない限り
STARTは使わなないらしい。オワコン。

#####準備用データ作成クエリ

    create (hoge {name:'hoge'}),(fuga {name:'fuga'}),(foo {name:'foo'}),(bar{name:'bar'}),(botti{name:'botti'})(hoge)-[:KNOWS]->(fuga),(fuga)-[:KNOWS]->(foo),(fuga)-[:KNOWS]->(bar)


[nameプロパティの値がhogeのノードが知っているノードの
更に知っているノードを特定する例]

    MATCH (a)-[:KNOWS]->(b),(b)-[:KNOWS]->(c) WHERE a.name='hoge' RETURN c
    または
    MATCH (a)-[:KNOWS*2]->(c) WHERE a.name='hoge' RETURN c

[nameプロパティの値がhogeのノードが知っているノードと、そのノードが
知っているノードを特定する例]

    MATCH (a)-[:KNOWS*1..2]->(c) WHERE a.name='hoge' RETURN c

    深さも一緒に出したりするとこうなる。
    MATCH l=(a)-[:KNOWS*1..2]->(c) WHERE a.name='hoge' RETURN c,length(l) AS depth (ORDER BY depth DESC)

    ついでにノードでなく、名前を返すようにすると下みたいになる。
    MATCH l=(a)-[:KNOWS*1..2]->(c) WHERE a.name='hoge' RETURN c.name AS name,length(l) AS depth ORDER BY depth DESC

[nameプロパティの値がhogeのノードが知っているノードを
知っているノードを特定する例]

    MATCH (a)-[:KNOWS]->(b),(b)-[:KNOWS]->(c) WHERE a.name='hoge' RETURN c (ORDER BY c.name DESC)

[他のノードを知っているノードを特定する例]

    MATCH (a)-[:KNOWS]->() RETURN a

[何かしらの関係を持っているノードを特定する例]

    MATCH (a)-[]-() RETURN a

[関係を全く持たないノードを特定する例]

    MATCH a WHERE NOT (a)-[]-() RETURN a

***
###CR[Update]D

基本的にSETを使う。
SETの内容を複数同時に追加したい場合は,でつなげる。

[既存のノードのプロパティ値を変更する例]

    MATCH a WHERE a.name='fuga' SET a.name='fuge' RETURN a
    態々、上みたいにWHEREで記載しないでも下記でもいける
    MATCH (a{name:'fuga'}) SET a.name='fuge' RETURN a


[既存のノードに新しいプロパティを追加する例]

    MATCH a WHERE a.name='fuge' SET a.type='ghost' RETURN a
    ※typeプロパティを追加する場合。

[既存のノードからプロパティを削除する例]

    MATCH a WHERE a.name='fuge' SET a.type=NULL RETURN a
    ※typeプロパティを削除する場合

[既存のノードからプロパティをコピーする例]
    MATCH (hoge{name:'hoge'}),(fuga(name:'fuga')) SET hoge=fuga RETURN hoge
    ※fugaのプロパティをhogeにコピーする場合

既存のリレーションのプロパティ値を変更する例は無さそう。
relationship→"変更される"ものではなく、"消える"、"追加される"ものということ？


***
###CRU[Delete]

リレーションを持たないノードに関してはそのまま消せる。

[リレーションを持たないノードを消す例]

    MATCH (botti {name:'botti'}) DELETE botti

relationshipが残っているとnodeをDELETEできないので、
全部消したい時はrelationshipも一緒に消す。

    MATCH n OPTIONAL MATCH (n)-[r]-() DELETE n,r



***
##4. その他
####★ノードIDの利用
* ノード固有に振られるIDが使いたい場合はid()を使ってください。


      MATCH (a{name:'hoge'}) WHERE id(a)=43 RETURN a

####★コンソールの利用
* 展開ディレクトリ内のneo4j-shellを実行すればいい。


      $bin/neo4j-shell


Cypherを実行すると結果が下みたいに返ってくる

      neo4j-sh (?)$ match (a)-[r]-(b) return a,r,b;
      +--------------------------------------------------------------+
      | a                     | r            | b                     |
      +--------------------------------------------------------------+
      | Node[44]{name:"fuge"} | :KNOWS[21]{} | Node[43]{name:"fuge"} |
      | Node[45]{name:"foo"}  | :KNOWS[22]{} | Node[44]{name:"fuge"} |
      | Node[46]{name:"bar"}  | :KNOWS[23]{} | Node[44]{name:"fuge"} |
      | Node[43]{name:"fuge"} | :KNOWS[21]{} | Node[44]{name:"fuge"} |
      | Node[44]{name:"fuge"} | :KNOWS[22]{} | Node[45]{name:"foo"}  |
      | Node[44]{name:"fuge"} | :KNOWS[23]{} | Node[46]{name:"bar"}  |
      +--------------------------------------------------------------+
***
##5. 参考文献

####書籍
* グラフデータベース(オライリージャパン、ISBN978-4-87311-714-0)

####Webページ
* [Neo4j(公式)]

* [グラフDBのNeo4jを1日触ってみた(Wantedly 開発チームブログ)](http://engineer.wantedly.com/)

* [Neo4j-グラフデータベースとは(CREATIONLINE,INC.)](http://www.creationline.com/lab/7168)

* [Neo4jで遊びながらここ最近のドキドキ！プリキュアを振り返ってみよう(igreque::Info)](http://the.igreque.info/posts/2013-12-22-precure-world-on-neo4j.html)
