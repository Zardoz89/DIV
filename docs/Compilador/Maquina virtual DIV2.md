## Máquina Virtual DIV2

La VM que ejecuta el bytecode DIV2 está en `src/div32run/`. Es una **máquina virtual basada en pila** (stack-based).

### Arquitectura

| Componente | Descripción |
|------------|-------------|
| **Pila** (`pila[]`) | Array de enteros para operandos |
| **sp** | Stack Pointer - índice al top de la pila |
| **bp** | Base Pointer - auxiliar para llamadas a funciones |
| **ip** | Instruction Pointer - punkter al siguiente opcode |
| **id** | Process ID - offset en memoria del proceso actual |

### Execution Loop (`i.cpp`)

```c
void nucleo_exec() {
  do {
    switch ((byte)mem[ip++]){
      #include "kernel.cpp"  // 127+ casos de opcodes
    }
  } while (1);
}
```

1. **Fetch**: Lee opcode de `mem[ip++]`
2. **Decode**: Ejecuta el `case` correspondiente
3. **Repeat**: until process termination

### Gestión de Procesos

| Opcode | Función |
|--------|---------|
| `lcal` | Crea nuevo proceso |
| `lret` | Auto-eliminación del proceso |
| `lfrm` | Detiene ejecución por este frame |
| `ljmp` | Salto incondicional |
| `ljpf` | Salto si falso |

### Archivos Principales

| Archivo | Propósito |
|---------|----------|
| `i.cpp` | Loop principal (`interprete()`, `nucleo_exec()`) |
| `kernel.cpp` | Implementación de todos los opcodes |
| `inter.h` | Definiciones de VM y variables globales |
| `f.cpp` | Funciones built-in (`signal()`, `key()`, etc.) |
| `c.cpp` | Detección de colisiones |

### Memoria

- Cada proceso tiene un **bloque de memoria** reservado en `mem[]`
- El bytecode se carga también en `mem[]`
- La pila `pila[]` es compartida entre procesos
- **Multitarea cooperativa**: cada proceso ejecuta un frame y cede el control

