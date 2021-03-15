---
title: "Conexión NodeJS con Redis Cache"
date: "2018-11-01"
description: "Redis se utiliza como base de datos y para caché, ya que es súper rápido debido a que los datos se almacenan 'en memoria', a diferencia de otras bases de datos en las que los datos generalmente se almacenan 'en disco'."

image: images/post/2018-11-01-nodejs-redis.jpg

tags:
 - JS
 - REDIS
categories:
 - Tutoriales

type: "featured"
---
> Redis se utiliza como base de datos y para caché, ya que es súper rápido debido a que los datos se almacenan "en memoria", a diferencia de otras bases de datos en las que los datos generalmente se almacenan "en disco".

Redis es una gran base de datos para usar con Node.js. Tanto Redis como Node.js comparten convenciones de tipo y modelos de subprocesos similares, lo que lo convierte en una experiencia de desarrollo muy predecible. Al asociar Node.js y Redis, puede lograr una plataforma de desarrollo escalable y productiva.   

### Requisitos
- NodeJS (https://nodejs.org/es/)
- Redis (https://redis.io/)
- NPM (https://www.npmjs.com/)

----
### Instalación de Redis en windows
Para obtener Redis da click al siguiente enlace: https://github.com/dmajkic/redis/downloads

----
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

----
### Instalando npm Redis
Para este caso utilizaremos al paquete IORedis (https://www.npmjs.com/package/ioredis), en la consola de comandos copiamos y pegamos lo siquiente: `npm i ioredis`, una ves terminado se creará una carpeta por defecto `node_modules`, en donde se guardan todas las dependencias.

----
### Creando la conexión (get, set)
Empezaremos creando un archivo de configuración (config.js) en donde estará las credenciales y otras cosas:

```
const config = {
    redis: {
        port: 6379,
        host: "localhost",
        retries: 3,
        time_to_retry: 100,
        time_live: 3600 // tiempo de vida en segundos
    }
}

module.exports = config;
```

Luego, empezamos a crear la conexión, para esta ocación haremos uso de ES6, el nuevo archivo tendra el nombre de "redis-connection.js", adicional a esto agregaremos dos métodos más que serían asincronos, uno para obtener y el otro para asignar.

```
"use strict";

const redis = require("ioredis");
const config = require("./config");

module.exports = class RedisConnection {
    constructor() {
        this.client = this.connect();
    }

    connect() {
        let client = new redis({
            host: config.redis.host,
            port: config.redis.port,
            retryStrategy(times){
                let delay = Math.min(times * config.redis.time_to_retry, 200);
                return delay;
            },
            maxRetriesPerRequest: config.redis.retries
        });

        client.on("connect", () => {
            console.log("Connectado a redis");
        });

        client.on("error", err => {
            console.log(`Redis error: ${err}`);
        });

        return client;
    }

    async get(key){
        return await this.client.get(key);
    }

    async set(key, value){
        return await this.client.set(key, value);
    }
}
```

Creamos el siguiente archivo "redisSet.js" en donde se codificará una función autoejecutala asincrona instanciando al objeto "redis-connection.js".

```
"use strict";

const redisConnection = require("./redis-connection");
const redis = new redisConnection();

(async () => {
    try {
        // Para este caso haremos uso de un JSON, tambien se puede guardar string, int, double, etc.
        let data = [{
                "_id": "5c21d04d34b4a04750f9aa6f",
                "index": 0,
                "guid": "c9f32788-0116-48e9-86d5-9c7649f70c58",
                "friends": [{
                        "id": 0,
                        "name": "Tania Cardenas"
                    },
                    {
                        "id": 1,
                        "name": "Cherry Bishop"
                    },
                    {
                        "id": 2,
                        "name": "Simpson French"
                    }
                ]
            },
            {
                "_id": "5c21d04db527f89279d44902",
                "index": 1,
                "guid": "144ef286-f059-4a62-98ca-b54c5130a4d7",
                "friends": [{
                        "id": 0,
                        "name": "Vaughn Ratliff"
                    },
                    {
                        "id": 1,
                        "name": "Delores Glover"
                    },
                    {
                        "id": 2,
                        "name": "Jayne Puckett"
                    }
                ]
            }
        ];
        // NOTA: cuando lo que se quiere registrar es un json o array, debemos de usar el JSON.strinify 
        await redis.set("dataPrueba", JSON.stringify(data));
    } catch (error) {
        console.log(`Error al procesar: ${error}`);
    }
})();
```

Hacemos lo mismo que el código de arriba, pero esta vez vamos a obtener lo registrado. Este nuevo archivo tendrá de nombre "redisGet.js".

```
"use strict";

const redisConnection = require("./redis-connection");
const redis = new redisConnection();

(async () => {
    try {
        let rr = await redis.get("dataPrueba");
        console.log(rr);
    } catch (error) {
        console.log(`Error al procesar: ${error}`);
    }
})();
```

### Ejecutando lo codificado
- Para ejecutar solo hay que tener levantado el Redis.
- Ejecutar la siguiente línea `node redisSet.js` en la consola
    - Esto registrará en redis el JSON.
- Para obtener lo registrado ejecutar la siguiente línea `node redisGet.js`
    - Esto retornará el JSON registrado pero en formato STRING, simplemente formateamos a json(`JSON.parse()`) y listo!

> Para obtener el código completo dar click [aquí](https://github.com/PORTAFOLIO-PROYECTOS/NODE_JS_REDIS_CACHE/archive/master.zip)


Para finalizar les dejo este pensamiento corto:
> La verdadera oportunidad hacia el éxito reside en la persona, no en el puesto de trabajo (Zig Ziglar).