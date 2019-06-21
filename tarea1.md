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
<img src="https://i.imgur.com/nOZmQ3c.png">

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

## DimVehiculo
Creación de la tabla dim de parqueo.

### Script de creación de tabla
```sql
create table DimVehiculo(
  Tipo varchar(30),
  Fabricante varchar(30),
  Pais varchar(30),
  Abreviatura varchar(5),
  Region varchar(30),
  Continente varchar(30),
  EstratoAño varchar(30)
);
```
A continuación la evidencia de la creación de la tabla.
<img src="https://i.imgur.com/u5wqnht.png">

### Creación de un estracto
```sql
IF OBJECT_ID('dbo.Estratos', 'U') IS NOT NULL 
  DROP TABLE dbo.Estratos; 

create table Estratos(
id_estrato int IDENTITY(1,1) PRIMARY KEY,
descripcion varchar(100),
minimo int not null,
maximo int not null,
tipo varchar(30)
);

BEGIN TRAN
set IDENTITY_INSERT Estratos ON;
insert into Estratos (id_estrato, descripcion, minimo, maximo, tipo) values(1, 'Antiguo', 1, 1980, 'Año Auto');
insert into Estratos (id_estrato, descripcion, minimo, maximo, tipo) values(100, 'Viejo', 1981, 2000, 'Año Auto');
insert into Estratos (id_estrato, descripcion, minimo, maximo, tipo) values(200, 'Actual', 2001, 2010, 'Año Auto');
insert into Estratos (id_estrato, descripcion, minimo, maximo, tipo) values(300, 'Nuevo', 2011, 2018, 'Año Auto');
insert into Estratos (id_estrato, descripcion, minimo, maximo, tipo) values(400, 'Muy nuevo', 2019, 9999, 'Año Auto');
set IDENTITY_INSERT Estratos OFF;

GO

COMMIT TRAN
```
A continuación la evidencia de la ejecución:
<img src="https://i.imgur.com/wMDhEAR.png">

### Select
Select con datos del vehículo y estratos.
```sql
select b.Descripcion as TipoVeh, c.NombreFabricante, D.NombrePais, D.AbreviaturaPais,
e.Descripcion as Región, f.Descripcion as Continente, g.descripcion as Estrato from Vehiculo a
join TipoVehiculo b on a.IDTipoVehiculo = b.IDTipoVehiculo
join Fabricante c on a.IDFabricante = c.IDFabricante
join Pais d on c.IDPais = d.IDPais
JOIN RegionContinente e on d.IDRegionContinente = e.IDRegionContinente
join Continente f on e.IDContinente = f.IDContinente
join (select * from Vehiculo a, Estratos b where b.Tipo = 'Año Auto' and a.Ano between
b.Minimo and b.maximo) g on a.IDVehiculo = g.IDVehiculo;

```

### Script completo
```sql
IF OBJECT_ID('dbo.Estratos', 'U') IS NOT NULL 
  DROP TABLE dbo.Estratos; 

create table Estratos(
id_estrato int IDENTITY(1,1) PRIMARY KEY,
descripcion varchar(100),
minimo int not null,
maximo int not null,
tipo varchar(30)
);

BEGIN TRAN
set IDENTITY_INSERT Estratos ON;
insert into Estratos (id_estrato, descripcion, minimo, maximo, tipo) values(1, 'Antiguo', 1, 1980, 'Año Auto');
insert into Estratos (id_estrato, descripcion, minimo, maximo, tipo) values(100, 'Viejo', 1981, 2000, 'Año Auto');
insert into Estratos (id_estrato, descripcion, minimo, maximo, tipo) values(200, 'Actual', 2001, 2010, 'Año Auto');
insert into Estratos (id_estrato, descripcion, minimo, maximo, tipo) values(300, 'Nuevo', 2011, 2018, 'Año Auto');
insert into Estratos (id_estrato, descripcion, minimo, maximo, tipo) values(400, 'Muy nuevo', 2019, 9999, 'Año Auto');
set IDENTITY_INSERT Estratos OFF;

GO

COMMIT TRAN


IF OBJECT_ID('dbo.DimVehiculo', 'U') IS NOT NULL 
  DROP TABLE dbo.DimVehiculo;
  
create table DimVehiculo(
	Tipo varchar(30),
	Fabricante varchar(30),
	Pais varchar(30),
	Abreviatura varchar(5),
	Region varchar(30),
	Continente varchar(30),
	EstratoAño varchar(30)
);

BEGIN TRAN

insert into DimVehiculo
select b.Descripcion as TipoVeh, c.NombreFabricante, D.NombrePais, D.AbreviaturaPais,
e.Descripcion as Región, f.Descripcion as Continente, g.descripcion as Estrato from Vehiculo a
join TipoVehiculo b on a.IDTipoVehiculo = b.IDTipoVehiculo
join Fabricante c on a.IDFabricante = c.IDFabricante
join Pais d on c.IDPais = d.IDPais
JOIN RegionContinente e on d.IDRegionContinente = e.IDRegionContinente
join Continente f on e.IDContinente = f.IDContinente
join (select * from Vehiculo a, Estratos b where b.Tipo = 'Año Auto' and a.Ano between
b.Minimo and b.maximo) g on a.IDVehiculo = g.IDVehiculo;

GO

COMMIT TRAN
```

A continuación la evidencia de la ejecución del script anterior.
<img src="https://i.imgur.com/J8Tj2RA.png">


## FactEstacionamiento
Creación de la tabla fact de estacionamiento.

### Script de creación de tabla
```sql
select a.TarifaBase as 'Tarifa Base', a.Ganancia, a.Mantenimiento, a.ImpVentas as 'Impuesto
de ventas', a.TotalACobrar as Total,b.descripcion as 'Ingreso', c.descripcion as 'Salida',
d.descripcion as 'Estadía', SUBSTRING(f.descripcion,1,2) as 'RestriccionEntrada',
SUBSTRING(g.descripcion,1,2) as 'RestriccionSalida',
h.descripcion, isnull(i.NombreFeriado,'Día no feriado') as 'Día Entrada',
isnull(j.NombreFeriado,'Día no feriado') as 'Día Salida'
from Estacionamiento a
join Estratos b on DATEPART(HH,a.FechaHoraIngreso) between b.Minimo and b.maximo and
b.Tipo = 'Hora'
join Estratos c on DATEPART(HH,a.FechaHoraIngreso) between c.Minimo and c.maximo and
c.Tipo = 'Hora'
join Estratos d on DATEDIFF(MI,a.FechaHoraIngreso,a.FechaHoraSalida) between d.Minimo
and d.maximo and d.Tipo='Minutos'
join Vehiculo e on a.IDVehiculo = e.IDVehiculo
join Estratos f on (select RIGHT(e.Placa,1)) between f.Minimo and f.maximo and f.Tipo
='Restriccion' and CHARINDEX(DATEName(DW,a.FechaHoraIngreso),f.descripcion) >0
join Estratos g on (select RIGHT(e.Placa,1)) between g.Minimo and g.maximo and
g.Tipo='Restriccion' and CHARINDEX(DATEName(DW,a.FechaHoraSalida),g.descripcion) >0
join Estratos h on convert(int, a.Ganancia,0) between h.Minimo and h.maximo and
h.Tipo='Ganancia'
left join DiasFeriadosAnuales i on DATEPART(DD,a.FechaHoraIngreso) =i.DiaFeriado and
DATEPART(MM,a.FechaHoraIngreso) = i.MesFeriado
left join DiasFeriadosAnuales j on DATEPART(DD,a.FechaHoraIngreso) =j.DiaFeriado and
DATEPART(MM,a.FechaHoraIngreso) = j.MesFeriado;
```
A continuación la evidencia de la creación de la tabla.
<img src="https://i.imgur.com/HkKWkl1.png">

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
