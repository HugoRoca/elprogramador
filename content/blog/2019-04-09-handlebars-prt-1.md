---
title: "Handlebars | Parte 1"
date: "2019-04-09"
description: "Handlebars.js es un motor de plantillas muy popular que es potente, fácil de utilizar y que cuenta con una gran comunidad. Se basa en el lenguaje de plantillas Mustache, pero lo mejora de ..."

image: images/post/2019-04-09-handlebars-prt-1.jpg
tags:
 - Handlebars
 - JS

categories:
 - Tutoriales

type: 'post'
---

> Handlebars.js es un motor de plantillas muy popular que es potente, fácil de utilizar y que cuenta con una gran comunidad. Se basa en el lenguaje de plantillas Mustache, pero lo mejora de distintas maneras.

Los sistemas de plantillas son muy importantes, porque nos permiten separar el código por responsabilidades y crear la salida de las aplicaciones de una manera sencilla, manteniendo el código HTML separado del código Javascript.

### Instalación
Para poder descargar el cndjs has click [aqui](https://cdnjs.cloudflare.com/ajax/libs/handlebars.js/4.1.2/handlebars.min.js).

### ¿Por qué son necesarios los motores de plantillas?
Para entender en que situaciones nos puede venir bien el uso de un motor de plantillas de javascript, vamos a ver un sencillo ejemplo de unos de los problemas que nos podemos encontrar que con código javascript convencional no suele resolverse de una manera óptima.
Para ello va ser una acción tan simple como la incluir un listado de elementos html en un código html existente mediente javascript la que nos permita apreciar las bondades de un motor de plantillas en este lenguaje.

```js
let alumnos = [{nombre:'Marcos', edad:20}, {nombre:'Armando', edad:22}, {nombre:'Guzmán', edad:24}];
```
Por un la lado tenemos un array de alumnos que en su interior solo tiene un nombre y una edad.
```js
function generarListadoAlumnos_A (alumnos) {
    var total = alumnos.length;
    var html = '';
    var i = 0;

    html = '<dl>';
    for (i=0; i<total; i++) {
        html += '<dt>'+alumnos[i].nombre+'</dt>'+'<dd>('+alumnos[i].edad+')</dd>';
    }
    html += '</dl>';

    return html;
}

$(document).ready(function() {			
    $('#contenedor').append(generarListadoAlumnos_A(alumnos));
})
```

En la función `generarListadoAlumnos_A` lo que hacemos es generar un listado, en ests caso una lista de definición mediante la etiqueta `<dl>` que va recorriendo ese array de alumnos y va pintando el nombre y la edad.

¿Qué problema tiene esta solucion? Tiene el problema de la importante mezcla que se esta dando entre etiquetas html y código javascript, como podemos ver simplemente en esta linea `html += '<dt>'+alumnos[i].nombre+'</dt>'+'<dd>('+alumnos[i].edad+')</dd>';` estamos haciendo varias concatenaciones que no es difícil imaginar que en un escenario más complejo que está pequeña lista que estamos usando de ejemplo se convierte en un código difícilmente mantenible, podríamos hacer una aproximación un poco más elaborada, de hecho mucha gente cuando se va enfrentando a este problema es lo que hace, que podría llevarnos a una solución alternativa como la que vamos a ver a continuación
```js
function generarListadoAlumnos_B ( alumnos ) {
    let total = alumnos.length;
    let html = '';
    let i = 0;
    let templateAlumno = '<dt>{{nombre}}</dt><dd>({{edad}})</dd>';
    let alumno = '';

    html = '<dl>';
    for (i=0; i<total; i++) {
        alumno = templateAlumno.replace('{{nombre}}', alumnos[i].nombre);
        alumno = alumno.replace( '{{edad}}', alumnos[i].edad);
        html += alumno;
    }
    html += '</dl>';

    return html;
}
```

Esta función lo que hace en vez de generar la combinación de etiquetas html y datos en una única línea lo que hacemos es tener la línea ya preparada mediante una cadena de texto donde identificamos donde va ir el nombre y la edad y hacemos un reemplazo del nombre y de la edad por estas etiquetas que nos hemos sacado un poco de la manga `{{nombre}}` y `{{edad}}` esto es un poco más limpio porque digamos que la plantilla de cada elemento del listado la tenemos en un único String con lo cual está más o menos separada pero aún así seguimos mezclando bastante y se nos dan situaciones que no resuelve como por ejemplo el hecho de que el nodo que abre la lista y el nodo que la cierra pues tienen que ir a la vez concatenadas en el bucle.

Pero sigue siendo una solución que no acaba de ser la más eficiente estas dos formas no son una forma limpia. Precisamente los motores de plantillas vienen para solucionar esta combinación de datos con html en un entorno del lado del cliente es decir en un entorno controlado por javascript.

### Conceptos básicos
Los componentes principales de cualquier motor de plantilla son :

- Por un lado evidentemente la `plantilla html` que normalmente es la representación visual de la información que queremos o bien modificar en la página existente o bien incluir como contenido nuevo y esta plantilla lo que persigue normalmente es ser una mera representación lo mas cercana html posible de manera que no tenga ninguna complejidad adicional.
- Por otro lado tenemos los `datos` que son los que se combinarán con la plantilla para obtener el resultado final, es decir si tenemos una plantilla para mostrar la ficha de una película pues tendríamos que tener por otro lado los datos de la película que completarán esa plantilla para personalizarla en el caso de esa película en concreto, pues normalmente cómo nos podemos imaginar estos datos vienen o bien de la introducción manual desde un formulario por parte del usuario o bien desde una petición al servidor que nos devuelve dicha información en el caso de javascript en la mayoría de los casos en formato json.
- Por otro lado tenemos las `expresiones` que lo que nos permiten es precisamente unir esa plantilla con esos datos, estás expresiones son la herramienta que nos da el motor de plantillas para poder indicar en la plantilla dónde van a ir los datos e incluso poder hacer determinados operaciones con esos datos antes de que se procesan para dar el resultado final, estás expresiones es donde normalmente se encuentra la diferencia principal entre motores de plantillas ya que algunos tienden a aumentar su complejidad y su potencia para que el propio motor de plantilla contemple diferentes opciones sobre los datos a la hora de mostrarlos en la plantilla y hay otra aproximación que es la que da handlebars que es que se diferencia al máximo posible lo que es la vista de la lógica de la aplicación de manera que la plantilla sea una plantilla pura, los datos vayan por separado y las expresiones sean lo más sencillas posible para que simplemente permitan incluir o no los datos, obviamente esta aproximación que tiene él va a extenderse con otras herramientas que nos van a permitir adaptar esa manera de pintar la plantilla en el caso de cada una de las necesidades de nuestro proyecto existe la diferencia entre los motores de plantillas principal en este punto que es como gestionar las expresiones o la manera de incluir los datos en la plantilla.
- Y finalmente están `los procesos de renderizado y compilación` en el caso de renderizado es el proceso a través del cual el motor consigue generar el html final para ser pintado en pantalla y por otro lado la compilación e incluso en algunos casos precompilacion es como gestionar esa plantilla a la hora de optimizar el trabajo con ella posteriormente al aplicar los datos, en el caso particular de handlebars permite la precompilacion incluso que como consecuencia nos ofrece un rendimiento notablemente superior a otros motores que no cuentan con esta característica.

Vamos a ver en este sencillo ejemplo de cómo hacer uso de las expresiones que es el mecanismo con el que cuenta el motor de plantillas en este caso handlebar para vincular los datos de los que disponemos a las zonas de la plantilla donde corresponda para ello vamos a crear un fichero html para poder mostar cómo se utilizan las expresiones.

```html
<div>
	<h2>{{titulo}}</h2>
	<h3>{{breve}}</h3>
	<p>{{descripcion}}</p>
	<img src="{{imagen}}" alt="{{breve}}" />
</div>
```
Evidentemente para que esto funcionará tendríamos que tener un objeto en javascript que tuviera esos campos título, breve, descripción y imagen de manera que handlebars fuera capaz de encontrar ese dato y hacer el reemplazo en la zona de la plantilla que correspondía, esto es el uso más sencillo de las expresiones en handlebars.


### El clásico Hola Mundo

```html
<!-- Hacemos refrencia al archivo handlebars -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/handlebars.js/4.1.2/handlebars.min.js"></script>

<!-- Creamos la plantilla en handlebar de la siguiente manera. -->
<script id="mi-plantilla" type="text/x-handlebars-template">
    ¡Hola mundo!
</script>

<!-- Luego agregamos la siguiente etiqueta -->
<div id="contenedor"></div>

<script>
// Hacemos uso de javascript para generar el handlebar
window.onload = function() {
    let contenedor;
    let origen;
    let plantilla;

    contenedor = document.getElementById('contenedor');
    origen = document.getElementById('mi-plantilla').innerHTML;

    // aquí se hace la compilación
    plantilla = Handlebars.compile(origen);

    contenedor.innerHTML = plantilla();
}
</script>
```

### Una plantilla más trabajada
```html
<!-- Hacemos refrencia al archivo handlebars -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/handlebars.js/4.1.2/handlebars.min.js"></script>

<!-- Creamos la plantilla en handlebar de la siguiente manera. -->
<script id="mi-plantilla" type="text/x-handlebars-template">
    <p>¡Hola {{nombre}}!</p>
    <!-- esta accidendo a esos datos a la pocisión 0 como si fuera un array y esta buscando ese campo nombre -->
    <p>¡Hola {{this.[0].nombre}}!</p>
</script>

<!-- Luego agregamos la siguiente etiqueta -->
<div id="contenedor"></div>

<script>
    $(document).ready(function() {
        var origen = $('#mi-plantilla').html();
        var plantilla = Handlebars.compile(origen);

        var persona_A = { nombre: 'Carlos', edad: 20};
        var persona_B = { nombre: 'Alicia', edad: 22};
        var alumnos = [persona_A, persona_B];

        $('#contenedor').html( plantilla(alumnos) );
    })
</script>

```
`This` hace alusión siempre al contexto mismo en el que nos encontramos en la plantilla, en este caso el array alumnos y vemos que la diferencia está en que al contrario de cómo ocurren en javascrit donde se utiliza `this` con el corchete seguido en handlebars para referirnos a un array hay que poner el punto siempre entre cada uno de los elementos, es decir el equivalente en javascript que tendríamos que aplicar para acceder al primer elemento del array alumno sería `this[0].nombre` y vemos como en el caso de handlebars el array también requiere del punto después del `this` es la única diferencia así notable que existe con la sintaxis habitual empleada en javascript y por lo tanto de esta manera podemos acceder a la posición de un array y concretamente a la propiedad nombre de elemento en caso de existir.

Cómo último detalle cabe destacar que este punto se puede sustituir en handlebars por el símbolo de la barra (`/` => `this/[0]/nombre`) precisamente también para hacerlo en parte compatible con mustasch que es el origen de handlebars. Personalmente prefiero el punto porque me parece que es mucho más legible a la hora de entenderlo como datos en JavaScript.

### Comentarios con handlebars
```html
<!-- Hacemos refrencia al archivo handlebars -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/handlebars.js/4.1.2/handlebars.min.js"></script>

<!-- Creamos la plantilla en handlebar de la siguiente manera. -->
<script id="mi-plantilla" type="text/x-handlebars-template">
    <p>¡Hola {{nombre}}!</p>
    {{! Esto es un comentario de Handlebars }}    
    {{!-- Esto es un tercer comentario con {{nombre}} --}}
</script>

<!-- Luego agregamos la siguiente etiqueta -->
<div id="contenedor"></div>

<script>
    $(document).ready(function() {
        var origen   = $("#mi-plantilla").html();
        var plantilla = Handlebars.compile(origen);
        var persona = { nombre: 'Carlos', edad: 30};
        
        $('#contenedor').html( plantilla(persona) );
    })
</script>

```

### Mi primera plantilla
```css
.poster, .info {
	box-sizing: border-box;
	float: left;
}

.poster {
	width: 50%;
	padding-right: 20px;
}

.poster img {
	width: 100%;
	height: auto;
}

.info {
	width: 50%;
	padding-left: 20px;
}

.clearfix {
	clear: both;
}
```
Guardamos el código anterior como `pelicula.css` y lo referenciamos al siguiente archivo.

```html
<!-- referencias -->
<link rel="stylesheet" href="css/pelicula.css">
<script src="https://cdnjs.cloudflare.com/ajax/libs/handlebars.js/4.1.2/handlebars.min.js"></script>

<!-- Creamos la plantilla en handlebar de la siguiente manera. -->
<script id="pelicula" type="text/x-handlebars-template">
    <div class="film">
        <div class="poster">
            <img src="{{cartel}}" alt="{{titulo}}" />
        </div>
        <div class="info">
            <h2>{{titulo}} ({{anio}})</h2>
            <p>Duración: {{duracion}} min.</p>
            <h3>Sinopsis</h3>
            <p>{{sinopsis}}</p>
            <p>{{{fichacompleta}}}</p>
        </div>
        <br class="clearfix" />
    </div>
</script>

<!-- Luego agregamos la siguiente etiqueta -->
<div id="contenedor"></div>

<script>
    $(document).ready(function() {
        
        let pelicula = { 
            titulo: 'Cadena Perpetua', 
            anio: 1994,
            duracion: 142,
            sinopsis: 'Dos hombres encarcelados durante años, que encuentran consuelo y redención a través de actos de decencia común.',
            cartel: 'https://gcdn.emol.cl/los-90/files/2016/08/Sueno-de-fuga.jpg',
            fichacompleta: '<a href="http://www.imdb.com/title/tt0111161/?ref_=nm_knf_i1" target="_blank">Ver ficha en IMDB</a>'
        };

        let origen = $('#pelicula').html();
        let plantilla = Handlebars.compile( origen );

        $('#contenedor').html( plantilla(pelicula) );
        
    })
</script>

```

El resultado sera el siguiente:

![](https://i.ibb.co/mtjvvb0/sue-osdefuga.png)