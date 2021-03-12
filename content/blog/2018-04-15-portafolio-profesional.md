---
title: "Portafolio Profesional"
date: "2018-04-15"
description: "¿Alguna véz haz querido hacer un curriculum profesional usando algo diferente? En esta ocación te traigo el siguiente sistema."

image: images/post/2018-04-15-portafolio-profesional.jpg

categories:
  - "Sistemas"
  
tag:
 - CSharp
 - JQuery
 - SQLServer

type: "post"
---

¿Alguna véz haz querido hacer un curriculum profesional usando algo diferente? En esta ocación te traigo el siguiente sistema.

Sistema de administración de portafolio desarrollado en C#, MVC5, Entity Framework, JQuery, Bootstrap. Esta formado en dos partes:

* Lo que se muestra al usuario final (Front)
* Parte administrable 
    - Edición de datos 
    - Registro y edición de estudios 
    - Registro y eliminación de hablidades 
    - Registro y edición de experiencia 
    - Edición de testimonios

## Paso 1
Debes de tener instalado visual studio y sqlsever.

Puedes obtener el codigo fuente de la siguiente forma:

1. Descargar el repositorio [PORTAFOLIO PROFESIONAL](https://github.com/PORTAFOLIO-PROYECTOS/PORTAFOLIO-PROFESIONAL/archive/master.zip)
2. Abrir Visual Studio
3. Buscar la solución y abrir
4. Tambien puedes hacer 'FORK' y abrirlo directo de GitHub

## Paso 2
Ejecutar el script de base de datos ```App/dataBase/base_de_datos.sql.sql``` en SQLServer.


## Paso 3
Configurar el archivo ```App/Portafolio.Web/Web.config```
```
<!-- 
Si tienes configurado el sqlserver con windows autentication solo debes de 
cambiar el data source por => "."; En caso que tenga un usuario y contraseña 
solamente agregarlo
-->
<connectionStrings>
    <add name="PortafolioContext" 
        connectionString="
            data source=HUGOROCA;
            initial catalog=portafolio;
            integrated security=True;
            MultipleActiveResultSets=True;
            App=EntityFramework" 
        providerName="System.Data.SqlClient" />
</connectionStrings>
```

## Paso 4
Ejecutar el proyecto, para acceder al administrador solamente debes de agregar a la URL "/Admin"

```
Ejemplo: http://localhost:666/admin
```

Comenta, disfruta y comparte! 
