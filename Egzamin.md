##Egzamin, Ruchlewicz Natalia

1. Import
2. Rozgrzewka- proste zapytania
3. JavaScript
4. Grupowanie
5. Agregacja



###Import
Do eksperymentów użyłam niezbyt dużego pliku [restauracje.json](https://dl.dropboxusercontent.com/u/15056258/mongodb/restaurant.json). Obliczenia wykonywałam na tym samym sprzecie co zaliczenia. (z tym, że średną ocenę restauracji wykonałam na mongo 3.0.6)
Do utworzenia wykresów użyłam narzędzia do tworzenia [wykresów](http://www.highcharts.com/)

Zaimportowanie do bazy MongoDB
```sh
time mongoimport_ -d restauracje -c restauracje < restauracje.json

connected to: 127.0.0.1
2015-12-29T19:14:29.313+0100 check 9 2548
2015-12-29T19:14:29.323+0100 import_ed 2548 objects

real    0m0.908s
user    0m0.116s
sys    0m0.024s
```

Sprawdzenie, czy wszystkie jsony zainstalowały się poprawnie.
``` sh
use restauracje

#switched to db restauracje
restauracje> db.restauracje.count()
#2548
```

###Rozgrzewka 
####Proste zapytania
Proste zapytania:

Wyświetl pierwsze
```sh
db.restauracje.findOne()
{
  "_id": ObjectId("55f14312c7447c3da7051b26"),
  "URL": "http://www.just-eat.co.uk/restaurants-cn-chinese-cardiff/menu",
  "address": "228 City Road",
  "address line 2": "Cardiff",
  "name": ".CN Chinese",
  "outcode": "CF24",
  "postcode": "3JH",
  "rating": 5,
  "type_of_food": "Chinese"
}

```
- **_id** - id obiektu nadane w MongoDB
- **URL** - adres strony internetowej danej restauracji
- **adress** i **adress line 2** - dokadny adres restauracji
- **name** - nazwa restauracji
- **outcode** i **postcode** - kody pocztowe
- **rating**- ranking restauracji
- **Type_of_food**- typ serwowanego jedzenia.

**Dlugosc nazwy**
```sh
db.restauracje.distinct("name").length
#1586
```
**Rodzaje serwowanego jedzenia w restauracjach **
```sh
db.restauracje.distinct("type_of_food").sort()
[
  "*NEW*",
  "Afghan",
  "African",
  "American",
  "Arabic",
  "Azerbaijan",
  "Bagels",
  "Bangladeshi",
  "Breakfast",
  "Burgers",
  "Cakes",
  "Caribbean",
  "Chicken",
  "Chinese",
  "Curry",
  "Desserts",
  "English",
  "Ethiopian",
  "Fish & Chips",
  "Greek",
  "Grill",
  "Healthy",
  "Ice Cream",
  "Japanese",
  "Kebab",
  "Korean",
  "Lebanese",
  "Mediterranean",
  "Mexican",
  "Middle Eastern",
  "Milkshakes",
  "Moroccan",
  "Nigerian",
  "Pakistani",
  "Pasta",
  "Peri Peri",
  "Persian",
  "Pick n Mix",
  "Pizza",
  "Polish",
  "Portuguese",
  "Punjabi",
  "Russian",
  "Sandwiches",
  "South Curry",
  "Spanish",
  "Sri-lankan",
  "Sushi",
  "Thai",
  "Turkish",
  "Vegetarian",
  "Vietnamese"
]
```
Jak widać jest dosyć duży wybór jedzenia w różnych restauracjach. Każdy znajdzie jakąś restaurację dla Siebie. 
####Zapytania z rzutowaniem i sortowaniem
**Posortowanie restauracji wg. nazwy i wywietlenie pierwszych 10**
```sh
restauracje> db.restauracje.find({}, {name: 1, _id: 0}).sort({name:1}).limit(10)
{
  "name": ".CN Chinese"
}
{
  "name": "@ Thai"
}
{
  "name": "@ Thai Restaurant"
}
{
  "name": "@ Thai Restaurant"
}
{
  "name": "@Indian.com"
}
{
  "name": "007 Takeaway"
}
{
  "name": "042 Restaurant & Bar"
}
{
  "name": "042 Restaurant & Bar"
}
{
  "name": "042 Restaurant & Bar"
}
{
  "name": "1 2 3 Chinese"
}
Fetched 10 record(s) in 333ms
```
niektóre nazwy restauracji się powtarzają. 

```sh
db.restauracje.find({}, {name: 1, _id: 0}).sort({name: -1}).skip(120).limit(10)
{
  "name": "A&A Peri Peri"
}
{
  "name": "A&S Fast Food"
}
{
  "name": "A.J's Pizza"
}
{
  "name": "Ă¨kó Food Market"
}
{
  "name": "Ă¨kó Food Market"
}
{
  "name": "ÂŁ1 Fish Shop"
}
{
  "name": "A1 Balti House"
}
{
  "name": "A1 Balti House"
}
{
  "name": "A1 Pizzas"
}
{
  "name": "A1 Rice 'N' Spice"
}
```
Nazwy posortowane alfabetyxcznie, i wyświetlne od końca dają dziwny rezultat.. Jest inne kodowanie i na końcu alfabetu nie jest "Z" tylko A1? oraz inne dziwne znaczki. 

Restauracje zaczynające się na 3. Wyświetlnie pierwszych trzech.
```sh
db.restauracje.find({"name": /^3/}, {_id: 0}).sort({name: 1}).limit(3)
{
  "URL": "http://www.just-eat.co.uk/restaurants-3chefsfishbar-de21/menu",
  "address": "47 Roosevelt Avenue",
  "address line 2": "Derby",
  "name": "3 Chef Fish Bar",
  "outcode": "DE21",
  "postcode": "6JR",
  "rating": 4.5,
  "type_of_food": "Fish & Chips"
}
{
  "URL": "http://www.just-eat.co.uk/restaurants-3chefs-de23/menu",
  "address": "558 Burton Road",
  "address line 2": "Littleover",
  "name": "3 Chefs",
  "outcode": "DE23",
  "postcode": "6FP",
  "rating": 4.5,
  "type_of_food": "American"
}
{
  "URL": "http://www.just-eat.co.uk/restaurants-3in1dunoon-pa23/menu",
  "address": "45 Argyll Street DUNOON",
  "address line 2": "Argyll",
  "name": "3 In 1 Dunoon",
  "outcode": "PA23",
  "postcode": "7HG",
  "rating": 5,
  "type_of_food": "Curry"
}
```
Restauracje serwujące polskie jedzenie:

```sh
restauracje> db.restauracje.find({type_of_food: "Polish"},{_id: 0})
{
  "URL": "http://www.just-eat.co.uk/restaurants-ani-mag-heavenly-food-walker/menu",
  "address": "73 Shields road",
  "address line 2": "Newcastle",
  "name": "Ani-Mag Heavenly Food",
  "outcode": "NE6",
  "postcode": "1DL",
  "rating": 5.5,
  "type_of_food": "Polish"
}
{
  "URL": "http://www.just-eat.co.uk/restaurants-bartskebab-bt43/menu",
  "address": "96 Lower Mill Street",
  "address line 2": "Ballymena",
  "name": "Barts Kebab",
  "outcode": "BT43",
  "postcode": "5AF",
  "rating": 4.5,
  "type_of_food": "Polish"
}
Fetched 2 record(s) in 2ms

```
jak widać można zjeść też polskie jedzenie za granicą.. Niestety na 2584 restauracje tylko 2 serwują polskie jedzenie. 

Wyświetlenie restauracji z rankingiem większym, równym 6.
```sh
db.restauracje.find({rating: {$gte: 6}},{_id: 0, name: 1, rating:1 }).limit(3)
{
  "name": "@Indian.com",
  "rating": 6
}
{
  "name": "007 Takeaway",
  "rating": 6
}
{
  "name": "Aarthi",
  "rating": 6
}

```


R-cje z rankingiem 1<= x <=2
```sh
db.restauracje.find({rating: {$gte: 1, $lte: 2}},{_id: 0, name: 1, rating:1 }).limit(3)
{
  "name": "Al Dixi Fried Chicken & Grill House",
  "rating": 1
}
{
  "name": "Alaowerri Restaurant",
  "rating": 1
}
{
  "name": "Beiteddine",
  "rating": 1.5
}
```

Jak widać restauracje maja ranking najwyższy 6, oraz najniższy 1. 
Jest duża rozpiętość rankingu w tych restauracjach. 
###JavaScript

Restauracje z nazwą dłuższą niż 1, krótszą, równą  3.
```js
db.restauracje.find({$where: "(this.name.length >=1) && (this.name.length <= 3)" })
{
  "_id": ObjectId("55f14312c7447c3da7051c5e"),
  "URL": "http://www.just-eat.co.uk/restaurants-afc-ln5/menu",
  "address": "376 High Street",
  "address line 2": "Lincoln",
  "name": "AFC",
  "outcode": "LN5",
  "postcode": "7RY",
  "rating": 4.5,
  "type_of_food": "Pizza"
}
{
  "_id": ObjectId("55f14312c7447c3da7051c5f"),
  "URL": "http://www.just-eat.co.uk/restaurants-afc-lu1/menu",
  "address": "47/53 Bute Street",
  "address line 2": "Luton",
  "name": "AFC",
  "outcode": "LU1",
  "postcode": "2EP",
  "rating": 4.5,
  "type_of_food": "Kebab"
}
{
  "_id": ObjectId("55f14312c7447c3da7051c60"),
  "URL": "http://www.just-eat.co.uk/restaurants-afc-pe1/menu",
  "address": "1241 Bourges Boulevard",
  "address line 2": "Cambridgeshire",
  "name": "AFC",
  "outcode": "PE1",
  "postcode": "2AU",
  "rating": 4.5,
  "type_of_food": "Chicken"
}
{
  "_id": ObjectId("55f14313c7447c3da70520b6"),
  "URL": "http://www.just-eat.co.uk/restaurants-bandnkebabhouse/menu",
  "address": "7 High Street",
  "address line 2": "Long Eaton",
  "name": "B&N",
  "outcode": "NG10",
  "postcode": "1HY",
  "rating": 5.5,
  "type_of_food": "Kebab"
}

#Fetched 4 record(s) in 25ms
```

Restauracje z rankingiem większym, równym 1, mniejszym, równym 2.
```js
db.restauracje.find({$where: "(this.rating >=1) && (this.rating <= 2)" })
{
  "_id": ObjectId("55f14312c7447c3da7051ceb"),
  "URL": "http://www.just-eat.co.uk/restaurants-aldixifriedchicken-b21/menu",
  "address": "343 Soho Road",
  "address line 2": "Birmingham",
  "name": "Al Dixi Fried Chicken & Grill House",
  "outcode": "B21",
  "postcode": "9SE",
  "rating": 1,
  "type_of_food": "Chicken"
}
{
  "_id": ObjectId("55f14312c7447c3da7051cec"),
  "URL": "http://www.just-eat.co.uk/restaurants-aldixifriedchicken-b21/menu",
  "address": "343 Soho Road",
  "address line 2": "Birmingham",
  "name": "Al Dixi Fried Chicken & Grill House",
  "outcode": "B21",
  "postcode": "9SE",
  "rating": 1,
  "type_of_food": "Chicken"
}
{
  "_id": ObjectId("55f14312c7447c3da7051d35"),
  "URL": "http://www.just-eat.co.uk/restaurants-alaowerrirestaurant-se14/menu",
  "address": "129 New Cross Road",
  "address line 2": "London",
  "name": "Alaowerri Restaurant",
  "outcode": "SE14",
  "postcode": "5DJ",
  "rating": 1,
  "type_of_food": "African"
}
{
  "_id": ObjectId("55f14313c7447c3da70522ef"),
  "URL": "http://www.just-eat.co.uk/restaurants-beiteddine-sw1x/menu",
  "address": "8 Harriet Street",
  "address line 2": "London",
  "name": "Beiteddine",
  "outcode": "SW1X",
  "postcode": "9JW",
  "rating": 1.5,
  "type_of_food": "Lebanese"
}
{
  "_id": ObjectId("55f14313c7447c3da70522f0"),
  "URL": "http://www.just-eat.co.uk/restaurants-beiteddine-sw1x/menu",
  "address": "8 Harriet Street",
  "address line 2": "London",
  "name": "Beiteddine",
  "outcode": "SW1X",
  "postcode": "9JW",
  "rating": 1.5,
  "type_of_food": "Lebanese"
}
{
  "_id": ObjectId("55f14313c7447c3da70523a1"),
  "URL": "http://www.just-eat.co.uk/restaurants-bennysle1/menu",
  "address": "83 Granby Street",
  "address line 2": "Leicester",
  "name": "Benny's",
  "outcode": "LE1",
  "postcode": "6FB",
  "rating": 1,
  "type_of_food": "Chicken"
}
{
  "_id": ObjectId("55f14313c7447c3da70523a2"),
  "URL": "http://www.just-eat.co.uk/restaurants-bennysle1/menu",
  "address": "83 Granby Street",
  "address line 2": "Leicester",
  "name": "Benny's",
  "outcode": "LE1",
  "postcode": "6FB",
  "rating": 1,
  "type_of_food": "Chicken"
}
{
  "_id": ObjectId("55f14313c7447c3da705246b"),
  "URL": "http://www.just-eat.co.uk/restaurants-bigboyzpizza/menu",
  "address": "112 Windermere Avenue",
  "address line 2": "Wembley",
  "name": "Big Boys Pizza",
  "outcode": "HA9",
  "postcode": "8RB",
  "rating": 2,
  "type_of_food": "Pizza"
}
{
  "_id": ObjectId("55f14313c7447c3da705246c"),
  "URL": "http://www.just-eat.co.uk/restaurants-bigboyzpizza/menu",
  "address": "112 Windermere Avenue",
  "address line 2": "Wembley",
  "name": "Big Boys Pizza",
  "outcode": "HA9",
  "postcode": "8RB",
  "rating": 2,
  "type_of_food": "Pizza"
}
{
  "_id": ObjectId("55f14313c7447c3da705246d"),
  "URL": "http://www.just-eat.co.uk/restaurants-bigboyzpizza/menu",
  "address": "112 Windermere Avenue",
  "address line 2": "Wembley",
  "name": "Big Boys Pizza",
  "outcode": "HA9",
  "postcode": "8RB",
  "rating": 2,
  "type_of_food": "Pizza"
}
#Fetched 10 record(s) in 23ms
```

##Agregacje
```js
db.restauracje.aggregate( [
  {$match: {rating: "2" }},
  {$group: {_id: "$cust_id", total: {$sum: "$amount"}}}
   ])
   
{
  "result": [ ],
  "ok": 1
}
```
**suma rankingu wszystkich restauracji**
```js
db.restauracje.aggregate(
 { $group: {_id: "outcode", totalRating: {$sum: "$rating"}}},
 { $match: {totalRating: {$gte: 100}}}
 )
 
{
  "result": [
    {
      "_id": "outcode",
      "totalRating": 12194
    }
  ],
  "ok": 1
}
```
Jak widać ranking wszystkich restauracji jest dosyć wysoki, biorąc pod uwagę, że mamy 2548 restauracji, a oceny wachają się od 1 do 6. Czyli w Angli restauracje są na dobrym poziomie, co pokazuje poniższa aggregacja, która wyświetla średnią.
Najpier wyświetlam najniższy i najwyższy ranking.

**Najniższa ocena restauracji**

```js
db.restauracje.aggregate(
 { $group: 
    {_id: "name", minRating: {$min: "$rating"}}}
 )
 
{
  "result": [
    {
      "_id": "name",
      "minRating": 1
    }
  ],
  "ok": 1
}
```
**Najwyższa ocena restauracji** *ciekawe* 
okazuje się, że najwyższa ocena nie jest jeszcze zdefiniowana.. 

```js
db.restauracje.aggregate(
 { $group: 
    {_id: "name", maxRating: {$max: "$rating"}}}
 )
 
{
  "result": [
    {
      "_id": "name",
      "maxRating": "not yet rated"
    }
  ],
  "ok": 1
}
```

**Średnia ocena wszystkich restauracji**

```js
db.restauracje.aggregate(
 { $group: 
    {_id: "name", avgRating: {$avg: "$rating"}}}
 )
 
{
  "result": [
    {
      "_id": "name",
      "avgRating": 4,9070422
    }
  ],
  "ok": 1
}
```
Biorąc pod uwage skalę od 1 do 6, średnia 4,9070422 jest wysoka. 

**5 najpopuularnieszne nazwy restauracji:**
```js
db.restauracje.aggregate(
[
  {"$group" : 
    {"_id" : "$name", "count" : {"$sum" : 1}}},
    {"$sort" : {"count" : -1}},
    {"$limit" : 5}
    ])
    
   "result":[
  {
    "name": "Best Kebab",
    "count": 30
  },
  {
    "name": "Bengal Spice",
    "count": 25
  },
  {
    "name": "Bella Pizza",
    "count": 23
  },
  {
    "name": "Bellati  Hut",
    "count": 18
  },
  {
    "name": "BBQ Express ",
    "count": 15
  }
  ]
```
![wykres](https://github.com/nruchlewicz/NoSQL/blob/master/img/chart1.png)
Wykres przedstawia najczeciej wystpujce nazwy, w udziale procentowym w restauracjach. 100% to 2548 nazw restauracji. Jak widać nazwa *Best Kebab* występuje 30 razy co daje tylko 1,2% udziału we wszystkich nazwach. Brytyjczycy sa kreatywni w tworzeniu nazw dla swoich restauracji.  

**3 najpopularniejsze typy serwowanego jedzenia**
```js
db.restauracje.aggregate(
[
  {"$group" : 
    {"_id" : "$type_of_food", "count" : {"$sum" : 1}}},
    {"$sort" : {"count" : -1}},
    {"$limit" : 5}
    ])
{
  "result": [
    {
      "_id": "Curry",
      "count": 902
    },
    {
      "_id": "Pizza",
      "count": 500
    },
    {
      "_id": "Chinese",
      "count": 174
    },
        {
      "_id": "Kebab",
      "count": 154
    },
        {
      "_id": "Fish & chips",
      "count": 116
    }
    ]
    }
```
![wykres](https://github.com/nruchlewicz/NoSQL/blob/master/img/wykres.png)
Wykres przedstawia najpopularniejsze typy jedzenia. 100% to 2548 wszystkich restauracji. Jak widać dominuje serwowanie curry w Wielkiej Brytani. Na drugim miejscu jest pizza. Dużo restauracji serwuje "niepopularne" typy jedzenia, std tak duży udział "inne" w wykresie.
   
##Grupowania
```js
var res = db.restauracje.group({
  key: {name: true}
  , initial: {count: 0}
  , reduce: function(doc, out){ out.count++; }
} );
res[0]
#{
# "name": ".CN Chinese",
#  "count": 1
#}
```
**Top 10 najczęściej występujących nazw:**
```js
var top10 = res.sort(function(a,b){ return b.count - a.count; }).slice(0, 10);

[
  {
    "name": "Best Kebab",
    "count": 30
  },
  {
    "name": "Bengal Spice",
    "count": 25
  },
  {
    "name": "Bella Pizza",
    "count": 23
  },
  {
    "name": "Balti Hut",
    "count": 18
  },
  {
    "name": "BBQ Express",
    "count": 15
  },
  {
    "name": "Balti Express",
    "count": 13
  },
  {
    "name": "Balti King",
    "count": 12
  },
  {
    "name": "Bayleaf",
    "count": 11
  },
  {
    "name": "Aroma",
    "count": 10
  },
  {
    "name": "Big Bite",
    "count": 9
  }
]
```
**To samo co wyżej, pogrupowane wg. typu serwowanego jedzenia.**
```js
var food = db.restauracje.group({
  key: {type_of_food: true}
  , initial: {count: 0}
  , reduce: function(doc, out){ out.count++; }
} );

food[0]
#{
#  "type_of_food": "Chinese",
#  "count": 174
#}
```

**3 najpopularniejsze typy jedzenia**
```js
var food3 = food.sort(function(a,b){ return b.count - a.count; }).slice(0, 3);

rastauracje> food3
[
  {
    "type_of_food": "Curry",
    "count": 902
  },
  {
    "type_of_food": "Pizza",
    "count": 500
  },
  {
    "type_of_food": "Chinese",
    "count": 174
  }
]
```

##Python
**minimalny ranking restauracji**
```py
import pymongo
from pymongo import MongoClient
client = MongoClient()

db = client['restaurants']
collection = db['resturants']

pipeline = [
  { "$group": {"_id": "$name", "minRating": {"$min": "$rating"}} }
]

pytanie = db.restaurants.aggregate(pipeline)
for doc in pytanie:
   print(doc)
``` 
**Średnia ocena restauracji python**

```py
import pymongo
from pymongo import MongoClient
client = MongoClient()

db = client['restaurants']
collection = db['resturants']

pipeline = [
  { "$group": {"_id": "$name", "avgRating": {"$avg": "$rating"}} }
]

pytanie = db.restaurants.aggregate(pipeline)
for doc in pytanie:
   print(doc)
```
**3 najpopuularnieszne nazwy restauracji python:**
```py
import pymongo
from pymongo import MongoClient
client = MongoClient()

db = client['restaurants']
collection = db['resturants']

pipeline = [
  {"$group" : {"_id" : "$name", "count" : {"$sum" : 1}}},
  {"$sort" : {"count" : -1}},
  {"$limit" : 3}
]

pytanie = db.restaurants.aggregate(pipeline)
for doc in pytanie:
   print(doc)
```
**3 najpopularniejsze typy serwowanego jedzenia**

```py
import pymongo
from pymongo import MongoClient
client = MongoClient()

db = client['restaurants']
collection = db['resturants']

pipeline = [
  {"$group" : {"_id" : "$type_of_food", "count" : {"$sum" : 1}}},
  {"$sort" : {"count" : -1}},
  {"$limit" : 3}
]

pytanie = db.restaurants.aggregate(pipeline)
for doc in pytanie:
   print(doc)
   ```
   
