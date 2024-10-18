# Configuración de alertas y monitoreo de rendimiento 

## Objetivos de la práctica:
- Aprender a configurar alertas en SQL Server para monitorear el rendimiento y detectar problemas de forma proactiva.
- Utilizar herramientas como el Agente de SQL Server, SQL Server Profiler y el Monitor de Rendimiento (Performance Monitor) para supervisar la actividad del servidor.
- Identificar y resolver problemas comunes de rendimiento, como uso excesivo de CPU, bloqueos (deadlocks), y uso ineficiente de índices.
- Configurar acciones automáticas en respuesta a las alertas, como la notificación por correo electrónico a los administradores.
 
## Duración aproximada:
- 60 minutos.


## Insumos necesarios:
- Acceso a un servidor de SQL Server 2019 con permisos de administrador para poder configurar el Agente de SQL Server y los sistemas de monitoreo.
- Instalación y configuración de Database Mail para notificaciones automáticas por correo electrónico (si se desea enviar alertas vía correo).
- Bases de datos activas donde puedas generar cargas de trabajo y observar problemas de rendimiento.

## Instrucciones:
### 1. **Configuración de alertas en SQL Server** <br>

**A.** Habilitar el Agente de SQL Server:
- Asegúrate de que el servicio SQL Server Agent esté habilitado. Este servicio permite programar trabajos (jobs) y configurar alertas.<br>
- En SQL Server Management Studio (SSMS), ve al Explorador de Objetos y asegúrate de que el Agente de SQL Server esté iniciado. Si no, haz clic derecho sobre él y selecciona "Iniciar".<br>

**B.** Configurar Database Mail (opcional): 
- Si deseas que las alertas envíen correos electrónicos, primero debes configurar Database Mail.<br>
- Configura Database Mail desde el Explorador de Objetos > Management > Database Mail > "Configure Database Mail".<br>
 Sigue los pasos del asistente para configurar un perfil de correo.<br>
**C.** Crear un operador:<br>

- Un operador es un contacto a quien se le enviarán alertas. Ve a SQL Server Agent > Operators > "New Operator".<br>
- Define el nombre del operador y especifica la dirección de correo electrónico a la cual enviar alertas.<br>
    ![imagen cap5](../images/Capitulo%205/img1.PNG)<br>
    ![imagen cap5](../images/Capitulo%205/img2.PNG)<br>
    ![imagen cap5](../images/Capitulo%205/img3.PNG)<br>
    ![imagen cap5](../images/Capitulo%205/img4.PNG)<br>
    ![imagen cap5](../images/Capitulo%205/img5.PNG)<br>

### 2. **Creación de alertas**<br>
**A.** Configurar alertas basadas en errores del sistema:<br>
- Puedes configurar alertas para monitorear errores específicos del sistema. Por ejemplo, el error 823 indica un problema de escritura en disco.<br>
- Ve a SQL Server Agent > Alerts > "New Alert". Crea una alerta para el error 823, de la siguiente manera:<br>
    ```sql
    USE msdb;
    EXEC msdb.dbo.sp_add_alert
        @name = N'Error 823',
        @message_id = 823,
        @severity = 0,
        @enabled = 1,
        @delay_between_responses = 0,
        @include_event_description_in = 1,
        @database_name = N'AdventureWorks2019',
        @notification_message = N'Error 823 detectado',
        @job_id = N'00000000-0000-0000-0000-000000000000';
    ```
**B.** Configurar alertas de rendimiento (CPU alta):<br>
- Para monitorear el uso excesivo de CPU, crea una alerta basada en el contador de rendimiento:<br>
    ```sql
    USE msdb;
    EXEC msdb.dbo.sp_add_alert
        @name = N'CPU Usage High',
        @performance_condition = N'SQLServer:Processor(_Total)\% Processor Time > 80',
        @enabled = 1,
        @delay_between_responses = 60,  -- Evitar recibir alertas repetitivas en un corto periodo
        @notification_message = N'CPU usage exceeded 80%',
        @job_id = N'00000000-0000-0000-0000-000000000000';
    ```
**C.** Configurar alertas para bloqueos (Deadlocks):<br>
- Configura alertas para capturar eventos de bloqueo utilizando el error 1205:
     ```sql
    USE msdb;
    EXEC msdb.dbo.sp_add_alert
        @name = N'Deadlock Occurred',
        @message_id = 1205,
        @severity = 0,
        @enabled = 1,
        @delay_between_responses = 0,
        @notification_message = N'Deadlock detected',
        @job_id = N'00000000-0000-0000-0000-000000000000';
    ```
### 3.  **Monitoreo de rendimiento**

**A.** Uso del Monitor de Actividad (Activity Monitor):<br>
- Accede al Activity Monitor en SSMS haciendo clic derecho sobre el servidor y seleccionando "Activity Monitor".<br>
 - En la pestaña de Procesos, observa si hay consultas que consumen excesiva CPU, memoria o están bloqueando otros procesos.<br>
- En la sección de Expensive Queries, identifica las consultas que tardan más en ejecutarse y consume más recursos.
**B.** Uso de SQL Server Profiler:<br>
- El SQL Server Profiler permite capturar eventos detallados en tiempo real.
- Configura una traza para capturar consultas lentas y eventos como bloqueos.<br> Define filtros para capturar solo las consultas que superen un tiempo determinado de ejecución (por ejemplo, consultas que tarden más de 5 segundos):<br>
- Ve a Tools > SQL Server Profiler.<br>
Crea una nueva traza y selecciona "TSQL_Duration" como filtro de eventos.<br>
Filtra para que solo se capturen consultas cuya duración sea superior a 5000 ms (5 segundos).

**C.** Uso del Monitor de Rendimiento (Performance Monitor):<br>
 - Desde Performance Monitor (herramienta de Windows), agrega contadores de SQL Server para supervisar el rendimiento de la memoria, el uso de CPU, y otros aspectos. <br>Algunos contadores clave incluyen:<br>
     - SQLServer:Buffer Manager\Page life expectancy<br>
     - SQLServer:Memory Manager\Memory Grants Pending<br>
    - SQLServer:SQL Statistics\Batch Requests/sec<br>
###  4. **Análisis de problemas comunes y resolución**

**A.** Bloqueos (Deadlocks):<br>
- Si las alertas muestran bloqueos recurrentes, puedes utilizar el monitor de bloqueos de SQL Server o configurar el Trace Flag 1222 para capturar detalles del bloqueo.
- Solución: Revisa el acceso a los recursos en las consultas involucradas en el bloqueo y optimiza el orden en que acceden a los objetos.<br>

**B.** Altos tiempos de CPU:
- Si el uso de CPU es consistentemente alto, identifica las consultas que consumen más CPU utilizando el Activity Monitor o el SQL Server Profiler.
- Solución: Optimiza las consultas que consumen demasiados recursos y asegúrate de que los índices sean correctos.<br>

**C.** Consulta lenta debido a índices faltantes:
- El monitoreo puede mostrar consultas lentas con un plan de ejecución que indica un Table Scan en lugar de un Index Seek.
- Solución: Crea índices adecuados para las columnas filtradas o utilizadas en las condiciones JOIN.

### Revisión y acción basada en alertas
Una vez configuradas las alertas, revisa si las alertas te proporcionan información suficiente para actuar ante problemas. Por ejemplo, si recibes una alerta de CPU alta, revisa el plan de ejecución de las consultas involucradas y ajusta el diseño de índices.

### Resultado esperado
Al finalizar la práctica, los estudiantes deben ser capaces de:
- Configurar alertas en SQL Server para monitorizar errores y rendimiento.
- Configurar el Agente de SQL Server para enviar notificaciones en caso de problemas de rendimiento.
- Utilizar herramientas de monitoreo en tiempo real como Activity Monitor, SQL Server Profiler y Performance Monitor para identificar problemas de rendimiento.
- Analizar y solucionar problemas comunes como bloqueos, uso excesivo de CPU, y consultas lentas debido a índices ineficientes.