# Práctica 7. Implementación de una estrategia de backup y restauración

## Objetivo de la práctica:
- Comprender la importancia de las estrategias de respaldo y restauración en la protección y recuperación de datos en entornos de producción.
- Aprender a implementar diferentes tipos de backups en SQL Server, como el backup completo, diferencial y de transacciones.
- Desarrollar un plan de respaldo que minimice la pérdida de datos y asegure una restauración rápida en caso de fallos.
- Ejecutar operaciones de restauración de bases de datos desde diferentes tipos de backups.
- Configurar y automatizar la estrategia de backups utilizando el Agente de SQL Server.

## Duración aproximada:
- 30 minutos.

### Importancia:
La gestión de backups (respaldos) y restauración es crítica para la continuidad operativa y la integridad de los datos en cualquier entorno de base de datos. Una estrategia de respaldo bien diseñada ayuda a minimizar el tiempo de inactividad y la pérdida de información en caso de fallos del sistema, corrupción de datos, errores humanos o desastres naturales. SQL Server ofrece varias opciones para realizar copias de seguridad y restauración que pueden adaptarse a las necesidades de cada organización.

## Insumos necesarios:
- SQL Server 2019 instalado con acceso a una base de datos que pueda ser respaldada y restaurada.
- Permisos de administrador en el servidor SQL para configurar y ejecutar tareas de backup y restauración.

## Instrucciones 
**1. Introducción a los tipos de backup en SQL Server** <br>
En SQL Server, existen varios tipos de respaldos que se utilizan en combinación para implementar una estrategia sólida de recuperación:
- Backup completo: Es una copia de toda la base de datos en un punto en el tiempo.
- Backup diferencial: Copia solo los cambios realizados desde el último backup completo.
- Backup del log de transacciones: Permite respaldar los registros de transacciones y es crucial para estrategias de recuperación punto en tiempo.<br>

**2. Configuración de una estrategia de backup** <br>
La estrategia a implementar en esta práctica va a consistir en:
- Realizar un backup completo de la base de datos.
- Programar un backup diferencial diario.
- Ejecutar backups del log de transacciones cada hora para minimizar la pérdida de datos.

**3. Realización de un backup completo** <br>
Ejecutar el siguiente comando para crear un backup completo de la base de datos:
 ```sql
-- Realizar un backup completo de la base de datos
BACKUP DATABASE [NombreBaseDatos]
TO DISK = 'C:\Backups\NombreBaseDatos_Full.bak'
WITH INIT, NAME = 'Backup completo';
```
Esto generará un archivo de backup completo de la base de datos. Puedes cambiar la ubicación y el nombre del archivo según tus necesidades.

**4. Creación de un backup diferencial** <br>
Un backup diferencial respalda solo los cambios realizados desde el último backup completo, lo que hace que sea más rápido y eficiente en términos de espacio. El siguiente código crea un backup diferencial:
 ```sql
-- Realizar un backup diferencial de la base de datos
BACKUP DATABASE [NombreBaseDatos]
TO DISK = 'C:\Backups\NombreBaseDatos_Diff.bak'
WITH DIFFERENTIAL, INIT, NAME = 'Backup diferencial';
```
Este backup será más pequeño que el completo, ya que solo incluye las modificaciones desde el último respaldo completo.

**5. Backup del log de transacciones** <br>
El backup del log de transacciones permite mantener un historial de todas las transacciones que han ocurrido en la base de datos, lo que es fundamental para recuperar la base de datos a un punto específico en el tiempo.
 ```sql
-- Realizar un backup del log de transacciones
BACKUP LOG [NombreBaseDatos]
TO DISK = 'C:\Backups\NombreBaseDatos_Log.trn'
WITH INIT, NAME = 'Backup de log de transacciones';
```
Se recomienda programar estos backups de log en intervalos regulares, como cada hora, para minimizar la pérdida de datos en caso de fallo.

**6. Programación de backups automáticos** <br>
Configurar trabajos en el Agente de SQL Server:
- Acceder a SQL Server Agent > Jobs y crear un nuevo trabajo para programar backups automáticos.
- Definir un job para realizar el backup completo una vez a la semana.
- Configurar otro job para realizar backups diferenciales diariamente.
- Configurar un tercer job para ejecutar backups del log de transacciones cada hora.

**7. Restauración de una base de datos** <br>
La restauración de una base de datos puede involucrar múltiples pasos dependiendo de los backups disponibles y la estrategia utilizada.<br>
- Restaurar desde un backup completo: El siguiente código restaura la base de datos desde un backup completo:
    ```sql
    -- Restaurar un backup completo
    RESTORE DATABASE [NombreBaseDatosRestaurada]
    FROM DISK = 'C:\Backups\NombreBaseDatos_Full.bak'
    WITH MOVE 'NombreBaseDatos' TO 'C:\SQLData\NombreBaseDatos.mdf',
    MOVE 'NombreBaseDatos_Log' TO 'C:\SQLLogs\NombreBaseDatos_Log.ldf',
    REPLACE, NORECOVERY;
    ```
- Restaurar desde un backup diferencial: Si hay un backup diferencial, restaurar primero el backup completo con la opción NORECOVERY y luego el backup diferencial:
    ```sql
    -- Restaurar un backup diferencial
    RESTORE DATABASE [NombreBaseDatosRestaurada]
    FROM DISK = 'C:\Backups\NombreBaseDatos_Diff.bak'
    WITH RECOVERY;
    ```
- Restaurar desde un backup del log de transacciones: Si has configurado backups de log, puedes restaurar la base de datos a un punto en el tiempo específico.
    ```sql
    -- Restaurar un backup de log de transacciones
    RESTORE LOG [NombreBaseDatosRestaurada]
    FROM DISK = 'C:\Backups\NombreBaseDatos_Log.trn'
    WITH STOPAT = '2024-09-01 12:00:00', RECOVERY;
    ```
**8. Pruebas y verificación de la restauración** <br>
- Después de restaurar la base de datos, verificar que la base de datos esté funcional y que los datos estén intactos.
- Revisar los logs del servidor SQL y los detalles de la operación de restauración para confirmar que no hubo errores.

### Resultado esperado
Al finalizar la práctica, los estudiantes deben ser capaces de:
- Entender la importancia de los backups y la restauración en la protección de los datos.
- Configurar y ejecutar diferentes tipos de backups en SQL Server, incluyendo backups completos, diferenciales y de log de transacciones.
- Implementar una estrategia efectiva de backups que minimice la pérdida de datos y el tiempo de recuperación.
- Restaurar una base de datos desde diferentes tipos de backups y recuperar los datos en caso de falla o corrupción.

