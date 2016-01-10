##Egzamin, Ruchlewicz Natalia

1. Import
2. Rozgrzewka- proste zapytania
3. JavaScript
4. Grupowanie
5. Agregacja



###Import
Do eksperymentów użyłam niezbyt dużego pliku [restauracje.json](https://raw.githubusercontent.com/mongodb/docs-assets/primer-dataset/dataset.json). Obliczenia wykonywałam na tym samum sprzecie co zaliczenia. 

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
####Zapytania z rzutowaniem i sortowaniem
**Posortowanie restauracji wg. nazwy i wywietlenie pierwszych 10**
```sh
restauracje> db.restauracje.find({}, {name: 1, _id: 0}).sort({word:1}).limit(10)
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

```sh
db.restauracje.find({}, {name: 1, _id: 0}).sort({word: -1}).skip(120).limit(10)
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

Restauracje zaczynające się na 3. Wyświetlnie pierwszych trzech.
```sh
db.restauracje.find({"name": /^3/}, {_id: 0}).sort({word: 1}).limit(3)
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

Wyświetlenie restauracji z rankingiem większym, równym 6.
```sh
db.restauracje.find({rating: {$gte: 6}},{_id: 0, name: 1, rating:1 })
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
{
  "name": "Abshar Indian Cuisine",
  "rating": 6
}
{
  "name": "Agra",
  "rating": 6
}
{
  "name": "Aji - Collection Only",
  "rating": 6
}
{
  "name": "Akash Accrington",
  "rating": 6
}
{
  "name": "Al Frango",
  "rating": 6
}
{
  "name": "Al Medina Delights - Collection Only",
  "rating": 6
}
{
  "name": "Alcatraz Caffe Bournemouth",
  "rating": 6
}
{
  "name": "Alex Plaice",
  "rating": 6
}
{
  "name": "Alex Plaice",
  "rating": 6
}
{
  "name": "All Siam Thai Restaurant - Collection Only",
  "rating": 6
}
{
  "name": "Amina",
  "rating": 6
}
{
  "name": "Ammanford Fish Bar",
  "rating": 6
}
{
  "name": "Andover Tandoori - Collection Only",
  "rating": 6
}
{
  "name": "Andover Tandoori - Collection Only",
  "rating": 6
}
{
  "name": "Angeethi Indian Express",
  "rating": 6
}
{
  "name": "Anis's - The Taste of Perfection",
  "rating": 6
}
{
  "name": "Ann's Thai@ The Bulls Head",
  "rating": 6
}
{
  "name": "Arabesque",
  "rating": 6
}
{
  "name": "Ark Burger & Salad Bar",
  "rating": 6
}
{
  "name": "Aroma Bar Restaurant Takeaway - Collection Only",
  "rating": 6
}
{
  "name": "Aroma Bar Restaurant Takeaway - Collection Only",
  "rating": 6
}
{
  "name": "Asli Zaiqa",
  "rating": 6
}
{
  "name": "Aspendos",
  "rating": 6
}
{
  "name": "Atash Persian Restaurant - Collection Only",
  "rating": 6
}
{
  "name": "Atash Persian Restaurant - Collection Only",
  "rating": 6
}
{
  "name": "Athena B Fish Bar - Collection Only",
  "rating": 6
}
{
  "name": "Athena B Fish Bar - Collection Only",
  "rating": 6
}
{
  "name": "Atlantic Gourmet",
  "rating": 6
}
{
  "name": "Ayubowan",
  "rating": 6
}
{
  "name": "Balti Hut",
  "rating": 6
}
{
  "name": "Barista House Restaurant & Cafe - Collection Only",
  "rating": 6
}
{
  "name": "Barnacle Bills",
  "rating": 6
}
{
  "name": "Bath Sushi",
  "rating": 6
}
{
  "name": "Bearstead Fish Bar - Collection Only",
  "rating": 6
}
{
  "name": "Beast Gourmet Burgers",
  "rating": 6
}
{
  "name": "Bella Panini",
  "rating": 6
}
{
  "name": "Benny's - Collection Only",
  "rating": 6
}
{
  "name": "Benny's Grill House",
  "rating": 6
}
{
  "name": "Best Fish & Chips",
  "rating": 6
}
{
  "name": "Best Pizza",
  "rating": 6
}
{
  "name": "Best Pizza & Kebab",
  "rating": 6
}
{
  "name": "Best Pizza & Kebab",
  "rating": 6
}
{
  "name": "Big Fish - Collection Only",
  "rating": 6
}
{
  "name": "Big Saj",
  "rating": 6
}
{
  "name": "Big Saj",
  "rating": 6
}
{
  "name": "Biggapot Caribbean - Collection Only",
  "rating": 6
}
#fatched 49 record(s) in 5ms
```


R-cje z rankingiem 1<= x <=2
```sh
db.restauracje.find({rating: {$gte: 1, $lte: 2}},{_id: 0, name: 1, rating:1 })
{
  "name": "Al Dixi Fried Chicken & Grill House",
  "rating": 1
}
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
{
  "name": "Beiteddine",
  "rating": 1.5
}
{
  "name": "Benny's",
  "rating": 1
}
{
  "name": "Benny's",
  "rating": 1
}
{
  "name": "Big Boys Pizza",
  "rating": 2
}
{
  "name": "Big Boys Pizza",
  "rating": 2
}
{
  "name": "Big Boys Pizza",
  "rating": 2
}
#Fetched 10 record(s) in 2ms
```
###JavaScript

Restauracje z nazwą dłuższą niż 1, krótszą, równą  3.
```sh
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
```sh
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
```sh
restauracje> db.restauracje.aggregate( [
{$match: {rating: "2" }},
{$group: {_id: "$cust_id", total: {$sum: "$amount"}}}
 ])
{
  "result": [ ],
  "ok": 1
}
```

```sh
natalia(mongod-2.6.3) restauracje> coll.aggregate(
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



##Grupowania
```sh
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

Top 10 najczęściej występujących nazw:
```sh
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
To samo co wyżej, pogrupowane wg. typu serwowanego jedzenia.
```sh
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

3 najpopularniejsze typy jedzenia
```sh
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

```sh
var winner = db.restauracje.find({name: [ "Best Kebab" ]})

db.winners.drop();
winner.forEach(function(obj) { db.winners.insert(obj) });
var winners = db.winners.group({
  key: {name: true}
  , initial: {count: 0}
  , reduce: function(doc, out){ out.count++; }
} );
```
