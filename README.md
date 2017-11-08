### Examen 2
**Universidad ICESI**  
**Curso:** Sistemas Operativos  
**Docente:** Daniel Barragán C.  
**Tema:** Namespaces, CGroups, LXC  
**Correo:** daniel.barragan at correo.icesi.edu.co  

**Estudiante:** Rubén Darío Ceballos Muriel  
**Código:** A00054636  
**Correo estudiante:** rubendcm9708@gmail.com  
**Git URL:** https://github.com/rubendcm9708/so-exam2/tree/A00054636/so-exam2

### Objetivos
* Comprender los fundamentos que dan origen a las tecnologías de contenedores virtuales
* Conocer y emplear funcionalidades del sistema operativo para asignar recursos a procesos
* Conocer y emplear capacidades de CentOS7 para la virtualización

### Prerrequisitos
* Virtualbox o WMWare
* Máquina virtual con sistema operativo CentOS7

### Descripción
El segundo parcial del curso sistemas operativos trata sobre el manejo de namespaces, cgroups y virtualización por medio de LXC/LXD

### Actividades
1. Incluir nombre, código (5%)
2. Ortografía y redacción cuando sea necesario (5%)
3. Realice una prueba de concepto empleando systemd y el recurso de control CPUQuota teniendo en cuenta los requerimientos que se describen a cotinuación. Incluya evidencias del funcionamiento de lo solicitado (30%):
 * Las pruebas se realizaran sobre un solo núcleo de la CPU
 * Se deben ejecutar dos procesos
 * Cada proceso debe poder acceder solo al 50% de la CPU
 * Cuando uno de los procesos se cancela, el que continua ejecutándose no debe acceder a mas del 50% de la CPU

### Desarrollo Punto 3  
Para poder tener la certeza de que los procesos que evaluaremos usan todos los recursos disponibles, generamos un script con una suma en ciclo infinito. Este se almacenó en la ruta */home/exam2/* el cual es el siguiente:

![][1]  

Como se solicita hacer la partición de la CPU entre dos procesos, se crearon 2 scripts identicos, llamados *ciclo.sh* y *ciclo2.sh*, ambos con las mismas líneas de código para evitar problemas a la hora de medir el consumo. A continuación se les agregó permisos de ejecución.

![][2]  

El sistema tiene ciertos servicios que siempre están en ejecución, estos son necesarios para el funcionamiento constante del sistema. Se pueden añadir más servicios a esta lista. Para ello, debemos acceder a */etc/systemd/system/* y crear allí dos servicios adicionales, que serán los script que creamos anteriormente. Para ello usamos el comando *touch*, se les dá todos los permisos necesarios y lo editamos usando el editor de texto *vi*. Esto para ambos servicios.

![][3] 

Lo que se escribe dentro de estos archivos .service es lo siguiente

```
[Unit]
Description=Ciclo_infinito
After=network.target

[Service]
ExceStart=/root/Parcial2/consume_cpu.sh
CPUQuote=50%
```
Donde el *CPUQuote* es el % de nuestro núcleo que le asignaremos al proceso, y *ExceStart* es la ruta donde se encuentra nuestro script.

A continuación, debemos hacer un reload al *daemon* que esta encargado de ejecutar los servicios de systemd. Finalmente, podemos usar *systemctl start* seguido del nombre del servicio que queremos ejecutar.

![][4]

Con esto, ya podremos consultar la lista de procesos en ejecución usando el comando *top*. Se despliega la siguiente información:

![][5]

Como podemos observar, existen 2 procesos llamados *ciclo2.sh* y *ciclo.sh* activos, los cuales consumen 49,8% y 49,5% de CPU, que son valores muy cercanos al 50%.

El siguiente punto a evaluar, es si al matar uno de los procesos, el otro aprovechará los recursos que no se están usando y este valor subirá al 100%. A continuación, usamos el comando *kill -9* seguido del PID *3124* que es el idenficador del proceso *ciclo2.sh*.

![][6]

Efectivamente, el proceso *ciclo1.sh* esta consumiendo exactamente el 50% de la CPU, ya que una de las funcionalidades de *CPUQuota* es que el valor asignado al proceso, es el límite máximo de recursos que podrá usar. 
 
4.  Realice una prueba de concepto empleando systemd y el recurso de control CPUShares teniendo en cuenta los requerimientos que se describen a continuación. Incluya evidencias del funcionamiento de lo solicitado (30%):
 * Las pruebas se realizaran sobre un solo núcleo de la CPU
 * Se deben ejecutar dos procesos
 * Uno de los procesos tendrá el 25% de la CPU mientras que el otro tendrá el 75% de la CPU
 * Cuando uno de los procesos se cancela, el que continua ejecutándose debe poder llegar al 100% de la CPU
5. Por medio de las evidencias obtenidas en los puntos anteriores y de fuentes de consulta en Internet, elabore las definiciones para los grupos de control CPUQuota y CPUShares, además concluya acerca de cuando es preferible usar un recurso de control sobre otro (20%)
6. El informe debe ser entregado en formato pdf a través del moodle y el informe en formato README.md debe ser subido a un repositorio de github. El repositorio de github debe ser un fork de https://github.com/ICESI-Training/so-exam2 y para la entrega deberá hacer un Pull Request (PR) respetando la estructura definida. El código fuente y la url de github deben incluirse en el informe (10%)  

### Referencias
https://github.com/ICESI/so-containers

[1]: images/proceso.PNG
[2]: images/procesos.PNG
[3]: images/cpuquote.PNG
[4]: images/cpuquote_inicio_procesos.PNG
[5]: images/cpuquote_top_procesos.PNG
[6]: images/cpuquote_top_proceso1.PNG
[7]: images/cpushares.PNG
[8]: images/cpushares_inicio_procesos.PNG
[9]: images/cpushares_top_procesos.PNG
[10]: images/cpushares_top_proceso1.PNG
