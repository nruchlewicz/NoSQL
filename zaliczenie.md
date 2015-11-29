##Zaliczenie, Ruchlewicz Natalia :panda_face: 

###Zadanie 2 EDA.
####Przygotowanie bazy/kolekcji
1 . Pobrałam plik

2 . Zaimportowałam plik do mongoDB poleceniem:

```sh
time bunzip2 -c RC_2015-01.bz2 | mongoimport --drop --host 127.0.0.1 -d test -c reddit
#2015-11-17T20:04:00.161+0100 check 9 53851542
#2015-11-17T20:04:00.275+0100 imported 53851542 objects

#real     97m20.301s
#user     121m4.976s
#sys       4m29.768s
```
![import](https://github.com/nruchlewicz/NoSQL/blob/master/img/import.jpg)

3 . Historia Procesora:

![Procesy](https://github.com/nruchlewicz/NoSQL/blob/master/img/dysk1.jpg)

![Pamiec](https://github.com/nruchlewicz/NoSQL/blob/master/img/pamiec1.jpg)

**Obserwacje: Procesory były obciążane od 20 do 98 procent, równomiernie. Pamięć była wykorzystywana do max. 20%. Dysk był obciżony nawet do 100%.**

4 . Następnie połączyłam się z mongo. I przeszłam do bazy test i wybrałam kolekcję redit.

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

1 . Znajdz pierwsze.
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
![procesor2](https://github.com/nruchlewicz/NoSQL/blob/master/img/dysk2.jpg)

![pamiec2](https://github.com/nruchlewicz/NoSQL/blob/master/img/pamiec2.jpg)

**Obciążenie procesora i pamięci przy wyszukiwaniu. Nie były to wielkie obciążenia. Dysk pracował na 100%... **
2 . 
```sh
db.reddit.distinct("score").length
4635
```
3 . Wyświetlenie wszystkich autorów

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

4 . Wywietlenie postów z wynikiem wikszym od 3000. //Wyświetlono ostatni (największy) score.
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
 ![find](https://github.com/nruchlewicz/NoSQL/blob/master/img/find.jpg)
 
5 . Przykładowy dokument z kolekcji reddit

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

6 . Grupowanie wzglęgdem atrybutu *author* + licznik wystąpień
Niestety nie da rady wyświetlić wyników.. 
```sh
2015-11-23T14:00:30.541+0100 group command failed: {
  "errmsg": "exception: group() can't handle more than 20000 unique keys",
  "code": 17203,
  "ok": 0
```

7 . Wywświetlenie 5-ciu autorów na literę A, (pominięcie pierwszych 5-ciu).

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

8 . Wyświetlenie 3 wpisów autora "zombie1939" (pominięcie pierwszych 10)
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

9 . Wyświetlenie 3 wpisów z wynikiem 1024 lub 2048.
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
10 . Wyświetlenie 5-ciu pierwszych wpisów autorów xanaxxanaxxanax i brazen
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
1 . Grupowanie po wyniku (score) większym, równym 45, mniejszym niż 55
```sh
test> db.reddit.group({
   cond: {"score": {$gte: 45, $lt:55}} ,     
    key: {score: true} ,     
    initial: {body_count: 0 , total_body_len: 0} ,     
    reduce: function(doc, out) {out.body_count++; out.total_body_len += doc.body.length;} ,     
    finalize: function(out) { out.srednia_dl_wpisu  = out.total_body_len/ out.body_count; }     } );
```

**Po 45 minutach oczekiwania zwątpiłam..**
2 . Grupowanie po score większym, równym 45, a mniejszym niż 46
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

###Zadanie 2d GeoJSON

Pobrałam GeoJsony zestacjami paliw Orlen.
####Import do MongoDB
```sh

time mongoimport -d GeoOrlen -c stacje < orlen.json
#connected to: 127.0.0.1
#2015-11-25T11:10:22.170+0100 check 9 1245
#2015-11-25T11:10:22.170+0100 imported 1245 objects

#real	0m0.217s
#user	0m0.040s
#sys	0m0.020s
```
####Przykłady
Do utworzenia mapki korzystalam z [geojson.io](  http://geojson.io/#map=2/20.0/0.0)

1 . Po zalogowaniu sie do bazy **mongo**, dodanie geo-indeksu do kolekcji stacje.
```sh
db.stacje.ensureIndex({"loc": "2dsphere"})
{
  "createdCollectionAutomatically": false,
  "numIndexesBefore": 1,
  "numIndexesAfter": 2,
  "ok": 1
}
```

2 . Wyświetlenie przykładowego jsona.
```sh
GeoOrlen> db.stacje.find().skip(5).limit(1)
{
  "_id": ObjectId("5655890eda9f01fcd706881c"),
  "loc": {
    "type": "Point",
    "coordinates": [
      18.72245,
      53.48882
    ]
  },
  "name": "Stacje paliw Orlen",
  "city": "Michale"
}
Fetched 1 record(s) in 0ms
```

#####3 . Point
Stacje w odleglosci 20km od wroclawia. 
```sh
> db.stacje.find({loc: {$near: {$geometry: {type: "Point", coordinates: [ 17.037048, 51.110419]}, $maxDistance: 20000}}}).skip(1)

{ "loc" : { "type" : "Point", "coordinates" : [17.022860,51.116420] }, "name" : "Stacje paliw Orlen", "city" : "Wrocław" }
{ "loc" : { "type" : "Point", "coordinates" : [17.092880,51.141100] }, "name" : "Stacje paliw Orlen", "city" : "Wrocław" }
{ "loc" : { "type" : "Point", "coordinates" : [16.969750,51.050260] }, "name" : "Stacje paliw Orlen", "city" : "Bielany Wrocławskie" }
{ "loc" : { "type" : "Point", "coordinates" : [17.070970,51.114250] }, "name" : "Stacje paliw Orlen", "city" : "Wrocław" }
{ "loc" : { "type" : "Point", "coordinates" : [17.046440,51.126630] }, "name" : "Stacje paliw Orlen", "city" : "Wrocław" }
{ "loc" : { "type" : "Point", "coordinates" : [16.992590,51.098040] }, "name" : "Stacje paliw Orlen", "city" : "Wrocław" }
{ "loc" : { "type" : "Point", "coordinates" : [16.882910,51.144160] }, "name" : "Stacje paliw Orlen", "city" : "Wrocław" }
{ "loc" : { "type" : "Point", "coordinates" : [17.117040,51.113720] }, "name" : "Stacje paliw Orlen", "city" : "Wrocław" }
{ "loc" : { "type" : "Point", "coordinates" : [16.966460,51.130190] }, "name" : "Stacje paliw Orlen", "city" : "Wrocław" }
{ "loc" : { "type" : "Point", "coordinates" : [17.057960,51.094610] }, "name" : "Stacje paliw Orlen", "city" : "Wrocław" }
{ "loc" : { "type" : "Point", "coordinates" : [17.058330,51.112190] }, "name" : "Stacje paliw Orlen", "city" : "Wrocław" }
{ "loc" : { "type" : "Point", "coordinates" : [17.029400,51.145880] }, "name" : "Stacje paliw Orlen", "city" : "Wrocław" }
{ "loc" : { "type" : "Point", "coordinates" : [17.018120,51.106770] }, "name" : "Stacje paliw Orlen", "city" : "Wrocław" }
{ "loc" : { "type" : "Point", "coordinates" : [17.127463,51.041774] }, "name" : "Stacje paliw Orlen", "city" : "Siechnice" }
{ "loc" : { "type" : "Point", "coordinates" : [17.190342,51.066131] }, "name" : "Stacje paliw Orlen", "city" : "Gajków" }
{ "loc" : { "type" : "Point", "coordinates" : [16.966174,51.213621] }, "name" : "Stacje paliw Orlen", "city" : "Szewce" }
{ "loc" : { "type" : "Point", "coordinates" : [16.790780,51.158080] }, "name" : "Stacje paliw Orlen", "city" : "Lutynia" }
{ "loc" : { "type" : "Point", "coordinates" : [16.833170,50.987760] }, "name" : "Stacje paliw Orlen", "city" : "Gniechowice" }
``` 
[Mapka Point](https://github.com/nruchlewicz/NoSQL/blob/master/mapka_punkt.geojson "Mapka point's Wroclaw")

10 najbliższych stacji od punktu 
```sh
var pkt= {
 "type" : "Point", 
    "coordinates" : [ 19.02995109558105,53.9202633713449 ] 
};

db.stacje.find({ loc: {$near: {$geometry: pkt}}},{_id:0,  city:1}).limit(5).toArray()
[
  {
    "city": "Sztum"
  },
  {
    "city": "Malbork"
  },
  {
    "city": "Gniew"
  },
  {
    "city": "Prabuty"
  },
  {
    "city": "Kwidzyn"
  }
]
```
[Mapka](https://github.com/nruchlewicz/NoSQL/blob/master/point.geojson "Mapka Point's")

#####4 . Line String. Wywietlenie stacji na lini Szczecin- Rzeszów

```sh
db.stacje.find({loc: {$geoIntersects: {$geometry: {type: "LineString", coordinates: [[14.570300,53.442940] ,[21.994270,50.057750]]}}}},{_id=0, city:1})

"city" : "Szczecin" }
"city" : "Rzeszów" }
```
[Mapka linia] (www_)

#####5 .Polyon. Wyświetlenie stacji(miejscowosci) na danym obszarze.
```sh
db.stacje.find({ loc: {$geoWithin : { $geometry: { type : "Polygon",  coordinates: [
 [ [
 18.72894287109375,
 53.71296473440685
 ],
 [
 18.72894287109375,
 54.10611237685854
 ],
 [
 19.47601318359375,
 54.10611237685854
 ],
 [
 19.47601318359375,
 53.71296473440685
 ],
 [
 18.72894287109375,
 53.71296473440685
 ]
 ]
 ] } } }},{_id:0, city:1} )
 
{
  "city": "Malbork"
}
{
  "city": "Tczew"
}
{
  "city": "Tczew"
}
{
  "city": "Gniew"
}
{
  "city": "Sztum"
}
{
  "city": "Kwidzyn"
}
{
  "city": "Prabuty"
}

```

[Mapka1](https://github.com/nruchlewicz/NoSQL/blob/master/mapka.geojson "Mapka polygon")

####Postgres - później ;) 👀
