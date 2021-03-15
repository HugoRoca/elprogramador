---
title: "Configuración de una cuenta en MongoDB Atlas"
date: "2019-05-06"
description: "MongoDB Atlas ofrece la base de datos líder mundial para aplicaciones modernas como un servicio en la nube totalmente automatizado, diseñado y ejecutado por el mismo equipo que construye la base de datos."

image: images/post/2019-05-06-mongodb-atlas.jpg

tags:
  - mongodb
categories:
  - base de datos

type: 'post'
---

MongoDB Atlas ofrece la base de datos líder mundial para aplicaciones modernas como un servicio en la nube totalmente automatizado, diseñado y ejecutado por el mismo equipo que construye la base de datos.

Antes de empezar la configuración debemos de entender que es MongoDB Atlas, que ventajas y caracteristicas nos ofrece a comparación de tenerlo en modo local.

> MongoDB Atlas es la base de datos como servicio que permite implementar, utilizar y escalar una base de datos de MongoDB con tan solo unos pocos clics.

Las caracteristicas que ofrece MongoDb Atlas son:

- **Automatización**: la manera más fácil de crear, lanzar y escalar aplicaciones en MongoDB.
- **Flexibilidad**: La única base de datos como servicio con tofo lo necesario para las aplicaciones modernas.
- **Seguridad**: disponible varios niveles de seguridad.
- **Escalabilidad**: gran escalabilidad sin interumpir la actividad.
- **Alta disponibilidad**: implementaciones con tolerancia a errores y autoreparación predeterminadas.
- **Alto rendimiento**: el rendimiento necesario para las cargas de trabajo más exigentes.

Ventajas de MongoDB Atlas

- **Ejecución**
  - Puesta en marcha de un clúster en segundos
  - Implementaciones replicadas y sin interrupción
  - Total escalabilidad: escalado horizontal o vertical en tan solo unos clics y sin interrumpir la actividad.
  - Revisines automáticas y actualizaciones simpleficadas para las nuevas funciones de MongoDB

- **Protección y seguridad**
  - Autenticación y cifrado
  - Copias de seguridad continuas con recuperación en un punto en el tiempo.
  - Supervición detallada y alertas personalizadas.

- **Libertad de movimiento**
  - Modelo de planes de precio según demanda: se factura por hora.
  - Compatible con diferentes tipos de de servicios de nube *(AWS, GCP, Azure)*.
  - Parte de un paquete de productos y servicios para todas las fases de la aplicación.

La configuración se realizará desde una cuenta free, MongoDb Atlas tambien ofrece una versión de pago. 

### Configuración de cuenta

Nos dirigimos a la página oficial de [MongoDB](https://www.mongodb.com/) y selecionamos ***TRY FREE*** y nos registramos con un correo.

![Sin titulo](/images/mongodb-atlas/paso1.jpg)

Una vez puesto los datos y haber presionado en el botón **Get started free** redirrecionará a la siguiente ventana donde nos muestra lo que tendra nuestro clúster. 

![Sin titulo](/images/mongodb-atlas/paso2.jpg)

Continuamos dando clic en **Create clúster**

![Sin titulo](/images/mongodb-atlas/paso3.jpg)

Nos redireccionará a un dashboard donde el clúster aún se seguirá creando, pero podemos explorar mientras se crea en segundo plano. Una vez finalizado la creación realizamos lo siguiente:

![Sin titulo](/images/mongodb-atlas/paso4.jpg)

![Sin titulo](/images/mongodb-atlas/paso5.jpg)

**Punto 1:** MongoDB Atlas nos ofrece una seguridad de conexión por IP, esto quiere decir que podemos configurarlo de 2 maneras.

  - **Add You Current IP Address** con esta opcion podemos poner nuestra IP, lo malo de configurar esta opción es que cada vez que nos cambiamos de laptop o PC debemos de volver a configurarlo.
  - **Add a Different IP Address** con esta opción podemos configurar una IP que permita las conexiones de cualquier laptop o PC, por ejemplo podemos colocar la siguiente IP **0.0.0.0/0**.

![Sin titulo](/images/mongodb-atlas/paso6.jpg)

**Punto 2** Configuración de usuario de acceso

![Sin titulo](/images/mongodb-atlas/paso7.jpg)

Al realizar todo lo anterior, va quedando así:

![Sin titulo](/images/mongodb-atlas/paso8.jpg)

Despues del paso anterior procedemos a obtener la cadena de conexión, nos mostrará 3 opciones de la cual seleccionamos la siguiente:

![Sin titulo](/images/mongodb-atlas/paso9.jpg)

![Sin titulo](/images/mongodb-atlas/paso10.jpg)

Con la URI copiada podras acceder desde tu GUI para MongoDB preferida.

Listo! ya tienes un servidor de MongoDB, ahora solo queda crear base de datos y colecciones.