# Cierre bancario con Spring Batch

**Autor:** Andrés Juárez Garduño

## Cómo correrlo

    docker compose up -d --wait
    ./correr.sh 2026-09-30 prueba
    ./ver-batch.sh

## Día 1 · Mi primer Job

### Boleto de salida

1. ¿Qué diferencia hay entre un proceso batch y la API REST de la Semana 3? Da dos.
Diferencia 1.- El programa batch corre solo, sin intervención, toma los archivos del día, lo guarda en una DB y al final de la semana publica el saldo de cada cuenta.
Diferencia 2.- La APIrest a diferencia de batch es que funciona por intreacción de usuarios o llamadas de un cliente HTTP
2. ¿Qué es un Job, qué es un Step y qué es un Tasklet?
Job: Es un proceso completo que contiene steps.
Step: Es una fase del job, puede tener 1 o más.
Tasklet: Operación que corre dentro de un step (el step que la corre se le llama step de tipo tasklet).
3. Con tus tablas: ¿qué diferencia hay entre una **JobInstance** y una **JobExecution**?
JobInstance: Crea el objeto java que representa la lógica del Job (prueba de que un job ya existe).
JobExecution: Es cuando se pone en marcha la lógica definida por la instancia del Job.
4. ¿Por qué Spring Batch no deja correr dos veces el cierre del 28?
Por qué la segunda vez que se intentó ejecutar ya existía la instancia de ese Job con esos parámetros.
5. (MP-4, paso 6) Si mañana llega el archivo del 25 y corres otra vez el cierre del 25, ¿será otra instancia u
   otra ejecución de la misma? ¿Por qué lo crees?
Sera otra ejecución de esta, por el mismo problema que tuvimos al ejecutar 2 veces el cierre del día 28.