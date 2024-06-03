# eje2.1:

```
db.companies.find({$expr: {$gt: ['$number_of_employees', '$founded_year']}}).count()
324

```

# eje2.2:

```
db.companies.find({$expr: {$eq: ['$permalink', '$twitter_username']}}).count()
1299

```
# eje2.3:

```
db.listingsAndReviews.find({$and: [{accommodates: {$gt: 6}}, {number_of_reviews: {$eq: 50}}]}, {name: 1, _id: 0})
[ { name: 'Sunset Beach Lodge Retreat' } ]

``` 
