## Opcodes del Bytecode DIV2

El compilador genera **127+ opcodes** para una máquina virtual.

### Opcodes Base (0-59)

| Opcode | Nombre | Descripción |
|--------|--------|-------------|
| 0      | `lnop` | No operación |
| 1      | `lcar` | Carga una constante en pila |
| 2      | `lasi` | Saca valor, offset y mete el valor en `[offset]` (asignación) |
| 3      | `lori` | Or lógico |
| 4      | `lxor` | Xor, or exclusivo |
| 5      | `land` | And lógico |
| 6      | `ligu` | Igual (comparación) |
| 7      | `ldis` | Distinto |
| 8      | `lmay` | Mayor (con signo) |
| 9      | `lmen` | Menor |
| 10     | `lmei` | Menor o igual |
| 11     | `lmai` | Mayor o igual |
| 12     | `ladd` | Suma |
| 13     | `lsub` | Resta |
| 14     | `lmul` | Multiplicación |
| 15     | `ldiv` | División de enteros |
| 16     | `lmod` | Módulo/resto |
| 17     | `lneg` | Negación (cambio de signo) |
| 18     | `lptr` | Pointer - saca offset y mete `[offset]` |
| 19     | `lnot` | Negación binaria (bit a bit) |
| 20     | `laid` | Suma id a la constante de la pila |
| 21     | `lcid` | Carga id en la pila |
| 22     | `lrng` | Comparación de rango |
| 23     | `ljmp` | Salto incondicional |
| 24     | `ljpf` | Salto si es falso |
| 25     | `lfun` | Llamada a proceso interno (`signal()`, etc.) |
| 26     | `lcal` | Crea un nuevo proceso |
| 27     | `lret` | Auto-eliminación del proceso |
| 28     | `lasp` | Desecha un valor apilado |
| 29     | `lfrm` | Detiene la ejecución por este frame |
| 30     | `lcbp` | Inicializa puntero a parámetros locales |
| 31     | `lcpa` | Saca offset, lee parámetro y bp++ |
| 32     | `ltyp` | Define tipo de proceso (colisiones) |
| 33     | `lpri` | Salta y carga variables privadas |
| 34     | `lcse` | Si switch <> expresión, salta al offfset |
| 35     | `lcsr` | Si switch no está en el rango, salta al offset |
| 36     | `lshr` | Shift right (binario) |
| 37     | `lshl` | Shift left (binario) |
| 38     | `lipt` | Incremento y pointer |
| 39     | `lpti` | Pointer e incremento |
| 40     | `ldpt` | Decremento y pointer |
| 41     | `lptd` | Pointer y decremento |
| 42     | `lada` | Add-asignación |
| 43     | `lsua` | Sub-asignación |
| 44     | `lmua` | Mul-asignación |
| 45     | `ldia` | Div-asignación |
| 46     | `lmoa` | Mod-asignación |
| 47     | `lana` | And-asignación |
| 48     | `lora` | Or-asignación |
| 49     | `lxoa` | Xor-asignación |
| 50     | `lsra` | Shr-asignación |
| 51     | `lsla` | Shl-asignación |
| 52     | `lpar` | Define número de parámetros privados |
| 53     | `lrtf` | Auto-eliminación, devuelve un valor |
| 54     | `lclo` | Crea un clon del proceso actual |
| 55     | `lfrf` | Pseudo-Frame (frame a un porcentaje) |
| 56     | `limp` | Importa una DLL externa |
| 57     | `lext` | Llama a una función externa |
| 58     | `lchk` | Comprueba validez de un identificador |
| 59     | `ldbg` | Invoca al debugger |

### Opcodes de Optimización DIV 2.0 (60-77)

| Opcode | Nombre | Descripción |
|--------|--------|-------------|
| 60     | `lcar2` | Carga constante 2 bytes en pila |
| 61     | `lcar3` | Carga constante 3 bytes en pila |
| 62     | `lcar4` | Carga constante 4 bytes en pila |
| 63     | `lasiasp` | Assign + discard |
| 64     | `lcaraid` | Car + add id |
| 65     | `lcarptr` | Car + pointer |
| 66     | `laidptr` | Add id + pointer |
| 67     | `lcaraidptr` | Car + add id + pointer |
| 68     | `lcaraidcpa` | Car + add id + copy param |
| 69     | `laddptr` | Add + pointer |
| 70     | `lfunasp` | Función + discard |
| 71     | `lcaradd` | Carga + suma |
| 72     | `lcaraddptr` | Carga + add + pointer |
| 73     | `lcarmul` | Carga + multiplicación |
| 74     | `lcarmuladd` | Carga + mul + add |
| 75     | `lcarasiasp` | Car + assign + discard |
| 76     | `lcarsub` | Carga + resta |
| 77     | `lcardiv` | Carga + división |

### Opcodes de Cadenas (78-108)

| Opcode | Nombre | Descripción |
|--------|--------|-------------|
| 78     | `lptrchr` | Pointer byte indexed |
| 79     | `lasichr` | Assign byte indexed |
| 80     | `liptchr` | Incr + pointer byte |
| 81     | `lptichr` | Pointer + incr byte |
| 82     | `ldptchr` | Decr + pointer byte |
| 83     | `lptdchr` | Pointer + decr byte |
| 84     | `ladachr` | Add-assign byte |
| 85     | `lsuachr` | Sub-assign byte |
| 86     | `lmuachr` | Mul-assign byte |
| 87     | `ldiachr` | Div-assign byte |
| 88     | `lmoachr` | Mod-assign byte |
| 89     | `lanachr` | And-assign byte |
| 90     | `lorachr` | Or-assign byte |
| 91     | `lxoachr` | Xor-assign byte |
| 92     | `lsrachr` | Shr-assign byte |
| 93     | `lslachr` | Shl-assign byte |
| 94     | `lcpachr` | Copy param byte |
| 95     | `lstrcpy` | strcpy(mem[di],[si]) |
| 96     | `lstrfix` | Amplía cadena para meter char |
| 97     | `lstrcat` | Concatena dos cadenas |
| 98     | `lstradd` | Suma dos strings |
| 99     | `lstrdec` | Añade o quita caracteres |
| 100    | `lstrsub` | Quita caracteres a cadena |
| 101    | `lstrlen` | Sustituye cadena por su longitud |
| 102    | `lstrigu` | Comparación igualdad cadenas |
| 103    | `lstrdis` | Cadenas distintas |
| 104    | `lstrmay` | Cadena mayor |
| 105    | `lstrmen` | Cadena menor |
| 106    | `lstrmei` | Cadena menor o igual |
| 107    | `lstrmai` | Cadena mayor o igual |
| 108    | `lcpastr` | Carga un parámetro en cadena |

### Opcodes de Words (109-125)

| Opcode | Nombre | Descripción |
|--------|--------|-------------|
| 109    | `lptrwor` | Pointer word indexed |
| 110    | `lasiwor` | Assign word indexed |
| 111    | `liptwor` | Incr + pointer word |
| 112    | `lptiwor` | Pointer + incr word |
| 113    | `ldptwor` | Decr + pointer word |
| 114    | `lptdwor` | Pointer + decr word |
| 115    | `ladawor` | Add-assign word |
| 116    | `lsuawor` | Sub-assign word |
| 117    | `lmuawor` | Mul-assign word |
| 118    | `ldiawor` | Div-assign word |
| 119    | `lmoawor` | Mod-assign word |
| 120    | `lanawor` | And-assign word |
| 121    | `lorawor` | Or-assign word |
| 122    | `lxoawor` | Xor-assign word |
| 123    | `lsrawor` | Shr-assign word |
| 124    | `lslawor` | Shl-assign word |
| 125    | `lcpawor` | Copy param word |

### Miscelánea (126)

| Opcode | Nombre | Descripción |
|--------|--------|-------------|
| 126    | `lnul` | Comprueba que un puntero no sea NULL |
