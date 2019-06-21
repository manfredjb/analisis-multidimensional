## DimParqueo
Creación de la tabla dim de parqueo.

### Script de creación de tabla
```sql
create table DimParqueo(
  IDParqueo numeric(10, 0) primary key,
  Distrito varchar(50) null,
  Canton varchar(50) null,
  Provincia varchar(50) null
);
```
A continuación la evidencia de la creación de la tabla.
<img src="https://i.imgur.com/aIkoR52.png">

### Select
```sql
select a.IDParqueo, b.Descripcion as distrito, c.Descripcion as canton, d.Descripcion as provincia
from Parqueo a
join Distrito b on a.ConsecutivoDistrito = b.ConsecutivoDistrito
join Canton c on b.IDCanton = c.IDCanton
join Provincia d on c.IDProvincia = d.IDProvincia
where b.IDProvincia = c.IDProvincia;
```

### Script completo
```sql


IF OBJECT_ID('dbo.DimParqueo', 'U') IS NOT NULL 
  DROP TABLE dbo.DimParqueo; 

create table DimParqueo(
IDParqueo numeric(10, 0) primary key,
Distrito varchar(50) null,
Canton varchar(50) null,
Provincia varchar(50) null
);

BEGIN TRAN

insert into DimParqueo
select a.IDParqueo, b.Descripcion as distrito, c.Descripcion as canton, d.Descripcion as provincia
from Parqueo a
join Distrito b on a.ConsecutivoDistrito = b.ConsecutivoDistrito
join Canton c on b.IDCanton = c.IDCanton
join Provincia d on c.IDProvincia = d.IDProvincia
where b.IDProvincia = c.IDProvincia;

GO

COMMIT TRAN
```

A continuación la evidencia de la ejecución del script anterior.
<img src="https://i.imgur.com/ZBEKauf.png">
