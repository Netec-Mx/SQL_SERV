# Práctica 9. Implementación de un Pipeline de CI/CD para Bases de Datos

## Objetivos de la práctica:
- Implementar un Pipeline de CI/CD (Integración Continua y Despliegue Continuo) para bases de datos en un entorno local.
- Automatizar tareas administrativas comunes en SQL Server como backups, optimización de índices, y mantenimiento de estadísticas.
- Configurar pruebas automatizadas para validar el correcto funcionamiento de los cambios aplicados en la base de datos.
- Realizar pruebas unitarias y de integración en la base de datos antes de desplegar cambios.
- Entender la importancia de la automatización tanto en la administración como en la implementación de cambios en bases de datos a través de CI/CD.

## Duración aproximada:
- 60 minutos.

## Insumos necesarios:
- SQL Server 2019 instalado en un equipo local.
- Git o cualquier herramienta de control de versiones instalada.
- Acceso a un servicio de integración continua (Jenkins, Azure DevOps, GitLab CI) o una herramienta local para simular el pipeline.
- SQL Server Management Studio (SSMS).
- Script de ejemplo para modificar la base de datos y automatizar tareas administrativas.
- Herramienta para automatizar pruebas en bases de datos.

## Instrucciones 
**1. Configuración de Control de Versiones (Git):**

- Inicializar un repositorio Git local para gestionar los scripts de la base de datos.
- Crear una estructura organizada de carpetas para almacenar scripts SQL (e.g., /scripts/alteraciones/, /scripts/mantenimiento/).
- Agregar un script de creación o modificación de base de datos al repositorio y realizar el commit inicial.

**2. Automatización de Tareas Administrativas:**
- Crear y documentar scripts para automatizar tareas comunes de administración en SQL Server, como:
- Backups automáticos: Un script para realizar copias de seguridad completas y diferenciales.
- Optimización de índices: Un script que reorganice o reconstruya índices en la base de datos según sea necesario.
- Mantenimiento de estadísticas: Un script que actualice las estadísticas para asegurar un rendimiento óptimo.
- Configurar un job en SQL Server Agent para ejecutar estos scripts automáticamente según una programación definida (diaria, semanal, etc.)
- Asegurarse de que los scripts también se versionen en Git para control de cambios.

**3. Configuración del Pipeline de CI:**
- Configurar el Pipeline de CI en tu herramienta de elección (e.g., Jenkins, Azure DevOps) para que:
- Extraer los cambios del repositorio Git.
- Aplicar los scripts de modificación de base de datos.
- Ejecutar los scripts de automatización de tareas administrativas.
- Simular el proceso de CI con un script local en PowerShell o un archivo batch, si no dispones de un servidor CI real.

**4. Pruebas Automatizadas de Base de Datos:**
- Instalar tSQLt en una base de datos de prueba para realizar pruebas unitarias.
- Crear una serie de pruebas unitarias y de integración, como:
- Validación de integridad de datos: Asegurar que los cambios en la base de datos no afecten la integridad referencial ni los constraints.
- Rendimiento de consultas: Probar que los tiempos de respuesta para ciertas consultas clave se mantengan dentro de los límites esperados.
- Pruebas de automatización de tareas: Verificar que las tareas administrativas (backups, optimización de índices) se ejecuten correctamente y produzcan los resultados esperados.
- Configurar el Pipeline para que ejecute estas pruebas antes de aplicar cualquier cambio a la base de datos.

**5. Despliegue Continuo (CD):**
- Configurar un paso en el Pipeline de CI para que, una vez que los cambios y las pruebas se hayan validado, los scripts se implementen automáticamente en una instancia de producción o en una base de datos simulada.
- Asegurarse de que el despliegue ocurra solo si todas las pruebas unitarias y de integración se completan con éxito.

**6. Prueba del Pipeline Completo:**
- Realizar un cambio en los scripts de la base de datos o las tareas administrativas (por ejemplo, ajusta un índice o actualiza las estadísticas).
- Hacer commit y push de los cambios al repositorio Git.
- Observar cómo el Pipeline de CI extrae los cambios, ejecutar las pruebas automatizadas y aplicar las modificaciones en la base de datos de prueba.
- Verificar que las tareas administrativas (backups, optimización, mantenimiento) se ejecutan de manera automática y correcta.



## Scripts necesarios 
**Parte 1: Script de Modificación de la Base de Datos**
En este ejemplo, vamos a agregar una nueva columna a una tabla existente y a crear un índice para mejorar el rendimiento de las consultas.
 ```sql
-- Modificación de la base de datos: Agregar una columna y crear un índice
USE [NombredetuBaseDeDatos]
-- Verificar si la columna ya existe antes de agregarla
IF NOT EXISTS (SELECT * FROM sys.columns 
               WHERE Name = N'NuevaColumna' 
               AND Object_ID = Object_ID(N'dbo.NombreTabla'))
BEGIN
    ALTER TABLE dbo.NombreTabla
    ADD NuevaColumna NVARCHAR(100) NULL;
END
-- Crear un índice en la nueva columna para optimizar las consultas
IF NOT EXISTS (SELECT * FROM sys.indexes 
               WHERE name = N'IX_NombreTabla_NuevaColumna' 
               AND object_id = OBJECT_ID(N'dbo.NombreTabla', N'U'))
BEGIN
    CREATE INDEX IX_NombreTabla_NuevaColumna
    ON dbo.NombreTabla (NuevaColumna);
END
```
**Parte 2: Automatización de tareas administrativas**
A continuación, se presenta un script que incluye la automatización de tareas administrativas comunes como backups, reconstrucción de índices y actualización de estadísticas.

**Script de Backup automático:**<br>
Este script realizará un backup completo de la base de datos.<br>
 ```sql
-- Reconstrucción y reorganización de índices fragmentados
USE [NombreBaseDeDatos]
DECLARE @TableName NVARCHAR(255);

DECLARE IndexCursor CURSOR FOR
SELECT object_name(i.object_id) AS TableName
FROM sys.indexes AS i
JOIN sys.dm_db_index_physical_stats(DB_ID(), NULL, NULL, NULL, 'DETAILED') AS d
ON i.object_id = d.object_id AND i.index_id = d.index_id
WHERE d.avg_fragmentation_in_percent > 30
AND i.index_id > 0;
OPEN IndexCursor;
FETCH NEXT FROM IndexCursor INTO @TableName;
WHILE @@FETCH_STATUS = 0
BEGIN
    PRINT 'Reconstruyendo índice en ' + @TableName;
    EXEC('ALTER INDEX ALL ON ' + @TableName + ' REBUILD');
    FETCH NEXT FROM IndexCursor INTO @TableName;
END
CLOSE IndexCursor;
DEALLOCATE IndexCursor;
```
**Actualización de estadísticas:**<br>
Este script actualizará las estadísticas de todas las tablas para garantizar que las consultas utilicen los mejores planes de ejecución.<br>
 ```sql
-- Actualización de estadísticas para optimizar el rendimiento de las consultas
USE [NombreBaseDeDatos]
EXEC sp_MSforeachtable 'UPDATE STATISTICS ? WITH FULLSCAN';
```

### Tareas Automatizadas en SQL Server Agent:
- Backup Automático: Puedes configurar este script como un job en el SQL Server Agent que se ejecute diariamente o semanalmente para crear copias de seguridad automáticas.
- Mantenimiento de Índices y Estadísticas: Configurar otro job en el SQL Server Agent para ejecutar los scripts de mantenimiento de índices y actualización de estadísticas en horarios de baja actividad, como durante la noche.

### Instrucciones para Configurar Tareas en SQL Server Agent:
- Abrir SQL Server Management Studio (SSMS).
- Expandir el nodo de SQL Server Agent.
- Crear un nuevo Job y agregar un Step que ejecute los scripts anteriores según el mantenimiento que quieras automatizar (backups, índices o estadísticas).
- Definir el Schedule de acuerdo a la frecuencia deseada (diario, semanal, etc.)
- Guardar el Job y verificar que se ejecute correctamente según lo programado.


### Resultado esperado
Al finalizar la práctica, los participantes habrán configurado un pipeline de CI/CD completo para bases de datos que automatiza tanto las tareas administrativas como las pruebas de los cambios. Comprenderán la importancia de automatizar tanto el mantenimiento de la base de datos como las pruebas, asegurando que los sistemas funcionen de manera óptima y sin interrupciones. Además, habrán experimentado cómo los cambios se despliegan de forma continua y confiable, minimizando los riesgos y maximizando la eficiencia.
