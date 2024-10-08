# Creación y manejo de particiones 

## Objetivos de la práctica:
- Comprender las estrategias de particionamiento de tablas en SQL Server y su importancia para mejorar el rendimiento y la administración de datos.
- Aprender a crear y manejar particiones en SQL Server mediante el uso de funciones y esquemas de particionamiento.
- Implementar particiones en una tabla con un gran volumen de datos para mejorar el rendimiento de consultas.
- Aplicar estrategias de particionamiento adecuado para diferentes escenarios.

## Duración aproximada:
- 30 minutos.

## Insumos necesarios:
- Acceso a un servidor de SQL Server 2019 con permisos para crear esquemas de particionamiento.
- Una base de datos con tablas que contengan grandes volúmenes de datos para observar las ventajas del particionamiento.
- Se sugiere que se genere o utilice una tabla con millones de registros para maximizar el impacto de la partición.

## Instrucciones 
**1. Introducción al particionamiento**<br>
El particionamiento de tablas permite dividir grandes cantidades de datos en segmentos más pequeños llamados "particiones". Esto puede mejorar el rendimiento de las consultas y la administración de los datos. Existen varios tipos de particionamiento:
- Particionamiento por rango de valores (comúnmente utilizado para fechas).
- Particionamiento por lista (grupos de valores específicos).
- Particionamiento por hash (distribución uniforme de datos).
En esta práctica, trabajaremos con el particionamiento por rango utilizando fechas.

**2. Creación de una tabla de ejemplo con gran volumen de datos**<br>
Asegúrate de tener una tabla con un volumen de datos significativo. Utiliza la siguiente consulta para crear una tabla de ventas con datos de varios años:
```sql
 CREATE TABLE Ventas (
    VentaID INT PRIMARY KEY,
    Cliente NVARCHAR(100),
    NumeroPedido NVARCHAR(50),
    Precio DECIMAL(10, 2),
    FechaVenta DATE
);

-- Declarar las variables necesarias antes del ciclo
DECLARE @i INT;
DECLARE @randomCliente NVARCHAR(100);
DECLARE @randomPrecio DECIMAL(10,2);
DECLARE @randomFechaVenta DATE;
DECLARE @randomPedido NVARCHAR(50);

-- Inicializar la variable contador
SET @i = 1;

-- Iniciar el ciclo para insertar 1 millón de registros
WHILE @i <= 1000000
BEGIN
    -- Generar un cliente aleatorio entre 1 y 1000
    SET @randomCliente = 'Cliente' + CAST(FLOOR(RAND() * 1000) + 1 AS NVARCHAR(100));

    -- Generar un precio aleatorio entre 10.00 y 1000.00
    SET @randomPrecio = CAST((RAND() * (1000 - 10) + 10) AS DECIMAL(10, 2));

    -- Generar una fecha de venta aleatoria en los últimos 2 años
    SET @randomFechaVenta = DATEADD(DAY, -FLOOR(RAND() * 730), GETDATE());

    -- Generar un número de pedido aleatorio
    SET @randomPedido = 'Pedido' + CAST(FLOOR(RAND() * 10000) + 1 AS NVARCHAR(50));

    -- Insertar el registro en la tabla
    INSERT INTO Ventas (VentaID, Cliente, NumeroPedido, Precio, FechaVenta)
    VALUES 
    (
        @i, 
        @randomCliente,         -- Cliente aleatorio
        @randomPedido,          -- Pedido aleatorio
        @randomPrecio,          -- Precio
        @randomFechaVenta       -- Fecha de venta
    );

    -- Incrementar el contador
    SET @i = @i + 1;
END;
```
**3. Definir una función de partición**<br>
El primer paso en la implementación de particiones es crear una función de partición que defina cómo se segmentarán los datos. Para este caso, vamos a particionar la tabla Ventas en función de la columna FechaVenta, distribuyéndola en diferentes años.
 ```sql
  CREATE PARTITION FUNCTION PF_VentasPorFecha (DATE)
AS
    RANGE LEFT FOR VALUES ('2022-01-01', '2023-01-01', '2024-01-01');
```
Esta función distribuye las filas de acuerdo a la fecha: las ventas anteriores a 2022 en una partición, las de 2023 en otra, y así sucesivamente.

**4. Crear un esquema de particionamienton**<br>
El siguiente paso es crear un esquema de particionamiento que defina dónde se almacenarán físicamente las particiones. Puedes distribuirlas en el mismo disco o en diferentes discos para mejorar el rendimiento:
 ```sql
  CREATE PARTITION SCHEME PS_VentasPorFecha
AS PARTITION PF_VentasPorFecha
TO (PRIMARY, PRIMARY, PRIMARY, PRIMARY);  -- Puedes especificar diferentes archivos de grupo para cada partición si lo deseas
```
En este ejemplo, todas las particiones estarán en el mismo grupo de archivos PRIMARY, pero en un entorno de producción puedes distribuir las particiones en diferentes grupos de archivos para un mejor rendimiento.

**5. Crear la tabla particionada**<br>
Ahora crea la tabla Ventas utilizando el esquema de particionamiento:
 ```sql
CREATE TABLE Ventas_particionada (
    VentaID INT PRIMARY KEY,
    Cliente NVARCHAR(100),
    NumeroPedido NVARCHAR(50),
    Precio DECIMAL(10, 2),
    FechaVenta DATE
) ON PS_VentasPorFecha(FechaVenta);
```
Esto indica que la tabla se dividirá en particiones basadas en la columna FechaVenta.

**6. Insertar datos en la tabla particionada**<br>
Una vez creada la tabla particionada, puedes comenzar a insertar datos. Los datos se asignarán automáticamente a la partición correspondiente basada en la FechaVenta.
 ```sql
-- Insertar datos en la tabla particionada EJEMPLO
INSERT INTO Ventas_Particionada (VentaID, ClienteID, ProductoID, FechaVenta, Monto)
SELECT VentaID, ClienteID, ProductoID, FechaVenta, Monto FROM Ventas;
```
**7. Consultar la tabla particionada**<br>
Puedes ejecutar consultas que solo afecten a una partición específica para mejorar el rendimiento:
```sql
-- Consulta que accede solo a una partición (año 2017)
SELECT *
FROM Ventas_Particionada
WHERE FechaVenta BETWEEN '2017-01-01' AND '2017-12-31';
```
Verifica el plan de ejecución de la consulta en SSMS para asegurarte de que solo se accede a la partición correspondiente.

**8. Mantenimiento de particiones**<br>
A medida que los datos crecen, es posible que necesites agregar nuevas particiones. Para ello, puedes modificar la función de partición:
 ```sql
-- Consulta que accede solo a una partición (año 2017)
-- Agregar una nueva partición para datos de 2020
ALTER PARTITION FUNCTION VentasRangoFecha() 
SPLIT RANGE ('2023-01-01');
```
Esto agrega una nueva partición para los datos posteriores al 1 de enero de 2023.


### Resultado esperado
Al finalizar la práctica, los estudiantes deben ser capaces de:
- Entender el propósito y las ventajas del particionamiento de tablas en bases de datos grandes.
- Configurar funciones de partición y esquemas en SQL Server.
- Implementar una tabla particionada basada en una columna de rango de fechas.
- Realizar consultas sobre tablas particionadas y entender cómo el particionamiento mejora el rendimiento de las consultas.
- Gestionar particiones añadiendo o eliminando particiones a medida que los datos crecen.



