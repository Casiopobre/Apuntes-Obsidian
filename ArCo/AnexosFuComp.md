# 1. Execución de instruccións
Execución de instruccións no MIPS:
- **Búsqueda/Captura da Instrución (IF - Instruction Fetch)**
    - A CPU le a instrución da memoria (normalmente a caché ou RAM).
    - O _Program Counter_ (PC) dille á CPU onde está a seguinte instrución.
    - O PC increméntase para apuntar á seguinte instrución (PC+4).
- **Decodificación (ID - Instruction Decode)**
    - A CPU interpreta a instrución e decide que operación hai que realizar.
    - Se a instrución usa operandos, estes poden vir de rexistros ou memoria.
    - O decodificador de instrucións separa os distintos campos da instrución (opcode, rexistros, valores inmediatos, etc.).
- **Acceso a Memoria (MEM - Memory Access)**
    - Só ocorre se a instrución necesita interactuar coa memoria (leese ou gárdase un valor en memoria)
- **Execución (EX - Execution)**
    - A ALU (_Arithmetic Logic Unit_) fai os cálculos (suma, comparación, desprazamento...)
- **Post-escritura (WB - Write Back)**
    - Se a instrución produce un resultado, este gárdase nun rexistro.