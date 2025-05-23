**Ejercicios de práctica para examen de Bases de Datos II (basados en los apuntes)**

---

**TEMA 1: Acceso a SQL desde Aplicaciones**

1. Explica dos razones por las que no se puede usar SQL puro para construir toda una aplicación de bases de datos.
2. Dado un ejemplo en pseudocódigo usando JDBC que:
   * Se conecte a una base de datos.
   * Realice una consulta con parámetros (usando `PreparedStatement`).
   * Imprima los resultados.
3. ¿Cuál es la diferencia entre SQL embebido y SQL dinámico? Pon ejemplos.
4. ¿Cuáles son las ventajas de usar sentencias preparadas? Relaciónalas con la inyección SQL.
5. Explica brevemente el funcionamiento de ODBC. Diseña una secuencia de pasos que siga un programa en C que se conecta a una base de datos usando ODBC.

**TEMA 2: Seguridad**

6. Dada la siguiente secuencia de instrucciones:

```sql
CREATE USER maria;
GRANT gestor TO maria;
SET ROLE gestor;
GRANT cajero TO juan;
REVOKE gestor FROM maria;
DROP USER maria;
```

* ¿Cuál es el estado final de los roles y permisos de `juan`?
* ¿Qué pasa si el `GRANT` a `juan` se hizo con `GRANTED BY CURRENT_ROLE`?

7. Explica el concepto de "revocación en cascada" y cuándo puede evitarse.
8. ¿Qué problemas tiene que varios empleados usen el mismo usuario para acceder a la BD? ¿Cómo se puede solucionar correctamente?
9. Explica tres mecanismos que eviten la inyección SQL en aplicaciones.
10. ¿Qué es una traza de auditoría? Diseña un trigger que registre en una tabla log las operaciones `UPDATE` sobre una tabla de clientes.

**TEMA 3: Transacciones y Recuperación**

11. Explica las propiedades ACID con un ejemplo.
12. En un sistema con actualizaciones diferidas, describe qué ocurre si el sistema falla antes del commit.
13. Dadas dos transacciones que modifican valores de A y B, indica:

* Si son recuperables.
* Si pueden generar retrocesos en cascada.
* Qué condiciones se deben cumplir para que no los generen.

14. Explica las diferencias entre almacenamiento volátil, no volátil y estable.
15. Dibuja una línea de tiempo con los estados de una transacción (activa, parcialmente comprometida, fallida, abortada, comprometida) y explica cada uno.

**TEMA 4: Control de Concurrencia**

16. Explica en qué consiste el protocolo de bloqueo en dos fases. ¿Qué es el punto de bloqueo?
17. Compara el protocolo en dos fases con el estricto y el riguroso. ¿Cuál asegura ausencia de retrocesos en cascada?
18. Dado el siguiente caso:

```
T1: bloquear-x(B), leer(B), B := B - 50, escribir(B), desbloquear(B)
T2: bloquear-c(B), leer(B), desbloquear(B)
```

* Construye el grafo de precedencia.
* ¿La planificación es secuenciable en cuanto a conflictos?

19. Describe qué es un interbloqueo. Dado el siguiente escenario:

```
T1: bloquear-x(A), bloquear-x(B)
T2: bloquear-x(B), bloquear-x(A)
```

* ¿Qué estrategia de prevención usarías (esperar-morir o herir-esperar)? Justifica.

20. Enumera y explica brevemente los cuatro niveles de aislamiento definidos por SQL.

---

**Ejercicios prácticos adicionales (estilo examen real)**

**Ejercicio 1 – Aislamiento y errores en transacciones**

Dadas las siguientes dos transacciones:

```
T1                         T2
leer(A)                  
A := A + 100             
bloquear-X(B)            leer(B)
B := B - 100             
escribir(B)              
commit                   
                          escribir(B)
                          commit
```

a) ¿Qué tipo de error puede producirse si no se controla adecuadamente el aislamiento entre transacciones?  
b) ¿Qué propiedad ACID se ve comprometida?  
c) ¿Qué componente del SGBD es responsable de garantizar dicha propiedad?

**Ejercicio 2 – Prevención de interbloqueos**

Considera el siguiente escenario:

```
T1: bloquear-X(B)
    leer(B)
    B := B - 50
    escribir(B)

T2: bloquear-C(A)
    leer(A)
    bloquear-C(B)
    bloquear-X(A)
```

a) ¿Qué ocurre si se utiliza el protocolo **Esperar-Morir**?  
b) ¿Y si se utiliza **Herir-Esperar**?

**Ejercicio 3 – Análisis de revocación de roles**

Supón que ejecutamos las siguientes instrucciones:

```sql
Usuario=ADMIN
CREATE USER Maria;
GRANT Gestor TO Maria;
Usuario=Maria
GRANT Cajero TO Juan;
GRANT Cajero TO Marta;
Usuario=ADMIN
REVOKE Gestor FROM Maria;
DROP USER Maria;
```

Analiza:

- ¿Qué ocurre con los roles de Juan y Marta?
    
- ¿Qué cambiaría si se hubiese usado `GRANTED BY current_role`?
    

**Ejercicio 4 – Seguridad en el diseño de aplicaciones**

Una empresa crea un usuario genérico llamado `Cajero` que es compartido por todos los empleados para operar el sistema bancario desde el cajero automático.

a) ¿Qué problemas de seguridad presenta este enfoque?  
b) Propón una solución más segura usando roles y usuarios individuales.

**Ejercicio 5 – Recuperación tras fallo con modificación diferida**

Supón que las transacciones T1 y T2 ejecutan las siguientes instrucciones:

```
1. T1: leer(B)
2. T1: B := B - 50
3. T1: escribir(B)
4. T1: leer(A)
5. T1: A := A + 50
6. T1: escribir(B)
7. T1: visualizar(A+B)
8. T1: commit
9. T2: leer(A)
10. T2: A := A * 2
11. T2: escribir(A)
12. T2: visualizar(A)
13. T2: commit
```

Asumiendo que se utiliza **modificación diferida**:

- ¿Qué valores se almacenan en disco y en el registro de historial (RH) en cada paso?
    
- ¿Qué sucede si hay un fallo del sistema justo después del paso 12?
    
- Describe el contenido del RH antes y después de la recuperación.
    

---

¿Quieres que añada una sección con soluciones sugeridas?
