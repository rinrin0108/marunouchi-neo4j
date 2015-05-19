1

      CREATE ({名前:'hoge'})

2

      CREATE (fuga{名前:'fuga'}) RETURN fuga

3

      CREATE (a{名前:'fuga'}) RETURN a

4

      CREATE (fuga{名前:'bar'}) RETURN fuga

5

      CREATE ({名前:'ネオ',種族:'人間',職業:'エンジニア',性別:'男'})

6

      CREATE (a:映画の登場人物{名前:'モーフィアス',種族:'人間',特徴:'ハゲ'})

7

      MATCH n DELETE n

8

      CREATE (neo:映画の登場人物{名前:'ネオ'}),(trinity:映画の登場人物{名前:'トリニティ'}),(neo)-[:気になる]->(trinity)

9

      CREATE (trinity)-[:救世主と信じる]->(neo)

10

      MATCH (neo{名前:'ネオ'}),(trinity{名前:'トリニティ'}) CREATE (neo)<-[:救世主と信じる]-(trinity)

11

      MATCH (neo{名前:'ネオ'}),(trinity{名前:'トリニティ'}) CREATE (neo)<-[:イチャつく]-(trinity)

12

      MATCH n DELETE n

13

      MATCH (a)-[b]-() DELETE a,b

14

      create (a:学生 {name:'Aさん'}),(b:学生 {name:'Bさん'}),(c:学生 {name:'Cさん'}),(d:学生 {name:'Dさん'}),(a)-[:友達]->(b),(b)-[:友達]->(c),(b)-[:友達]->(d)

15

      MATCH (a{name:'Aさん'}),(a)-[:友達]->(friend) RETURN friend

16

      MATCH a,(a)-[:友達]->(friend) WHERE a.name='Aさん' RETURN friend

17

      MATCH (a)-[:友達]->() RETURN a

18

      MATCH (a)-[:友達]->(b) RETURN b

19

      MATCH (a)-[:友達]->() RETURN DISTINCT a

20

      MATCH (a{name:'Aさん'}),(a)-[:友達]->(b),(b)-[:友達]->(c) RETURN c


21

      MATCH (a{name:'Aさん'}),(a)-[:友達*2]->(c) RETURN c


22

      MATCH (a{name:'Aさん'}),(a)-[:友達*1..2]->(c) RETURN c

23

      MATCH (a{name:'Aさん'}),l=(a)-[:友達*1..2]->(c) RETURN c,length(l)

24

      MATCH (a{name:'Aさん'}),l=(a)-[:友達*1..2]->(c) RETURN c,length(l) ORDER BY length(l) DESC

25

      MATCH (a)-[]-() RETURN a

26

      MATCH a WHERE NOT (a)-[]-() RETURN a

27

      CREATE (k:俳優{name:'キアヌリーブス'})

28

      MATCH (a)-[b]-() DELETE a,b

29
      MATCH (a{名前:'キアヌリーブス'}) DELETE a

30

      CREATE (a:学生{name:'Aさん'}),(b:学生{name:'Bさん'}),(c:学生{name:'Cさん'}),(a)-[:友達]->(b),(a)<-[:友達]-(b),(a)-[:友達]->(c),(a)<-[:友達]-(c),(b)-[:友達]->(c),(b)<-[:友達]-(c)

31

      MATCH (a{name:'Aさん'}),(a)-[:友達*6]->(b) RETURN b
⇒謎？

32

      MATCH (a{name:'Aさん'}),(b{name:'Bさん'}),(c{name:'Cさん'}) SET a.gender='男性',b.gender='男性',c.gender='女性'

33

      MATCH (a{name:'Aさん'}),(b{name:'Bさん'}),(c{name:'Cさん'}) SET a.gender=NULL,b.gender=NULL,c.gender=NULL

34

      MATCH (a{name:'Aさん'}),(c{name:'Cさん'}) SET c.gender=a.gender RETURN c

35

      MATCH (a{name:'Aさん'}),(b{name:'Bさん'}),(a)-[r]-(b) SET r.レベル='親友'
