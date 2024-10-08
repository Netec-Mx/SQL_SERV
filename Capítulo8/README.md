# Configuración de un entorno de alta disponibilidad

## Objetivos de la práctica:
- Entender los conceptos de replicación en SQL Server, incluyendo los diferentes tipos (snapshot, transactional y merge).
- Configurar la replicación de bases de datos entre dos instancias de SQL Server.
- Implementar Log Shipping para garantizar la alta disponibilidad y la recuperación ante desastres.
- Configurar Mirroring para mantener la sincronización de una base de datos entre dos servidores.
- Evaluar la efectividad de las estrategias implementadas en términos de disponibilidad y recuperación. 


## Duración aproximada:
- 60 minutos.

## Insumos necesarios:
- Dos instancias de SQL Server (pueden ser en diferentes máquinas virtuales o servidores físicos).
- Una base de datos de ejemplo con datos significativos para replicar.
- Acceso a SQL Server Management Studio (SSMS).
- Conexiones de red entre las instancias de SQL Server

## Instrucciones 
### Parte 1: Replicación de Bases de Datos
1. Crear la Base de Datos de Ejemplo:
2. Abre SSMS y crea una nueva base de datos llamada BaseDatosEjemplo.
3. Crea una tabla llamada Clientes con las siguientes columnas: Id (int), Nombre (varchar(50)), Email (varchar(50)).
    ```sql
    CREATE DATABASE BaseDatosEjemplo;
    USE BaseDatosEjemplo;
    CREATE TABLE Clientes (
        Id INT PRIMARY KEY,
        Nombre VARCHAR(50),
        Email VARCHAR(50)
    );
    --Insertar Datos en la Tabla:
    Agrega al menos 10 registros a la tabla Clientes.
    INSERT INTO Clientes (Id, Nombre, Email) VALUES
    (1, 'Juan Pérez', 'juan@example.com'),
    (2, 'Ana Gómez', 'ana@example.com'),
    (3, 'Carlos Ruiz', 'carlos@example.com'),
    (4, 'María López', 'maria@example.com'),
    (5, 'Sofía Torres', 'sofia@example.com'),
    (6, 'Luis Martínez', 'luis@example.com'),
    (7, 'Javier Fernández', 'javier@example.com'),
    (8, 'Clara Morales', 'clara@example.com'),
    (9, 'Daniela Reyes', 'daniela@example.com'),
    (10, 'Pedro Castillo', 'pedro@example.com');
    ```
4. Configurar la Replicación:
    - Establece uno de los servidores como el publicador y el otro como el suscriptor.
    - Configura la replicación transaccional entre los dos servidores. Puedes usar el asistente de configuración de replicación en SSMS para facilitar el proceso.
5. Probar la Replicación:
    - Realiza cambios en la base de datos del publicador y verifica que se reflejen en la base de datos del suscriptor.

### Parte 2: Log Shipping
Configurar Log Shipping:
- Selecciona uno de los servidores como servidor primario y el otro como servidor secundario.
- Realiza los siguientes pasos:
    - Habilita el Log Shipping en el servidor primario.
    - Configura el backup del log de transacciones.
    -  Establece un programa de restauración en el servidor secundario.
- Probar Log Shipping:

Realiza algunas transacciones en la base de datos del servidor primario y verifica que los logs se envían y aplican en el servidor secundario.

### Parte 3: Mirroring
- Configurar Mirroring:
    - Selecciona un servidor como servidor principal y el otro como servidor espejo.
    - Configura la base de datos para el mirroring:
    - Inicia el proceso de mirroring y selecciona la opción de sincronización completa.
- Probar el Mirroring:
    - Realiza cambios en la base de datos del servidor principal y verifica que se reflejan en el servidor espejo.

## Reporte
- Documenta el proceso que seguiste para cada parte de la práctica, incluyendo cualquier problema encontrado y cómo lo resolviste.
- Presenta un breve informe sobre las ventajas y desventajas de cada enfoque (replicación, Log Shipping y Mirroring).Importancia de los Backups y la Alta Disponibilidad

### Backups (Respaldos):
Los respaldos de bases de datos son fundamentales en cualquier sistema productivo. Garantizan que, en caso de un fallo grave, ya sea por corrupción de datos, errores humanos o desastres naturales, la información se pueda recuperar con un mínimo de pérdida. Sin respaldos regulares, las empresas se exponen a la pérdida de datos crítica, afectando operaciones, reputación y causando pérdidas económicas significativas.

**Tipos de backups:**

- Completo
- Diferencial
- Logs de transacciones

**Frecuencia:**  La frecuencia de los respaldos debe estar alineada con los requisitos de negocio. Cuanto más importantes sean los datos, más frecuente debe ser el respaldo.
**Riesgos de no tener backups:**

- Pérdida irreversible de datos: Si ocurre un fallo sin backups recientes, la base de datos puede quedar irrecuperable.
- Interrupción del negocio: Sin datos, los sistemas no pueden operar, lo que provoca interrupciones.
- Impacto financiero: La pérdida de información crítica puede conllevar costos financieros directos (pérdida de ventas, multas) e indirectos (pérdida de confianza de los clientes).

**Alta Disponibilidad:**

La alta disponibilidad implica asegurar que un sistema esté accesible y operativo la mayor parte del tiempo, minimizando interrupciones. En un entorno de bases de datos, garantizar la disponibilidad es crucial para mantener las operaciones en curso sin importar si un servidor falla. Las soluciones como Always On Availability Groups, Log Shipping y Database Mirroring juegan un papel vital en esta garantía.

**Beneficios de la alta disponibilidad:**
- Redundancia: Si una instancia de SQL Server falla, otra está lista para tomar su lugar sin perder datos.
- Mínima interrupción: Los sistemas continúan funcionando, lo que asegura que las operaciones del negocio no se detengan.
- Sin pérdida de datos: Los datos se sincronizan entre las réplicas, asegurando que los cambios recientes no se pierdan.

**Consecuencias de no implementar alta disponibilidad:**
- Tiempo de inactividad prolongado: Un fallo en el servidor puede resultar en horas, o incluso días, de inactividad mientras se soluciona el problema.
- Pérdida de productividad: Los usuarios no pueden acceder a la base de datos, afectando el funcionamiento general de la empresa.
- Impacto económico: La indisponibilidad del sistema puede causar pérdidas económicas debido a la interrupción de operaciones críticas.

### Resultado esperado
Al finalizar la práctica, los participantes habrán comprendido la importancia de la replicación de bases de datos, Log Shipping y Mirroring en entornos de alta disponibilidad. También aprenderán a configurar estas estrategias y validar su efectividad. La implementación de backups es crucial para garantizar la protección de datos, permitiendo una rápida recuperación ante desastres y minimizando el tiempo de inactividad. En el contexto empresarial, una infraestructura sólida de backups fortalece la reputación de la organización y optimiza recursos. Esto asegura la confianza del cliente en la continuidad del servicio y permite el crecimiento sin temor a pérdidas de datos. En resumen, la práctica proporciona una visión integral de la gestión de bases de datos en SQL Server.