---
title: "¿Que son las aplicaciones web progresivas?"
date: 2019-07-21T10:07:47+06:00
description: "En este pequeña publicación vamos a definir y entender que es una aplicación web progresiva. Empecemos hablando de los beneficios de crear una página web sobre una aplicación nativa. "

image: images/post/2019-07-21-AppWebProgresiva-que-es.jpg

tags:
 - nodejs
 - mongodb
 - JS

categories:
  - Tutoriales

type: 'post'
---

En esta pequeña publicación vamos a definir y entender que es una aplicación web progresiva. 

Empecemos hablando de los beneficios de crear una página web sobre una aplicación nativa. 

- Son **linkeables**, eso quiere decir que te puedes mover entre páginas con un simple y sencillo link.
- Son **indexables**, aquí hacemos referencia el hecho de que uno puede navegar y buscar entre páginas de manera simple a diferencia de las aplicaciones nativas.
- Tambien son **multiplataforma**, una página web corre en cualquier navegador sin la necesidad de que modifiquemos el código
- Y por ultimo son **fáciles de actualizar** ha diferencia de las aplicaciones nativas donde hay un proceso más lento de modificaciones a través de las tiendas.

Por otro lado las aplicaciones nativas aventajan a las páginas web en lo siguiente:

- Pueden funcionar **con o sin internet**, ahí frente a diferencia las páginas web que requiere una conexión de internet pues para acceder a la página.
- Tienen acceso al **hardware** el dispositivo, cosas como la cámara el GPS los micrófonos y otras cosas.
- Tienen carga inmediata el proceso del rénder una aplicación nativa es más rápido porque lo que necesitan para mostrar la vista inicial ya se encuentra en el dispositivo a diferencia de las páginas web que tienen que descargar el contenido de un servidor remoto.
- **Push notifications**, las aplicaciones nativas reciben una notificaciónes push que tienen un montón de beneficios que nos vamos a ir de discutir en esta ocasión.

Ahora bien las aplicaciones web progresivas como te habrás imaginado busca en combinar lo mejor de ambos mundos, cumplen con las características de una página web pues porque prácticamente son una página web, además cumplen con muchas de las caracteristicas que destacan a una aplicación nativa, reciben push notifications, funcionan offline, tienen acceso al hardware vía las APIS de HTML5 y cargan de inmediato.

La pregunta ahora es como lo hacen que hace, que hace una aplicación web progresiva distinta de a una página web y que las hace diferentes de una aplicación nativa, empecemos por lo más sencillo, una aplicación web progresiva se diferencia de una app nativa en múltiples aspectos:

- Se escribe con tecnologías web estándar como HTML, CSS y javascript.
- No se instala en vías tiendas propietarias como la Play Store o al AppStore.
- No usa código nativo como JAVA o SWIFT, en pocas palabras no funciona como una aplicación nativa, simplemente no es una, no tiene acceso a las mismas Apis ni al mismo hardware.

Diferenciarlas de una página web tradicional requiere de un poquito mas de detalle porque vamos a decir que una aplicación web progresiva es un tipo de página web, una página web con capacidades añadidas, veamos cuáles son estas capacidades extras como se agreguen y por qué lo hacemos.

### 1. Offline

Las aplicaciones web progresivas tienen acceso sin internet, tradicionalmente sabemos que necesitamos internet para poder acceder a una página web, en términos sencillos para que podamos visitar una página el navegador envía una petición al servidor en el que ésta se aloja, ese servidor puede encontrarse en cualquier parte del mundo y es precisamente vía el internet que buscamos y nos conectamos con dicho servidor, una aplicación web progresivo utilice una tecnología de nombre **service workers** que actúa como un pequeño servidor dentro del navegador que permite mostrar páginas aunque no halla internet o estemos usando una conexión lenta, para poder hacer esto el navegador implementa un cache propio donde podemos guardar los archivos que no cambian dentro de nuestra página para que sea este el servidor interno el que los envié desde el caché local en lugar de tener que ir hasta el servidor remoto, el beneficio de esto es que cuando no existe conexión a internet es la service worker la que se encarga de entregar el contenido que tiene guardado.
- Instalación local, vía un archivo de configuración al que llamamos **manifesto** el navegador sabe que tu APP se puede agregar a la pantalla principal de un teléfono. haciendo que tu aplicación web se muestren prácticamente como una aplicación nativa.

 ![manifesto.jpg](/images/PWA-que-es/manifesto.JPG)

 Los navegadores que soportan esta funcionalidad muestran un banner en la parte inferior de la pantalla para hacer saber al usuario que la página se puede agregar al home de su teléfono, el proceso de **"instalación local"** implica agregar un icono al home pero además algo que mencionamos antes, guarda parte de la página del caché para mostrarlo desde el navegador en lugar de descargarlo del servidor remoto. 

 ![manifesto.jpg](/images/PWA-que-es/home.JPG)

### 2. Carga inmediata
Como parte de la creación de aplicaciones web progresivas introduce el concepto de **APP Shell**, el Shell de tu aplicación es aquello que se debe mostrar inicialmente, es como el Layout de la estructura que rodea tu contenido.

 ![manifesto.jpg](/images/PWA-que-es/app-shell.JPG)

El Shell debe ser estático y puede incluir cosas como el encabezado, el menú y probablemente un indicador de que la aplicación se está cargando, la ventaja de definir esta sección de tu página es optimizarlo para que se muestre de inmediato, eso podría significar construir el app shell desde el servidor, cachearlo con service workers y servirlos sin la necesidad de esperar una respuesta del servidor remoto.
 
### 3. Notificaciones push
Una de las características de las notificaciones push es que se ejecutan sin ninguna acción del usuario, vienen de algún lado, incluso cuando la app ni siquiera está abierta, no quiero que andemos mucho en por qué es complicado en el desarrollo web tradicional hacer esto pero lo es, básicamente en la mayoría en los casos el cliente sabe acerca del servidor pero el servidor no sabe nada acerca del cliente, no sabe dónde está, de manera que él servidor no podría enviarle push notifications, por otro lado usualmente entendemos que una página se muere, se acaba por completo cuando cerramos las pestañas, entonces cómo podríamos hacer esto, resulta ser que la **service worker** es una tecnologías que se ejecutan en el background, lo que permite introducir nuevas tecnologías para notificaciones push o actualizaciones en el background incluso cuando la página está cerrada cosas que no se podían hacer antes.

### 4. Hardware
Desde hace ya varios años los navegadores no son lo que parece, tienen capacidades extendidas que les permiten acceder a hardware del usuario, la Apis son un montón y muy diversas desde acceso a la cámara, acceso al GPS, sensores de iluminación, almacenamiento local, bases de datos internas y muchas más.


### 5. Diseño responsive
No lo había mencionado antes pero una parte esencial de crear una aplicación web progresivo es que funcione en cualquier dispositivo, desde teléfonos hasta un reloj inteligente. 
Esta necesidad implica por un lado aprender diseño responsiva pero además estrategias de experiencia de usuario que se adapten a los cambios en el tamaño de cada una de las pantallas, aquí podemos apoyarnos de cosas como material desing, que es la guía de diseño que Google creó para aplicar a sus productos, material desing es un tema que incluye colores, grillas y componentes pero además algo muy importante animaciones y transiciones que forman parte de la experiencia con la que el usuario navega dentro de tu aplicación.


En conclusión, podemos ver que las aplicaciones web progresivas hacen uso de múltiples tecnologías para poder ofrecer la experiencia de una app nativa sin perder los beneficios de ser una aplicación web.
Progressive web app es más acerca de una experiencia que de una herramienta, un lenguaje o un framework, por lo tanto a pesar de de que es fácil enlazar una serie de tecnologías como la service Workers construir una aplicación web progresiva es más bien utilizar las herramientas existentes de manera creativa para poder ofrecer una experiencia enriquecida para nuestros usuarios.

#### Comenta, disfruta y comparte! 
