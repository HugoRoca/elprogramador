---
title: "CHAT en tiempo real con NodeJS, Express, BlueBirdJS, Socket.IO y mongoDB"
date: "2019-06-30"
description: "En este pequeño post te explicaré como crear una aplicación de chat en tiempo real paso a paso. Para entender todo este proceso se requiere que tengas conocimientos en nodeJS, mongoDB, javascript, html5 y css3."

image: images/post/2019-06-30-app-chat-express-socket.io-mongodb-bluebird.jpg

tags:
 - nodejs
 - mongodb
 - JS

categories:
    - Tutoriales

type: 'post'
---

En este pequeño post te explicaré como crear una aplicación de chat en tiempo real paso a paso. Para entender todo este proceso se requiere que tengas conocimientos en nodeJS, mongoDB, javascript, html5 y css3.

### Instalando paquetes y configuración
Lo primero que tenemos que hacer es crear un directorio en donde estarán todos nuestros archivos, puedes utilizar el editor de código que mas se adecue a tí. En mi caso usaré *VSCode*. 

En la consola ejecutamos la siguiente linea: `npm init` esto quiere decir que estamos iniciando el directorio como una aplicación nodeJS. Se te pedirá que completes algo de información. Esta se utilizará para configurar el archivo *package.json*.

Utilizaremos **express** para crear nuestro servidor web que alojará nuestros archivos estáticos y **body-parser** para extraer todo el cuerpo de una solicitud entrante.

```cmd
npm install express body-parser --save
```

A continuación, instalamos **mongoose**, esto es un ODM (Object Document Mapper) para MongoDB y hará que nuestro trabajo sea mucho más fácil. Vamos a instalarlo junto a **socket.io** y **bluebird**. Para tener algo de contexto de estas dos ultimas; **Socket.io** es una biblioteca de javascript para aplicaciones web en tiempo real. **Bluebird** es una biblioteca Promise con todas las funciones para javascript.

```cmd
npm install mongoose socket.io bluebird --save
```

> Puedes instalar nodemon para no estar iniciando el servidor con cualquier modificación que hagamos, esto es opcional. `npm install nodemon`

Este será nuestro árbol de archivos para esta app.
```
.
├── client
|   ├── css
|   |   └── style.css
|   ├── js
|   |   ├── chat.js
|   |   ├── fortmatTimeStamp.js
|   |   └── socket.io
|   └── index.html      
├── server
|   ├── controllers
|   |   └── chat.js
|   ├── mongoDB
|   |   ├── chatSchema.js
|   |   └── dbconnection.js
|   └── routes
|       └── chat.js
├── app.js
└── package.json
```

### Creando el lado cliente
```html
<!DOCTYPE html>
<html lang="es">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Chat</title>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css"
        integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
    <link rel="stylesheet" href="./css/style.css">
</head>

<body>
    <div class="container">
        <div class="row justify-content-md-center mt-3">
            <div class="col-md-7">
                <div class="card">
                    <div class="card-header">
                        <h4 class="text-center">CHAT</h4>
                    </div>
                    <div class="card-body">
                        <ul class="chat" id="listaMensajes">

                        </ul>
                    </div>
                    <div class="card-footer">
                        <small id="escribiendo">&nbsp;</small>
                        <form id="form">
                            <div class="input-group">
                                <input id="mensaje" type="text" class="form-control"
                                    placeholder="Escribe tu mensaje aquí..." autocomplete="off" />
                                <span class="input-group-append">
                                    <button class="btn btn-warning" id="btn-chat">Enviar</button>
                                </span>
                            </div>
                        </form>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>
    <script src="./js/formatTimeStamp.js"></script>
    <script src="./js/socket.io.js"></script>
    <script src="./js/chat.js"></script>
</body>

</html>
```

Este será nuestro archivo HTML que servirá para la interfaz, para instanciar socket.io en el cliente, puedes obtener el código haciendo click [aquí](https://github.com/socketio/socket.io-client/blob/master/dist/socket.io.js).

> formatTimeStamp.js nos servirá para obtener el momento en que se envio el mensaje, si fue hoy, ayer o la semana pasada. Puedes obtener el código dando click [aquí](https://cdn.jsdelivr.net/gh/rexeze/formatTimeStamp/src/index-cdn.js).

Antes de configurar el archivo **chat.js** debemos de centrarnos en el desarrollo del backend.

### Creando el lado servidor

Empezaremos creando la conexión con mongoDB, para ello crearemos dos archivos dentro de `./server/mongoDB/`.

El primero lo llamaremos **chatSchema.js**, aquí crearemos un esquema de la colección que se creará. 
Solo tendremos 3 campos en nuestro esquema: 

- un campo de mensaje
- un campo de remitente 
- una marca de tiempo.

```js
const mongoose = require("mongoose");
const Schema = mongoose.Schema;

const chatSchema = new Schema({
    message: {
        type: String
    },
    sender: {
        type: String
    }
}, {
    timestamps: true
});

let Chat = mongoose.model("chats", chatSchema);

module.exports = Chat;
```

Luego creamos el archivo **dbconnection.js**, aquí tendremos la conexión con mongoDB a partir del esquema creado y tambien veremos como implementamos **bluebird.js**.
```js
const mongoose = require("mongoose");
mongoose.Promise = require("bluebird");

const url = "localhost:1111/test"; //conexion mongo

const connect = mongoose.connect(url, { useNewUrlParser: true });

module.exports = connect;
```

Luego de haber realizado la conexión, crearemos el controlador, para ellos creamos un archivo dentro de `./server/controller/`, este archivo tendrá el nombre de **chat.js**. En este archivo referenciaremos la conexión y el esquema de mongo para realizar lo siguiente:
```js
"use strict";

const connectdb = require("../mongoDB/dbconnection");
const chat = require("../mongoDB/chatSchema");

exports.obtener = ((req, res, nect) => {
    res.setHeader("Content-Type", "application/json");
    res.statusCode = 200;

    connectdb.then(db => {
        chat.find({}).then(chat =>{
            res.json(chat);
        });
    });
});
```

Este controlador nos permitirá obtener un listado de todos los chats que se han registrado en mongoDB.

Estamos a pocos pasos de terminar, ahora solo queda crear la ruta y configurar todo dentro del archivo **app.js**. Para realizar el routeo del controlador debemos de crear un archivo dentro de `./server/routes/` al cual llamaremos chat.js, realizamos lo siguiente:
```js
const express = require("express");
const controller = require("../controllers/chat");
const router = express.Router();

router.route("/").get(controller.obtener);

module.exports = router;
```

### Configurando app.js
Empezaremos creando las referencias hacia los paquetes npm y los archivos creados asi como tambien el puerto donde se desplegará.
```js
const port = 2705;
const express = require("express");
const app = express();
const bodyParser = require("body-parser");
const http = require("http").Server(app);
const io = require("socket.io");
const socket = io(http);
const chatRouter = require("./server/routes/chat");
const chatSchema = require("./server/mongoDB/chatSchema");
const connectDB = require("./server/mongoDB/dbconnection");
```

Luego configuramos las rutas, ademas de eso configuramos bodyParser y una ruta estatica para mostrar el lado cliente
```js
app.use(bodyParser.json());
app.use("/chat", chatRouter);
app.use(express.static(`${__dirname}/client`));
```

Ahora viene lo bueno, configuraremos socket.io para los siguientes eventos:

- usuario conectado
- usuario desconectado 
- escribiendo
- dejar de escribir
- mensaje enviado

Empezaremos por el evento usuario conectado, este evento sera el contenedor de los demas eventos, basicamente funcionará como un *change stream*. De paso tambien configuramos el evento de usuario desconectado.
```js
socket.on("connection", (socket) => {
    console.log("usuario conectado");

    socket.on("disconnect", () => {
        console.log("usuario desconectado");
    });

    // aquí va evento escribiendo

    // aquí va evento dejar de escribir

    // aquí va mensaje enviado
});
```

> Si un nuevo usuario visita localhost:2705, se imprimirá el mensaje "usuario conectado" en la consola. Socket.on() toma un nombre de evento y una devolución de llamada como parámetros.

Ahora nos toca configurar la parte de escribir y dejar de escribir, esto es como un adicional, es muy sencillo implementarlo, hay que tener en cuenta que esto va dentro del evento "connection" del código anterior.
```js
socket.on("typing", data => {
    socket.broadcast.emit("notifyTyping", {
        user: data.user,
        message: data.message
    });
});

socket.on("stopTyping", () => {
    socket.broadcast.emit("notifyStopTyping");
});
```

Y por ultimo configuramos el evento principal, el que escuchará los mensajes y lo registrará en la base de datos.
```js
socket.on("chat message", (msg) => {
    console.log(`message: ${msg}`);

    socket.broadcast.emit("received", {
        message: msg
    });

    connectDB.then(db => {
        let mensaje = new chatSchema({
            message: msg,
            sender: "Anónimo"
        });
        mensaje.save();
    });
});
```

Dentro de este evento, podemos elegir lo que hacemos con el mensaje del cliente: insertarlo en la base de datos, enviarlo de vuelta al cliente, etc. En nuestro caso, lo guardaremos en la base de datos y también lo enviaremos al cliente.

Lo transmitiremos. Eso significa que el servidor lo enviará a todas las demás personas conectadas al servidor, aparte del remitente. Por lo tanto, si el Sr. A envía el mensaje al servidor y el servidor lo transmite, el Sr. B, C, D, etc. lo recibirá, pero el Sr. A no lo hará. No queremos recibir un mensaje que enviamos, ¿verdad? 😭

Eso no significa que no podamos recibir un mensaje que también enviamos. Si eliminamos la bandera de transmisión, también eliminaremos el mensaje.

Aquí es cómo se transmite un evento:
```js
socket.broadcast.emit("received", {
    message: msg
});
```

Y como un paso adicional pero muy importante es poner a escuchar nuestro servidor:
```js
http.listen(port, () => {
    console.log(`Corriendo en el puerto ${port}`);
});
```

### Configuración del archivo chat.js
Este archivo es parte del cliente, el que esta referenciado en el html, esto enviará y escuchará los eventos del servidor.
```js
"use strict";

const _SOCKET = io();

const chat = (() => {
    const _elementos = {
        mensajes: $("#listaMensajes"),
        escribiendo: $("#escribiendo"),
        inputMensaje: $("#mensaje")
    }

    const _utils = {
        crearHTML: ((mensaje, fecha) => {
            return `
            <li class="left clearfix">
                <div class="chat-body clearfix">
                    <div class="header">
                        <strong class="primary-font">Anónimo</strong>
                        <small class="pull-right text-muted">
                            <span class="glyphicon glyphicon-time"></span>${fecha}
                        </small>
                    </div>
                    <p>${mensaje}</p>
                </div>
            </li>`;
        }),

        scroolListaMensajes: (() => {
            $(".card-body").animate({
                scrollTop: $(this).height()
            }, "slow");
        })
    }

    const _eventos = {
        enviarMensaje: (() => {
            $("form").submit((e) => {
                e.preventDefault();

                if (!_elementos.inputMensaje.val() ||_elementos.inputMensaje.val() === "") return false;

                _SOCKET.emit("chat message", _elementos.inputMensaje.val());

                let html = _utils.crearHTML(_elementos.inputMensaje.val(), "justo ahora");

                _elementos.mensajes.append(html);

                _elementos.inputMensaje.val("");

                _utils.scroolListaMensajes();

                return false;
            });
        }),

        obtenerMensaje: (() => {
            _SOCKET.on("received", data => {
                let html = _utils.crearHTML(data.message, "justo ahora");
                _elementos.mensajes.append(html);
                _utils.scroolListaMensajes();
            });
        }),

        cargarMensajes: (() => {
            fetch("/chat").then(data => {
                return data.json();
            }).then(json => {
                json.map(data => {
                    let html = _utils.crearHTML(data.message, formatTimeAgo(data.createdAt));
                    _elementos.mensajes.append(html);
                });
                _utils.scroolListaMensajes();
            });
        }),

        detectarEscribir: (() => {
            _elementos.inputMensaje.on("keypress", () => {
                _SOCKET.emit("typing", {
                    user: "Someone",
                    message: "is typing..."
                });
            });

            _SOCKET.on("notifyTyping", data => {
                _elementos.escribiendo.html(data.user + " " + data.message);
            });
        }),

        detectarDejarEscribir: (() => {
            _elementos.inputMensaje.on("keyup", () => {
                _SOCKET.emit("stopTyping", "");
            });

            _SOCKET.on("notifyStopTyping", () => {
                _elementos.escribiendo.html("&nbsp;");
            })
        })
    }

    const inicializar = (() => {
        _eventos.enviarMensaje();
        _eventos.obtenerMensaje();
        _eventos.cargarMensajes();
        _eventos.detectarEscribir();
        _eventos.detectarDejarEscribir();
    });

    return {
        inicializar: inicializar
    }
})();

(() => {
    chat.inicializar();
})();
```

El código es muy fácil de entender.

Puedes mejorar este código, agregar autenticación, agregar grupos o convertirlo en un chat individual, volver a modelar el esquema para adaptarse a todo eso, etc.

Espero que esto te haya sido útil. El código completo está en Github. Puedes conseguirlo [aquí](https://github.com/PORTAFOLIO-PROYECTOS/AppChat_NodeJS_Socket.io_MongoDB/archive/master.zip).

#### Comenta, disfruta y comparte!