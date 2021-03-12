---
title: "Entity Framework Básico"
date: "2018-05-06"
description: "Una forma mas rápida de realizar CRUD's, a tan solo unos clicks. Este es el poderosisimo Entity Framework"

image: images/post/2018-05-06-EntityFramework-basico.jpg

tags:
 - SQLSERVER
 - CSharp

categories:
 - Base de datos

type: "post"
---

> Una forma mas rápida de realizar CRUD's, a tan solo unos clicks. Este es el poderosisimo Entity Framework

## Paso 1
Puedes obtener el codigo fuente de la siguiente forma:

1. Descargar el repositorio [ENTITY-FRAMEWORK-BASICO](https://github.com/PORTAFOLIO-PROYECTOS/ENTITY-FRAMEWORK-BASICO/archive/master.zip)
2. Abrir la solución y explorar el código fuente

## Paso 2
Ejecutar lo siguiente en SQLServer
```
CREATE DATABASE Test
GO

USE Test
GO

CREATE TABLE [dbo].[Alumno](
	[id] [int] IDENTITY(1,1) NOT NULL,
	[Nombre] [varchar](50) NOT NULL,
	[Apellido] [varchar](100) NOT NULL,
	[Sexo] [int] NOT NULL,
	[FechaNacimiento] [varchar](10) NOT NULL
    primary key (id))
GO
```

## Paso 3
Una vez ejecutado el script, abrir el archivo App.config y cambiar las siguientes lineas:
```
<connectionStrings>
    <add name="TestContext" 
    connectionString="
        data source=.; 
        initial catalog=Test;
        user id=sa; => 'cambiar por tu usuario o en caso no tengas, quitar'
        password=1234; => 'cambiar por tu usuario o en caso no tengas, quitar'
        MultipleActiveResultSets=True;
        App=EntityFramework" 
        providerName="System.Data.SqlClient" />
</connectionStrings>
```

## Paso 4
Ejecutar y puedes comenzar a realizar registros... Comenta, disfruta y comparte! 

