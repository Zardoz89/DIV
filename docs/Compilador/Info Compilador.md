# Compilador DIV2

## Localización del código

El código del compilador DIV2 que parsea y genera bytecode está en `src/div/divc.cpp` (~7,700 líneas).

| Función               | Línea | Propósito                                                 |
|-----------------------|-------|-----------------------------------------------------------|
| `compilar()`          | 954   | **Entry point principal de compilación**                  |
| `lexico()`            | 1664  | **Analizador léxico** - tokeniza el código fuente         |
| `sintactico()`        | 3130  | **Parser sintáctico** - parsea estructura del programa    |
| `expresion()`         | 5287  | **Parser de expresiones**                                 |
| `sentencia()`         | 4875  | **Parser de sentencia** - if, loop, while, etc.           |
| `generar_expresion()` | 5325  | **Genera bytecode** a partir de expresiones parseadas     |
| `g1()`/`g2()`         | 7382  | **Emite opcodes** al array `mem[]`                        |
| `save_exec_bin()`     | 7099  | **Guarda** el ejecutable final `system/exec.exe`          |


---

## Pipeline de compilación

```
1. lexico()      → Analiza léxicamente (tokens)
2. sintactico()   → Parsing sintáctico (estructura)
3. sentencia()/expresion() → Parsing de declaraciones y expresiones
4. generar_expresion() → Genera bytecode (127+ opcodes)
5. save_exec_bin() → Comprime y guarda ejecutable
```

---

## Función compilar()

**Ubicación**: `src/div/divc.cpp:954`

### Descripción

Función principal que orquesta toda la compilación. Es el entry point del compilador.

### Flujo de ejecución

```
compilar()
├── Inicialización de variables globales
│   ├── mem = NULL, loc = NULL, frm = NULL
│   ├── memset(obj, 0)      → limpiar tabla de objetos
│   ├── memset(lex_simb, 0)  → limpiar símbolos
│   ├── lex_case[256]       → inicializar estados del lexer
│
├── analiza_ltlex()
│   └── Carga tokens desde system/ltlex.def
│
├── precarga_obj()
│   └── Carga objetos predefinidos desde system/ltobj.def
│
├── psintactico()
│   └── Pre-scan: calcula longitud de textos
│
├── sintactico()
│   └── Parsing principal del programa
│
├── save_exec_bin()
│   └── Genera ejecutable comprimido system/exec.bin
```

### Inicialización de memoria

| Variable | Valor inicial | Propósito |
|----------|---------------|-----------|
| `imem_max` | 16384*8 | Tamaño máximo del buffer de código |
| `iloc_max` | default_buffer/2 | Tamaño de variables locales |
| `ifrm_max` | default_buffer/2 | Tamaño de frame |

### Salida

El compilador genera:
- `system/exec.exe` - Executable comprimigo (zlib)
- `system/exec.lin` - Info de líneas
- `system/exec.pgm` - Programa tokenizado

---

## Función lexico()

**Ubicación**: `src/div/divc.cpp:1664`

### Descripción

Analizador léxico basado en tabla de estados. Convierte el código fuente en tokens.

### Algoritmo

1. Recibe puntero `_source` al código fuente
2. Clasifica el carácter actual via `lex_case[*_source]`
3. Ejecuta lógica según el tipo de carácter
4. Actualiza variables globales `pieza` y `pieza_num`

### Estados del lexer (`lex_case[256]`)

| Estado | Código | Descripción |
|--------|--------|------------|
| `l_err` | 0 | Carácter no esperado |
| `l_cr` | 1 | Fin de línea (CR) |
| `l_id` | 2 | Identificador o palabra reservada |
| `l_spc` | 3 | Espacio o tabulación |
| `l_lit` | 4 | Literal entre comillas |
| `l_num` | 5 | Constante numérica |

### Tipos de tokens retornados (`pieza`)

#### Palabras reservadas principales

| Token | Valor | Descripción |
|-------|-------|------------|
| `p_program` | 0x01 | keyword PROGRAM |
| `p_const` | 0x02 | keyword CONST |
| `p_global` | 0x03 | keyword GLOBAL |
| `p_local` | 0x04 | keyword LOCAL |
| `p_begin` | 0x05 | keyword BEGIN |
| `p_end` | 0x06 | keyword END |
| `p_process` | 0x07 | keyword PROCESS |
| `p_private` | 0x08 | keyword PRIVATE |
| `p_struct` | 0x09 | keyword STRUCT |
| `p_function` | 0x11 | keyword FUNCTION |

#### Operadores

| Token | Valor | Descripción |
|-------|-------|------------|
| `p_if` | 0x20 | IF |
| `p_loop` | 0x21 | LOOP |
| `p_while` | 0x22 | WHILE |
| `p_for` | 0x24 | FOR |
| `p_switch` | 0x25 | SWITCH |
| `p_case` | 0x26 | CASE |
| `p_return` | 0x18 | RETURN |
| `p_frame` | 0x28 | FRAME |

#### Tokens especiales

| Token | Valor | Descripción |
|-------|-------|------------|
| `p_lit` | 0xfc | Puntero a literal en `pieza_num` |
| `p_id` | 0xfd | Identificador (buscar en `obj[]`) |
| `p_num` | 0xfe | Número entero en `pieza_num` |
| `p_ultima` | 0x00 | Fin de archivo (EOF) |

### Lógica por tipo de token

#### `l_id` (identificador)
1. Lee caracteres `[a-zA-Z0-9_]` y construye nombre
2. Calcula hash: `h = (h<<1) ^ char`
3. Busca en tabla `vhash[256]`
4. Si encuentra: retorna token existente
5. Si no encuentra: crea nuevo objeto en `obj[]`

#### `l_lit` (literal/archivo)
1. Reconoce formato `"..."` o `'...'`
2. Detecta archivos multimedia por extensión
3. Empaqueta archivos si es necesario (.PCX, .FPG, .MOD, etc.)

#### `l_num` (número)
1. Soporta decimal y hexadecimal (`0x...`)
2. Convierte a entero y guarda en `pieza_num`

---

## Función sintactico()

**Ubicación**: `src/div/divc.cpp:3130`

### Descripción

Parser sintáctico principal. Procesa la estructura completa del programa DIV2.

### Flujo de parsing

```
sintactico()
├── Opciones de compilador (compiler_options)
│   └── _max_process, _ignore_errors, _no_check, etc.
│
├── Cabecera (PROGRAM nombre)
│   └── Valida y registra el nombre del programa
│
├── Import de librerías (IMPORT "dll")
│   └── Carga funciones externas
│
├── Constantes (CONST)
│   └── Define constantes
│
├── Variables globales (GLOBAL)
│   ├── int, byte, word
│   ├── string
│   ├── struct
│   └── pointers
│
├── Procesos y funciones (PROCESS / FUNCTION)
│   ├── Sección PRIVATE
│   ├── Sección BEGIN
│   └── Cuerpo (sentencias)
│
└── Fin de archivo (EOF)
```

### Secciones del lenguaje

#### Compiler Options (líneas 3148-3232)

```div
compiler_options
    _max_process(100),
    _ignore_errors,
    _extended_conditions,
    _simple_conditions,
    _case_sensitive,
    _free_sintax,
    _no_check,
    _no_strfix,
    _no_optimization,
    _no_range_check,
    _no_id_check,
    _no_null_check;
```

#### Variables globales

| Tipo | Sintaxis | Descripción |
|------|----------|-------------|
| `int` | `int var;` | Entero |
| `byte` | `byte var[n];` | Array de bytes |
| `word` | `word var[n];` | Array de words |
| `string` | `string var[len];` | Cadena |
| `struct` | `struct name ... end` | Estructura |
| `pointer` | `pointer var;` | Puntero a INT |

---

## Función sentencia()

**Ubicación**: `src/div/divc.cpp:4875`

### Descripción

Parser de sentencias de control de flujo. Genera el bytecode correspondiente.

### Sentencias soportadas

| Sentencia | Token | Bytecode generado |
|------------|-------|-------------------|
| `IF` | `p_if` | `ljpf`, `ljmp` |
| `LOOP` | `p_loop` | `ljmp` |
| `WHILE` | `p_while` | `ljpf`, `ljmp` |
| `REPEAT...UNTIL` | `p_repeat` | `ljpf` |
| `FOR...TO...STEP...END` | `p_from` | comparaciones, `ljpf` |
| `SWITCH...CASE...END` | `p_switch` | `lcse`, `lcsr` |
| `RETURN` | `p_return` | `lret` / `lrtf` |
| `FRAME` | `p_frame` | `lfrm` |
| `BREAK` | `p_break` | `ljmp` |
| `CONTINUE` | `p_continue` | `ljmp` |
| `CLONE` | `p_clone` | `lclo` |
| `DEBUG` | `p_debug` | `ldbg` |

### Generación de código para IF

```
IF (condición) THEN
    sentencia_true
ELSE
    sentencia_falsa
END
```

Genera:
```
<condición>
LJPF L1     ; saltar si falso
<sentencia_true>
JMP L2       ; saltar al final
L1:
<sentencia_falsa>
L2:
```

### Generación de código para WHILE

```
WHILE (condición)
    sentencia
END
```

Genera:
```
L1:
<condición>
LJPF L2     ; saltar si falso
<sentencia>
JMP L1      ; volver al inicio
L2:
```

### Tablas de break/continue

| Variable | Tamaño | Propósito |
|----------|--------|-----------|
| `tbreak[]` | 512 | Índices de saltos de break |
| `tcont[]` | 256 | Índices de saltos de continue |

---

## Función expresion()

**Ubicación**: `src/div/divc.cpp:5287`

### Descripción

Parser de expresiones. Analiza y genera bytecode para expresiones aritméticas y lógicas.

### Funciones relacionadas

| Función | Propósito |
|--------|----------|
| `expresion()` | Entry point principal |
| `expresion_cpa()` | Expresión para parámetros |
| `exp00()` | Parser recursivo descendente |
| `generar_expresion()` | Genera bytecode |
| `constante()` | Evalúa expresiones constantes |

### Tabla de expresiones (`tabexp[]`)

Almacena elementos intermedios durante el parsing. Máximo 512 elementos.

| Tipo | Código | Descripción |
|------|--------|-------------|
| `econs` | 0 | Constante numérica |
| `estring` | - | String |
| `erango` | 2 | Comprobación de rango |
| `eoper` | 1 | Operador |
| `ecall` | - | Llamada a proceso |
| `efunc` | - | Función interna |
| `efext` | - | Función externa |
| `echeck` | - | Comprobación de ID |
| `enull` | - | Comprobación de NULL |
| `ewhoami` | - | whoami |

### Algoritmo de parsing

```
expresion()
├── Inicializa _exp = tabexp
├── save_error(0)
├── exp00(0)           → Parsing recursivo
├── generar_expresion() → Genera bytecode
└── Retorna
```

### `generar_expresion()`

Itera sobre `tabexp[]` y genera el bytecode correspondiente:

| Tipo de elemento | Opcode generado |
|-----------------|------------------|
| Constante | `lcar` valor |
| String | `lcar` offset |
| Rango | `lrng` valor |
| whoami | `lcid` |
| Llamada proceso | `lcal` offset |
| Función interna | `lfun` código |
| Función externa | `lext` código |
| Operador aritmético | `ladd`, `lsub`, etc. |
| Operador comparación | `ligu`, `lmay`, etc. |
| Puntero | `lptr` |
| Asignación | `lasi` |

### Funciones de parsing de expresiones

| Función | Propósito |
|---------|----------|
| `exp00()` | Parser principal |
| `exp0()` | Expresión nivel 0 (asignación) |
| `exp1()` | Expresión nivel 1 (OR) |
| `exp2()` | Expresión nivel 2 (XOR) |
| `exp3()` | Expresión nivel 3 (AND) |
| `exp4()` | Expresión nivel 4 (comparación) |
| `exp5()` | Expresión nivel 5 (shift) |
| `exp6()` | Expresión nivel 6 (add/sub) |
| `exp7()` | Expresión nivel 7 (mul/div) |
| `exp8()` | Expresión nivel 8 (unario) |
| `factor()` | Factor: literal, ID, función |

### Precedencia de operadores

| Nivel | Operadores | Descripción |
|-------|------------|-------------|
| 0 | `=` | Asignación |
| 1 | `OR` | Or lógico |
| 2 | `XOR` | Xor lógico |
| 3 | `AND` | And lógico |
| 4 | `==`, `!=`, `>`, `<`, etc. | Comparación |
| 5 | ` shr`, `<<` | Shift |
| 6 | `+`, `-` | Adición |
| 7 | `*`, `/`, `MOD` | Multiplicación |
| 8 | `-`, `NOT`, `*`, `&` | Unario |

---

## Función constante()

**Ubicación**: `src/div/divc.cpp:5450`

### Descripción

Evalúa expresiones constantes en tiempo de compilación. No procesa valores locales ni llamadas a procesos.

### Uso

Se utiliza para:
- Inicialización de constantes
- Dimensiones de arrays
- Valores en declaraciones GLOBAL

### Algoritmo

```
constante()
├── exp00(0)           → Parser de expresión
├── Itera sobre tabexp[]
│   ├── econs/estring → apila valor
│   └── eoper         → opera con valores de pila
└── Retorna resultado
```

---

## Opciones de compilador

**Ubicación**: `src/div/divc.cpp:3148-3220`

### Lista completa

| Opción | Valor | Descripción |
|--------|-------|-------------|
| `_max_process` | 0 | Máximo número de procesos |
| `_extended_conditions` | 1 | Condiciones extendidas |
| `_simple_conditions` | 2 | Condiciones simples |
| `_case_sensitive` | 3 | Distinción mayúsculas |
| `_ignore_errors` | 4 | Ignorar errores en ejecución |
| `_free_sintax` | 5 | Sintaxis libre |
| `_no_check` | 6 | Desactivar comprobaciones |
| `_no_strfix` | 7 | No añadir terminador |
| `_no_optimization` | 8 | Desactivar optimización |
| `_no_range_check` | 9 | No comprobar rangos |
| `_no_id_check` | 10 | No comprobar IDs |
| `_no_null_check` | 11 | No comprobar nulls |

### Variables flags

| Variable | Default | Propósito |
|----------|---------|-----------|
| `max_process` | 0 | Máximo de procesos |
| `ignore_errors` | 0 | Ignorar errores |
| `free_sintax` | 0 | Sintaxis libre |
| `extended_conditions` | 0 | Condiciones extendidas |
| `simple_conditions` | 0 | Condiciones simples |
| `comprueba_rango` | 1 | Comprobar rangos |
| `comprueba_id` | 1 | Comprobar identificadores |
| `comprueba_null` | 1 | Comprobar nulls |
| `hacer_strfix` | 1 | Añadir terminador |
| `optimizar` | 1 | Optimización |

---

## Variables globales del compilador

### Variables de parsing

| Variable | Tipo | Descripción |
|----------|------|------------|
| `pieza` | int | Token actual parseado |
| `pieza_num` | int | Valor numérico (constante o offset) |
| `o` | objeto* | Objeto actual (cuando pieza=p_id) |
| `bloque_actual` | objeto* | Bloque siendo analizado |
| `bloque_lexico` | objeto* | Bloque para análisis léxico |
| `member` | objeto* | Miembro de struct |
| `linea` | int | Línea actual del código fuente |
| `coment` | int | Nivel de anidación de comentarios |
| `convert` | int | Flag: generar tokens en disco |

### Tablas de símbolos

| Variable | Tipo | Descripción |
|----------|------|------------|
| `obj[max_obj]` | objeto[] | Tabla de objetos (4096 máx) |
| `iobj` | objeto* | Puntero siguiente objeto libre |
| `num_obj` | int | Número de objetos definidos |
| `vhash[256]` | byte* | Tabla hash de identificadores |
| `lex_case[256]` | lex_ele* | Estados del lexer |
| `tabexp[max_exp]` | exp_ele[] | Tabla de expresión (512 máx) |
| `_exp` | exp_ele* | Puntero actual en tabexp |

### Memoria de compilación

| Variable | Tipo | Descripción |
|----------|------|------------|
| `mem[]` | int* | Memoria de código final |
| `imem` | int | Índice actual en mem[] |
| `imem_max` | int | Tamaño máximo de mem[] |
| `loc[]` | int* | Variables locales inicializadas |
| `iloc` | int | Inicio de variables locales |
| `iloc_len` | int | Longitud total de locales |
| `frm[]` | int* | Datos de frame |
| `itxt` | int | Índice de textos |

### Control de flujo

| Variable | Tipo | Descripción |
|----------|------|------------|
| `tbreak[]` | int[512] | Índices de saltos break |
| `itbreak` | int | Índice actual en tbreak |
| `tcont[]` | int[256] | Índices de saltos continue |
| `itcont` | int | Índice actual en tcont |

### Pila de compilación

| Variable | Tipo | Descripción |
|----------|------|------------|
| `pila[long_pila+max_exp+64]` | int[] | Pila de evaluación |

---

## Estructura objeto

**Ubicación**: `src/div/divc.cpp:711`

```c
struct objeto {
  byte tipo;           // Tipo de objeto (tcons, tvglo, tproc, etc.)
  byte usado;          // Flag: usado antes de definirse
  byte * name;        // Nombre del objeto
  byte * ierror;      // Posición en código fuente
  int linea;          // Línea de definición
  int param;         // Flag: declarado en parámetros
  objeto * anterior; // Objeto de igual nombre anterior
  objeto * bloque;    // Bloque padre (0=global, N=proceso)
  objeto * member;    // Struct al que pertenece (0=n/a)
  union { ... };     // Datos según tipo
};
```

### Tipos de objetos

| Tipo | Constante | Descripción |
|------|----------|------------|
| `tnone` | 0 | No definido |
| `tcons` | 1 | Constante |
| `tvglo` | 2 | Variable global |
| `ttglo` | 3 | Tabla global |
| `tcglo` | 4 | Cadena global |
| `tvloc` | 5 | Variable local |
| `tproc` | 8 | Proceso |
| `tfunc` | 9 | Función interna |
| `tbglo` | 13 | Byte global |
| `twglo` | 14 | Word global |
| `tpigl` | 17 | Puntero a int global |
| `tpcgl` | 23 | Puntero a string |
| `tpsgl` | 25 | Puntero a struct |

---

## Estructura exp_ele

**Ubicación**: `src/div/divc.cpp:811`

```c
struct exp_ele {
  byte tipo; // econs, eoper, erango, ewhoami, ecall
  union {
    int valor;      // valor constante
    int token;     // token de operador
    objeto * objeto; // objeto referenciado
  };
};
```

---

## Otras funciones del compilador

| Función | Línea | Propósito |
|---------|-------|----------|
| `compilar()` | 954 | Entry point principal |
| `lexico()` | 1664 | Analizador léxico |
| `sintactico()` | 3130 | Parser sintáctico principal |
| `sentencia()` | 4875 | Parser de sentencias |
| `expresion()` | 5287 | Parser de expresiones |
| `generar_expresion()` | 5325 | Genera bytecode |
| `constante()` | 5450 | Expresión constante |
| `g1()`/`g2()` | 7382 | Emite opcodes |
| `analiza_ltlex()` | 1275 | Carga tokens desde .def |
| `precarga_obj()` | 1349 | Carga objetos predefinidos |
| `psintactico()` | 7699 | Pre-scan sintáctico |
| `save_exec_bin()` | 7099 | Guarda ejecutable |
| `c_error()` | 1179 | Reporta errores |

---

## Archivos relacionados

| Archivo | Descripción |
|---------|-------------|
| `src/div/divc.cpp` | Compilador principal |
| `src/div/divfrm.cpp` | Versión formateadora |
| `system/ltlex.def` | Definiciones de tokens |
| `system/ltobj.def` | Objetos predefinidos |
