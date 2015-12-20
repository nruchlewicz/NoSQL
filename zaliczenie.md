##Zaliczenie, Ruchlewicz Natalia :panda_face: 

###Zadanie 2 EDA.
|Polecenie | MonboDB | Postgres|
|---------|---------|----------|
|Rozpakowanie i import| W jednej lini wpisujemy polecenie do rozpakowania i importu. Procesory są wykorzystwane naprzemiennie, równomiernie. Pamięć była wykorzystwana max. 20%. Dysk -100%, Było słychać jak pracuje.  | NAjpierw musimy rozpakować plik, następnie za pomocą programu *pgfutter* musimy zaimportować plik do postgresa. :-1:  |Procesory są mniej obciążnone niż w przypadku mongoDB, dysk był mocno obciążony. Laptop głośno pracował. |
|czas trwania | 121 minut | 145 minut|
|Zliczenie rekordów| Czas: natychmiast :+1: | Czas: ok 24 minut. :-1: |
|Inne polecenia| W mongoDB niektóre wyszukiwania zajmowały dużo czasu, komputer __Zamulał__ dysk był obciążony na 100%.Nie można było korzystać z lapotpa.. | W postgresie selecty róœnież zajmowały dużo czasu, ale znacznie więcej niż Mongo.  Komputer również __zamulał__ |
|Przegląd kolekcji| Używałam *MongoHacker* dzięki czemu rekordy były dobrze widoczne, "kolorowe", dobrze się czytało te dane. :+1: |Wszystkie dane w jednej lini, ciężko przeczytać cokolwiek :-1: |
|Obsługa poleceń| trudna składnia bardziej złożonych poleceń, dużó ({[]}), trzeba uważać by dobrze wszystko wpisać :+1: | łatwiejsza składnia, bardziej intuicyjna *select * from ... where*, lepsza znajomość poleceń, ze względu na korzystanie w poprzednich latach na zajęciach z postgresa. :+1:
|Ogólne wrażenie (0-5\*) | *** (Szkoda dysku :) )  | * (zbyt długie wyszukiwanie, zbyt skomplikowany import) |


####Przygotowanie bazy/kolekcji
1 . Pobrałam plik z bazy komentarzy Reddit (5.5GB)

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

-**_id** - id objektu zaimporotwanego do MongoDB

-**name, link_id, parent_id, subreddit_id, id** - rożne id, niepotrzebnie widoczne, nic nie wnosza do wyników wyszukiwania, tylko powoduja nadmiar danych. 

-**body**- tresc posta

-**creted_utc**- data w formacie string

-**score**- wynik

-**author**- Nick osoby, która utworzyla dany post

-**subreddit**- tytul posta

-**ups**- polubienia

-**controversiality**- czy temat jest kontrowersyjny

-**edited**- czy post byl edytowany.

![procesor2](https://github.com/nruchlewicz/NoSQL/blob/master/img/dysk2.jpg)

![pamiec2](https://github.com/nruchlewicz/NoSQL/blob/master/img/pamiec2.jpg)

**Obciążenie procesora i pamięci przy wyszukiwaniu. Nie były to wielkie obciążenia. Dysk pracował na 100%... **
2 . 
```sh
db.reddit.distinct("score").length
4635
```
3 . Wyświetlenie wszystkich autorów

Wyświetlenie 10 autorów ```(db.reddit.find({},{author:1}).limit(10)``` zajmuje *1ms*.

Wyświetlenie 100 autorów ```(db.reddit.find({},{author:1}).limit(100)``` zajmuje *8ms*.

Wyświetlenie 1000 autorów ```(db.reddit.find({},{author:1}).limit(1000)``` zajmuje *87ms*.

Wyświetlenie 10000 autorów ```(db.reddit.find({},{author:1}).limit(10000)``` zajmuje *814ms*.

Wyświetlenie 100000 autorów ```(db.reddit.find({},{author:1}).limit(100000)``` zajmuje *8226ms*.

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

4 . Wywietlenie postów z wynikiem wikszym od 4000. //Wyświetlono trzy rekordy: autora, treść ,tytuł i score)
```sh
test> db.reddit.find({score: { $gte: 4000}}, {_id:0, author:1, body:1, subreddit:1, score:1}))
{
  "body": "Marilyn Monroe being a size 12, 14, 16 in today's US sizes.\n\nMarilyn Monroe was a dressmaker pattern size 12, not a Lane Bryant size 12 (much less a 14 or 16, poor girl gets fatter the longer she's dead). Her measurements were 35-23-35 and her weight was about 120 on average at 5'5\". That's about a size 2-4 today.",
  "score": 4589,
  "subreddit": "AskReddit",
  "author": "idiosyncrassy"
}
{
  "body": "Men always think about sex every 4 to sex seconds.",
  "score": 4021,
  "subreddit": "AskReddit",
  "author": "medulla_oblongata"
}
{
  "subreddit": "AskReddit",
  "author": "[deleted]",
  "score": 4349,
  "body": "/╲/\\╭( ͡° ͡° ͜ʖ ͡° ͡°)╮/\\╱\\"
}

Fetched 550 record(s) in 1106623ms
 ```

 
5 . Zliczenie wpisów autoa "vhisic"

 ```sh
test> db.reddit.find({author: "vhisic"}).count()
#428
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
3. Agregacja, suma *score* ale autora *Brazen*
```sh
db.reddit.aggregate ([   
  { $match: { "author": "brazen"  } },   
  {  $group: { _id: "$author", total:{ $sum: "$score"  } } } ])
  
  {
  "result": [
    {
      "_id": "brazen",
      "total": 1178
    }
  ],
  "ok": 1
}

```
####Postgres 👀
Wcześniej pobrany plik z komentarzami bazy Reddit (5.5GB), rozpakowałam używając bunzip2. 
Następnie za pomocą [Pgfutter](https://github.com/lukasmartinelli/pgfutter) zaimporotwałam jsony
```sh
time | pgfutter --db postgres --user postgres --pw natalia json RC_2015_03
#29.47GB / 29.47GB [================================================] 100% 145m.604s
#53851542 rows imported into import.rc_2015_03

#real     121m3.423s
#user     145m4.604s
#sys       12m32.103s
```
#####Zliczenie rekordów count
```sh
select count(*) from import.rc_2015_03;
##53851542
```
Wszystkie rekordy zaimportowały się poprawnie, sprawdzenie ilości trwało bardzo długo.. ok 24 minut. 

Pierwszy Json (w jedej linijce)
```sh
1 | { "score_hidden": false,"name": "t1_cnas8zv", "link_id": "t3_2qyr1a", "body": "Most of us have some family members like this. *Most* of my family is like this. ", "downs": 0, "created_utc": "1420070400", "score": 14,"author": "YoungModern", "distinguished": null, "id": "cnas8zv", "archived": false, "parent_id": "t3_2qyr1a", "subreddit": "exmormon", "author_flair_css_class": null, "author_flair_text": null, "gilded": 0, "retrieved_on": 1425124282, "ups": 14,  "controversiality": 0, "subreddit_id": "t5_2r0gj", "edited": false}+
  |
(1 wiersz)
```
Wyświetlenie 3 wpisów autora __zombie1939__ z pominięciem pierwszych dziesięćiu
```sh
 SELECT data->>'body' AS body FROM import.rc_2015_03 WHERE data->>'author' = 'zombie1939' LIMIT 3 OFFSET 10;
        body
  -------------------------------------
   BRRRRRRRT\nflares\nflares\nflares\n
   Gravity's Rainbow
   i think i see the NSA's backdoor
  (3 wiersze)
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
**_id** - id z mongoDB

**loc type**- typ obiektu, punkt

**coordinates** - wspołrzędne geograficzne

**name** - nazwa stacji

**city** - nazwa mista 

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
[Mapka linia](https://github.com/nruchlewicz/NoSQL/blob/master/mapka_linia.geojson "Mapka String Szczecin-Rzeszów")

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

