# Comandos de MongoDB

## Creación de colección y operaciones básicas

use tienda 

db.createCollection("ejemplo")

show collections

db.ejemplo.insertOne({"nombre": "Casemiro", "edad": 23})


db.libros.find({editorial:{$eq:"Biblio"}})

db.libros.find({precio:25})

db.libros.find({precio:{$eq:25}})


db2> db.libros.find({precio:{$gte:20}})

db.libros.find({precio:{$gte:20}})

db.libros.find({precio:{$lt:5}})

db.libros.find({editorial:{$in:['Bilio', 'Planeta']}})

db.libros.findOne({precio:{$in:[20, 25]}})

db.libros.find({precio:{$gt:25},cantidad:{$lt:15}})


db.libros.find({$and: [{precio:{$gt:25}}, {cantidad:{$lt:15}}]})



## operadores con or
sintaxis { $or: [ { <expression1> }, { <expression2> }, ... , { <expressionN> } ] }
todos aquells libris que valgan mas de 25 o que su cuya cantidad se infeir a 15

db.libros.find({$or:[{precio:{$gt:25}}, {cantidad:{$lt:15}}] })

Seleccionar los libris de la editorial con precio mayor a 40 
o libros de la editorial Planeta coon predio mayor a 30

db.libros.find({},{titulo:1})

db.libros.find({titulo:'Don quijote'},{titulo:1})

db.libros.find({titulo:'Don quijote'},{_id:0,titulo:1})

db.libros.find({titulo:{$exists:true}})

db.libros.find({$and:[{precio:{$gt:5}} ,{autor:{$exists:true}} ]})

db.libros.insertOne({_id:12, titulo: "Estupro", finaza:true, anios_carcel:2,precio:13.35 })
db.libros.find({precio:{$type:1}})

db.libros.updateOne({titulo:'JSON para todos'}, {$set:{titulo:'Java el rey'}})

db.libros.updateMany({precio:{$gt:9}}, {$set:{precio:150}} )

db.libros.updateMany({cantidad:{$gt:20}}, {$mul:{precio:2} })

db.libros.replaceOne(
... {"_id":13},{ "_id":14,"titulo":"Las pato aventuras de Ezequiel Alias Mateo"})

db.libros.deleteOne({"_id":13})

//borrar el primer documentos que cumpla la condicion
db.libros.deleteOne({"titulo":"Java el rey"})

//borrar todo loas dumentos que cumplan con esa condicion
db.libros.deleteMany({"cantidad":{$gt:20}})

// expresiones regulares

db.libros.find({"titulo":/t/})

//para buscar todos los documentos que tengan Java
db.libros.find({"titulo":/Java/})

// todos los que terminan con esa palabra
db.libros.find({"titulo":/tos$/})


//todos los cocumentos que empize con esa letra
db.libros.find({"titulo":/^J/})


//
db.libros.find({"titulo":{$regex:"para"}})

// este comado busca un documento de consida con el  nombre y la i deja que sea sensible a mayuscilas
// no importa como se escriba con M o m si la encunetra la  pinta
db.libros.find({"titulo":{$regex:"JAVA" , $options:'i'}})

// muestra los documentos ordenanos de forma acedente
db.libros.find({},{titulo:1,precio:1}).sort({titulo:1})


// muestra todos los documentos de  ordenados por el titulo de foroma desendente
db.libros.find({},{titulo:1,precio:1}).sort({titulo:-1})

//orbenar dos columnas primero le da prioridar a la primera columna y despues de la la segunda
db2> db.libros.find({},{titulo:1,precio:1,editorial:1,_id:0}).sort({titulo:1,editorial:1})

// skip es un saltar alginos documentos
db.libros.find({},{"titulo":1,"precio":1,_id:0, "editorial":1}).skip(2)

// limit da los que estan hatn en la parte superior
db.libros.find({},{"titulo":1,"precio":1,_id:0, "editorial":1}).limit(2)

// size nuestra el numero dse los documentos que cumplasn la cindicion
db2> db.libros.find({},{"titulo":1,"precio":1,_id:0, "editorial":1}).size()


// trae  el nunmero de documentos de forma acedentes
db.libros.find({},{"titulo":1,"precio":1,_id:0, "editorial":1}).sort({titulo:1}).limit(3)

// eliminar una collecion
db.ejemplo.drop()

eliminar una base de datos
db.dropDatabase

## Agregations 

db.libros.aggregate( [ { $match: {editorial:'Biblio'}}, { $project:{id_:0, titulo:1, precio:1,'Nombre Editorial': '$editorial' }}])

### pipeline1
[
    {
      $match:
        /**
         * query: The query in MQL.
         */
        {
          editorial: "Biblio",
        },
    },
    {
      $project:
        /**
         * specifications: The fields to
         *   include or exclude.
         */
        {
          _id: 0,
          titulo: 1,
          precio: 1,
          cantidad: 1,
          "Nombre Editorial": "#editorial",
          "Total Ganancia": {
            $multiply: ["$precio", "$cantidad"],
          },
        },
    },
    {
      $sort:
        /**
         * Provide any number of field/order pairs.
         */
        {
          precio: 1,
        },
    },
  ]
  ### pipeline2 group
  [
    {
      $group:
        /**
         * _id: The id of the group.
         * fieldN: The first field name.
         */
        {
          _id: "$editorial",
          "Numero documentos": {
            $count: {},
          },
        },
    },
    {
      $sort:
        /**
         * Provide any number of field/order pairs.
         */
        {
          "Numero documentos": 1,
        },
    },
  ]

  ### pipeline 3
  [
    {
      $group:
        /**
         * _id: The id of the group.
         * fieldN: The first field name.
         */
        {
          _id: "$editorial",
          "Numero de docuemtos ": {
            $count: {},
          },
          "media:": {
            $avg: "$precio",
          },
        },
    },
  ]
  ### pipeline 3 actualizado
  [
    {
      $group:
        /**
         * _id: The id of the group.
         * fieldN: The first field name.
         */
        {
          _id: "$editorial",
          "Numero de docuemtos ": {
            $count: {},
          },
          "media:": {
            $avg: "$precio",
          },
          "Precio Max:": {
            $max: "$precio",
          },
        },
    },
    {
      $sort:
        /**
         * Provide any number of field/order pairs.
         */
        {
          "Precio Max:": 1,
        },
    },
  ]


  ### pipeline 4
  [
    {
      $group:
        /**
         * _id: The id of the group.
         * fieldN: The first field name.
         */
        {
          _id: "$editorial",
          Numero: {
            $count: {},
          },
          media: {
            $avg: "precio",
          },
        },
    },
    {
      $set:
        /**
         * field: The field name
         * expression: The expression.
         */
        {
          "Media Total": {
            $trunc: ["$media", 2],
          },
        },
    },
    {
      $out:
        /**
         * Provide the name of the output collection.
         */
        "Media_Editoriales",
    },
  ]
  // 
  [
    {
      $group:
        /**
         * _id: The id of the group.
         * fieldN: The first field name.
         */
        {
          _id: "$editorial",
          Numero: {
            $count: {},
          },
          media: {
            $avg: "precio",
          },
        },
    },
    {
      $set:
        /**
         * field: The field name
         * expression: The expression.
         */
        {
          "Media Total": {
            $trunc: ["$media", 2],
          },
        },
    },
    {
      $unset:
        /**
         * Provide the name of the output collection.
         */
        "media",
    },
  ]