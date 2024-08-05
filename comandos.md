# Comandos MongoDB en Mongoshell

## DB Comandos 
```
db: ubica la base de datos en la que estas posicionado
use <name_db>: crea la base de datos en caso de no existir, en caso de existir te dirige a esta
show dbs: muestra las bases de datos creadas en tu cluster o local
```

## CRUD Operations
```
	### Insert
	* db.your_collection.insertOne, que como su nombre lo dice inserta un nuevo documento a la coleccion que nosotros seleccionemos, este por default creara un campo _id, el cual es el identificador de nuestro documento que es de tipo objectId
	
	* db.your_collection.insertMany, este inserta uno o mas documentos a la coleccion que nosotros indiquemos, para esto ocupamos pasarlo como un array, y de ahi los campos de nuestros documentos.
	
	## FIND 
	* db.your_collection.find({your_filters}): busca todos los registros de la coleccion o por filtros
	
	### Operadores 
	### Logical
	$and: Returns documents where both queries match
	$or: Returns documents where either query matches
	### Comparison
    $eq: Values are equal
    $ne: Values are not equal
    $gt: Value is greater than another value
    $gte: Value is greater than or equal to another value
    $lt: Value is less than another value
    $lte: Value is less than or equal to another value
    $in: Value is matched within an array
	
	db.movies.find( { "title": "Titanic" } ) <- condicional
	db.movies.find( { rated: { $in: [ "PG", "PG-13" ] } } ) <- condicionales
	db.movies.find( { countries: "Mexico", "imdb.rating": { $gte: 7 } } ) <- operadores
	
	### UPDATE
	db.collection.updateOne()-> Buscara el primer documento que haga match con los parametros enviados y se actualizara el documento.
	db.collection.updateMany() -> Actualiza todos los documentos que hagan match con las especificaciones
	db.collection.replaceOne() -> Este es similar al updateOne, solo que aqui se reemplazara el documento.
	db.collection.findOneAndReplace()
	db.collection.findOneAndUpdate()
	db.collection.findAndModify()
	
	
	## DELETE
	db.collection.deleteMany({}) -> este podra uno hasta todos los documentos de una coleccion.
	db.collection.deleteOne({ status: 'D' }) -> este metodo elimina el primer documento que haga match con los parametros enviados
```

## Aggregation pipeline
Mira todos los operadores para el aggregate [aqui](https://www.mongodb.com/docs/manual/reference/operator/aggregation/#std-label-aggregation-expression-operators) la documentacion oficial de MongoDB
```
	db.items.aggregate([
		// Stage 1: Filtra por nuestra categoria Higiene personal
	   {
		  $match: { category: "Higiene personal" }
	   },
	   // Stage 2: Ordena por precio de manera descendiente
	   {
		  $sort: { price: -1 }
	   }

	])


	db.items.aggregate([
		// Stage 1: 
		{
			$match: { category: "Higiene personal" }
		},
		// Stage 2: 
		{
			$sort: { price: -1 }
		},
		// Stage 3: 
		{
			$addFields: {
				totalPrice: { $add: ["$price", { $multiply: ["$price", "$taxes"] }] }
			}
		}
	]);
```
## INDEXES 
```

Creacion de indexes:
db.collection.createIndex( <key and index type specification>, <options> )
db.collection.createIndex( { name: -1 } ) -> creacion de index

Obtener tus indexes de tu coleccion: 
db.collection.getIndexes()

Eliminar tus indexes: 
db.<collection>.dropIndex("<indexName>")
db.<collection>.dropIndexes( [ "<index1>", "<index2>", "<index3>" ] )
```

## Data Modeling

```
Create collections:
db.createCollection("your_collection", {
   validator: {
      $jsonSchema: { // creador de data rules para los documentos que se insertaran
         bsonType: "object", // especifica que la coleccion deberia de ser de documentos tipo BSON
         title: "Student Object Validation", // descripcion de tus reglas de validacion
         required: [ "address", "major", "name", "year" ], // defines los atributos requeridos para tu modelo
         properties: { // Aqui van los atributos que quieres que tengan un tipo en particular
            name: {
               bsonType: "string",
               description: "'name' must be a string and is required"
            },
            year: {
               bsonType: "int",
               minimum: 2017,
               maximum: 3017,
               description: "'year' must be an integer in [ 2017, 3017 ] and is required"
            },
            gpa: {
               bsonType: [ "double" ],
               description: "'gpa' must be a double if the field exists"
            }
         }
      }
   }
} )

db.createCollection("contact", {
   validator: {
      $jsonSchema: {
         bsonType: "object",
         title: "Contact Object Validation",
         required: [ "address", "employee_id", "phone" ],
         properties: {
            employee_id: {
               bsonType: "objectId",
               description: "'employee_id' must be an ObjectId and is required"
            },
            address: {
               bsonType: "string",
               description: "'address' must be a string and is required"
            },
            phone: {
               bsonType: "string",
               description: "'phone' must be a string and is required"
            }
         }
      }
   }
})
```


## Link Related Data
```
Query para embedded data
db.yourcollection.find({ "movieDetails.yearRelease"" {$lt: 2000 }})

Query para data relacionada basada por references:

Con tu campo ya validado como ObjectID
db.contact.aggregate([
    {
        $lookup: {
            from: "employees",                
            localField: "employee_id",              
            foreignField: "_id",     
            as: "employeeDetails"           
        }
    }
])

Conversion de campo a object id
db.contact.aggregate([
    {
        $addFields: {
            converted_employee_id: { $toObjectId: "$employee_id" }
        }
    },
    {
        $lookup: {
            from: "employees",
            localField: "converted_employee_id",
            foreignField: "_id",
            as: "employeeDetails"
        }
    },
])

```


## INSERTS DE COLECCIONES DE LAS PRACTICAS
```
	db.users.insertOne({
		name:  "Marcos",
		lastName: "Hernandez",
		genre: "Male",
		age: 189
	  }
	)
	
	db.users.insertMany(
	  [
		{
			name:  "Jessica 2",
			lastName: "Hernandez",
			genre: "Female",
			age: 18
		},
		{
			name:  "Jack 2",
			lastName: "Smith",
			genre: "Male",
			age: 19
		},
		{
			name:  "Johana 2",
			lastName: "Lopez",
			genre: "Female",
			age: 20
		}	
	  ]
	)
	db.movies.insertMany([
		{
			name: "Shrek",
			genre: ["Comedy", "For Kids"],
			cast: ["Mike Myers", "Eddie Murphy", "Cameron Diaz"],
			movieDetails: {
				yearRelease: 2001,
				running Time: 90
			}
		},
		{
			name: "Shrek 2",
			genre: ["Comedy", "For Kids"],
			cast: ["Mike Myers", "Eddie Murphy", "Antonio Banderas"],
			movieDetails: {
				yearRelease: 2004,
				running Time: 93
			}
		},
		{
			name: "Talk to me",
			genre: ["Horror Film", "Supernatural"],
			cast: ["Sophie Wilde", "Eddie Murphy", "Alexandra Jensen"],
			movieDetails: {
				yearRelease: 2022,
				running Time: 95
			}
		},
	])
	db.items.insertOne({
			name: "Shampoo",
			price: 80,
			taxes: 0.16,
			category: "Higiene personal"
		})

	db.items.replaceOne({ name: "Shampoo"}, {  name: 'Shampoo',
		price: 80,
		taxes: 0,
		category: 'Higiene personal', 
		currency: "MXN"
		}  )
		
	use supermercadodb

	db.items.insertMany([
		{
			name: "Shampoo",
			price: 80,
			taxes: 0.16,
			category: "Higiene personal"
		},
		{
			name: "Jabon Neutro",
			price: 10,
			taxes: 0.16,
			category: "Higiene personal"
		},
		{
			name: "Agua",
			price: 16,
			taxes: 0.16,
			category: "Bebida"
		},
		{
			name: "Refresco de cola",
			price: 22,
			taxes: 0.16,
			category: "Bebida"
		}
	])
	
	db.items.insertMany([
		{
			name: "Toallitas sanitarias",
			price: 80,
			taxes: 0.16,
			category: "Higiene personal"
		},
		{
			name: "Pasta dental",
			price: 10,
			taxes: 0.16,
			category: "Higiene personal"
		},
		{
			name: "Refresco de naranja",
			price: 16,
			taxes: 0.16,
			category: "Bebida"
		},
		{
			name: "Agua mineral",
			price: 22,
			taxes: 0.16,
			category: "Bebida"
		},
		{
			name: "Barra de chocolate",
			price: 16,
			taxes: 0.16,
			category: "Snacks"
		},
		{
			name: "Paquete de chicles",
			price: 22,
			taxes: 0.16,
			category: "Snacks"
		},
	])


    db.students.insertOne({
        address: "Fake 101, address",
        year: NumberInt(2024),
        gpa: Double(3.0)
    })

    db.students.insertOne({
        address: "Fake 101, address",
        year: NumberInt(2024),
        gpa: NumberInt(100),
        name: "Fake Student"
    })


    db.students.insertOne({
        address: "Fake 101, address",
        year: Double(2024.00),
        gpa: Double(3.0),
        name: "Fake Student 2"
    })

	db.employees.insertMany([
		{
			name: "Juan Perez",
			age: 32
		},
		{
			name: "Juan Guzman",
			age: 40
		},
		{
			name: "Clemente Diaz",
			age: 23
		},
		{
			name: "Lizbeth Ramirez",
			age: 27
		},
		{
			name: "Linda Gonzalez",
			age: 26
		},
		{
			name: "Giovanni Talan",
			age: 19
		}
		])

	db.contact.insertOne({
		phone: "0210005978",
		address: "FAKE 505, ADDRESS",
		employee_id: ObjectId("663fa5389b7c01ce3246b799")
	})

```


## Ejercicios para que refuerces lo aprendido en el curso
- Crea tu primera base de datos usando MongoShell
- Crea tu primera coleccion de datos junto a un registro, usando MongoShell
- Crea una base de datos de tu servicio de streaming favorito junto a dos colecciones, "Movies" y "Series", cada una debe tener una estructura de Nombre, Actores, genero de la serie y detalles como donde fue grabada, año en el que se lanzo y cuanto dura la pelicula. Una vez termines de crear estas colecciones, realiza todas las operaciones CRUD en estas. Una vez acabes, elimina las colecciones.
- Identifica un caso de uso de una empresa de ventas como por ejemplo, amazon, seven eleven, oxxo, walmart, etc... Una vez la identifiques, creale una base de datos. Crea una coleccion llamada productos, tu puedes agregar el modelo que sea, pero asegurate que los productos tengan atributos los cuales puedas realizar operaciones como suma, resta, multiplicacion. Una vez finalices con lo anterior, ejecuta una operacion aggregate de dos stages, en la primera debes de filtrar por algun atributo o ordenarlos de manera ascendiente o descendiente, en la segunda stage deberas de agregar un campo con alguna operacion de suma y resta con los atributos como impuestos*precio + precio
- Crea una base de datos para una biblioteca. Cada libro puede tener varios autores, y en este ejercicio, los datos de los autores estarán embebidos dentro del documento del libro
- Crea una base de datos para una escuela. Cada estudiante puede estar inscrito en varios cursos y cada curso puede tener varios estudiantes. Utilizaremos referencias para vincular a los estudiantes y los cursos, una vez finalices, ejecuta una operacion de busqueda usando el aggregate con la operacion lookup para visualizar los datos relacionados.
