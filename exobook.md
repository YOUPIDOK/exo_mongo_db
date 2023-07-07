1.  *Créez une base de données sample nommée "sample_db" et une collection appelée "employees".* 

   ```
   use sampble_db
   db.createCollection("employees", {"collation": {"locale": "fr"}})
   ```

2. *Insérez les documents suivants dans la collection "employees": {   name: "John Doe",   age: 35,   job: "Manager",   salary: 80000 } {   name: "Jane Doe",   age: 32,   job: "Developer",   salary: 75000 } {   name: "Jim Smith",   age: 40,   job: "Manager",   salary: 85000 }.*

   ```
   db.employees.insertMany([
   	{"name": "John Doe","age": 35, "job": "Manager", "salary": 80000 },
       {"name": "Jane Doe", "age": 32, "job": "Developer", "salary": 75000},
       {"name": "Jim Smith", "age": 40, "job": "Manager", "salary": 85000}
   ])
   ```

3. *Écrivez une requête MongoDB pour trouver tous les documents dans la collection "employees".* 

   ```
   db.employees.find({})
   ```

3. *Écrivez une requête pour trouver tous les documents où l'âge est supérieur à 33.* 

   ```
   db.employees.find({ "age": { $gt: 33 } })
   ```

4. *Écrivez une requête pour trier les documents dans la collection "employees" par salaire décroissant.* 

   ``````
   db.employees.find({}).sort({ "salary": -1 })
   ``````

5. *Écrivez une requête pour sélectionner uniquement le nom et le job de chaque document.* 

   ```
   db.employees.find({}, { "_id": false })
   ```

6. *Écrivez une requête pour compter le nombre d'employés par poste.* 

   ``````
   db.employees.aggregate([{ $group: { "_id": "$job", count: { $sum: 1 } } }])

7. *Écrivez une requête pour mettre à jour le salaire de tous les développeurs à 80000.* 

   ```
   db.employees.updateMany({ "job": "Developer" }, { $set: { "salary": 80000 }})
   ```