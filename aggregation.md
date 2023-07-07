1. Exercice 1 : Écrivez le pipeline qui affichera dans un champ nommé ville le nom de celles abritant une salle de plus de 50 personnes ainsi qu’un booléen nommé grande qui sera positionné à la valeur « vrai » lorsque la salle dépasse une capacité de 1 000 personnes. 
```js
var pipeline = [
  {
    $match: { "capacite": { $gt: 50 } }
  },
  {
    $addFields: {
      "ville": "$adresse.ville",
      "grande": { $cond: { if: { $gt: ["$capacite", 1000] }, then: true, else: false } }
    }
  },
  {
    $project: { "_id": false, "ville": true, "grande": true }
  }
];

db.salles.aggregate(pipeline);
```

2. Exercice 2 : Écrivez le pipeline qui affichera dans un champ nommé apres_extension la capacité d’une salle augmentée de 100 places, dans un champ nommé avant_extension sa capacité originelle, ainsi que son nom.
```js
var pipeline = [
  {
    $addFields: {
      "apres_extension": { $add: ["$capacite", 100] },
      "avant_extension": "$capacite"
    }
  },
  {
    $project: { "_id": false, "nom": true, "avant_extension": true, "apres_extension": true }
  }
];

db.salles.aggregate(pipeline);
```

3. Exercice 3 : Écrivez le pipeline qui affichera, par numéro de département, la capacité totale des salles y résidant. Pour obtenir ce numéro, il vous faudra utiliser l’opérateur $substrBytes dont la syntaxe est la suivante :
```
{$substrBytes: [ < chaîne de caractères >, < indice de départ >, < longueur > ]} 
```

```js
var pipeline = [
  {
    $group: {
      _id: {
        $substrBytes: ["$adresse.codePostal", 0, 2]
      },
      capacite_totale: { $sum: "$capacite" }
    }
  },
  {
    $project: {
      "_id": false,
      "numero_departement": "$_id",
      "capacite_totale": true
    }
  }
];

db.salles.aggregate(pipeline);
```

4. Exercice 4 : Écrivez le pipeline qui affichera, pour chaque style musical, le nombre de salles le programmant. Ces styles seront classés par ordre alphabétique.
```js
var pipeline = [
  {
    $unwind: "$styles"
  },
  {
    $group: {
      _id: "$styles",
      count: { $sum: 1 }
    }
  },
  {
    $sort: { _id: 1 }
  },
  {
    $project: {
      "_id": false,
      "style": "$_id",
      "nombre_salles": "$count"
    }
  }
];

db.salles.aggregate(pipeline);
```

5. Exercice 5 : À l’aide des buckets, comptez les salles en fonction de leur capacité :

celles de 100 à 500 places

celles de 500 à 5000 places

```js
var pipeline = [
  {
    $bucket: {
      groupBy: "$capacite",
      boundaries: [100, 500],
      default: "null",
      output: {
        "nombre_salles": { $sum: 1 }
      }
    }
  },
  {
    $project: { "_id": false, "nombre_salles": true }
  }
];

db.salles.aggregate(pipeline);

```

6. Exercice 6 : Écrivez le pipeline qui affichera le nom des salles ainsi qu’un tableau nommé avis_excellents qui contiendra uniquement les avis dont la note est de 10.
```js
var pipeline = [
  {
    $match: {
      "avis.note": 10
    }
  },
  {
    $project: {
      _id: false,
      nom: true,
      avis_excellents: {
        $filter: {
          input: "$avis",
          as: "avis",
          cond: { $eq: ["$$avis.note", 10] }
        }
      }
    }
  }
];

db.salles.aggregate(pipeline);
```