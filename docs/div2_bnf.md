# Gramática BNF del Lenguaje DIV2

Basado en el análisis del código fuente (`src/div/divc.cpp`) y la documentación del proyecto.

> **Nota**: Esta gramática describe el lenguaje DIV2 de DIV Games Studio 2. No es una gramática formal ISO-14977, sino una representación funcional basada en las convenciones del compilador.

---

## 1. Estructura General del Programa

```bnf
<programa> ::= PROGRAM <identificador> ";"
             [ COMPILER_OPTIONS <lista_opciones_compilador> ";" ]
             [ <seccion_constantes> ]
             [ <seccion_globales> ]
             [ IMPORT <lista_dlls> ";" ]
             <lista_procesos_funciones>
             [ <seccion_setup> ]
             EOF

<identificador> ::= <letra> { <letra> | <digito> | "_" }

<letra> ::= "A".."Z" | "a".."z"
<digito> ::= "0".."9"
```

### 1.1 Opciones de Compilador

```bnf
<lista_opciones_compilador> ::= <opcion_compilador> { "," <opcion_compilador> }

<opcion_compilador> ::= _MAX_PROCESS "=" <expresion>
                     | _EXTENDED_CONDITIONS
                     | _SIMPLE_CONDITIONS
                     | _CASE_SENSITIVE
                     | _IGNORE_ERRORS
                     | _FREE_SINTAX
                     | _NO_CHECK
                     | _NO_STRFIX
                     | _NO_OPTIMIZATION
                     | _NO_RANGE_CHECK
                     | _NO_ID_CHECK
                     | _NO_NULL_CHECK
```

| Opción | Descripción |
|-------|-------------|
| `_MAX_PROCESS = n` | Número máximo de procesos simultáneos (0 = ilimitado) |
| `_EXTENDED_CONDITIONS` | Habilita condiciones extendidas |
| `_SIMPLE_CONDITIONS` | Usa condiciones simples |
| `_CASE_SENSITIVE` | Distingue mayúsculas de minúsculas |
| `_IGNORE_ERRORS` | Ignora errores y continúa la compilación |
| `_FREE_SINTAX` | Sintaxis libre (menos estricta) |
| `_NO_CHECK` | Deshabilita comprobación de rango, ID y null |
| `_NO_STRFIX` | No corrige automáticamente strings |
| `_NO_OPTIMIZATION` | Deshabilita optimización de código |
| `_NO_RANGE_CHECK` | No comprueba rangos en tiempo de ejecución |
| `_NO_ID_CHECK` | No comprueba IDs de procesos/gráficos |
| `_NO_NULL_CHECK` | No comprueba punteros nulos |

### 1.2 Importación de DLLs

```bnf
<lista_dlls> ::= <nombre_dll> { "," <nombre_dll> }

<nombre_dll> ::= <texto_l>
```

```div
// Ejemplo de importación de DLLs
IMPORT "div32run.dll", "misdlls.dll";
```

> **Nota**: Se pueden importar hasta 64 DLLs simultáneamente. La primera (`div32run.dll`) contiene el runtime y se incluye implícitamente.

---

## 2. Secciones de Declaraciones

```bnf
<seccion_constantes> ::= CONST <lista_definiciones_constantes> END

<lista_definiciones_constantes> ::= <definicion_constante> { <separador> <definicion_constante> }
                              | epsilon

<definicion_constante> ::= <identificador> "=" <expresion> <separador>
                         | <identificador> "=" <texto_l> <separador>

<seccion_globales> ::= GLOBAL <lista_definiciones_variables> END

<seccion_imports> ::= IMPORT <lista_nombres_fichero> END

<lista_nombres_fichero> ::= <nombre_fichero> { "," <nombre_fichero> }
                        | epsilon

<nombre_fichero> ::= <texto_l>
```

---

## 3. Declaraciones de Variables

```bnf
<lista_definiciones_variables> ::= <definicion_variable> { <separador> <definicion_variable> }
                               | epsilon

<definicion_variable> ::= <tipo> <lista_nombres_variables> [ "=" <inicializacion> ]
                      | <tipo> <identificador> "[" <expresion> "]"     // arrays unidimensionales
                      | <tipo> <identificador> "[" <expresion> "]" "[" <expresion> "]"  // arrays 2D
                      | <tipo> <identificador> "[" <expresion> "]" "[" <expresion> "]" "[" <expresion> "]"  // arrays 3D
                      | STRUCT <identificador> <lista_miembros_struct>
                      | POINTER <tipo_puntero> <identificador>

<tipo> ::= INT | BYTE | WORD | STRING | CHAR

<tipo_puntero> ::= INT | BYTE | WORD | STRING | STRUCT <identificador>

<lista_nombres_variables> ::= <identificador> { "," <identificador> }

<lista_miembros_struct> ::= <definicion_variable> END
                       | <separador> <lista_miembros_struct>

<inicializacion> ::= <expresion>
                 | "{" <lista_expresiones> "}"

<lista_expresiones> ::= <expresion> { "," <expresion> }
                    | epsilon
```

---

## 4. Procesos y Funciones

```bnf
<lista_procesos_funciones> ::= <definicion_proceso> | <definicion_funcion>
                           { <definicion_proceso> | <definicion_funcion> }

<definicion_proceso> ::= PROCESS <identificador>
                      [ "(" <lista_parametros> ")" ] ";"
                      [ <seccion_locales> ]
                      BEGIN
                        <cuerpo_proceso>
                      END

<definicion_funcion> ::= FUNCTION <tipo> <identificador>
                         [ "(" <lista_parametros> ")" ] ";"
                         [ <seccion_locales> ]
                         BEGIN
                           <cuerpo_funcion>
                         END

<seccion_locales> ::= LOCAL <lista_definiciones_variables>

<lista_parametros> ::= <parametro> { "," <parametro> }
                    | epsilon

<parametro> ::= <tipo> <identificador>
               | <tipo> <identificador> "[" <expresion> "]"  // array como parámetro
               | <tipo> POINTER <identificador>
               | POINTER <tipo_puntero> <identificador>
```

---

## 5. Cuerpo de Código

```bnf
<cuerpo_proceso> ::= <lista_sentencias>
                   | LOCAL <definicion_variable> { <separador> <definicion_variable> }
                     [ PRIVATE <definicion_variable> { <separador> <definicion_variable> } ]
                     <lista_sentencias>

<cuerpo_funcion> ::= <lista_sentencias>
                   | RETURN [ <expresion> ] ";"

<lista_sentencias> ::= <sentencia> <separador> { <sentencia> <separador> }
                     | epsilon

<separador> ::= ";" | epsilon
```

---

## 6. Tipos de Sentencias

```bnf
<sentencia> ::= <sentencia_asignacion>
            | <sentencia_if>
            | <sentencia_switch>
            | <sentencia_loop>
            | <sentencia_while>
            | <sentencia_repeat>
            | <sentencia_for>
            | <sentencia_llamada>
            | <sentencia_frame>
            | <sentencia_break>
            | <sentencia_continue>
            | <sentencia_return>
            | <sentencia_clone>
            | <sentencia_debug>

<sentencia_asignacion> ::= <variable> "=" <expresion>
                      | <variable> "+=" <expresion>
                      | <variable> "-=" <expresion>
                      | <variable> "++"
                      | <variable> "--"

<variable> ::= <identificador>
            | <identificador> "[" <lista_expresiones_indice> "]"
            | <identificador> "." <identificador>     // miembro de struct
            | POINTER <expresion>               // indirección
            | "*" <expresion>                  // alternativa a POINTER
```

---

## 7. Estructuras de Control de Flujo

```bnf
<sentencia_if> ::= IF <expresion> THEN
                   <lista_sentencias>
                   [ ELSE <lista_sentencias> ]
                 END

<sentencia_switch> ::= SWITCH <expresion>
                        <lista_cases>
                        [ DEFAULT <lista_sentencias> ]
                      END

<lista_cases> ::= CASE <expresion> ":" <lista_sentencias>
                 { CASE <expresion> ":" <lista_sentencias> }

<sentencia_loop> ::= LOOP
                     <lista_sentencias>
                   END

<sentencia_while> ::= WHILE <expresion>
                       <lista_sentencias>
                     END

<sentencia_repeat> ::= REPEAT
                        <lista_sentencias>
                      UNTIL <expresion>

<sentencia_for> ::= FOR <identificador> "=" <expresion> TO <expresion> [ STEP <expresion> ]
                    <lista_sentencias>
                  END
                | FOR <identificador> "=" <expresion> DOWNTO <expresion> [ STEP <expresion> ]
                    <lista_sentencias>
                  END
```

---

## 8. Expresiones

```bnf
<expresion> ::= <expresion_logica>

<expresion_logica> ::= <expresion_bitwise>
                    | <expresion_logica> "AND" <expresion_bitwise>
                    | <expresion_logica> "OR" <expresion_bitwise>
                    | "NOT" <expresion_logica>

<expresion_bitwise> ::= <expresion_comparacion>
                    | <expresion_bitwise> "&" <expresion_comparacion>
                    | <expresion_bitwise> "|" <expresion_comparacion>
                    | <expresion_bitwise> "XOR" <expresion_comparacion>

<expresion_comparacion> ::= <expresion_aditiva>
                        | <expresion_comparacion> "==" <expresion_aditiva>
                        | <expresion_comparacion> "!=" <expresion_aditiva>
                        | <expresion_comparacion> "<" <expresion_aditiva>
                        | <expresion_comparacion> ">" <expresion_aditiva>
                        | <expresion_comparacion> "<=" <expresion_aditiva>
                        | <expresion_comparacion> ">=" <expresion_aditiva>

<expresion_aditiva> ::= <expresion_multiplicativa>
                    | <expresion_aditiva> "+" <expresion_multiplicativa>
                    | <expresion_aditiva> "-" <expresion_multiplicativa>

<expresion_multiplicativa> ::= <expresion_unaria>
                           | <expresion_multiplicativa> "*" <expresion_unaria>
                           | <expresion_multiplicativa> "/" <expresion_unaria>
                           | <expresion_multiplicativa> "MOD" <expresion_unaria>
                           | <expresion_multiplicativa> "%" <expresion_unaria>

<expresion_unaria> ::= <expresion_primary>
                  | "-" <expresion_unaria>
                  | "NOT" <expresion_unaria>
                  | "~" <expresion_unaria>
                  | "++" <expresion_unaria>
                  | "--" <expresion_unaria>
                  | "<<" <expresion_unaria>      // shift left
                  | ">>" <expresion_unaria>      // shift right

<expresion_primary> ::= <numero>
                     | <texto_l>
                     | <identificador>
                     | <identificador> "(" <lista_argumentos> ")"  // llamada a función
                     | "(" <expresion> ")"
                     | "-" <numero>              // literal negativo

<lista_argumentos> ::= <expresion> { "," <expresion> }
                      | epsilon

<numero> ::= [ "-" ] <digito> { <digito> }
          | "0x" <digito_hex> { <digito_hex> }  // hexadecimal
          | "0b" <digito_bin> { <digito_bin> }  // binario

<digito_hex> ::= <digito> | "A".."F" | "a".."f"
<digito_bin> ::= "0" | "1"

<texto_l> ::= """ { <caracter> | "\n" | "\t" | "\" <caracter> } """
```

---

## 9. Palabras Reservadas

| Palabra | Código | Descripción |
|---------|--------|-------------|
| `PROGRAM` | 0x01 | Cabecera del programa |
| `CONST` | 0x02 | Sección de constantes |
| `GLOBAL` | 0x03 | Sección de variables globales |
| `LOCAL` | 0x04 | Sección de variables locales |
| `BEGIN` | 0x05 | Inicio del cuerpo |
| `END` | 0x06 | Fin de un bloque |
| `PROCESS` | 0x07 | Definición de proceso |
| `PRIVATE` | 0x08 | Variables privadas (dentro de procesos) |
| `STRUCT` | 0x09 | Definición de estructura |
| `IMPORT` | 0x0A | Importación de librerías |
| `SETUP` | 0x0B | Programa de instalación |
| `STRING` | 0x0C | Tipo cadena |
| `BYTE` | 0x0D | Tipo byte (8 bits) |
| `WORD` | 0x0E | Tipo word (16 bits) |
| `INT` | 0x0F | Tipo entero (32 bits) |
| `CHAR` | 0x50 | Tipo carácter |
| `FUNCTION` | 0x11 | Definición de función |
| `COMPILER_OPTIONS` | 0x10 | Opciones del compilador |
| `RETURN` | 0x18 | Retorno de valor |
| `FROM` | 0x19 | Inicio de bucle for |
| `TO` | 0x1A | Límite superior |
| `DOWNTO` | 0x1C | Bucle inverso |
| `STEP` | 0x1B | Paso del bucle |
| `UNTIL` | 0x16 | Condición de terminación |
| `ELSE` | 0x17 | Alternativa |
| `IF` | 0x20 | Condicional |
| `LOOP` | 0x21 | Bucle infinito |
| `WHILE` | 0x22 | Bucle con condición al inicio |
| `REPEAT` | 0x23 | Bucle con condición al final |
| `FOR` | 0x24 | Bucle con contador |
| `SWITCH` | 0x25 | Selector múltiple |
| `CASE` | 0x26 | Caso en switch |
| `DEFAULT` | 0x27 | Caso por defecto |
| `FRAME` | 0x28 | Esperar un frame |
| `BREAK` | 0x29 | Salir del bucle |
| `CONTINUE` | 0x2A | Siguiente iteración |
| `CLONE` | 0x2B | Clonar proceso |
| `DEBUG` | 0x2C | Invocar debugger |
| `POINTER` | 0x4F | Operador de indirección |

---

## 10. Operadores

### 10.1 Operadores Aritméticos

| Símbolo | Alternativa | Descripción |
|---------|----------|-------------|
| `+` | | Suma |
| `-` | | Resta |
| `*` | | Multiplicación |
| `/` | | División |
| `%` | `MOD` | Módulo |
| `++` | | Incremento |
| `--` | | Decremento |
| `<<` | | Shift izquierdo |
| `>>` | | Shift derecho |

### 10.2 Operadores de Comparación

| Símbolo | Alternativa | Descripción |
|---------|----------|-------------|
| `==` | `_EQ` | Igual |
| `!=` | `_NE` | Diferente |
| `<` | | Menor que |
| `>` | | Mayor que |
| `<=` | | Menor o igual |
| `>=` | | Mayor o igual |

### 10.3 Operadores Lógicos

| Símbolo | Alternativa | Descripción |
|---------|----------|-------------|
| `AND` | `&` | Y lógico |
| `OR` | `\|` | O lógico |
| `NOT` | `~` | NO lógico |

---

## 11. Variables Predefinidas del Sistema

```bnf
// El lenguaje incluye diversas variables y funciones predefinidas como:
// - screen_width, screen_height
// - mouse_x, mouse_y, mouse_b
// - key (_key)
// - fpg, map, x, y, z, size, angle, etc. (para procesos gráficos)
// - rand(), abs(), sign(), etc.
```

---

## 12. Ejemplos de Programa

```div
PROGRAM ejemplo;

CONST
  MAX = 100;
END

GLOBAL
  mi_array[10] INT;
  mi_texto STRING = "Hola mundo";
  estructura STRUCT
    x INT;
    y INT;
  END
END

PROCESS jugador();
BEGIN
  LOCAL
    temp INT;
  END
  
  PRIVATE
    vidas INT = 3;
  END
  
  LOOP
    IF (key(_right)) THEN
      x += 2;
    END
    
    IF (key(_left)) THEN
      x -= 2;
    END
    
    FRAME;
  END
END

PROCESS dispara();
BEGIN
  cloned = clone(proyectil, x, y);
END

FUNCTION INT mi_funcion(a INT, b INT);
BEGIN
  RETURN a + b;
END
```

---

## 14. Funciones Builtin del Runtime

> **Nota**: Estas funciones están disponibles en el runtime (`div32run.dll`) y se pueden llamar directamente desde cualquier proceso o función. La tabla completa se encuentra en `src/div32run/f.cpp`.

### 14.1 Procesos y Control

| Función | Descripción |
|---------|------------|
| `signal(id, señal)` | Envía una señal a un proceso |
| `let_me_alone()` | Espera a que mueran todos los procesos hijos |
| `exit(código)` | Termina el programa con código de salida |

### 14.2 Input - Teclado

| Función | Descripción |
|---------|------------|
| `key(código_tecla)` | Devuelve el estado de una tecla (0=no pulsada, 1=pulsada) |
| `_key` | Variable predefinida: código de última tecla pulsada |

### 14.3 Input - Ratón

| Función | Descripción |
|---------|------------|
| `get_point(x, y)` | Obtiene coordenadas del puntero del ratón |
| `get_real_point(x, y)` | Obtiene coordenadas reales del ratón |
| `mouse_x` | Variable: posición X del ratón |
| `mouse_y` | Variable: posición Y del ratón |
| `mouse_b` | Variable: botones del ratón |

### 14.4 Input - Joystick

| Función | Descripción |
|---------|------------|
| `get_joy_button(joy, botón)` | Estado de los botones del joystick |
| `get_joy_position(joy, eje)` | Posición de los ejes del joystick |

### 14.5 Graphics - Cargar Archivos

| Función | Descripción |
|---------|------------|
| `load_fpg(nombre)` | Carga archivo de gráficos .fpg |
| `load_pal(nombre)` | Carga paleta (.pal, .fpg, .fnt, .pcx) |
| `load_fnt(nombre)` | Carga fuente .fnt |
| `load_pcx(nombre)` | Carga imagen PCX |
| `load_map(id, nombre)` | Carga mapa |
| `unload_fpg(id)` | Descarga archivo .fpg |
| `unload_fnt(id)` | Descarga fuente |
| `unload_map(id)` | Descarga mapa |
| `new_map(ancho, alto, profundidad)` | Crea nuevo mapa en memoria |

### 14.6 Graphics - Guardar Archivos

| Función | Descripción |
|---------|------------|
| `save(id, nombre)` | Guarda proceso/mapa en archivo |
| `load(nombre)` | Carga proceso/mapa desde archivo |
| `save_pcx(nombre)` | Guarda gráfica como PCX |
| `save_map(id, nombre)` | Guarda mapa |
| `write_in_map(id, x, y, gráfico)` | Escribe gráfico en mapa |
| `load_screen(nombre)` | Carga imagen de pantalla |

### 14.7 Graphics - Sprites

| Función | Descripción |
|---------|------------|
| `put(x, y, gráfico)` | Dibuja gráfica simple |
| `xput(x, y, ángulo, tamaño, flags, gráfico)` | Dibuja con rotación y escalado |
| `put_screen(fichero, x, y)` | Dibuja en pantalla completa |
| `map_put(id, x, y, gráfica)` | Dibuja mapa como gráfica |
| `map_xput(id, x, y, ángulo, tamaño, flags, gráfica)` | Dibuja mapa con rotación/escalado |
| `map_block_copy(dest, dx, dy, orig, ox, oy, w, h)` | Copia bloque entre gráficas |
| `screen_copy(x1, y1, x2, y2, dx, dy)` | Copia región de pantalla |

### 14.8 Graphics - Píxeles

| Función | Descripción |
|---------|------------|
| `put_pixel(x, y, color)` | Dibuja un píxel |
| `get_pixel(x, y)` | Lee un píxel |
| `map_put_pixel(id, x, y, color)` | Dibuja píxel en mapa |
| `map_get_pixel(id, x, y)` | Lee píxel de mapa |
| `clear_screen()` | Limpia la pantalla |

### 14.9 Graphics - Texto

| Función | Descripción |
|---------|------------|
| `write(fichero, x, y, tamaño, texto)` | Escribe texto |
| `write_int(fichero, x, y, tamaño, valor)` | Escribe texto con variables |
| `delete_text(id)` | Borra texto |
| `move_text(id, x, y)` | Mueve texto |

### 14.10 Graphics - Paleta/Color

| Función | Descripción |
|---------|------------|
| `fade(velocidad, colores, negro)` | Efecto de fundido de paleta |
| `fade_on()` | Inicia fundido |
| `fade_off()` | Finaliza fundido |
| `roll_palette(inicio, fin, velocidad)` | Rota colores de paleta |
| `convert_palette(fichero)` | Convierte paleta |
| `set_color(n, r, g, b)` | Establece color RGB |
| `find_color(r, g, b)` | Busca color más cercano |
| `force_pal(fichero)` | Fuerza recarga de paleta |

### 14.11 Graphics - Scroll

| Función | Descripción |
|---------|------------|
| `start_scroll(id_fondo, x, y, ancho, alto, velocidad)` | Inicia scroll parallax |
| `stop_scroll()` | Detiene scroll |
| `refresh_scroll()` | Refresca scroll |
| `move_scroll(x, y)` | Mueve scroll automáticamente |

### 14.12 Graphics - Regiones

| Función | Descripción |
|---------|------------|
| `define_region(id, x, y, ancho, alto, graphically)` | Define región de clipping |
| `out_region(id)` | Obtiene información de región |

### 14.13 Graphics - Mode 7

| Función | Descripción |
|---------|------------|
| `start_mode7(id_fondo, id_mascara, x, y, ancho, alto)` | Inicia modo 7 |
| `stop_mode7()` | Detiene modo 7 |

### 14.14 Graphics - Mode 8 (3D)

| Función | Descripción |
|---------|------------|
| `start_mode8(id_fondo, x, y, límite)` | Inicia modo 8 (motor 3D) |
| `stop_mode8()` | Detiene modo 8 |
| `set_sector_height(x, y, altura)` | Establece altura de sector |
| `get_sector_height(x, y)` | Obtiene altura de sector |
| `set_point_m8(x, y, z, ángulo)` | Establece punto 3D |
| `get_point_m8(x, y)` | Obtiene punto 3D |
| `set_fog(distancia, color)` | Establece niebla |
| `set_sector_texture(x, y, gráfica)` | Textura de sector |
| `get_sector_texture(x, y)` | Obtiene textura de sector |
| `set_wall_texture(dirección, gráfica)` | Textura de pared |
| `get_wall_texture(dirección)` | Obtiene textura de pared |
| `set_env_color(color)` | Color de entorno |
| `advance(velocidad)` | Avanzar en modo 8 |
| `x_advance(velocidad, altura)` | Avanzar con parámetros |

### 14.15 Graphics - Primitivas

| Función | Descripción |
|---------|------------|
| `draw(tipo, parámetros)` | Dibuja primitiva |
| `delete_draw(id)` | Borra primitiva |
| `move_draw(id, dx, dy)` | Mueve primitiva |

### 14.16 Graphics - Información

| Función | Descripción |
|---------|------------|
| `graphic_info(fichero, info, parámetro)` | Información de gráfica |
| `collision(tipo, id1, id2)` | Detecta colisión |
| `get_id(nombre)` | Obtiene ID de proceso por nombre |
| `screen_width` | Ancho de pantalla |
| `screen_height` | Alto de pantalla |

### 14.17 Audio - Sonido

| Función | Descripción |
|---------|------------|
| `sound(sonido)` | Reproduce sonido |
| `stop_sound(sonido)` | Detiene sonido |
| `change_sound(sonido, volumen, frecuencia)` | Cambia sonido |
| `set_volume(sonido, volumen)` | Ajusta volumen |
| `load_pcm(nombre)` | Carga PCM/WAV |
| `unload_pcm(id)` | Descarga PCM/WAV |
| `is_playing_sound(sonido)` | ¿Está reproduciendo? |
| `change_channel(sonido, canal)` | Cambia canal de sonido |
| `reset_sound()` | Resetea todos los sonidos |

### 14.18 Audio - CD

| Función | Descripción |
|---------|------------|
| `play_cd(pista, tiempo)` | Reproduce CD de audio |
| `stop_cd()` | Detiene CD |
| `is_playing_cd()` | ¿Está reproduciendo CD? |

### 14.19 Audio - MIDI

| Función | Descripción |
|---------|------------|
| `load_song(nombre)` | Carga canción MOD/XM |
| `unload_song()` | Descarga canción |
| `song(velocidad)` | Reproduce canción |
| `stop_song()` | Detiene canción |
| `set_song_pos(posición)` | Establece posición |
| `get_song_pos()` | Obtiene posición |
| `get_song_line(línea)` | Obtiene línea de la canción |
| `is_playing_song()` | ¿Está reproduciendo? |

### 14.20 Audio - FLI (Animación)

| Función | Descripción |
|---------|------------|
| `start_fli(fichero)` | Inicia animación FLI |
| `frame_fli()` | Siguiente frame FLI |
| `end_fli()` | Finaliza animación FLI |
| `reset_fli()` | Resetea FLI al inicio |

### 14.21 Matemáticas - Distancia/Ángulo

| Función | Descripción |
|---------|------------|
| `get_distx(x1, y1, x2, y2, ángulo)` | Distancia X |
| `get_disty(x1, y1, x2, y2, ángulo)` | Distancia Y |
| `get_angle(id1, id2)` | Ángulo entre dos procesos |
| `get_dist(id1, id2)` | Distancia entre dos procesos |
| `fget_dist(id1, id2)` | Distancia "float" entre procesos |
| `fget_angle(id1, id2)` | Ángulo "float" entre procesos |
| `near_angle(actual, objetivo, cambio)` | Ángulo más cercano |

### 14.22 Matemáticas - Varias

| Función | Descripción |
|---------|------------|
| `abs(valor)` | Valor absoluto |
| `sqrt(valor)` | Raíz cuadrada |
| `pow(base, exponente)` | Potencia |
| `rand(mín, máx)` | Número aleatorio |
| `rand_seed(semilla)` | Establece semilla aleatoria |
| `calculate(expresión)` | Calcula expresión string |

### 14.23 Matemáticas - Trigonométricas

| Función | Descripción |
|---------|------------|
| `sin(ángulo)` | Seno |
| `cos(ángulo)` | Coseno |
| `tan(ángulo)` | Tangente |
| `asin(seno)` | Arcoseno |
| `acos(coseno)` | Arcocoseno |
| `atan(tangente)` | Arcotangente |
| `atan2(y, x)` | Arcotangente de 2 valores |

### 14.24 Strings

| Función | Descripción |
|---------|------------|
| `strcpy(destino, origen)` | Copia string |
| `strcat(destino, origen)` | Concatena strings |
| `strlen(cadena)` | Longitud de string |
| `strcmp(cadena1, cadena2)` | Compara strings |
| `strchr(cadena, carácter)` | Busca carácter |
| `strstr(cadena1, cadena2)` | Busca substring |
| `strset(cadena, carácter)` | Fija todos los caracteres |
| `strupr(cadena)` | Convierte a mayúsculas |
| `strlwr(cadena)` | Convierte a minúsculas |
| `strdel(cadena, posición, cantidad)` | Borra caracteres |
| `itoa(número)` | Convierte entero a string |
| `char(código)` | Convierte código a carácter |

### 14.25 Archivos - E/S

| Función | Descripción |
|---------|------------|
| `fopen(nombre, modo)` | Abre archivo |
| `fclose(fichero)` | Cierra archivo |
| `fread(fichero, buffer, tamaño)` | Lee datos |
| `fwrite(fichero, buffer, tamaño)` | Escribe datos |
| `fseek(fichero, posición)` | Cambia posición |
| `ftell(fichero)` | Posición actual |
| `filelength(fichero)` | Tamaño de archivo |
| `flush(fichero)` | Vuelca buffers |

### 14.26 Archivos - Directorios

| Función | Descripción |
|---------|------------|
| `get_dirinfo(directorio)` | Información de directorio |
| `get_fileinfo(fichero)` | Información de archivo |
| `getdrive()` | Unidad actual |
| `setdrive(unidad)` | Cambia unidad |
| `chdir(directorio)` | Cambia directorio |
| `mkdir(directorio)` | Crea directorio |
| `remove(fichero)` | Borra archivo |
| `disk_free()` | Espacio libre en disco |
| `memory_free()` | Memoria libre |

### 14.27 Sistema

| Función | Descripción |
|---------|------------|
| `set_mode(modo)` | Establece modo de vídeo |
| `set_fps(fps)` | FPS objetivo |
| `system(comando)` | Ejecuta comando del sistema |
| `ignore_error()` | Ignora errores siguientes |
| `get_env变量)` | Obtiene variable de entorno |

### 14.28 Memoria Dinámica

| Función | Descripción |
|---------|------------|
| `malloc(tamaño)` | Asigna memoria |
| `free(puntero)` | Libera memoria |
| `encode(puntero, tamaño, clave)` | Codifica datos |
| `encode_file(fichero, clave)` | Codifica archivo |
| `compress_file(fichero_dest, fichero_orig)` | Comprime archivo |
| `uncompress_file(fichero_dest, fichero_orig)` | Descomprime archivo |

### 14.29 Red

| Función | Descripción |
|---------|------------|
| `net_join_game(juego, jugador)` | Se une a juego en red |
| `net_get_games()` | Obtiene lista de juegos disponibles |

### 14.30 Pathfinding 3D

| Función | Descripción |
|---------|------------|
| `path_find(x1, y1, z1, x2, y2, z2)` | Busca camino |
| `path_line(puntero, índice)` | Obtiene punto del camino |
| `path_free(puntero)` | Libera camino |

### 14.31 World 3D

| Función | Descripción |
|---------|------------|
| `load_wld(nombre)` | Carga mundo 3D |
| `go_to_flag(flags)` | Va a bandera |

### 14.32 Miscelánea

| Función | Descripción |
|---------|------------|
| `sort(datos, cantidad, tamaño, modo)` | Ordena array |
| `graphic_info(fichero, tipo, valor)` | Información de gráfica |
| `collision(tipo, id1, id2)` | Detecta colisión |

---

### Variables Predefinidas del Sistema

| Variable | Descripción |
|----------|------------|
| `mouse_x` | Posición X del ratón |
| `mouse_y` | Posición Y del ratón |
| `mouse_b` | Botones del ratón (bit 0=izq, bit 1=der) |
| `screen_width` | Ancho de la pantalla |
| `screen_height` | Alto de la pantalla |
| `key` | Código de última tecla pulsada |
| `fps` | FPS actuales |
| `frame_time` | Tiempo del último frame |
| `argc` | Argumentos de línea de comandos |
| `argv[n]` | Argumento n |
| `x`, `y`, `z` | Posición del proceso (en procesos gráficos) |
| `graph` | ID de graphical del proceso |
| `size` | Tamaño del graphical |
| `angle` | Ángulo de rotación |
| `flags` | Banderas del proceso |
| `priority` | Prioridad del proceso |
| `id` | ID único del proceso |
| `father` | ID del proceso padre |
| `son` | ID del primer hijo |
| `brother` | ID del hermano siguiente |
| `big BROTHER` | ID del proceso anterior |

---

## 13. Notas

1. **Case-insensitive**: El lenguaje no distingue mayúsculas de minúsculas.
2. **Comentarios**: Se admiten con `//` (línea) y `/* ... */` (bloque).
3. **Literales de cadena**: Soportan secuencias de escape como `\n`, `\t`, `\\`.
4. **Dimensiones**: Arrays de hasta 3 dimensiones.
5. **Punteros**: Soporte para punteros a int, byte, word, string y structs.

---

*Documento generado a partir del análisis del código fuente del compilador DIV2 (`src/div/divc.cpp`).*