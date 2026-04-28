## Fichero DBC (Div Byte Code)

Basicamente el EXE que se genera, sin el STUB, y sin comprimir con ZLib el bytece + datos iniciales:

### Estructura

* Cabecera
* bytecode]
* tabla de procesos
* tabla de variables
* strings / constantes

#### Cabecera

La cabecera se obtiene de `int mem[0..9]` en el compilador. A los offset, hay
que sumarle 0x100

| Índice    | Valor             | Descripción                                           |
|-----------|-------------------|-------------------------------------------------------|
| `mem[0]`  | Bytefield         | **0** = programa normal, **1** = install (setup.ins)  |
| `mem[1]`  | `imen`            | Tamaño bloque ?                                       |
| `mem[2]`  | `imen`            | Tamaño bloque ?                                       |
| `mem[3]`  | `max_process`     | Nº maximo de procesos                                 |
| `mem[4]`  | 0                 | Sin usar                                              |
| `mem[5]`  | `iloc_len-iloc`   | Longitud variables locales                            |
| `mem[6]`  | `iloc`            | Offset variables locales ?                            |
| `mem[7]`  | 0                 | Sin usar                                              |
| `mem[8]`  | `imen+iloc`       | Offset ?                                              |
| `mem[9]`  |                   | Offset bytecode.                                      |

Además, `mem[0]` usa bits de flags (en `div32run/i.cpp:1351-1358`):

| Bit   | Flag              | Descripción                               |
|-------|-------------------|-------------------------------------------|
| 128   | `trace_program`   | Depuración activada                       |
| 512   | `ignore_errors`   | Ignorar errores en tiempo de ejecución    |
| 1024  | `demo`            | Modo demo                                 |

#### Variables locales

Empieza en el *offset* apuntado por mem[6] y tiene el tamaño indicado en mem[5].

#### ???

#### Constantes

#### Bytecode



## Apuntes

00 00 00 00 mem[0]
40 06 00 00 mem[1] 0x0640
5D 06 00 00 mem[2] 0x065D
00 00 00 00 mem[3]
00 00 00 00 mem[4]
00 00 00 00 mem[5]
2D 00 00 00 mem[6] 0x002D
00 00 00 00 mem[7] 
8A 06 00 00 mem[8] 0x068A
04 1A 00 00 mem[9] 0x1A04 -> offset bytecode
