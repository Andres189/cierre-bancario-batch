# Cierre bancario con Spring Batch

**Autor:** Andrés Juárez Garduño

## Cómo correrlo

    docker compose up -d --wait
    ./correr.sh 2026-09-30 prueba
    ./ver-batch.sh

## Día 1 · Mi primer Job

### Boleto de salida

1. ¿Qué diferencia hay entre un proceso batch y la API REST de la Semana 3? Da dos. <br>
Diferencia 1.- El programa batch corre solo, sin intervención, toma los archivos del día, lo guarda en una DB y al final de la semana publica el saldo de cada cuenta. <br>
Diferencia 2.- La APIrest a diferencia de batch es que funciona por intreacción de usuarios o llamadas de un cliente HTTP <br>
2. ¿Qué es un Job, qué es un Step y qué es un Tasklet? <br>
Job: Es un proceso completo que contiene steps.<br>
Step: Es una fase del job, puede tener 1 o más.<br>
Tasklet: Operación que corre dentro de un step (el step que la corre se le llama step de tipo tasklet).<br>
3. Con tus tablas: ¿qué diferencia hay entre una **JobInstance** y una **JobExecution**?<br>
JobInstance: Crea el objeto java que representa la lógica del Job (prueba de que un job ya existe).<br>
JobExecution: Es cuando se pone en marcha la lógica definida por la instancia del Job.<br>
4. ¿Por qué Spring Batch no deja correr dos veces el cierre del 28?<br>
Por qué la segunda vez que se intentó ejecutar ya existía la instancia de ese Job con esos parámetros.<br>
5. (MP-4, paso 6) Si mañana llega el archivo del 25 y corres otra vez el cierre del 25, ¿será otra instancia u
   otra ejecución de la misma? ¿Por qué lo crees?<br>
Sera otra ejecución de esta, por el mismo problema que tuvimos al ejecutar 2 veces el cierre del día 28.<br>

## Día 2 · El primer chunk

### Boleto de salida

1. ¿Qué diferencia hay entre un step de tipo Tasklet y uno de tipo chunk?<br>
Tasklet es cuando se tiene una tarea especifica que se tiene que ejecutar 1 o varias veces. <br>
Chunk para procesar registros, leer procesar y escribir. <br>
2. ¿Qué hace cada una de las tres piezas de un chunk? ¿Cuál es opcional?<br>
Lector los datos un renglon a la vez hasta juntar los chunks <br>
Procesador (Opcional) Limpia los movimientos (procesar/transformar).<br>
Escritor Guarda los bloques completos de los movimientos (En este caso en MySQL).<br>
4. Con 45 movimientos y chunks de 10, ¿Cuántos commits habría? ¿Y con chunks de 50? <br>
45 movimientos con chunks de 10 dará 5 commits 10+10+10+10+5. <br>
45 movimientos con chunks de 50 dará 1 commit 45.
5. ¿Por qué el Escritor recibe el chunk completo y no un movimiento a la vez?<br>
Por que esta batch esta diseñado para procesar datos por bloques (los chunks) y no hacer 1 commit por cada movimiento. 
6. Mi predicción de la MP-3, paso 1: ¿qué habría pasado sin el Procesador?<br>
No se haría la transformación de los datos, así como se leen los datos se guardarían en MySQL incluidos los espacios y diferencia entre mayúsculas y minúsculas.<br>

## Día 3 · Parámetros, fallas y reinicio

### Boleto de salida

1. ¿Qué diferencia hay entre una JobInstance y una JobExecution? Usa como ejemplo el cierre del 25.<br>
JobInstance es la ejecución lógica de un job.<br>
JobExecution es el intento de ejecutar JobInstance y contiene información como el inicio, fin y el resultado.<br>
2. ¿En qué caso Spring Batch se niega a correr un cierre, y en qué caso lo reinicia? <br>
Se niega cuando el cierre ya se ejecuto correctamente y se intenta ejecutar el mismo jobInstance. <br>
Se puede reiniciar cuando ocurrió algún error en la ejecución del jobInstance. <br>
3. En el reinicio del día 5, ¿por qué el step de carga leyó 10 movimientos y no 20? <br>
Spring batch anoto hasta donde había llegado y siguio desde donde se quedo. <br>
4. ¿Qué diferencia hay entre un movimiento **filtrado** y uno **omitido**? <br>
Los movimientos filtrados no se escriben pero no significa que sean un error. <br>
los omitidos son como excepciones y podemos saltar estos movimientos. <br>
5. ¿Por qué importa el código de salida, si el estado ya queda en las tablas? <br>
El código de salida importa porque spring batch puede registrar un Job como failed pero si el proceso termina con un código de salida 0, Se puede interpretar como exitoso.
