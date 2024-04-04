# AirBnB MongoDB Analysis
## Rawd data

id	name	host_id	neighbourhood	room_type	price	number_of_reviews	review_scores_rating
277913	SELF-CONTAINED APART., SOLAR,WI-FI,SHOPPING CENTER	1450636	Upper Mt Gravatt, Queensland, Australia	Entire home/apt	$60	145	4.57
37380	Handy to Brisbane Airport	161432	Wavell Heights, Queensland, Australia	Entire home/apt	$220	44	4.91

## Analysis

1. db.listings.find().limit(2)
2. db.listings.find().limit(10).pretty()
3. db.listings.find({
  "host_is_superhost": "t"
}, {
  "name": 1, "price": 1, "neighbourhood": 1, "host_name": 1, "host_is_superhost": 1
})
4. db.listings.distinct("host_name")
5. db.listings.find({
  "beds": { $gt: 2 },
}, {
  "name": 1, "beds": 1, "review_scores_rating": 1, "price": 1
}).sort({ "review_scores_rating": -1 })
6. db.listings.aggregate([
  {
    $group: {
      _id: "$host_id",
      listingsCount: { $sum: 1 }
    }
  }
])

7. db.listings.aggregate([
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

## Conclusion
Some neighborhoods boast exceptionally high average review scores, potentially indicating areas with overall higher guest satisfaction. This information could be valuable for both hosts looking to improve their offerings and guests seeking the best experiences.


