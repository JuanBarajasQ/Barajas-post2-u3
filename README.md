# Barajas-post2-u3
<div align="justify">
En el presente repositorio se presenta el desarrollo del laboratorio de la actividad postcontenido 2 de la unidad 3 "MANEJO DEL DEBUG". El laboratorio fue desarollado por Juan Carlos Barajas Quintero, con código 1152455. El propósito del laboratorio es aprender a ensamblar programas directamente en memoria usando el
comando A del DEBUG, ejecutar instrucciones paso a paso con el comando T, registrar el estado de los registros y banderas después de cada instrucción en una tabla de traza, y analizar el comportamiento de un programa con bucle utilizando el registro CX y la instrucción LOOP. Los comandos utilizados durante el laboratorio fueron A, U, R, D y T.
</div>

## COMENTARIOS SOBRE CADA CHECKPOINT:

### CHECKPOINT 1:

<div align="justify">
PARTE A: En esta sección se prepara el entorno de desarrollo DOSBox. Procedemos a ensamblar el programa suma haciendo uso del comando A 100. Verficamos el ensamblaje correcto haciendo uso del comando U, ubicamos correctamente el registro IP con R IP y ejecutamos el comando T en 6 ocasiones para ejecutar nuestro programa de suma previamente ensamblado. La tabla a continuación permite visualizar el camino de trazas:
</div>

### Tabla de Trazas

| N° Traza | Instrucción Ejecutada | AX | BX | CX | IP Siguiente | ZF | CF | SF |
| :---: | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **1** | MOV AX, 000A | 000A | 0000 | 0000 | 0103 | NZ | NC | PL |
| **2** | MOV BX, 0005 | 000A | 0005 | 0000 | 0106 | NZ | NC | PL |
| **3** | MOV CX, 0003 | 000A | 0005 | 0003 | 0109 | NZ | NC | PL |
| **4** | ADD AX, BX | 000F | 0005 | 0003 | 010B | NZ | NC | PL |
| **5** | ADD AX, CX | 0012 | 0005 | 0003 | 010D | NZ | NC | PL |
| **6** | INT 20 | 0012 | 0005 | 0003 | 010D | NZ | NC | PL |

### CHECKPOINT 2:
<div align="justify">
PARTE B: En esta sección se realiza el programa de LOOP. Primero, se ensambla el programa haciendo uso del comando A 100, verificamos el codigo de ensamblado con U 100 10D. Ubicamos el registro IP en 0100 usando R IP. Hacemos uso del comando T en 11 ocasiones para ejecutar completamente el programa. 
<br><br>  
En el código se escribió ADD AX, 0002 (que ocupa 3 bytes), pero DEBUG lo ensambló automáticamente como ADD AX, +02. Esto es una optimización del procesador para ahorrar espacio en memoria cuando el número cabe en un solo byte con signo.
</div>

### Tabla de Traza del Bucle 

| Iteración | Instrucción Ejecutada | AX después | CX después | IP Siguiente | ¿LOOP salta? |
| :---: | :--- | :---: | :---: | :---: | :---: |
| **-** | MOV AX, 0000 | 0000 | 0000 | 0103 | No aplica |
| **-** | MOV CX, 0004 | 0000 | 0004 | 0106 | No aplica |
| **1** | ADD AX, +02 | 0002 | 0004 | 0109 | No aplica |
| **1** | LOOPW 0106 | 0002 | 0003 | 0106 | Si (CX $\neq$ 0) |
| **2** | ADD AX, +02 | 0004 | 0003 | 0109 | No aplica |
| **2** | LOOPW 0106 | 0004 | 0002 | 0106 | Si (CX $\neq$ 0) |
| **3** | ADD AX, +02 | 0006 | 0002 | 0109 | No aplica |
| **3** | LOOPW 0106 | 0006 | 0001 | 0106 | **Si** (CX $\neq$ 0) |
| **4** | ADD AX, +02 | 0008 | 0001 | 0109 | No aplica |
| **4** | LOOPW 0106 | 0008 | 0000 | 010B | No (CX = 0) |
| **-** | INT 20 | 0008 | 0000 | 010B | No aplica |

<div align="justify">
La instrucción LOOP del procesador 8086 realiza dos tareas en un solo paso: primero decrementa automáticamente el registro CX en 1, y luego evalúa si CX es diferente de cero. Si es diferente, salta a la dirección indicada (0106), si llegó a cero, el bucle termina y el programa continúa con la siguiente instrucción (INT 20 en 010B).
</div>

### CHECKPOINT 3:
<div align="justify">
ANÁLISIS DEL CÓDIGO MÁQUINA DEL PASO 8: El comando -D CS:100 L0C de la herramienta DEBUG de MS-DOS realiza un volcado de memoria (Dump) en el segmento de código actual (CS) a partir del desplazamiento 0100h, con una longitud estricta de 0Ch bytes (es decir, 12 bytes en decimal). La salida refleja fielmente el paradigma de arquitectura Little Endian del procesador Intel 8086, donde los bytes menos significativos de los datos se almacenan en las direcciones de memoria más bajas. Esto se evidencia claramente en las instrucciones de carga inmediata de registros (como B8 00 00 para MOV AX, 0 y B9 04 00 para MOV CX, 4), donde el código de operación precede al operando numérico. Asimismo, el guion situado entre el octavo y el noveno byte actúa puramente como un separador visual estándar de la herramienta para facilitar la lectura de bloques de 16 bytes, dividiendo la fila en dos grupos iguales.
<br><br>
A nivel de análisis de software, este volcado expone una curiosa anomalía de truncamiento provocada por la propia sintaxis del comando ejecutado. El programa completo requiere en realidad 13 bytes para operar de manera íntegra, culminando con la instrucción de terminación INT 20, cuyo código de operación completo en lenguaje máquina es CD 20h. No obstante, al haber limitado la lectura con el parámetro L0C, el sistema detuvo la impresión exactamente tras leer el byte número 12 (CD), dejando el byte 20h fuera del rango de visualización. Este ejercicio demuestra cómo los comandos de inspección en bajo nivel requieren de un dimensionamiento exacto por parte del programador, ya que un conteo de longitud inferior al tamaño real de las instrucciones puede resultar en interpretaciones de código máquina incompletas o erróneas.
</div>
