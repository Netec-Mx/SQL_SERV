# Instalación y configuración de SQL Server


## Objetivos de la práctica:
- Instalar SQL Server 2019 en un equipo local.
- Configurar SQL Server Management Studio (SSMS) como herramienta de administración.
- Configurar opciones básicas de seguridad, incluyendo la autenticación SQL y Windows.
- Verificar el correcto funcionamiento del servidor a través de la creación de una base de datos.
- Conectar al servidor y validar funcionamiento. 


## Duración aproximada:
- 60 minutos.

## Instrucciones 

### Paso 1. Descarga SQL Server 2019
Visita el sitio web oficial de Microsoft y descarga la versión gratuita de SQL Server 2019 Developer.
 > [!NOTE]
> Lo puedes realizar mediante el siguiente link <br>
> https://go.microsoft.com/fwlink/?linkid=866662

### Paso 2. Ejecutar el instalador
![imagen instalacion](../images/Capitulo%201/img1.png)<br>

La opción básica descarga los archivos desde la web y realiza una instalación por defecto, está es la que vamos a utilizar.

![imagen instalacion](../images/Capitulo%201/img2.png)<br>

Aceptamos términos y condiciones y configuramos el idioma que deseemos.

![imagen instalacion](../images/Capitulo%201/img3.png)<br>

Escogemos una ruta de instalación, es recomendable dejarla por defecto y procedemos a darle instalar. 

![imagen instalacion](../images/Capitulo%201/img4.png)<br>

Finalmente nos muestra que la aplicación se instaló correctamente. Sin cerrar está ventana le daremos click en el apartado de Personalizar. 

### Paso  3. Instalar SQL Server 2019 edición desarrollo
Se te abrirá la siguiente ventana.

![imagen instalacion](../images/Capitulo%201/img5.png)<br>

El programa hace una serie de validaciones y si hay alguna advertencia o error nos lo muestra en pantalla, por ejemplo, en este caso advirtió que no se tiene configurado el Firewall para SQL Server. Esto lo haremos en los siguientes puntos. 

![imagen instalacion](../images/Capitulo%201/img6.png)<br>
![imagen instalacion](../images/Capitulo%201/img7.png)<br>

Debes seleccionar la edición “Developer”, no es necesario poner ninguna llave.  

![imagen instalacion](../images/Capitulo%201/img8.png)<br>

![imagen instalacion](../images/Capitulo%201/img9.png)<br>

En nuestro caso como solo deseamos SQL Server se van a seleccionar las siguientes opciones que están señaladas en la siguiente imagen. 

![imagen instalacion](../images/Capitulo%201/img10.png)<br>

![imagen instalacion](../images/Capitulo%201/img11.png)<br>

> [!WARNING]
> Si se presenta algún error en la imagen anterior, deberás crear una instancia.<br>

El inicio del servicio de agente en el instalador viene por defecto en modo Manual, se recomienda pasarlo a modo Automático, como también darle los privilegios de Perform Volumen Maintenance a las cuentas del servicio para así lograr una mejor performance a la hora de expandir nuestros archivos MDF o LDF.

![imagen instalacion](../images/Capitulo%201/img12.png)<br>

Para configurar el tipo de autentificación de SQL Server, haremos la siguiente configuración, el usuario por defecto es SA, la contraseña la escogemos nosotros y agregamos un usuario.

![imagen instalacion](../images/Capitulo%201/img13.png)<br>

![imagen instalacion](../images/Capitulo%201/img14.png)<br>

![imagen instalacion](../images/Capitulo%201/img15.png)<br>

Una vez todo esté instalado, le damos al botón de Close. 

### Paso  4. Configuración de acceso remoto

Para configurar el acceso remoto debemos abrir SQL Server 2019 Configuration<br>

![imagen instalacion](../images/Capitulo%201/img16.png)<br>

Nos dirigiremos a Protocols for MSSQLSERVER y en el apartado de TCP/IP le daremos “Enabled”


![imagen instalacion](../images/Capitulo%201/img17.png)<br>

Una vez realizado el paso anterior debemos dirigirnos a SQL server Services y darle Restar al SQL Server.


![imagen instalacion](../images/Capitulo%201/img18.png)<br>

Ahora solo nos quedará habilitar la regla en el firewall de Windows, para ello haremos los siguientes pasos

- Ir al panel de control
- Seleccionar la opción “System & Security”
- Seleccionar Windows Firewall
- Seleccionar la opcion de “Advance Settings”
 
 ![imagen instalacion](../images/Capitulo%201/img19.png)<br>
 ![imagen instalacion](../images/Capitulo%201/img20.png)<br>

 Creamos una nueva regla de entrada y seleccionamos que es una regla que controla las conexiones de un puerto TCP o UDP.

 ![imagen instalacion](../images/Capitulo%201/img21.png)<br>
 ![imagen instalacion](../images/Capitulo%201/img22.png)<br>

 > [!TIP]
> El puerto específico para SQL Server es el 1433.

 ![imagen instalacion](../images/Capitulo%201/img23.png)<br>
 ![imagen instalacion](../images/Capitulo%201/img24.png)<br>

 Escogemos un nombre de la regla, puede ser por ejemplo Regla SQL

  ![imagen instalacion](../images/Capitulo%201/img25.png)<br>


## Instalación de SQL Server Management Studio (SSMS)
## Paso 1: Descargar instalador
- Ejecuta el instalador de SSMS y sigue los pasos para completar la instalación.
- Debes descargar desde la página oficial de Microsoft el medio de instalación de SQL Server 2019 Developer edition. 
 > [!NOTE]
> Lo puedes realizar mediante el siguiente link <br>
> https://aka.ms/ssmsfullsetup

## Paso 2: Ejecutar el instalador

![imagen instalacion](../images/Capitulo%201/img26.png)<br>
![imagen instalacion](../images/Capitulo%201/img27.png)<br>
![imagen instalacion](../images/Capitulo%201/img28.png)<br>

Una vez terminada la instalación debemos darle en cerrar y procederemos a buscar en nuestros archivos SQL Server Managment Studio 20.

![imagen instalacion](../images/Capitulo%201/img29.png)<br>
![imagen instalacion](../images/Capitulo%201/img30.png)<br>

Nos conectaremos al servidor lo podremos hacer de dos formas distintos, a través de la autentificación o a través del usuario y contraseña creados al momento de instalar SQL Server. <br>

Si todo está correcto podremos visualizar la correcta conexión al servidor. 

1. **Configuración inicial de SQL Server:**
    - Inicia SQL Server Management Studio.
    - Conéctate al servidor utilizando autenticación de Windows o SQL.
    - Accede a la configuración de seguridad y verifica que esté habilitada la autenticación mixta.
    - Asegúrate de que el servicio de SQL Server esté ejecutándose.
2. **Creación de una base de datos de prueba:**
    - Desde SSMS, conéctate a tu instancia de SQL Server.
    - En el panel de Explorador de objetos, haz clic derecho sobre "Bases de datos" y selecciona "Nueva base de datos".
    - Nombra la base de datos como Prueba_DB y completa su creación.

### Resultado esperado
Al finalizar la práctica, el estudiante habrá instalado exitosamente SQL Server 2019 y SQL Server Management Studio. Además, deberá haber configurado la autenticación mixta y creado una base de datos de prueba. La correcta ejecución de la consulta demostrará que el servidor está operativo y funcional para realizar operaciones básicas de bases de datos.
