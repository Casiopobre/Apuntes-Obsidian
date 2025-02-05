# Tema 5. Shell
O **shell** _traduce comandos en ordes_ ao SO.
O **bash** é un shell que ten todas as características de _sh_ (que era o shell que implementaba as primeiras versións de linux) e ademáis inclúe algunhas características avanzadas de _C_.ç

### Funcionamento do shell
Hai **comandos internos** (`cd`, `alias`, `echo`, ...), que se executan no _mesmo proceso_, e **comandos externos** (`cp`, `cat`, `mkdir`, ...), que inician un _novo proceso_ e cambian a súa imaxe pola do arquivo executable.
Para ver o tipo dun comando: `type [comando]`

### Liña de comandos
Estrutura dun comando: `COMANDO [OPCIONS] [PARAMETROS]`
Os comandos execútanse en **primer plano** (foreground), onde o shell espera a que termine o comando antes de  aceptar outro, ou en **segundo plano** (background).
Para **pausar** un coamndo: `CTRL-Z`; Para **terminar** un comando: `CTRL-C`
Para **ver os comandos en background**: `jobs`
Para **traer un comando a primer plano**: `fg %job_id`
Para **levar un comando a segundo plano**: `bg %job_id`
Para especificar varios ficheiros como parámetros pódense empregar **comodíns**:

###### Parámetros

| **Caracter**   | **Correspondencia**                             |
| -------------- | ----------------------------------------------- |
| *              | 0 ou + caracteres                               |
| ?              | 1 caracter                                      |
| [ ]            | un dos caracteres entre corchetes               |
| [\! ] ou [\^ ] | calquera caracter que non estea entre corchetes |
Hai algúns **caracteres especiais** que son recoñecidos polo shell como tal (&, \*,  ?,  $, ...). Para _eliminar ese significado especial_ temos varias opcións: 
+ Comilla simple ( ' ): ignora tódolos caracteres especiais
+ Comilla dobre ( " ): ignora tódolos caracteres especiais excepto $, \\, '
+ Barra invertida ( \\ ): ignora o caracter especial que sigue a \

Bash permite outras **expansións para parámetros**:
+ Chaves `{}` para xerar strings (Ex: `echo a{1,2,3}b` --> `a1b a2b a3b`)
+ Tilde `~` para o directorio de usuario (Ex: `cat ~/.bash_history`)
+ Aritmética `$[expr]` para avaliar expersións (Ex: `echo $[(4+11)/3]`)

###### Variables
En shell pódense **crear variables** dende a liña de comandos:
`$> variable='algo'` ou `$> read variable (ENTER)` = scanf

Para **acceder ao contido das variables**: `$variable` (para mostralo como string: `echo`)

Hai dous **tipos de variables**: _variables locais_, que son visibles só dende o shell actual e se poñen en minúsculas normalmente; e _variables de entorno_, que van sempre en maiúsculas e hai que empregar `export VARIABLE` para que sexa visible polos shells fillos.
\*Para **ver o listado de variables**: `$> printenv`

| _Nome_     | _Propósito_                |
| ---------- | -------------------------- |
| `HOME`     | directorio base do usuario |
| `SHELL`    | executable da shell        |
| `USERNAME` | nome de usuario            |
| `PWD`      | directorio actual          |
| `PATH`     | path dos executables       |



