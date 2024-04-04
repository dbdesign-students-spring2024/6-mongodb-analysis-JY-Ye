# AirBnB MongoDB Analysis

## Data Details
This is [AirBnB listings data](http://insideairbnb.com/get-the-data.html) from Brisbane, Queensland, Australia. 
It is formatted in csv file.

## Rawd data

| id       | name                                         | host_name | neighbourhood      | price | minimum_nights |
|----------|----------------------------------------------|-----------|--------------------|-------|----------------|
| 660e1889 | Handy to Brisbane Airport                    | Ann       | Wavell Heights     | 220   | 2              |
| 660e1890 | Room 1 A room, a home a place to stay/base   | Carol     | Riverhills         | 36    | 1              |
| 660e1891 | Spacious 2BR unit–5 Min Walk Convention Centre | Jan     | South Brisbane     | 281   | 3              |
| 660e1892 | Beautiful Suburban Acreage                   | Ming      | Mount Gravatt      | 85    | 2              |
| 660e1893 | Stylish 2 Bedroom Unit - Aircon WiFi Phone   | Dianne    | Morningside        | 180   | 5              |
| 660e1894 | New Farm Brisbane, Inner City Suburb         | Jean      | New Farm           | 117   | 1              |
| 660e1895 | Valley room with private courtyard           | Rowan     | Fortitude Valley   | 77    | 3              |
| 660e1896 | 2 bedroom apartment in leafy suburb          | Karen     | Toowong            | 175   | 4              |
| 660e1897 | Brisbane Townhouse on the outskirts of the City | Christine | Annerley         | 160  | 2              |
| 660e1898 | Riverfront 3bd 2ba - Long stay discount available | Mimi   | Kangaroo Point     | 413   | 6            |


## Analysis

1. show exactly two documents

- query:

db.listings.find().limit(2)

- result:

{
"_id": "660e1889b6515eb20579aa0b",
"name": "SELF-CONTAINED APART., SOLAR,WI-FI,SHOPPING CENTER",
"host_name": "Jean-Marc",
"neighbourhood": "Upper Mt Gravatt, Queensland, Australia",
"price": "$60.00",
"review_scores_rating": 4.57
}
{
"_id": "660e1889b6515eb20579aa0a",
"name": "Handy to Brisbane Airport",
"host_name": "Ann",
"neighbourhood": "Wavell Heights, Queensland, Australia",
"price": "$220.00",
"review_scores_rating": 4.91
},
......


2. show exactly 10 documents in any order, but "prettyprint"

- query:

db.listings.find().limit(10).pretty()

- result:

{
  "_id": "660e1889b6515eb20579a1",
  "title": "Researchers reconstruct detailed 'family tree' of tumors to understand cancer evolution",
  "url": "https://www.sciencedaily.com/releases/2023/04/230405113423.htm",
  "snippet": "Scientists have created a detailed 'family tree' of tumors from 12 patients to study the evolutionary history of cancer. This new approach, which combines advanced DNA sequencing with computational analyses, offers insights into how cancers evolve over time, providing potential pathways for targeted therapies."
}
{
  "_id": "660e1889b6515eb20579a2",
  "title": "The Evolutionary Journey of Cancer: Unveiling its Past to Chart Future Therapies",
  "url": "https://www.medicalnewstoday.com/articles/327114",
  "snippet": "This article discusses the significance of understanding cancer's evolutionary trajectory for developing future treatments. Researchers are now looking at cancer through an evolutionary lens, examining how tumors adapt and evolve. This perspective is crucial for identifying vulnerabilities in cancer cells that could be targeted by new treatments."
}
{
  "_id": "660e1889b6515eb20579a3",
  "title": "New Insights Into Cancer Evolution Could Transform Treatment",
  "url": "https://www.nature.com/articles/d41586-023-00859-8",
  "snippet": "Nature reports on groundbreaking research providing new insights into the evolutionary processes of cancer. This research has implications for the development of novel cancer treatments, highlighting the importance of understanding the evolutionary dynamics of tumors. The study suggests that targeting the evolutionary mechanisms of cancer could lead to more effective therapies."
},
......


3. choose two hosts (by reffering to their `host_id` values) who are superhosts

- query:

db.listings.find({
  "host_is_superhost": "t"
}, {
  "name": 1, "price": 1, "neighbourhood": 1, "host_name": 1, "host_is_superhost": 1
})

- result: 

{
    _id: ObjectId('660e1889b6515eb20579aa0a'),
    name: 'Handy to Brisbane Airport',
    host_name: 'Ann',
    host_is_superhost: 't',
    neighbourhood: 'Wavell Heights, Queensland, Australia',
    price: '$220.00'
  },
  {
    _id: ObjectId('660e1889b6515eb20579aa10'),
    name: 'Room 1 A room, a home a place to stay/base',
    host_name: 'Carol',
    host_is_superhost: 't',
    neighbourhood: 'Riverhills, Queensland, Australia',
    price: '$36.00'
  },
  {
    _id: ObjectId('660e1889b6515eb20579aa14'),
    name: 'Spacious 2BR  unit– 5 Min Walk Convention Centre',
    host_name: 'Jan',
    host_is_superhost: 't',
    neighbourhood: '',
    price: '$281.00'
  },
  ......



4. find all the unique `host_name` values

- query:

db.listings.distinct("host_name")

- result:

'Aakash',
  'Aaneta',
  'Aaron',
    ......



5. find all of the places that have more than 2 `beds`

- query:

db.listings.find({
  "beds": { $gt: 2 },
}, {
  "name": 1, "beds": 1, "review_scores_rating": 1, "price": 1
}).sort({ "review_scores_rating": -1 })

- result:

{
    _id: ObjectId('660e1889b6515eb20579ab06'),
    name: 'Entire home in Paddington 5km from CBD',
    beds: 5,
    price: '$189.00',
    review_scores_rating: ''
  },
  {
    _id: ObjectId('660e1889b6515eb20579aba8'),
    name: 'Private yacht for your special occasion',
    beds: 4,
    price: '$1,600.00',
    review_scores_rating: ''
  },
  {
    _id: ObjectId('660e1889b6515eb20579abce'),
    name: 'MORDEN HOUSE NEAR CBD WITH A HEATED POOL',
    beds: 4,
    price: '$189.00',
    review_scores_rating: ''
  },
  ......



6. show the number of listings per host

- query:

db.listings.aggregate([
  {
    $group: {
      _id: "$host_id",
      listingsCount: { $sum: 1 }
    }
  }
])


- result:

{ _id: 523649633, listingsCount: 1 },
  { _id: 565796606, listingsCount: 3 },
  { _id: 14898877, listingsCount: 4 },
  ......



7. find the average `review_scores_rating` per neighborhood

- query: 

db.listings.aggregate([
  {
    $group: {
      _id: "$neighbourhood",
      averageRating: { $avg: "$review_scores_rating" }
    }
  },
  {
    $match: {
      averageRating: { $gte: 4 }
    }
  },
  {
    $sort: {
      averageRating: -1
    }
  }
])

- result:

{ _id: 'MacKenzie, Queensland, Australia', averageRating: 5 },
  { _id: 'Ransome, Queensland, Australia', averageRating: 5 },
  { _id: 'Durack, Queensland, Australia', averageRating: 5 },
......



## Insight
Every single host that is considered "superhot" has an average rating above "4". This indicates that hosts' popularities have a strong correlation with their review ratings.


