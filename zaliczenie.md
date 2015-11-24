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

4. Wywietlenie postów z wynikiem wikszym od 3000. //Wyświetlono ostatni (największy) score.
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
 
5. Przykładowy dokument z kolekcji reddit
 ```sh
test> db.reddit.find({author: "vhisic"}).limit(1)
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
6. Grupowanie wzglęgdem atrybutu *author* + licznik wystąpień
Niestety nie da rady wyświetlić wyników.. 
```sh
2015-11-23T14:00:30.541+0100 group command failed: {
  "errmsg": "exception: group() can't handle more than 20000 unique keys",
  "code": 17203,
  "ok": 0
```

7. Wywświetlenie 5-ciu autorów na literę A, (pominięcie pierwszych 5-ciu).

```sh
test> db.reddit.find({author: /^a/}, {_id:0, author:1}).skip(5).limit(5)
{
  "author": "animationLand"
}
{
  "author": "allergictoapples"
}
{
  "author": "acini"
}
{
  "author": "andaag"
}
{
  "author": "adalab"
}
Fetched 5 record(s) in 1ms

```

8. Wyświetlenie 3 wpisów autora "zombie1939" (pominięcie pierwszych 10)
```sh
test> db.reddit.find({author: "zombie1939"}, {_id:0, body:1}).skip(10).limit(3)
{
  "body": "BRRRRRRRT\nflares\nflares\nflares\n"
}
{
  "body": "Gravity's Rainbow"
}
{
  "body": "i think i see the NSA's backdoor"
}

Fetched 3 record(s) in 245591ms
```

9. Wyświetlenie 3 wpisów z wynikiem 1024 lub 2048.
```sh
db.reddit.find({score : {$in: [1024, 2048]}},{_id:0, subreddit:1, score: 1}).limit(3)
{
  "subreddit": "hiphopheads",
  "score": 1024
}
{
  "score": 1024,
  "subreddit": "TumblrInAction"
}
{
  "subreddit": "AskReddit",
  "score": 2048
}
Fetched 3 record(s) in 13642ms
```
10. Wyświetlenie 5-ciu pierwszych wpisów autorów xanaxxanaxxanax i brazen
```sh
test> db.reddit.find({author: {$in: ["xanaxxanaxxanax", "brazen"]}}, {_id:0, author:1, body:1}).limit(5)
{
  "body": "I thought that is what the picture was about at first - like is it a Canadian thing to eat chips with utensils, and requiring two utensils at that.",
  "author": "brazen"
}
{
  "body": "Ice bong sounds nice.\n\nNo babysitter so will be at home puffing on the doja after the little one shleeps",
  "author": "xanaxxanaxxanax"
}
{
  "author": "brazen",
  "body": "I have this happen, too only on one computer and only on youtube.  It is not a hardware problem, as other's are claiming.  This computer dual boots Windows and it never happens in Windows.  I've asked about it and googling around I found another person ask about it with no useful response.  And of several Ubuntu desktops I've used, this is the only one it does it on.\n\nI suspect it's a driver issue.  My computer uses the Nvidia proprietary drivers, and I'm not sure if I've ever tried it with the opensource drivers."
}
{
  "author": "brazen",
  "body": "&gt; This is intense.\n\nNo those are just canopies."
}
{
  "author": "brazen",
  "body": "I worked a 50-60 hr/week job in IT while I studied for the MCAT that still left me with 4 hours to study after work and all day on the weekends.  But as others have said, it's kinda moot at this point considering all your retakes without much improvement.  Did you take practice tests?  You should not even consider retaking the MCAT until you are scoring much higher on the practice tests."
}
Fetched 5 record(s) in 61803ms
``` 

####Grupowania:
1. Grupowanie po wyniku (score) większym, równym 45, mniejszym niż 55
```sh
test> db.reddit.group({
   cond: {"score": {$gte: 45, $lt:55}} ,     
    key: {score: true} ,     
    initial: {body_count: 0 , total_body_len: 0} ,     
    reduce: function(doc, out) {out.body_count++; out.total_body_len += doc.body.length;} ,     
    finalize: function(out) { out.srednia_dl_wpisu  = out.total_body_len/ out.body_count; }     } );
```

**Po 45 minutach oczekiwania zwątpiłam..**
2. Grupowanie po score większym, równym 45, a mniejszym niż 46
```sh
test> db.reddit.group({    
    cond: {"score": {$gte: 45, $lt:46}} ,     
    key: {score: true} ,     
    initial: {body_count: 0 , total_body_len: 0} ,     
    reduce: function(doc, out) {out.body_count++; out.total_body_len += doc.body.length;} ,    
    finalize: function(out) { out.srednia_dl_wpisu  = out.total_body_len/ out.body_count; }     } );
[
  {
    "score": 45,
    "body_count": 17968,
    "total_body_len": 3277467,
    "srednia_dl_wpisu": 182.4057769367765
  }
]
```

