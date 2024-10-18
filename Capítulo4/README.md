# Optimización de una consulta compleja

## Objetivo de la práctica:
- Entender los principios fundamentales del query tuning (optimización de consultas) en SQL Server.
- Identificar consultas SQL ineficientes y aprender a mejorarlas para reducir tiempos de ejecución y mejorar el rendimiento.
- Utilizar herramientas de análisis de rendimiento como el plan de ejecución y indexing en SQL Server.
- Aplicar estrategias de optimización en consultas complejas mediante reestructuración y uso adecuado de índices.


## Duración aproximada:
- 60 minutos.

## Insumos necesarios:
Para esta práctica es recomendable trabajar con una base de datos que contenga un gran volumen de información (varios miles o millones de registros) para observar diferencias significativas en el rendimiento antes y después de la optimización.<br>
Puedes utilizar:
- La base de datos de ejemplo AdventureWorks2019 de SQL Server (que contiene gran cantidad de datos).
- Generar una tabla con datos masivos de prueba si no se tiene una base de datos disponible
## Instrucciones 

### Preparación del entorno:
Asegúrate de estar trabajando en una base de datos con gran volumen de datos para observar los efectos de la optimización. Si no tienes una base de datos grande, utiliza la siguiente consulta para generar datos de prueba:

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
### Identificación de una consulta ineficiente:
```sql
  -- Consulta no optimizada con problemas
SELECT *
FROM Ventas
WHERE YEAR(FechaVenta) = 2023  -- Uso de una función que deshabilita el uso de índices
  AND Cliente LIKE '%50%'      -- Búsqueda con un comodín al inicio que también evita índices
ORDER BY Precio DESC;           -- Ordenación de una columna sin índice
```
Para analizar el rendimiento de la consulta, utiliza la opción de "Mostrar plan de ejecución estimado" en SQL Server Management Studio (SSMS) o ejecuta la consulta con SET STATISTICS TIME ON; y SET STATISTICS IO ON; para obtener información detallada sobre el tiempo de ejecución y el uso de recursos.

### Análisis del plan de ejecución:
Observa el plan de ejecución para identificar cuellos de botella, como:
- Escaneos de tabla completos (Table Scan) en lugar de índices.
- Uso excesivo de memoria o CPU.
- Filtros aplicados después de un escaneo completo en vez de utilizar índices eficientes.

### Optimización de la consulta:
1. Creación de índices:
Si el plan de ejecución muestra que se está haciendo un Table Scan sobre la Tabla Ventas, es posible que no haya un índice adecuado para la consulta. Crea un índice sobre las columnas FechaVenta y Monto para mejorar el rendimiento de las consultas con filtros y agregaciones:
```sql
-- Crear un índice en FechaVenta
CREATE INDEX IDX_Ventas_FechaVenta ON Ventas(FechaVenta);

-- Crear un índice en Cliente
CREATE INDEX IDX_Ventas_Cliente ON Ventas(Cliente);

-- Crear un índice en Precio
CREATE INDEX IDX_Ventas_Precio ON Ventas(Precio);
```
2. Reestructuración de la consulta:
Optimiza la consulta aprovechando índices y evitando subconsultas o funciones que ralenticen la ejecución. Si tu consulta contiene subconsultas innecesarias, reescríbela para simplificarla.
```sql
-- Consulta optimizada
SELECT VentaID, Cliente, NumeroPedido, Precio, FechaVenta
FROM Ventas
WHERE FechaVenta BETWEEN '2023-01-01' AND '2023-12-31'  -- Evitar el uso de funciones
  AND Cliente LIKE 'Cliente50%'  -- Búsqueda optimizada usando el índice en Cliente
ORDER BY Precio DESC;            -- Ordenación optimizada gracias al índice en Precio
```
3. Utilización de Hints:
En algunos casos, puedes usar hints para optimizar aún más. Por ejemplo, si sabes que los índices deberían estar siendo usados, puedes forzarlos con INDEX o FORCESEEK.

### Reevaluación del rendimiento:
Vuelve a ejecutar la consulta optimizada y compara los tiempos de ejecución y uso de recursos con los de la consulta original.<br>
Revisa nuevamente el plan de ejecución para verificar si ahora se utilizan los índices creados, y si los escaneos completos han sido reemplazados por búsquedas eficientes (Index Seek).


### Análisis y recomendaciones:
Discute cómo los cambios realizados mejoraron el rendimiento. ¿Hubo una reducción significativa en el tiempo de ejecución? ¿Se optimizó el uso de memoria y CPU?<br>
Menciona otras estrategias que podrían aplicarse en diferentes situaciones, como la partición de tablas, materialización de vistas o uso de columnas calculadas.<br>
Realizar diferentes consultas que sean ineficientes sin uso de índices y luego realizar la creación de índices para verificar los rendimientos. A su vez, dar realizar un escrito sobre que significa los parallelism, sort, clustered index scan, entre otros que pueden aparecer en el execution plan. 


### Resultado esperado
Al finalizar la práctica, los estudiantes deben ser capaces de
- Identificar una consulta ineficiente y analizar su plan de ejecución.
- Aplicar estrategias de optimización mediante el uso de índices, reestructuración de consultas y ajustes en el plan de ejecución.
- Evaluar la mejora en el rendimiento de la consulta después de aplicar las optimizaciones, y comprender cómo se pueden aplicar estas técnicas a otras consultas en escenarios del mundo real.
