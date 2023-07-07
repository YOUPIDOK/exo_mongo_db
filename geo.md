1. Exercice 1 : Vous disposez du code JavaScript suivant qui comporte une fonction de conversion d’une distance exprimée en kilomètres vers des radians ainsi que d’un document dont les coordonnées serviront de centre à notre sphère de recherche. Écrivez la requête $geoWithin qui affichera le nom des salles situées dans un rayon de 60 kilomètres et qui programment du Blues et de la Soul.
```js
var KilometresEnRadians = function(kilometres){
  var rayonTerrestreEnKm = 6371;
  return kilometres / rayonTerrestreEnKm;
};

// Paramètre de position
var lat = 43.923005;
var lng = 5.020077;
var rayon = 60;

var requete = [
  {
    $match: {
      "adresse.localisation": {
        $geoWithin: {
          $centerSphere: [[lat, lng], KilometresEnRadians(rayon)]
        }
      },
      styles: { $all: ["blues", "soul"] }
    }
  },
  {
    $project: {
      "_id": false,
      "nom": true
    }
  }
];

db.salles.aggregate(requete);
```

* Exercice 2: Écrivez la requête qui permet d’obtenir la ville des salles situées dans un rayon de 100 kilomètres autour de Marseille, triées de la plus proche à la plus lointaine :

```js
db.salles.createIndex({ "adresse.localisation": "2dsphere" });

var marseille = {
  "type": "Point",
  "coordinates": [43.300000, 5.400000]
};

var rayon = 100;

var KilometresEnRadians = function(kilometres) {
  var rayonTerrestreEnKm = 6371;
  return kilometres / rayonTerrestreEnKm;
};

var requete = [
  {
    $geoNear: {
      near: marseille,
      distanceField: "distance",
      spherical: true,
      query: {
        "adresse.localisation": {
          $geoWithin: {
            $centerSphere: [marseille.coordinates, KilometresEnRadians(rayon)]
          }
        }
      }
    }
  },
  {
    $sort: {
      "distance": 1
    }
  },
  {
    $project: {
      "_id": false,
      "nom": true
    }
  }
];

db.salles.aggregate(requete);

```

3. Exercice 3: Soit polygone un objet GeoJSON de la forme suivante :

```
var polygone = { 
     "type": "Polygon", 
     "coordinates": [ 
            [ 
               [43.94899, 4.80908], 
               [43.95292, 4.80929], 
               [43.95174, 4.8056], 
               [43.94899, 4.80908] 
            ] 
     ] 
} 
```

Donnez le nom des salles qui résident à l’intérieur.

```js
var polygone = { 
     "type": "Polygon", 
     "coordinates": [ 
            [ 
               [43.94899, 4.80908], 
               [43.95292, 4.80929], 
               [43.95174, 4.8056], 
               [43.94899, 4.80908] 
            ] 
     ] 
}; 

var requete = {
   "adresse.localisation": {
      $geoWithin: {
         $geometry: polygone
      }
   }
};

db.salles.find(requete, { nom: true, _id: false });
```