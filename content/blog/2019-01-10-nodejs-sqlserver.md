---
title: "Conexión NodeJS con SQLServer"
date: "2019-01-10"
description: "Microsoft SQL Server es un sistema de gestión de base de datos relacional (RDBMS) producido por Microsoft. Su principal lenguaje de consulta es Transact-SQL.."

image: images/post/2019-01-10-nodejs-sqlserver.jpg

tags:
 - JS
 - SQLSERVER
 - JavaScript

categories:
  - Tutoriales

type: 'post'
---
> Microsoft SQL Server es un sistema de gestión de base de datos relacional (RDBMS) producido por Microsoft. Su principal lenguaje de consulta es Transact-SQL, una aplicación de las normas ANSI / ISO estándar Structured Query Language (SQL) utilizado por ambas Microsoft y Sybase.

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

----
### Instalando npm mssql
Para este caso utilizaremos al paquete mssql (https://www.npmjs.com/package/mssql), en la consola de comandos copiamos y pegamos lo siquiente: `npm i mssql`, una ves terminado se creará una carpeta por defecto `node_modules`, en donde se guardan todas las dependencias.

----
### Creando tabla y store procedure
En SQLServer ejecutamos el siguiente script, que nos creará las tablas, creará datos y store procedures.

```
CREATE DATABASE NodeSQLPruebas
GO
USE NodeSQLPruebas
GO

CREATE TABLE test(
id int identity(1,1),
descripcion varchar(200),
fecha datetime
primary key (id)
)
GO

insert into test(descripcion, fecha) values('Lorem ipsum dolor sit amet, consectetur adipiscing elit.', getdate())
insert into test(descripcion, fecha) values('Mauris sagittis, dolor vitae posuere sollicitudin, sem lorem euismod nunc, quis consectetur dolor augue ac turpis.', getdate())
insert into test(descripcion, fecha) values('Phasellus in dui vitae magna placerat aliquam eget nec velit.', getdate())
insert into test(descripcion, fecha) values('Praesent ut arcu in nunc eleifend aliquam vel id metus.', getdate())
insert into test(descripcion, fecha) values('Ut ac lectus fringilla felis imperdiet malesuada.', getdate())
insert into test(descripcion, fecha) values('Morbi at mattis turpis.', getdate())
insert into test(descripcion, fecha) values('Cras gravida diam in lorem venenatis fermentum. ', getdate())
insert into test(descripcion, fecha) values('Donec suscipit odio ut leo fermentum lacinia.', getdate())
insert into test(descripcion, fecha) values('Aenean laoreet feugiat nunc vel rutrum.', getdate())
insert into test(descripcion, fecha) values('Nam at tellus erat. In ante elit, imperdiet sit amet eleifend vitae, fermentum a urna.', getdate())
insert into test(descripcion, fecha) values('Phasellus pellentesque placerat pellentesque.', getdate())
insert into test(descripcion, fecha) values('Sed et sapien dignissim, lacinia arcu ac, suscipit elit.', getdate())
insert into test(descripcion, fecha) values('Fusce ullamcorper mi ut lorem cursus, sed euismod nunc auctor. ', getdate())
insert into test(descripcion, fecha) values('Mauris id massa eget ligula efficitur pellentesque.', getdate())
GO

CREATE PROC USP_Test_List
AS
BEGIN
	SELECT * FROM test
END
GO

CREATE PROC USP_Test_ListById (@id int)
AS
BEGIN
	SELECT * FROM test where id = @id
END
```

----
### Paso 1: Archivo de configuración
Empezaremos creando un archivo al cual nombraremos como `config.js`, en este archivo se encontrará la cadena de conexión.

```
const config = {
    connectionSQL: "Data Source=[server];Initial Catalog=[database];Persist Security Info=True;User ID=[user];Password=[pass];"
}

module.exports = config;
```

----
### Paso 2: Conexión y operaciones
Creamos un nuevo archivo al cual nombraremos como `sql.js`, en este archivo crearemos la conexión y las operaciones básicas (select, exec store-procedure).

```
"use strict";

const mssql = require('mssql');

module.exports = class Sql {
    constructor(stringConnection) {
        this.stringConnection = stringConnection;
    }

    connect() {
        mssql.on('error', err => {
            console.log(err);
            mssql.close();
        });

        return mssql.connect(this.stringConnection);
    }

    close() {
        return mssql.close();
    }

    async select(table) {
        return new Promise((resolve, reject) => {
            this.connect().then(pool => {
                return pool.request().query(`select * from ${table}`);
            }).then(result => {
                mssql.close();
                resolve(result);
            }).catch(err => {
                reject(err);
            });
        });
    }

    async selectById(table, id) {
        if (id == undefined || id === 0) {
            return await this.select(table);
        } else {
            return new Promise((resolve, reject) => {
                this.connect().then(pool => {
                    return pool.request().query(`select * from ${table} where id=${id}`);
                }).then(result => {
                    mssql.close();
                    resolve(result);
                }).catch(err => {
                    reject(err);
                });
            });
        }
    }

    async execStoreProcedure(storeProcedure){
        return new Promise((resolve, reject) => {
            this.connect().then(pool => {
                return pool.request().execute(storeProcedure);
            }).then(result => {
                mssql.close();
                resolve(result);
            }).catch(err => {
                reject(err);
            });
        });
    }

    async execStoreProcedureById(storeProcedure, parameter){
        return new Promise((resolve, reject) => {
            this.connect().then(pool => {
                return pool.request()
                    .input("id", mssql.Int, parameter)
                    .execute(storeProcedure);
            }).then(result => {
                mssql.close();
                resolve(result);
            }).catch(err => {
                reject(err);
            });
        });
    }

}
```

----
### Paso 3: Ejecuciones
Para ejecutar crearemos una funcion asincrona autoejecutable de la siguiente manera:

```
"use strict";

const config = require("./config");
const sqlConnection = require("./sql");
const sql = new sqlConnection(config.connectionSQL);

(async () => {
    try {
        let select = await sql.select("test");
        console.log(select.recordset);
    } catch (error) {
        sql.close();
        console.log(error);
    }
})();
```

Ejecución de store procedure.

```
"use strict";

const config = require("./config");
const sqlConnection = require("./sql");
const sql = new sqlConnection(config.connectionSQL);

(async () => {
    try {
        let select = await sql.execStoreProcedureById("USP_Test_ListById", 1);
        console.log(select.recordset);
    } catch (error) {
        sql.close();
        console.log(error);
    }
})();
```

- Para verlo en acción solo debemos de ejecutar lo siguiente en la consola: `node [nombre-archivo].js` 
- El retorno siempre es en formato JSON

> Para obtener el código completo dar click [aquí](https://github.com/PORTAFOLIO-PROYECTOS/NODE_JS_SQLSERVER/archive/master.zip)
#### Comenta, disfruta y comparte! 