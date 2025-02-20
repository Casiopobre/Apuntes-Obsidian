# Tema 2. Seguridade
## Autorización
**Para condecer privilexios:** 
`grant <lista privilexios> on <nomeTaboa/nomeVista> to <lista usuarios/ roles>;`
\* Se poñemos `allprivileges` na lista de privilegios, concedemos todos
### Tipos de autorizacións
+ `select`: para **ler** as tuplas dunha relación → executar consultas sobre a relación
+ `update`: para **actualizar** calquera tupla da relación. Pode concederse sobre todos os atributos da relación ou só sobre algúns (`grant update(atrib1, atrib2, ...)`)
+ `insert`: para **insertar** tuplas na relación. Podese limitar só a un subconxunto de atributos (o sistema asignará ao resto de atributos valores predeterminados/null)
+ `delete`: para **borrar** tuplas da relación
\- Para facer referencia a _todos os usuarios_ (pasados e futuros) do sistema: nome de usuario `public`
\- Para **revocar** unha autorización:
`revoke <lista privilexios> on <nomeTaboa/nomeVista> from <usuarios/roles>;`

### Roles
Créase un conxunto de roles na base de datos de tal forma que _aos usuarios se lles concedan roles_, e
a estes se lles concedan _privilexios_.
- Para **crear roles**: `create role nomeRol;`
- Para **conceder roles**: `grant nomeRol to nomeUsuario/nomeRol;`

### Autorizacións sobre esquemas:
Só o **propietario** do esquema pode realizar _modificacións_ sobre o mesmo.
- Para que un usuario poida declarar **claves externas** cando crea relacións: privilexio de **referenciar** (references), que se concede sobre _atributos concretos_: 
		`grant references (atrib1) on nomeTboa to usuario;`

### Transferencia de privilexios:
- Para que un usuario ao que se lle concede un privilexio llo poida **conceder a outros usuarios** engádese a cláusula `with grant option`: 
		`grant privilexio on nomeTaboa to usuario with grant option;`

Esta transferencia de autorizacións pódese representar mediante un **grafo de autorización**, onde _os vertices son os usuarios_. Un usuario ten unha _autorización_ ⇔∃ un _camiño_ dende a raíz ata o usuario
![[GrafoAutorizacions.png | center | 200]]
### Revocación de privilexios:
Por exemplo, se o administrador da BD (ABD) lle quere revocar a autorización ao usuario U 1, entón tamen lla revoca a U4 , pero non a U5, xa que inda ten un camino que o conecte á raíz.

**Revocación en cascada**: se o ABD autoriza ao U2, este autoriza ao U3 e o U3 lle devolve a autorización ao U2, no momento no que o ABD lle revoque a autorización a U 2, asegúrase de revocarlla tamén ao U 3.![[RevocacionCascada.png| center | 150]]
- Para _evitar a revocación en cascada_ ṕodese engaditr a clausula restrict: 
		`revoke privilexio on nomeTaboa from usuario restrict;`


## Seguridade das aplicacións
### Inxección SQL
Consiste en que o atacante consiga que _unha aplicación execute unha consulta SQL creada por el mediante a inserción de caracteres especiais_.
Para **evitar** estes ataques pódense empergar **sentencias preparadas**: JDBC engade automaticamente _caracteres de escape_. Similarmente, poderíase crear unha función propia que engada estes caracteres antes de pasarlle a string á consulta SQL.

%% 
### Secuencia de comandos en sitios cruzados e falsificación de peticións (non entra Sebastian fillo de puta)
Nos ataques de **secuencias de comandos en sitios cruzados** (_XXS: cross-site scripting_), un atacante introduce _código malicioso_ (normalmente en JavaScript) que despois se executa nos _navegadores doutros usuarios_ e lle permite _roubar cookies de sesión_, redirixir aos usuarios a sitios falsos... 
Para **evitar** estes ataques pódense empregar técnicas como _escaping_ ou implementar _Content Security Policy (CSP)_ para limitar a execución de scripts non autorizados.

Os ataques de **Cross-Site Request Forgery** (_XSRF: cross-site request forgery_) consisten en enganar a un usuario autenticado para que realice accións non desexadas nun sitio web (clicando nunha ligazón maliciosa...). Así o navegador do usuario realiza unha solicitude do atacante ao sitio web (por exemplo, un banco) con credenciais válidas (a cookie de sesión está activa).
Estes ataques pódense **evitar** empregando _tokens CSR_ ou evitando accións críticas mediante _métodos GET_ (xa que poden ser explotados facilmente). 

En xeral, para evitar que un sitio web se empregue **para lanzar ataques** XSS ou XSRF, pódese _impedir que os usuarios escriban calquera tipo de etiqueta HTML_. Por outro lado, para **protexer o sitio web** de estes ataques pódese comprobar que o _referente_ dun acceso é válido (mediante HTTP), e empregar unha combinación de cookies e IP patra identificar ao usuario.
%%
### Fuga de contrasinais
Ocorre cando as contrasinais quedan **expostas a usuarios non autorizados** debido a un almacenamento pouco seguro. Pode ocorrer polas seguintes **causas**:
+ Almacenamento en _texto plano_: cando as contrasinais se gardan directamente no código fonte (nun script, por exemplo).
+ Emprego de _contrasinais embebidos_: cando as contrasinais se inclúen directamente no código para conectarse á BD.
+ _Cifrado inadecuado_: cando clave de cifrado das contrasinais está mal protexida.
Para **evitar** a fuga de contrasinais, ademais de evitar os anteriores casos, tamén se pode _restrinxir o acceso á BD por IP_ ou _minimizar os privilexios_ das aplicacións.

### Autenticación das aplicacións
Consiste en **verificar a identidade** dunha persoa/software que se conecta a unha aplicación, normalmente mediante o emprego dunha _contrasinal_ que se debe introducir cando se quere acceder á aplicación. Para aplicacións críticas empréganse esquemas máis robustos como a autenticación por cifrado.

**Métodos de auteticación avanzada**:
+ **Verificación en dous pasos (2FA)**: existen dúas pezas de _información distintas_ que non deben compartir unha _vulnerabilidade común_, e que se empregan para verificar o usuario. Normalmente se empregan contrasinais como primer paso e como segundo paso: _claves físicas_, dispositivos que xeran contrasinais de uso único _sincronizados cun servidor_ e envío de códigos únicos vía _SMS_.
+ **Protección contra ataques**: o protocolo _HTTPS_ evita ataques do _home no medio_ ao cifrar os datos.
+ **Sistemas de autenticación centralizada**:
	+ _LDAP (Lightweight Data Access Protocol)_: un servidor central almacena credenciais e permite que varias aplicacións verifiquen a identidade dun usuario.
	- *Single Sign-On (SSO)*: permite que un usuario acceda a varias aplicacións cun único inicio de sesión.
	- *SAML (Security Assertion Markup Language) e OpenID*: protocolos que permiten a autenticación entre diferentes dominios. OpenID é utilizado por plataformas como Google para facilitar o acceso a servizos con credenciais unificadas.

### Autorización a nivel de aplicación
Controla o acceso dos usuarios aos datos dentro dunha aplicación. Aínda que a norma SQL permite unha autorización baseada en roles, presenta varias **limitacións**:
- **Falta de información sobre o usuario final**: moitas aplicacións web usan _un só identificador de usuario_ para acceder á BD, polo que _non se poden asignar permisos específicos_ a cada usuario, xa que estes non teñen identificadores individuais na propia BD.
- **Falta de autorización de grano fino**: SQL non permite establecer permisos a nivel de _filas individuais_. Isto impide, por exemplo, que un estudante só poida ver as súas propias notas sen ver as dos demais.

**Alternativas para a autorización na aplicación**:
+ Creación de _vistas personalizadas_ que filtren os datos segundo o usuario, aínda que complica o desenvolvemento da aplicación.
+ Implementación da autorización directamente no _código da aplicación_, aínda que pode xerar problemas.

**Sistemas avanzados de autorización**: 
A _Virtual Private Database (VPD)_ de Oracle, que permite engadir restricións automáticas ás consultas SQL baseándose no usuario, o que proporciona _autorización a nivel de fila_, pero pode alterar o comportamento das consultas.

### Trazas de auditoría
Unha traza de auditoría é un **rexistro detallado** de tódalas _modificacións_ nos datos dunha aplicación (acción realizada, usuario, fecha e hora, dirección IP...), que ten o propósito de _rastrear cambios_, detectar _accesos non autorizados_ e _corrixir erros e fraudes_.

Para **implementalas** pódense empregar _triggers_ (disparadores) na BD para rexistrar cada actualización; ou, en algúns sistemas, _mecanismos integrados de auditoría_.

Teñen algunhas **limitacións**, xa que non sempre identifican correctamente ao _usuario final_ e só rexistran _cambios a nivel baixo_ (de filas e tuplas), e non en termos da lóxica do negocio.

É posible que un atacante sexa capaz de **modificar a traza de auditoría**, aínda que se pode **evitar** copiando a traza nunha _máquina separada_ ou rexistrando os cambios en _tempo real_.

### Privacidade
O aumento dos **datos persoais accesibles en liña** xerou preocupación pola privacidade. Existen leis que regulan quen pode acceder a información sensible, como os datos médicos, e o seu incumprimento pode ter consecuencias legais. Para compartir datos útiles para a investigación **sen comprometer a privacidade**, pódense _anonimizar_ certos datos, como ofrecer só o ano de nacemento no canto da data completa. No ámbito web, as empresas recollen información persoal para transaccións, pero _os usuarios poden solicitar que certos datos sexan eliminados ou non compartidos con terceiros_. Os sitios web deben respectar as preferencias de privacidade dos seus clientes.

