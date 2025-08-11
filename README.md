# Flex-y-Bison   Daniel Ayala Guzman

### 1. Análisis y ejecución de ejemplos

#### Ejemplo `fb1-1.l`

* **Descripción:** Primer escáner en Flex. Lee texto de entrada y reconoce palabras y números.
* **Componentes clave:**

  * Patrones regulares simples.
  * Acción por cada patrón.
* **Ejecución:**

  ```bash
  flex fb1-1.l
  cc lex.yy.c -lfl
  ./a.out
  ```
* **Salida esperada:** Imprime las coincidencias según los patrones definidos.
* **Hallazgos:** Base para entender cómo Flex convierte expresiones regulares en un autómata.

---

#### Ejemplo `fb1-2.l`

* **Descripción:** Escáner que reconoce tokens más complejos, como operadores y números.
* **Novedades:** Incluye reglas adicionales y casos por defecto.
* **Ejecución:**

  ```bash
  flex fb1-2.l
  cc lex.yy.c -lfl
  ./a.out
  ```
* **Salida esperada:** Lista de tokens detectados con sus categorías.
* **Hallazgos:** Introduce manejo más específico de patrones.

---

#### Ejemplo `fb1-3.l`

* **Descripción:** Escáner con funciones auxiliares para procesar tokens.
* **Novedades:** Separación de lógica y patrones.
* **Ejecución:**

  ```bash
  flex fb1-3.l
  cc lex.yy.c -lfl
  ./a.out
  ```
* **Salida esperada:** Reconocimiento más estructurado.
* **Hallazgos:** Más modularidad en el escáner.

---

#### Ejemplo `fb1-4.l`

* **Descripción:** Escáner manuscrito para comparar resultados.
* **Ejecución:**

  ```bash
   flex fb1-4.l
   cc lex.yy.c -lfl
   ./a.out
  ```
* **Salida esperada:** Resuldato dependiendo de la comparción.
* **Hallazgos:** Sirve para contrastar código generado vs. código manual.

---

#### Ejemplo `fb1-5.y` + `fb1-5.l`

* **Descripción:** Calculadora con Flex y Bison.
* **Ejecución:**

  ```bash
  bison -d fb1-5.y
  flex fb1-5.l
  cc lex.yy.c -lfl
  ./a.out
  ```
* **Salida esperada:** Operaciones matemáticas simples.
* **Hallazgos:** Integración completa escáner + analizador.

---

### 2. Preguntas

#### 2.1 Manejo de comentarios

* **Pregunta:** ¿La calculadora aceptará una línea que contenga solo un comentario?
* **Respuesta:** No, porque el analizador espera al menos un token válido. El escáner reconoce el comentario y lo descarta, dejando la línea vacía para el parser.
* **Solución:** Más fácil corregir en el escáner, agregando una regla que devuelva un token especial `COMMENT` que el parser pueda ignorar.

```lex
"//".*        { /* Ignorar comentario */ }
```

---

#### 2.2 Conversión hexadecimal

* **Modificación en `fb1-5.l`:**

```lex
0x[0-9a-fA-F]+ {
    yylval = strtol(yytext, NULL, 16);
    return NUMBER;
}
```

* **Modificación en `fb1-5.y`:**

```c
printf("Resultado: %d (0x%X)\n", resultado, resultado);
```

---

#### 2.3 Operadores de nivel de bits

* **En `fb1-5.l`:**

```lex
"&"   return AND;
"|"   return OR;
```

* **En `fb1-5.y`:**

```yacc
exp: exp AND exp { $$ = $1 & $3; }
    | exp OR exp  { $$ = $1 | $3; }
```

* **Diferencia OR / ABS:** El OR binario usa dos operandos, mientras que el ABS unario es un operador que se aplica a un único valor.

---

#### 2.4 Reconocimiento de tokens (fb1-4.l vs Flex)

* **Hallazgos:** La versión manuscrita no gestiona todas las variantes de espacios en blanco y puede fallar con caracteres no previstos. Flex maneja casos extra automáticamente.

---

#### 2.5 Limitaciones de Flex

* Lenguajes con dependencias de contexto complejas (ej. Python con indentación significativa).
* Lenguajes con sintaxis ambigua o dependiente de ejecución.

---

#### 2.6 Programa de conteo de palabras en C

```c
#include <stdio.h>
#include <ctype.h>

int main(void) {
    int c, inword = 0, count = 0;
    while ((c = getchar()) != EOF) {
        if (isspace(c)) {
            inword = 0;
        } else if (!inword) {
            inword = 1;
            count++;
        }
    }
    printf("Palabras: %d\n", count);
    return 0;
}
```

* **Resultados:** Versión C es más rápida en archivos grandes por menor sobrecarga que Flex.
* **Dificultad:** Menos legible, más difícil de extender.
