# Diseño de una base de datos normalizada 

## Objetivos de la práctica:
Al finalizar la práctica, serás capaz de:
- Entender los principios fundamentales del query tuning (optimización de consultas) en SQL Server.
- Identificar consultas SQL ineficientes y aprender a mejorarlas para reducir tiempos de ejecución y mejorar el rendimiento.
- Utilizar herramientas de análisis de rendimiento como el plan de ejecución y indexing en SQL Server.
- Aplicar estrategias de optimización en consultas complejas mediante reestructuración y uso adecuado de índices.

## Duración aproximada:
- 60 minutos.

## Instrucciones 
Se tiene la siguiente capa organizacional, se deben hacer con ella los pasos del 1 al 3, los cuales son el modelo seudomatemático, modelo relacional y finalmente el codigo sql.

![imagen cap2](../images/Capitulo%202/img1.png)<br>


1. **Modelo Seudomatemático (Forma verbal)**
**Ejemplo**: Para esta práctica, diseñaremos un sistema simple de gestión de clientes, productos, y pedidos. A continuación un ejemplo de el modelo seudomatemático:

    Cliente: Cada cliente tiene un ID de cliente, nombre, dirección y correo electrónico.<br>
    Producto: Cada producto tiene un ID de producto, nombre del producto, precio y cantidad en stock.<br>
    Pedido: Cada pedido tiene un ID de pedido, fecha del pedido y cliente que realiza el pedido.<br>
    Detalle del pedido: Cada detalle de pedido tiene una relación con el producto que se está comprando, la cantidad comprada y el precio por unidad en el momento del pedido.<br>

2. **Modelo Relacional** <br>
Este es el modelo relacional derivado del seudomatemático, con las entidades y relaciones entre ellas.
**Ejemplo**
![imagen cap2](../images/Capitulo%202/img2.png)<br>

3. **Creación de una Base de datos en SQL Server** <br>
**Ejemplo**
```sql
-- Crear base de datos
CREATE DATABASE SistemaPedidos;

-- Usar la base de datos recién creada
USE SistemaPedidos;

-- Crear tabla Cliente
CREATE TABLE Cliente (
    ClienteID INT PRIMARY KEY IDENTITY(1,1),
    Nombre NVARCHAR(100),
    Direccion NVARCHAR(255),
    Correo NVARCHAR(100)
);

-- Crear tabla Producto
CREATE TABLE Producto (
    ProductoID INT PRIMARY KEY IDENTITY(1,1),
    NombreProducto NVARCHAR(100),
    Precio DECIMAL(10,2),
    CantidadStock INT
);
-- Crear tabla Pedido
CREATE TABLE Pedido (
    PedidoID INT PRIMARY KEY IDENTITY(1,1),
    FechaPedido DATE,
    ClienteID INT,
    FOREIGN KEY (ClienteID) REFERENCES Cliente(ClienteID)
);

-- Crear tabla DetallePedido
CREATE TABLE DetallePedido (
    DetalleID INT PRIMARY KEY IDENTITY(1,1),
    PedidoID INT,
    ProductoID INT,
    Cantidad INT,
    PrecioUnidad DECIMAL(10,2),
    FOREIGN KEY (PedidoID) REFERENCES Pedido(PedidoID),
    FOREIGN KEY (ProductoID) REFERENCES Producto(ProductoID)
);
```
4. **Validación de diseño**

Se deberá hacer una validación de diseño entre las relaciones de colaboradores y pagos salarios, en la cual se indique que un colaborador puede tener un pago asociado y un pago asociado le debe pertenecer a un colaborador. 


### Resultado esperado
Al finalizar esta práctica, los estudiantes deben ser capaces de:
- Diseñar un modelo seudomatemático y un modelo relacional.
- Crear una base de datos con tablas normalizadas en SQL Server 2019.
- Establecer relaciones entre tablas mediante claves foráneas, asegurando integridad referencial.

Esta práctica les proporcionará las bases para diseñar sistemas relacionales eficientes y optimizados, siguiendo los principios de normalización.

