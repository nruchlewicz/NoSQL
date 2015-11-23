##Zaliczenie, Ruchlewicz Natalia

###Zadanie 1 EDA.
####Przygotowanie bazy/kolekcji
1. Pobrałam plik

2. Zaimportowałam plik do mongoDB poleceniem:

```sh
time bunzip2 -c RC_2015-01.bz2 | mongoimport --drop --host 127.0.0.1 -d test -c reddit
#2015-11-17T20:04:00.161+0100 check 9 53851542
#2015-11-17T20:04:00.275+0100 imported 53851542 objects

#real     97m20.301s
#user     121m4.976s
#sys       4m29.768s
```

3. Historia Procesora:



image

4. Następnie połączyłam się z mongo. I przeszłam do bazy test i wybrałam kolekcję redit.

```sh
mongo
#MongoDB shell version: 2.6.3
#connecting to: test

show dbs
#local   0.078GB
#test   45.932GB

use test
#switched to db test

show collections
#reddit
#system.indexes
``` 

####Count
Sprawdziłam, czy wszystkie jsony zaimportowały się poprawnie, za pomocą polecenia:

```sh
db.reddit.count()
#53851542
```
Czyli ilość zaimportowanycj jsonów się zgadza.

####Przykładowe  zapytania

1. Znajdz pierwsze.
```sh
db.reddit.findOne()
{
  "_id": ObjectId("564b635081d89fc9eec8a4ac"),
  "score_hidden": false,
  "name": "t1_cnas8zv",
  "link_id": "t3_2qyr1a",
  "body": "Most of us have some family members like this. *Most* of my family is like this. ",
  "downs": 0,
  "created_utc": "1420070400",
  "score": 14,
  "author": "YoungModern",
  "distinguished": null,
  "id": "cnas8zv",
  "archived": false,
  "parent_id": "t3_2qyr1a",
  "subreddit": "exmormon",
  "author_flair_css_class": null,
  "author_flair_text": null,
  "gilded": 0,
  "retrieved_on": 1425124282,
  "ups": 14,
  "controversiality": 0,
  "subreddit_id": "t5_2r0gj",
  "edited": false
}
```
2. 
```sh
db.reddit.distinct("score").length
4635
```
3. Wyświetlenie wszystkich autorów

Wyświetlenie 10 autorów ```(db.reddit.find({},{author:1})``` zajmuje *1ms*.

Wyświetlenie 100 autorów ```(db.reddit.find({},{author:1})``` zajmuje *8ms*.

Wyświetlenie 1000 autorów ```(db.reddit.find({},{author:1})``` zajmuje *87ms*.

Wyświetlenie 10000 autorów ```(db.reddit.find({},{author:1})``` zajmuje *814ms*.

Wyświetlenie 100000 autorów ```(db.reddit.find({},{author:1})``` zajmuje *8226ms*.

Wyświetlenie wszyskich trwa ( *bardzo dłuuuuuugo* )

```sh
db.reddit.find({},{author:1})
(...)
{
  "_id": ObjectId("564b65b281d89fc9ee24caa3"),
  "author": "Solkre"
}
{
  "_id": ObjectId("564b65b281d89fc9ee24caa4"),
  "author": "aStarving0rphan"
}
{
  "_id": ObjectId("564b65b281d89fc9ee24caa5"),
  "author": "ZcSx"
}
{
  "_id": ObjectId("564b65b281d89fc9ee24caa6"),
  "author": "Steampunk_Moustache"
}
(...)
```

Wywietlenie postów z wynikiem wikszym od 3000. //Wyświetlono ostatni (największy) score.
```sh
test> db.reddit.find({score: { $gte: 3000}})
{
  "_id": ObjectId("564b7a1f81d89fc9eefe40e6"),
  "author": "thebestisyetocome",
  "retrieved_on": 1424281837,
  "edited": 1422762310,
  "archived": false,
  "subreddit": "funny",
  "score": 3518,
  "subreddit_id": "t5_2qh33",
  "link_id": "t3_2ucr4e",
  "controversiality": 0,
  "ups": 3518,
  "distinguished": null,
  "downs": 0,
  "created_utc": "1422748520",
  "author_flair_css_class": null,
  "name": "t1_co77bxd",
  "body": "Has to be husband's agreeing. \n\nIf more people find it helpful, it makes it more true!\n\nEdit: Anyone know why in the hell this is getting SO many upvotes? ",
  "author_flair_text": null,
  "gilded": 0,
  "id": "co77bxd",
  "score_hidden": false,
  "parent_id": "t1_co770jy"
}
Fetched 2510 record(s) in 671475ms
 ```
 
 Przykładowy dokument z kolekcji reddit
 ```sh
 natalia(mongod-2.6.3) test> db.reddit.find({author: "vhisic"}).limit(1)
{
  "_id": ObjectId("564b635081d89fc9eec8a4ae"),
  "score_hidden": false,
  "link_id": "t3_2qxefp",
  "name": "t1_cnas8zx",
  "created_utc": "1420070400",
  "downs": 0,
  "body": "Mine uses a strait razor, and as much as i love the clippers i love the razor so much more. Then he follows it up with a warm towel. \nI think i might go get a hair cut this week.",
  "distinguished": null,
  "id": "cnas8zx",
  "archived": false,
  "author": "vhisic",
  "score": 1,
  "subreddit": "AdviceAnimals",
  "parent_id": "t3_2qxefp",
  "retrieved_on": 1425124282,
  "ups": 1,
  "author_flair_css_class": null,
  "author_flair_text": null,
  "gilded": 0,
  "controversiality": 0,
  "subreddit_id": "t5_2s7tt",
  "edited": false
}
```
Grupowanie wzglęgdem atrybutu *author* + licznik wystąpień
Niestety nie da rady wyświetlić wyników.. 
```sh
2015-11-23T14:00:30.541+0100 group command failed: {
  "errmsg": "exception: group() can't handle more than 20000 unique keys",
  "code": 17203,
  "ok": 0
```
