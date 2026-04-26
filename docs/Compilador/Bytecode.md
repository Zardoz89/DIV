## Opcodes del Bytecode DIV2

El compilador genera **127+ opcodes** para una máquina virtual.

### Opcodes Base (0-59)

| Opcode | Hex   | Nombre   | Nemotécnico   | Descripción |
|--------|-------|----------|---------------|-------------|
| 0      | 0x00  | `lnop`   | `nop`         | No operación |
| 1      | 0x01  | `lcar`   | `car IMM`     | Carga una constante en pila |
| 2      | 0x02  | `lasi`   | `asi`         | Saca valor, offset y mete el valor en `[offset]` (asignación) |
| 3      | 0x03  | `lori`   | `ori`         | Or lógico |
| 4      | 0x04  | `lxor`   | `xor`         | Xor, or exclusivo |
| 5      | 0x05  | `land`   | `and`         | And lógico |
| 6      | 0x06  | `ligu`   | `igu`         | Igual (comparación) |
| 7      | 0x07  | `ldis`   | `dis`         | Distinto |
| 8      | 0x08  | `lmay`   | `may`         | Mayor (con signo) |
| 9      | 0x09  | `lmen`   | `men`         | Menor |
| 10     | 0x0A  | `lmei`   | `mei`         | Menor o igual |
| 11     | 0x0B  | `lmai`   | `mai`         | Mayor o igual |
| 12     | 0x0C  | `ladd`   | `add`         | Suma |
| 13     | 0x0D  | `lsub`   | `sub`         | Resta |
| 14     | 0x0E  | `lmul`   | `mul`         | Multiplicación |
| 15     | 0x0F  | `ldiv`   | `div`         | División de enteros |
| 16     | 0x10  | `lmod`   | `mod`         | Módulo/resto |
| 17     | 0x11  | `lneg`   | `neg`         | Negación (cambio de signo) |
| 18     | 0x12  | `lptr`   | `ptr`         | Pointer - saca offset y mete `[offset]` |
| 19     | 0x13  | `lnot`   | `not`         | Negación binaria (bit a bit) |
| 20     | 0x14  | `laid`   | `aid`         | Suma id a la constante de la pila |
| 21     | 0x15  | `lcid`   | `cid`         | Carga id en la pila |
| 22     | 0x16  | `lrng`   | `rng IMM`     | Comparación de rango |
| 23     | 0x17  | `ljmp`   | `jmp IMM`     | Salto incondicional |
| 24     | 0x18  | `ljpf`   | `jpf IMM`     | Salto si es falso |
| 25     | 0x19  | `lfun`   | `fun IMM`     | Llamada a proceso interno (`signal()`, etc.) |
| 26     | 0x1A  | `lcal`   | `cal IMM`     | Crea un nuevo proceso |
| 27     | 0x1B  | `lret`   | `ret`         | Auto-eliminación del proceso |
| 28     | 0x1C  | `lasp`   | `asp`         | Desecha un valor apilado |
| 29     | 0x1D  | `lfrm`   | `frm`         | Detiene la ejecución por este frame |
| 30     | 0x1E  | `lcbp`   | `cbp IMM`     | Inicializa puntero a parámetros locales |
| 31     | 0x1F  | `lcpa`   | `cpa`         | Saca offset, lee parámetro y bp++ |
| 32     | 0x20  | `ltyp`   | `typ IMM`     | Define tipo de proceso (colisiones) |
| 33     | 0x21  | `lpri`   | `pri IMM`     | Salta y carga variables privadas |
| 34     | 0x22  | `lcse`   | `cse IMM`     | Si switch <> expresión, salta al offset |
| 35     | 0x23  | `lcsr`   | `csr IMM`     | Si switch no está en el rango, salta al offset |
| 36     | 0x24  | `lshr`   | `shr`         | Shift right (binario) |
| 37     | 0x25  | `lshl`   | `shl`         | Shift left (binario) |
| 38     | 0x26  | `lipt`   | `ipt`         | Incremento y pointer |
| 39     | 0x27  | `lpti`   | `pti`         | Pointer e incremento |
| 40     | 0x28  | `ldpt`   | `dpt`         | Decremento y pointer |
| 41     | 0x29  | `lptd`   | `ptd`         | Pointer y decremento |
| 42     | 0x2A  | `lada`   | `ada`         | Add-asignación |
| 43     | 0x2B  | `lsua`   | `sua`         | Sub-asignación |
| 44     | 0x2C  | `lmua`   | `mua`         | Mul-asignación |
| 45     | 0x2D  | `ldia`   | `dia`         | Div-asignación |
| 46     | 0x2E  | `lmoa`   | `moa`         | Mod-asignación |
| 47     | 0x2F  | `lana`   | `ana`         | And-asignación |
| 48     | 0x30  | `lora`   | `ora`         | Or-asignación |
| 49     | 0x31  | `lxoa`   | `xoa`         | Xor-asignación |
| 50     | 0x32  | `lsra`   | `sra`         | Shr-asignación |
| 51     | 0x33  | `lsla`   | `sla`         | Shl-asignación |
| 52     | 0x34  | `lpar`   | `par IMM`     | Define número de parámetros privados |
| 53     | 0x35  | `lrtf`   | `rtf`         | Auto-eliminación, devuelve un valor |
| 54     | 0x36  | `lclo`   | `clo IMM`     | Crea un clon del proceso actual |
| 55     | 0x37  | `lfrf`   | `frf`         | Pseudo-Frame (frame a un porcentaje) |
| 56     | 0x38  | `limp`   | `imp IMM`     | Importa una DLL externa |
| 57     | 0x39  | `lext`   | `ext IMM`     | Llama a una función externa |
| 58     | 0x3A  | `lchk`   | `chk`         | Comprueba validez de un identificador |
| 59     | 0x3B  | `ldbg`   | `dbg`         | Invoca al debugger |

### Opcodes de Optimización DIV 2.0 (60-77)

| Opcode | Hex   | Nombre        | Nemotécnico       | Descripción |
|--------|-------|---------------|-------------------|-------------|
| 60     | 0x3C  | `lcar2`       | `car2 IMM, IMM`   | Carga constante 2 bytes en pila |
| 61     | 0x3D  | `lcar3`       | `car3 IMM, IMM, IMM` | Carga constante 3 bytes en pila |
| 62     | 0x3E  | `lcar4`       | `car4 IMM, IMM, IMM, IMM` | Carga constante 4 bytes en pila |
| 63     | 0x3F  | `lasiasp`     | `asiasp`          | Assign + discard |
| 64     | 0x40  | `lcaraid`     | `caraid IMM`      | Car + add id |
| 65     | 0x41  | `lcarptr`     | `carptr IMM`      | Car + pointer |
| 66     | 0x42  | `laidptr`     | `aidptr`          | Add id + pointer |
| 67     | 0x43  | `lcaraidptr`  | `caraidptr IMM`   | Car + add id + pointer |
| 68     | 0x44  | `lcaraidcpa`  | `caraidcpa IMM`   | Car + add id + copy param |
| 69     | 0x45  | `laddptr`     | `addptr`          | Add + pointer |
| 70     | 0x46  | `lfunasp`     | `funasp IMM`      | Función + discard |
| 71     | 0x47  | `lcaradd`     | `caradd IMM`      | Carga + suma |
| 72     | 0x48  | `lcaraddptr`  | `caraddptr IMM`   | Carga + add + pointer |
| 73     | 0x49  | `lcarmul`     | `carmul IMM`      | Carga + multiplicación |
| 74     | 0x4A  | `lcarmuladd`  | `carmuladd IMM`   | Carga + mul + add |
| 75     | 0x4B  | `lcarasiasp`   | `carasiasp IMM`   | Car + assign + discard |
| 76     | 0x4C  | `lcarsub`     | `carsub IMM`      | Carga + resta |
| 77     | 0x4D  | `lcardiv`     | `cardiv IMM`      | Carga + división |

### Opcodes de Cadenas (78-108)

| Opcode | Hex   | Nombre       | Nemotécnico      | Descripción |
|--------|-------|--------------|------------------|-------------|
| 78     | 0x4E  | `lptrchr`    | `ptrchr`         | Pointer byte indexed |
| 79     | 0x4F  | `lasichr`     | `asichr`         | Assign byte indexed |
| 80     | 0x50  | `liptchr`     | `iptchr`         | Incr + pointer byte |
| 81     | 0x51  | `lptichr`     | `ptichr`         | Pointer + incr byte |
| 82     | 0x52  | `ldptchr`     | `dptchr`         | Decr + pointer byte |
| 83     | 0x53  | `lptdchr`     | `ptdchr`         | Pointer + decr byte |
| 84     | 0x54  | `ladachr`     | `adachr`         | Add-assign byte |
| 85     | 0x55  | `lsuachr`     | `suachr`         | Sub-assign byte |
| 86     | 0x56  | `lmuachr`     | `muachr`         | Mul-assign byte |
| 87     | 0x57  | `ldiachr`     | `diachr`         | Div-assign byte |
| 88     | 0x58  | `lmoachr`     | `moachr`         | Mod-assign byte |
| 89     | 0x59  | `lanachr`     | `anachr`         | And-assign byte |
| 90     | 0x5A  | `lorachr`     | `orachr`         | Or-assign byte |
| 91     | 0x5B  | `lxoachr`     | `xoachr`         | Xor-assign byte |
| 92     | 0x5C  | `lsrachr`     | `srachr`         | Shr-assign byte |
| 93     | 0x5D  | `lslachr`     | `slachr`         | Shl-assign byte |
| 94     | 0x5E  | `lcpachr`     | `cpachr`         | Copy param byte |
| 95     | 0x5F  | `lstrcpy`     | `strcpy`         | strcpy(mem[di],[si]) |
| 96     | 0x60  | `lstrfix`     | `strfix`         | Amplía cadena para meter char |
| 97     | 0x61  | `lstrcat`     | `strcat`         | Concatena dos cadenas |
| 98     | 0x62  | `lstradd`     | `stradd`         | Suma dos strings |
| 99     | 0x63  | `lstrdec`     | `strdec`         | Añade o quita caracteres |
| 100    | 0x64  | `lstrsub`     | `strsub`         | Quita caracteres a cadena |
| 101    | 0x65  | `lstrlen`     | `strlen`         | Sustituye cadena por su longitud |
| 102    | 0x66  | `lstrigu`     | `strigu`         | Comparación igualdad cadenas |
| 103    | 0x67  | `lstrdis`     | `strdis`         | Cadenas distintas |
| 104    | 0x68  | `lstrmay`     | `strmay`         | Cadena mayor |
| 105    | 0x69  | `lstrmen`     | `strmen`         | Cadena menor |
| 106    | 0x6A  | `lstrmei`     | `strmei`         | Cadena menor o igual |
| 107    | 0x6B  | `lstrmai`     | `strmai`         | Cadena mayor o igual |
| 108    | 0x6C  | `lcpastr`     | `cpastr`         | Carga un parámetro en cadena |

### Opcodes de Words (109-125)

| Opcode | Hex   | Nombre       | Nemotécnico      | Descripción |
|--------|-------|--------------|------------------|-------------|
| 109    | 0x6D  | `lptrwor`    | `ptrwor`         | Pointer word indexed |
| 110    | 0x6E  | `lasiwor`    | `asiwor`         | Assign word indexed |
| 111    | 0x6F  | `liptwor`    | `iptwor`         | Incr + pointer word |
| 112    | 0x70  | `lptiwor`    | `ptiwor`         | Pointer + incr word |
| 113    | 0x71  | `ldptwor`    | `dptwor`         | Decr + pointer word |
| 114    | 0x72  | `lptdwor`    | `ptdwor`         | Pointer + decr word |
| 115    | 0x73  | `ladawor`    | `adawor`         | Add-assign word |
| 116    | 0x74  | `lsuawor`    | `suawor`         | Sub-assign word |
| 117    | 0x75  | `lmuawor`    | `muawor`         | Mul-assign word |
| 118    | 0x76  | `ldiawor`    | `diawor`         | Div-assign word |
| 119    | 0x77  | `lmoawor`    | `moawor`         | Mod-assign word |
| 120    | 0x78  | `lanawor`    | `anawor`         | And-assign word |
| 121    | 0x79  | `lorawor`    | `orawor`         | Or-assign word |
| 122    | 0x7A  | `lxoawor`    | `xoawor`         | Xor-assign word |
| 123    | 0x7B  | `lsrawor`    | `srawor`         | Shr-assign word |
| 124    | 0x7C  | `lslawor`    | `slawor`         | Shl-assign word |
| 125    | 0x7D  | `lcpawor`    | `cpawor`         | Copy param word |

### Miscelánea (126)

| Opcode | Hex   | Nombre | Nemotécnico | Descripción |
|--------|-------|--------|-------------|-------------|
| 126    | 0x7E  | `lnul` | `nul`       | Comprueba que un puntero no sea NULL |
