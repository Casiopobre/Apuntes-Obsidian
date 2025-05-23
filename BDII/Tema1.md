# Tema 1. Acceso a SQL dende aplicacións de BD
## Introducción
#### Para qué acceder a SQL mediante linguaxes convencionais?
Principalmente hai **dous motivos**:
+ Para _expresar consultas_ que non se poden expresar en SQL
+ Para levar a cabo _accións non declarativas_ que non se poden levar a cabo en SQL (interacción cos usuarios, interfaz gráfica...)

#### Enfoques sobre o acceso a SQL:
+ **SQL dinámico**: un programa de propósito xeral pode conectarse a un servidor de base de datos empregando unha colección de funcións ou métodos. Permite que os programas constrúan e envíen consultas _en tempo de execución_ (JDBC e ODBC)
+ **SQL embebido**: proporciona un mecanismo para que os programas interactúen cunha base de datos, pero as sentencias de SQL se identifican durante a compilación empregando un preprocesador, que as envía ao sistema de BD para a súa precompilación e optimización. Entón, substitúense as sentencias de SQL do programa polo código apropiado e as chamadas a funcións antes de iniciar a compilación.

\*Problema principal: a distinta forma en que ambas linguaxes manexan os datos

## JDBC
A norma JDBC (Java DataBase Connectivity) define unha _API_ (interfaz de programación de aplicacións) que poden empregar os _programas en Java para conectarse a servidores de BD_.

### Modelo básico de emprego de JDBC
#### 1. Conexión á base de datos:
Antes de nada, hai que **cargar o controlador de JDBC** invocando `Class.forName` cun arg que especifica a clase concreta que implementa a interfaz java.sql.Driver: 
```java
Class.forName("oracle.jdbc.driver.OracleDriver");
```

Despois hai que **abrir unha conexión** á BD. Para esto emprégase o _método `getConnection`_ da clase `DriverManager`, que ten 3 parámetros:
1. String que especifica a _máquina na que se executa o servidor_ (url ou nome), xunto co _protocolo_, o número de _porto_, e a _base de datos_ concreta que se vai empregar.
2. String identificador do _usuario_ da BD
3. String: _contraseña_
```java
Connection conn = DriverManager.getConnection(
            "jdbc:oracle:thin:@db.yale.edu:1521:univdb",
            usuarioid, contraseña
        );
```

#### 2. Envío de sentencias SQL ao sistema de bases de datos:
Faise mediante un **obxeto Statement**, que permite invocar os métodos que envían unha sentencia de SQL para que se execute no sistema de bases de datos. Así, para executar unha sentencia invócase o _método `executeQuery`_ (se é unha _consulta_ e, polo tanto, devolve un resultado), ou o _método `executeUpdate`_ (se _non é unha consulta_).

#### 3. Obtención do resultado dunha consulta:
O conxunto de tuplas resultado dunha consulta recupérase mediante un **obxeto da clase ResultSet**. Para _determinar se existen máis tuplas pendentes_ emprégase o método `next` sobre o conxunto de resultados (ver apuntes POO T3), e para _obter os atributos_ da tupla obtida empréganse os getters (`getString`, `getInt`...).

#### 4. Finalización da conexión:
Ao final do programa de Java debemos **pechar a sentencia e a conexión** (é importante que esta última non quede aberta): `conn.close();`

#### Exemplo:
```java
public static void JDBCejemplo(String usuarioid, String contraseña) {
    try {
        // Cargamos o Driver (1)
        Class.forName("oracle.jdbc.driver.OracleDriver");
        
        // Creamos a conexión (1)
        Connection conn = DriverManager.getConnection(
            "jdbc:oracle:thin:@db.yale.edu:1521:univdb",
            usuarioid, contraseña
        );
        
        // Creamos a consulta
        Statement stmt = conn.createStatement();
        
        try {
            // Insertamos un rexistro na taboa profesor
            stmt.executeUpdate(
                "insert into profesor values('77Q87', 'Kim', 'Física', 98000)"
            );
        } catch (SQLException sqle) {
            System.out.println("No se pudo insertar la tupla. " + sqle);
        }

        // Gardamos o resultado dunha consulta
        ResultSet rset = stmt.executeQuery(
            "select nombre_dept, avg(sueldo) " +
            "from profesor " +
            "group by nombre_dept"
        );
        
        while (rset.next()) {
            // Imprimimos os resultados
            System.out.println(rset.getString("nombre_dept") + " " + rset.getFloat(2));
        }
        
        // Pechamos a consulta e a conexión
        stmt.close();
        conn.close(); // !!!!! Importante
    } catch (Exception sqle) {
        System.out.println("Excepción: " + sqle);
    }
}
```

### Sentencias preparadas
Pódense crear sentencias preparadas para poder **proporcionar algúns valores ('?') máis adiante**.
Para esto, o método `prepareStatement` da clase `Connection` _envía unha sentencia SQL_ para a súa compilación e _devolve un obxeto_ da clase `PreparedStatement`. Inda non e realizou ningunha consulta, xa que antes **debemos asignar valores aos parámetros '?'** cos métodos `setString`, `setInt` e similares, que teñen como _argumentos_ o _parámetro '?'_ para o que se asigna un valor (comenzando por 1) e o _valor_ a asignar.
**Vantaxes das sentencias preparadas**:
+ Permiten unha _execución máis eficiente_ cando a _mesma consulta_ se pode compilar unha vez e _executar varias veces_ con diferentes valores.
+ Proporcionan _protección frente ás inxeccións SQL_ sempre que se empreguen datos introducidos polos usuarios (ver apuntamentos HTB).

> [! Exemplo]
Código para asignar parámetros a unha sentencia preparada
```java
PreparedStatement pStmt = conn.prepareStatement(
    "insert into profesor values(?, ?, ?, ?)"
);

// Asignación de parámetros para a sentencia preparada
pStmt.setString(1, "88877");  // Primeiro parámetro
pStmt.setString(2, "Perry");  // Segundo parámetro
pStmt.setString(3, "Finanzas");  // Terceiro parámetro
pStmt.setInt(4, 125000);  // Cuarto parámetro

// Executamos a sentencia de inserción
pStmt.executeUpdate();

pStmt.setString(1, "88878");  // Actualización do primeiro parámetro
pStmt.executeUpdate();
```

### Metadatos
Dado que dende o programa Java _non podemos coñecer a organización concreta da base de datos_, un programa en Java que emprega JDBC debe **determinar a información** directamente da base de datos **en tempo de execución** para adaptarse a posibles cambios no esquema e procesar consultas de forma dinámica.

Cando empregamos `executeQuery()`, o resultado da consulta se obtén nun obxeto `ResultSet`, que ten un método **`getMetaData()`** para obter os metadatos nun obxeto **`ResultSetMetaData`** que, á súa vez, contén _métodos para determinar a información dos metadatos_ (número de columnas dun resultado, nome dunha columna concreta, tipo de datos dunha columna dada...). Esto permite **executar unha consulta sen necesidade de coñecer o esquema** do resultado. 
> [! Exemplo]
> ```java
>ResultSetMetaData rsmd = rs.getMetaData();
>for(int i = 1; i <= rsmd.getColumnCount(); i++) {
 >   System.out.println(rsmd.getColumnName(i));
 >   System.out.println(rsmd.getColumnTypeName(i));
>}
>```

Tamén podemos obter os **metadatos da base de datos** mediante a interfaz **DatabaseMetaData**: a interfaz _Connection_ ten un método `getMetaData()` que _devolve un obxeto do tipo DatabaseMetaData_, que contén varios _métodos para obter os metadatos da BD_ á que está conectada a aplicación.

>[! Exemplo]
> Os argumentos de getColumns son catálogo, esquema, táboa e columna. Neste caso estámoslle indicando que queremos obter información sobre todas (`"%"`)as columnas da táboa `"departamento"` na base de datos `"univdb"`. Con `"COLUMN_NAME"` accédese ao nome da columna e con `"TYPE_NAME"`, ao tipo de dato da columna.
```java
DatabaseMetaData dbmd = conn.getMetaData();
ResultSet rs = dbmd.getColumns(null, "univdb", "departamento", "%");
// Argumentos de getColumns: Catálogo, esquema, taboa e columna
// Devuelve: Una fila por columna; la fila tiene un número
// de atributos como COLUMN_NAME, TYPE_NAME
while(rs.next()) {
    System.out.println(rs.getString("COLUMN_NAME") + ", " +  s.getString("TYPE_NAME"));
}
```

### Outras características
Os **conxuntos de resultados actualizables** son un tipo de obxecto `ResultSet` en JDBC que permite non só consultar datos dunha base de datos, senón tamén _modificalos directamente_ a través do conxunto de resultados obtido.

De forma predeterminada, **cada instrucción de SQL trátase como unha transacción independente** que se compromete de forma _automática_. Este comportamento _pódese activar ou desactivar_ mediante o método `setAutoCommit()` da interfaz Connection. Se o _desactivamos_, entón as transaccións terán que comprometerse de maneira _explícita_ mediante o método `commit()` de Connection ou retrocederse con `rollback()`.

Para o tratamento de **obxetos de gran tamaño**, JDBC proporciona _interfaces_ para non ter que crealos na memoria (métodos `getBlob()` e `getClob()` de ResultSet).

## ODBC
A norma ODBC (Open DataBase Connectivity) define unha _API_ que poden empregar todas as aplicacións para abrir conexións cunha BD, enviar consultas e actualizacións e obter resultados.
### Modelo básico de emprego de ODBC
#### 1. Conexión ao servidor da base de datos:
Primeiro debemos **configurar a conexión**, asignando un _entorno de SQL_ e un _manexador_ para a conexión á BD; e despois **abrimos a conexión** á BD mediante **`SQLConnect`**.
#### 2. Envío de sentencias SQL ao sistema de bases de datos:
Para **enviar comandos SQL** á BD empregamos **`SQLExecDirect`**. 
#### 3. Obtención do resultado dunha consulta:
Para poder **almacenar** os valores dos atributos dunha tupla **resultado** (obtida mediante `SQLFetch`) podemos _vincular variables de C a eses atributos_ mediante `SQLBindCol`.
A sentencia `SQLFetch` está nun _bucle while que se executa ata que `SQLFetch` devolva un valor diferente a `SQL_SUCCESS`_.
#### 3. Finalización da conexión:
O programa **libera o controlador** da instrucción, **desconéctase da base de datos** e **libera a conexión e os manexadores** do entorno de SQL.

```c
void ODBCejemplo() {
    RETCODE error;
    HENV env; // entorno
    HDBC conn; // conexión a base de datos

    SQLAllocEnv(&env);
    SQLAllocConnect(env, &conn);
    SQLConnect(conn, “db.yale.edu”, SQL_NTS, “avi”, SQL_NTS, “contr”, SQL_NTS);

    {
        char nombredept [80];
        float sueldo;
        int lenOut1, lenOut2;
        HSTMT stmt;

        char * sqlquery = “select nombre_dept, sum (sueldo)
                           from profesor
                           group by nombre_dept “;
        SQLAllocStmt(conn, &stmt);
        error = SQLExecDirect(stmt, sqlquery, SQL_NTS);
        if (error == SQL_SUCCESS) {
            SQLBindCol(stmt, 1, SQL_C_CHAR, nombredept, 80, &lenOut1);
            SQLBindCol(stmt, 2, SQL_C_FLOAT, &sueldo, 0 , &lenOut2);
            while (SQLFetch(stmt) == SQL_SUCCESS) {
                printf (“ %s %g\n”, nombredept, sueldo);
            }
        }
        SQLFreeStmt(stmt, SQL_DROP);
    }
    SQLDisconnect(conn);
    SQLFreeConnect(conn);
    SQLFreeEnv(env);
}
```

### Outras características
Ao igual que en JDBC, de forma predeterminada, cada **sentencia de SQL** se trata como unha transacción separada que se **compromete automáticamente**. Neste caso, este comportamento pódese activar ou desactivar con `SQLSetConnectOption(con, SQL_AUTOCOMMIT, 0)` (0 para desactivalo, 1 para activalo). Se o _desactivamos_, cada transacción se debe _comprometer explícitamente_ mediante `SQLTransact(con, SQL_COMMIT)` ou retroceder con `SQLTransact(con, SQL_ROLLBACK)`.

A norma ODBC define tamén **niveis de conformidade**, que _especifican cantas funcionalidades proporciona a implementación_. O nivel 1 só exixe soporte para a captura de información sobre o catálogo, mentres que o nivel 2 tamen exixe características como a capacidaed de enviar e obter arrays de valores de parámetros.

A norma de SQL define unha **CLI** (interfaz do nivel de chamadas), parecida á interfaz de ODBC.

## SQL incorporado/embebido
É o conxunto de _estruturas SQL que se admiten na linguaxe anfitriona_ (a que incorpora as consultas SQL).

==**Principal diferencia entre SQL incorporado e JDBC/ODBC**==:
Conta cun **preprocesador** que _procesa as sentencias SQL_ da linguaxe anfitriona _antes da súa compilación_ e as _substitúe_ con declaracións e chamadas a procedemientos da linguaxe anfitriona que permitan a execución en tiempo de ejecución dos accesos á BD. Despois, o programa resultado, _compílase_ co compilador da liguaxe anfitriona.

Para **identificar as consultas**, o preprocesador emprega a instrucción `EXEC SQL`:
`EXEC SQL <instruccion de SQL incorporado>`

Para identificar o lugar no que o preprocesador debe insertar as **variables especiais** que se empregan para a **comunicación** entre o programa e a BD,  no programa inclúese a sentencia `SQL INCLUDE SQLCA` 

### Modelo básico de emprego de SQL incorporado
#### 1. Conexión á base de datos:
```postgresql
EXEC SQL connect to servidor
	user usuario using contraseña;
```
#### 2. Declaración de sentencias SQL:
Para poder empregar as _variables da linguaxe anfitriona_ coas sentencias de SQL incorporado, estas deben estar precedidas por _dous puntos_ (:) para distinguilas das variables SQL, e se deben declarar nunha sección:
```postgresql
EXEC SQL BEGIN DECLARE SECTION;
	int cant_creditos;
EXEC SQL END DECLARE SECTION;
```
As sentencias de SQL incorporado **diferéncianse das sentencias normais** en que para escribir unha consulta relacional emprégase a sentencia **`declare cursor`**: o programa emprega os comandos `open` e `fetch` para obter as tuplas resultado. O emprego deste cursor é análogo á _iteración_ do conxunto de resultados en JDBC:
```postgresql
EXEC SQL
	declare c cursor for
	select ID, nombre
	from estudiante
	where tot_créditos > :cantidad_créditos;
```
Aquí, a variable `c` emprégase para _identificar a consulta_. 

#### 3. Avaliación das consultas:
Emprégase **`open`** para **avaliar a consulta**: `EXEC SQL open c;`
	\*Se esta consulta xera un erro, almacénase un diagnóstoco de erro nas variables da área de comunicación   de SQL (SQLCA).

A continuación, execútanse unha serie de sentencias **`fetch`** que obteñen os **valores da consulta**, que se sitúan en _variables da linguaxe anfitriona_: `EXEC SQL fetch c into :variable1, :variable2;`
Dado que cada `fetch` devolve unha tupla, o programa debe incorporar un _bucle while para iterar sobre tódalas tuplas_, aproveitando que cada vez que se executa unha instrucción `fetch` o cursor apunta á seguinte tupla do resultado (cando non quedan máis tuplas, a variable `SQLSTATE` de SQLCA = '0200')

#### 4. Finalización dunha consulta
Para indicarlle ao sistema de BD que **borre a relación temporal** que almacenaba o _resultado da consulta_ debemos empregar **`close`**: `EXEC SQL close c`

### Outras características
As expresións de SQL incorporado para a **modificación** das BD (update, insert e delete) _non devolven ningún resultado_, polo que son simplemente da forma: `EXEC SQL <consulta>`

As relacións das BD tamén se poden **actualizar mediante cursores**:
```postgresql
EXEC SQL
	declare c cursor for select *
	from profesor
	where nombre_dept = ´Música´
	for update;

EXEC SQL
	update profesor
	set sueldo = sueldo + 100
	where current of c;
```

As **transaccións** pódense _comprometer_ con `EXEC SQL COMMIT` ou _retroceder_ con `EXEC SQL ROLLBACK`. 