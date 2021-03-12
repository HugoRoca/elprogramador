---
title: "Scripts SQLServer"
date: "2018-04-29"
description: "Les comparto algunos scripts que me sirvieron de mucho durante el tiempo que llevo trabajando, y es más aún los sigo usando."

image: images/post/2018-04-29-scripts-sqlserver.jpg

tags:
 - SqlServer

categories:
 - Base de datos

type: "post"
---

Les comparto algunos scripts que me sirvieron de mucho durante el tiempo que llevo trabajando, y es más aún los sigo usando.

## Split
```sql
-- select * from dbo.[Split]('1,2,3,4,5,6',',')
CREATE FUNCTION [dbo].[Split] (
	@ItemList NVARCHAR(MAX)
	,@delimiter CHAR(1)
	)
RETURNS @IDTable TABLE (
	nro INT identity(1, 1)
	,Item VARCHAR(MAX)
	)
AS
BEGIN
	DECLARE @tempItemList NVARCHAR(MAX)
	SET @tempItemList = @ItemList
	DECLARE @i INT
	DECLARE @Item NVARCHAR(MAX)
         
	SET @i = CHARINDEX(@delimiter, @tempItemList)

	WHILE (LEN(@tempItemList) > 0)
	BEGIN
		IF @i = 0
			SET @Item = @tempItemList
		ELSE
			SET @Item = LEFT(@tempItemList, @i - 1)

		INSERT INTO @IDTable (Item)
		VALUES (@Item)

		IF @i = 0
			SET @tempItemList = ''
		ELSE
			SET @tempItemList = RIGHT(@tempItemList, LEN(@tempItemList) - @i)

		SET @i = CHARINDEX(@delimiter, @tempItemList)
	END

	RETURN
END
```
## Encriptación
Recomendado para contraseñas y/o datos sensibles.
```sql
CREATE FUNCTION dbo.EncriptarClave (@Clave NVARCHAR(MAX))
RETURNS NVARCHAR(MAX)
AS
BEGIN
	SET @Clave = UPPER(@Clave)

	DECLARE @ClaveEncriptada NVARCHAR(MAX);

	SELECT
		@ClaveEncriptada = UPPER(
            SUBSTRING(master.dbo.fn_varbintohexstr(HASHBYTES('SHA1', @Clave)), 3, 40)
            );

	RETURN @ClaveEncriptada;
END
/*SELECT dbo.EncriptarClave('HUGO')
Esto no es reversible, una vez encriptada ya no se puede desencriptar, se recomienda guardar log.
*/
```

## Exists store, function, index
```sql
IF EXISTS(SELECT * FROM sys.SYSOBJECTS A INNER JOIN SYS.SCHEMAS B ON A.uid =B.schema_id 
    WHERE A.NAME='Gen_SelBandejaProducto_SP' 
        AND B.name='dbo')
 DROP PROCEDURE Gen_SelBandejaProducto_SP 
GO

-- para indeces 
IF EXISTS (SELECT * FROM sys.indexes 
    WHERE name='Cedula_INDEX' 
        AND object_id = OBJECT_ID('CertificadoTributarioDetalle'))
BEGIN
	DROP INDEX Cedula_INDEX ON dbo.CertificadoTributarioDetalle; 
END
```

## Formato miles
```sql
select CONVERT(varchar(50), CONVERT(money, 12345.6), 1)
```

## Try Catch
```sql
BEGIN
BEGIN TRY
BEGIN TRANSACTION  
BEGIN
<<Cuerpo de procedimiento>>
END
COMMIT TRAN
END TRY
BEGIN CATCH
	IF @@TRANCOUNT > 0
	BEGIN
		ROLLBACK TRAN
	END
DECLARE @nLinea INT=ERROR_LINE()                            
DECLARE @cError VARCHAR(8000)=ERROR_MESSAGE()                            
DECLARE @cStored varchar(500)=(SELECT TOP 1 name FROM sys.objects 
                                WHERE object_id=@@PROCID)                                           
    EXEC SIS_RegitrarError @nLinea, @cError, @cStored
END CATCH
END 

/**************************/
CREATE PROCEDURE [dbo].[SIS_RegitrarError] @nLineaError INT
	,@cDescrip VARCHAR(8000)
	,@cNombreStore VARCHAR(500)
AS
BEGIN
	INSERT INTO Errores (
		dFecha
		,nLineaError
		,cDescrip
		,cDatos
		,cStore
		)
	VALUES (
		GETDATE()
		,@nLineaError
		,@cDescrip
		,CONVERT(VARCHAR(50), HOST_NAME() + ' - ' + SUSER_SNAME(SUSER_SID()))
		,@cNombreStore
		)     
END
```

## Select XML
```sql
DECLARE @oData XML = '<GENESYS><DATA nombre="Hugo" apellido="Roca" /></GENESYS>'
DECLARE @nDoc int
EXEC sp_xml_preparedocument @nDoc OUTPUT,@oData
SELECT *	
FROM openxml(@nDoc, '/GENESYS/DATA', 1) 
WITH (
    nombre varchar(50), 
    apellido varchar(50)
)
```

## Buscar tabla en store
```sql
SELECT Name
FROM sys.procedures
WHERE OBJECT_DEFINITION(OBJECT_ID) LIKE '%table%'
Go

------
SELECT DISTINCT so.name
FROM syscomments sc
INNER JOIN sysobjects so ON sc.id=so.id
WHERE sc.TEXT LIKE '%table%'
Go

----- Con tipo, si es store o function
SELECT DISTINCT o.name, o.xtype
FROM syscomments c
INNER JOIN sysobjects o ON c.id=o.id
WHERE c.TEXT LIKE '%SolicitudCredito%'
go
```

## Ceros a la izquierda
```sql
select RIGHT('0000000000' + Ltrim(Rtrim('987654')),10)
```

## Enviar correo
```sql
CREATE PROCEDURE [dbo].[PA_EnviarCorreoDBA] @pcTo AS VARCHAR(255)
	,@pcCC AS VARCHAR(255)
	,@pcSubject AS VARCHAR(255)
	,@pcMessage AS VARCHAR(max)
AS
BEGIN
	EXEC msdb..sp_send_dbmail @profile_name = 'Database Management'
		,@recipients = @pcTo
		,@copy_recipients = @pcCC
		,@subject = @pcSubject
		,@body = @pcMessage
END
```

## Config de parametros
```sql
CREATE PROCEDURE dbo.SCC_Configurar_Parametros
AS
--ACTIVAR VOLCADO DE DATOS A ARCHIVOS PLANOS  
EXEC sp_configure 'show advanced options',1;

-- To update the currently configured value for advanced options.  
RECONFIGURE
WITH OVERRIDE;

-- To enable the feature.  
EXEC sp_configure 'xp_cmdshell',1;

-- To update the currently configured value for this feature.  
RECONFIGURE
WITH OVERRIDE;

/************************/
CREATE PROCEDURE dbo.SCC_Configurar_Parametros_Cerrar
AS
-- To enable the feature.  
EXEC sp_configure 'xp_cmdshell',0;

-- To update the currently configured value for this feature.  
RECONFIGURE
WITH OVERRIDE;

--ACTIVAR VOLCADO DE DATOS A ARCHIVOS PLANOS  
EXEC sp_configure 'show advanced options',0;

-- To update the currently configured value for advanced options.  
RECONFIGURE
WITH OVERRIDE;
```
Comenta, disfruta y comparte! 
