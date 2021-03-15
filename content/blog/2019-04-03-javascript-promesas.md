---
title: "Javascript: Promesas"
date: "2019-04-03"
description: "A una promesa, como su propio nombre lo dice, es simplemente un objeto que puede o no devolver algún valor en la línea de tiempo presente y futuro. Me gusta describir una promesa..."

image: images/post/2019-04-03-javascript-promesas.jpg

tags:
 - JS
categories:
 - Tutoriales

type: 'post'
---

> A una promesa, como su propio nombre lo dice, es simplemente un objeto que puede o no devolver algún valor en la línea de tiempo presente y futuro. Me gusta describir una promesa como una especie de Karma: Tú haces algo, y en consecuencia obtendrás algo, ahora o en un futuro. Esto aplica igual a las promesas, tu ejecutas código asíncrono y obtienes la promesa de que obtendrás una respuesta, que puede ser en ese instante o en un futuro.

```
// sintaxis de una promesa
let promise = new Promise(function(resolve, reject) {
  // executor (the producing code, "singer")
});
```

El `promise` objeto resultante tiene propiedades internas:

- `state` - inicialmente "pendiente", luego cambia a "cumplido" o "rechazado",
- `result` - Un valor arbitrario de su elección, inicialmente undefined.

Cuando el ejecutor finaliza el trabajo, debe llamar a una de las funciones que obtiene como argumentos:

- `resolve(value)` - para indicar que el trabajo terminó con éxito:
    - establece stateque "fulfilled",
    - establece resulta value.
- `reject(error)` - para indicar que ocurrió un error:
    - establece stateque "rejected",
    - establece resulta error.


![promise](https://i.ibb.co/QD7GSkd/promise-resolve-reject.png)

### Problema

Se requiere mostrar por consola los post con sus respectivos comentarios consumiendo la siguientes urls:

```
/*
post: https://jsonplaceholder.typicode.com/posts
commets: https://jsonplaceholder.typicode.com/comments?postId={id}
*/
```

La posible solucion que se nos viene a la mente es la siguiente:

```
"use strict";

const request = require("request");

(async () => {
    let url1 = "https://jsonplaceholder.typicode.com/posts";
    let url2 = "https://jsonplaceholder.typicode.com/comments?postId={id}";

    request(url1, (err, res, bodyPost) => {
        if (err) console.error(err);
        console.log("bodyPost", bodyPost);

        for (const key in bodyPost) {
            const element = bodyPost[key];
            let url = url2.replace("{id}", element.id);

            request(url, (e, r, bodyComments) => {
                if (e) console.error(e);
                console.log("bodyComments", bodyComments);
            })
        }
    });
})();
```

Pero tenemos un problema, el request que esta dentro del bucle `for` no esperara a que responda, eso causaría un enredo, la solución que se plantea será la siguiente en la cual usaremos promesas con funciones asincronas.

```
"use strict";

const request = require("request");

const config = {
    url: {
        post: "https://jsonplaceholder.typicode.com/posts",
        comments: "https://jsonplaceholder.typicode.com/comments?postId={id}"
    }
}

async function getPost() {
    return new Promise((resolve, reject) => {
        request(config.url.post, (err, res, body) => {
            if (err) reject(err);
            resolve(body);
        })
    });
}

async function getComments(id) {
    let url = config.url.comments.replace("{id}", id);
    return new Promise((resolve, reject) => {
        request(url, (err, res, body) => {
            if (err) reject(err);
            resolve(body);
        })
    });
}

(async () => {
    let posts = JSON.parse(await getPost());
    
    for (const key in posts) {
        const element = posts[key];

        console.log("post", element);

        let comments = await getComments(element.id);
        console.log("comments", comments);
    }

})();
```

#### Comenta, disfruta y comparte! 
