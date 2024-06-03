## eje2.1:

```
db.companies.find({$expr: {$gt: ['$number_of_employees', '$founded_year']}}).count()
324

```

## eje2.2:

```
db.companies.find({$expr: {$eq: ['$permalink', '$twitter_username']}}).count()
1299

```
## eje2.3:

```
db.listingsAndReviews.find({$and: [{accommodates: {$gt: 6}}, {number_of_reviews: {$eq: 50}}]}, {name: 1, _id: 0})
[ { name: 'Sunset Beach Lodge Retreat' } ]

```

## eje2.4: 

```
db.listingsAndReviews.find({$and: [{property_type: "House"}, {amenities: {$elemMatch: {$eq: "Changing table"}}}]}).count()
11

```

## eje2.5: 

```
db.companies.find({offices: {$elemMatch: {city: 'Seattle'}}}).count()
117
``` 

## eje2.6:

```
db.companies.find({funding_rounds: {$size: 8}}, {name: 1, _id: 0})
[
  { name: 'Twitter' },
  { name: 'LinkedIn' },
  { name: 'PayScale' },
  { name: 'Xobni' },
  { name: 'Zynga' },
  { name: 'ShareThis' },
  { name: 'TicketLeap' },
  { name: 'Moblyng' },
  { name: 'PlumChoice' },
  { name: 'SolFocus' },
  { name: 'HyperWeek' },
  { name: 'Virident Systems' },
  { name: 'Extreme Enterprises' },
  { name: 'CipherMax' },
  { name: 'Stemgent' },
  { name: 'Sonos' },
  { name: 'BridgeLux' },
  { name: 'Silicor Materials' },
  { name: '1366 Technologies' },
  { name: 'Biolex Therapeutics' }
]
```

## eje2.7: 

- En sample_training.trips, ¿cuántos viajes empiezan en
estaciones que están al oeste de la longitud -74?

```
db.trips.find({"start station location.coordinates.0": {$lt: -74}}).count()
1928

```
