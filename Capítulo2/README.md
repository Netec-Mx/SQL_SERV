# Práctica 2. Creación y ejecución de consultas básicas

## Objetivos de la práctica:
- Comprender los fundamentos de las consultas SQL para recuperar datos de una base de datos.
- Aplicar las instrucciones básicas de consulta: SELECT, WHERE, ORDER BY, GROUP BY y funciones de agregación.
- Familiarizarse con las uniones de tablas mediante JOIN y el uso de subconsultas.
- Identificar y corregir errores comunes en las consultas SQL Server.




## Duración aproximada:
- 60 minutos.

## Instrucciones:
- Configuración inicial:
    - Abrir SQL Server Management Studio (SSMS).
    - Conectarse a la base de datos de prueba proporcionada.
- Consulta 1: Selección básica de datos
    - Realizar una consulta simple para seleccionar todas las columnas de una tabla específica.
- Consulta 2: Filtrado de datos
    -  Filtrar los resultados de la tabla xxxxx para mostrar solo aquellos registros donde el campo xxxxxx sea igual a 5.
- Consulta 3: Ordenación de resultados 
    - Ordenar los registros filtrados de la consulta anterior en orden ascendente por el campo.
- Consulta 4: Funciones de agregación
    - Contar cuántos clientes hay en cada territorio.
- Consulta 5: Uniones de tablas (JOIN)
    -  Unir dos tablas para obtener información combinada. Para obtener el número de pedido y el cliente que lo realizó.
- Consulta 6: Subconsultas (Subquery)
    - Realizar una subconsulta para encontrar el cliente con el mayor número de pedidos. Primero, usar una subconsulta para contar los pedidos por cliente y luego filtrar el resultado.

## Codigos de ayuda
1. **Selección básica de datos (SELECT)**
```sql
-- Selección de columnas específicas
SELECT * 
FROM nombre_tabla;
```
2. **Filtrado de datos (WHERE)**
```sql
-- Selección de columnas específicas
SELECT columna1, columna2, ...
FROM nombre_tabla
WHERE condicion;
```

3. **Ordenación de resultados (ORDER BY)**
```sql
-- Selección de columnas específicas
SELECT columna1, columna2, ...
FROM nombre_tabla
WHERE condicion
ORDER BY columna ASC|DESC;
```

4. **Funciones de agregación (COUNT, SUM, AVG, etc.)**
```sql
-- Selección de columnas específicas
SELECT columna_agrupada, COUNT(columna_agregada) AS alias
FROM nombre_tabla
GROUP BY columna_agrupada;
```

5. **Alias para columnas (AS)**
```sql
-- Selección de columnas específicas
SELECT columna AS 'alias'
FROM nombre_tabla;
```
6. **Uniones de tablas (INNER JOIN)**
```sql
-- Selección de columnas específicas
SELECT t1.columna1, t2.columna2
FROM tabla1 t1
INNER JOIN tabla2 t2
ON t1.columna_comun = t2.columna_comun;
```
7. **Uniones con LEFT JOIN**
```sql
-- Selección de columnas específicas
SELECT t1.columna1, t2.columna2
FROM tabla1 t1
LEFT JOIN tabla2 t2
ON t1.columna_comun = t2.columna_comun;
```
8. **Subconsultas (Subqueries)**
```sql
-- Selección de columnas específicas
--Subconsulta en WHERE:
SELECT columna1, columna2
FROM nombre_tabla
WHERE columna = (SELECT valor FROM nombre_tabla WHERE condicion);
--Subconsulta en el FROM:
SELECT columna1, columna2
FROM (SELECT columna1, columna2 FROM nombre_tabla WHERE condicion) AS alias_subconsulta;
```
### Resultado esperado
Al finalizar la práctica, deben ser capaces de:
-   Ejecutar consultas básicas en SQL Server con SELECT, WHERE, ORDER BY, funciones de agregación, JOIN, y subconsultas.
- Combinar datos de múltiples tablas usando INNER JOIN y LEFT JOIN.
- Utilizar subconsultas para realizar consultas más avanzadas y obtener resultados específicos. <br>

Esta práctica les permitirá tener una comprensión más amplia y profunda de cómo trabajar con bases de datos relacionales en SQL Server, desde consultas básicas hasta la combinación y análisis de datos más complejos.




### EJEMPLO:
```sql
-- Crear base de datos
CREATE DATABASE PracticaSQL;

-- Usar la base de datos recién creada
USE PracticaSQL;
-- Crear tabla Cliente
CREATE TABLE Cliente (
    ClienteID INT PRIMARY KEY IDENTITY(1,1),
    Nombre NVARCHAR(100),
    Ciudad NVARCHAR(100),
    FechaRegistro DATE,
    TerritorioID INT,
    Cuenta NVARCHAR(50)
);

-- Crear tabla Pedido
CREATE TABLE Pedido (
    PedidoID INT PRIMARY KEY IDENTITY(1,1),
    ClienteID INT,
    FechaPedido DATE,
    Total DECIMAL(10,2),
    FOREIGN KEY (ClienteID) REFERENCES Cliente(ClienteID)
);

-- Insertar datos en la tabla Cliente
INSERT INTO Cliente (Nombre, Ciudad, FechaRegistro, TerritorioID, Cuenta)
VALUES
('Juan Pérez', 'Bogotá', '2021-01-15', 1, 'C001'),
('María García', 'Medellín', '2021-02-20', 2, 'C002'),
('Carlos López', 'Cali', '2021-03-25', 3, 'C003'),
('Ana Martínez', 'Bogotá', '2021-04-10', 1, 'C004'),
('Luis Rodríguez', 'Cartagena', '2021-05-05', 4, 'C005');

-- Insertar datos en la tabla Pedido
INSERT INTO Pedido (ClienteID, FechaPedido, Total)
VALUES
(1, '2021-06-01', 5000),
(1, '2021-07-15', 3000),
(2, '2021-06-10', 4000),
(3, '2021-06-20', 2500),
(4, '2021-07-01', 3500),
(5, '2021-07-05', 4500);
Consultas que puedes realizar en esta base de datos
Selección de datos básicos:

SELECT * FROM Cliente;
Filtrado por ciudad:

SELECT * FROM Cliente
WHERE Ciudad = 'Bogotá';
Ordenar los pedidos por fecha:

SELECT * FROM Pedido
ORDER BY FechaPedido ASC;
Contar clientes por territorio:

SELECT TerritorioID, COUNT(ClienteID) AS Total_Clientes
FROM Cliente
GROUP BY TerritorioID;
Consulta con INNER JOIN:

SELECT Cliente.Nombre, Pedido.PedidoID, Pedido.FechaPedido, Pedido.Total
FROM Cliente
INNER JOIN Pedido ON Cliente.ClienteID = Pedido.ClienteID;
Consulta con LEFT JOIN:

SELECT Cliente.Nombre, Pedido.PedidoID, Pedido.FechaPedido, Pedido.Total
FROM Cliente
LEFT JOIN Pedido ON Cliente.ClienteID = Pedido.ClienteID;
Subconsulta para contar el número total de pedidos por cliente:

SELECT ClienteID, (SELECT COUNT(PedidoID) FROM Pedido WHERE Pedido.ClienteID = Cliente.ClienteID) AS Total_Pedidos
FROM Cliente;

```



