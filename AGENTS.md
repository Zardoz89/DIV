# AGENTS.md — DIV (fork vii1)

Proyecto **MS-DOS**: IDE y runtime de DIV Games Studio 2, compilado con **Open Watcom** y **wmake**. No hay Node/npm ni CMake en la raíz.

## Entorno antes de compilar

- **Open Watcom 1.9** (compiladores DOS 16/32 bits **y** host Linux/Windows). El README advierte **incompatibilidades con Open Watcom 2**.
- **Linux**: `source <ruta-watcom>/owsetenv.sh` (bash).
- **Windows**: consola “Build Environment” de Open Watcom.
- **DOS**: `OWSETENV.BAT`.

Ejecutar `**wmake` desde la raíz del repo** (no desde subcarpetas salvo depuración puntual).

## Comandos útiles (raíz)


| Objetivo                                        | Comando                                                                                  |
| ----------------------------------------------- | ---------------------------------------------------------------------------------------- |
| Build principal                                 | `wmake`                                                                                  |
| Herramientas auxiliares (bin2h, testdll, unpak) | `wmake tools`                                                                            |
| Instalar árbol en `INSTALL_DIR`                 | `wmake install` (por defecto `INSTALL_DIR` = `$HOME/dosbox/DIV` en Unix si no se define) |
| Limpieza amplia                                 | `wmake clean`                                                                            |
| Tests DLL (Makefile en `dll`)                   | `wmake test_dll`                                                                         |
| Tests div32run (necesitan emulador)             | `wmake test_div32run_386` / `wmake test_div32run_586`                                    |
| Batería completa de tests                       | `wmake test` (= DLL + div32run 386 + 586)                                                |
| Build depurable                                 | `wmake CONFIG=debug`                                                                     |


- `**DOSBOX`**: por defecto el makefile usa `dosbox-x` para tests que lo requieren; en DOS real no aplica.
- `**libclean**`: destructivo (borra `3rdparty/lib`); exige TASM para recompilar esas libs o recuperarlas del repo.

## CI (`.travis.yml`)

En Travis: `wmake` → `wmake tools` → `wmake test_dll`. **No** ejecuta `wmake test` completo (los tests de div32run dependen de DOSBox).

## Estructura (resumen)

- `**src/div`**: IDE (`d.exe` / `d.386`).
- `**src/div32run**`: intérprete (`div32run.*`, `session.*`).
- `**src/vpe**`, `**dll**`, `**pmwlite**`, `**tools/**`: según README.
- `**3rdparty/**`: fuentes; `**3rdparty/lib**` trae `.lib` ya compiladas (TASM solo si recompilas esas piezas).
- `**formats/**`: especificaciones Kaitai Struct; la wiki del proyecto documenta formatos en detalle.

## Convenciones que suelen pillar

- `**.gitattributes**`: muchos `*.c`, `*.cpp`, `*.h`, `makefile`, `*.mif` van con **EOL CRLF**. No convertir masivamente a LF sin revisar política del repo.
- Hay `**.clang-format`** en la raíz (referencia de estilo C/C++ si se formatea con clang-format).
- Los artefactos de build suelen ir bajo `**build.dos**` / `**build.<sys-host>**` (`os.mif`); el `.gitignore` cubre `bin`, objetos Watcom, etc.

## Documentación externa

- Wiki y milestones: enlaces en `README.md` (compilación con Vagrant, formatos, herramientas).