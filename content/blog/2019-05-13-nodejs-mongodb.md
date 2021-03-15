---
title: "Conexión NodeJS con MongoDB Atlas"
date: "2019-05-13"
description: "MongoDB Atlas es la base de datos como servicio que permite implementar, utilizar y escalar una base de datos de MongoDB en la nube haciendo conexión con NodeJS."

image: images/post/2019-05-13-nodejs-mongodb.jpg

tags:
 - JS
 - mongodb

categories:
    - base de datos

type: 'post'
---

MongoDB Atlas es la base de datos como servicio que permite implementar, utilizar y escalar una base de datos de MongoDB en la nube haciendo conexión con NodeJS.

Usaremos clases y funcion asincronas con promesas en javascript ES6.

### Creando el Package.json
Antes de proceder con cualquier instalación de paquetes npm necesitamos crear nuestro package.json.

- Como primer paso creamos una carpeta donde estarán todos nuestros archivos.
- Luego, levantar la ventana de comandos dentro de la carpeta y ejecutar el siguiente comando `npm init`.
- Podemos personalizar o simplemente precionar la tecla enter hasta terminar.

Al finalizar tendremos un archivo como este:

```
{
  "name": "[nombre]",
  "version": "[versión]",
  "description": "[descripción]",
  "main": "",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Hugo Roca - hugo.rock20@hotmail.com",
  "license": "[licencia]"
}
```

### Instalando npm mongodb
Para este caso utilizaremos al paquete mongodb (https://www.npmjs.com/package/mongodb), en la consola de comandos copiamos y pegamos lo siquiente: `npm i mongodb`, una ves terminado se creará una carpeta por defecto `node_modules`, en donde se guardan todas las dependencias.

## Creando colección
Podemos crear la colección tanto por una GUI para mongo como por código. Dejare un ejemplo de como crearlo por código.

```js
let MongoClient = require('mongodb').MongoClient;
let url = "mongodb://localhost:27017/"; // => url de conexión mongo

MongoClient.connect(url, function(err, db) {
  if (err) throw err;
  let dbo = db.db("mydb");
  dbo.createCollection("customers", function(err, res) {
    if (err) throw err;
    console.log("Collection created!");
    db.close();
  });
});
/* esto es un ejemplo básico */
```

## Creando el proyecto
Empezaremos creando un archivo `config.js`, en donde se alojaran las configuraciones, conexiones, etc.

```js
const config = {
    mongo: {
        url: "mongodb://localhost:27017/", // => url de conexión mongo
        database: "test",
        collection: "nodejs_mongo_atlas"
    }
}

module.exports = config;

```

Creamos otro archivo y lo nombraremos `mongoUtils.js`, este archivo tendrá funciones de configuración.

```js
const mongoClient = require("mongodb").MongoClient;
const config = require("./config");

module.exports = class MongoUtils {
    getConnectionString() {
        return config.mongo.url;
    }

    getDataBaseString() {
        return config.mongo.database;
    }

    getCollectionString() {
        return config.mongo.collection;
    }

    buildCollection(message) {
        return {
            message,
            createdAt: new Date()
        }
    }

    async insertData(data) {
        return new Promise((resolve, reject) => {
            mongoClient.connect(this.getConnectionString(), { useNewUrlParser: true }).then(client => {

                const db = client.db(this.getDataBaseString());
                const collection = db.collection(this.getCollectionString());

                collection.insertOne(data, (insertError, insertResponse) => {
                    if (insertError) reject(insertError);
                    resolve(insertResponse);
                });

            }).catch(err => {
                console.error("Error al conectarse con la base de datos: ", err);
            });
        });
    }

    async insert(message){
        let data = this.buildCollection(message);
        return await this.insertData(data);
    }

}

```

Para finalizar, creamos un archivo `index.js` en donde haremos el llamado a la funciones creadas en `mongoUtils.js`.

```js
const mongoUtils = require("./mongoUtils");
const mongo = new mongoUtils();

(async() => {
    let insert = await mongo.insert("Ejecutando mongo desde nodeJS");
    console.log(insert);
})();


```

Para ejecutar, solamente basta ejecutar el siguiente comando en la consola `node index.js`

![Sin titulo](/images/nodejs-mongodb-atlas/resultado.jpg)

> Para obtener el código completo dar click [aquí](https://github.com/PORTAFOLIO-PROYECTOS/NODEJS_MONGODB/archive/master.zip)
#### Comenta, disfruta y comparte! 