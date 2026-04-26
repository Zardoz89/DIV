## Fichero DBC (Div Byte Code)

Basicamente el EXE que se genera, sin el STUB, y sin comprimir con ZLib el bytece + datos iniciales:

### Estructura

#### Cabecera

La cabecera se obtiene de `int mem[0..8]` en el compilador. Se define en `divc.cpp:1048-1056`:

| Índice    | Valor             | Descripción                                           |
|-----------|-------------------|-------------------------------------------------------|
| `mem[0]`  | `program_type`    | **0** = programa normal, **1** = install (setup.ins)  |
| `mem[1]`  | `imem`            | Tamaño del bytecode (instrucciones)                   |
| `mem[2]`  | `imem`            | Tamaño del bytecode (duplicado?)                       |
| `mem[3]`  | `max_process`     | Máximo número de procesos (antes long_header)         |
| `mem[4]`  | 0                 | (sin uso)                                             |
| `mem[5]`  | `iloc_len-iloc`   | Longitud variables locales                            |
| `mem[6]`  | `iloc`            | Offset variables locales                              |
| `mem[7]`  | 0                 | (sin uso)                                             |
| `mem[8]`  | `imem+iloc`       | Total elementos en mem[]                              |

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

