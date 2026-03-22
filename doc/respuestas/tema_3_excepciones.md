<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Excepciones". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 3. Excepciones

## 1. Empecemos un tema sobre control de errores en lenguajes de programación, con algo básico. En C, donde no existen las excepciones, pongamos un ejemplo de una raíz que toma número flotante positivo. Queremos controlar el error si la función recibe un número negativo. El usuario debe ser informado pero desde fuera de la función `raiz` ¿Cómo indicamos ese error?. Enumera dos opciones diferentes de diseñar, poniendo un ejemplo de código de cada una.

En C, el mecanismo más habitual es el **valor de retorno especial**: la función devuelve un valor concreto que el llamador debe comprobar para detectar el error. Por convenio se suele usar −1, `NaN` o un valor fuera del dominio válido.
```c
#include <stdio.h>
#include <math.h>

// Opción A: valor de retorno especial (-1.0 para indicar error)
double raiz(double x) {
    if (x < 0) return -1.0;   // señal de error
    return sqrt(x);
}

int main(void) {
    double resultado = raiz(-4.0);
    if (resultado == -1.0)
        printf("Error: número negativo\n");
    else
        printf("Resultado: %f\n", resultado);
    return 0;
}
```

La segunda opción consiste en pasar un **parámetro de salida por puntero** que indique si hubo error. Así la función puede devolver el resultado real y el estado del error por separado, evitando ambigüedades (¿y si −1.0 es un resultado válido?):
```c
#include <stdio.h>
#include <math.h>

// Opción B: parámetro de error por puntero
double raiz(double x, int *error) {
    if (x < 0) {
        *error = 1;
        return 0.0;
    }
    *error = 0;
    return sqrt(x);
}

int main(void) {
    int error = 0;
    double resultado = raiz(-4.0, &error);
    if (error)
        printf("Error: número negativo\n");
    else
        printf("Resultado: %f\n", resultado);
    return 0;
}
```

Ambas opciones tienen en común que el llamador **debe** acordarse de comprobar el error manualmente; si lo olvida, el programa continúa con un valor incorrecto sin ningún aviso.

---

## 2. Brevemente ¿Qué es una **"excepción"**? ¿Con qué objetivo las usa un programador cuando implementa funciones o cuando las llama?

Una **excepción** es un mecanismo del lenguaje para señalar que ha ocurrido una situación anómala o inesperada durante la ejecución de un programa, situación que impide continuar el flujo normal del código. A diferencia de los valores de retorno especiales de C, la excepción interrumpe automáticamente la ejecución del método donde se produce y "sube" por la pila de llamadas hasta que alguien la gestiona.

Desde el punto de vista del programador, las excepciones sirven para dos propósitos complementarios: quien **implementa** una función las lanza (`throw`) cuando detecta un error que no puede o no debe resolver él mismo; quien **llama** a esa función las captura (`catch`) para decidir qué hacer ante ese error (informar al usuario, reintentar, abortar, etc.). De esta forma el código de manejo de errores queda separado del código funcional, mejorando la legibilidad.

---

## 3. Reescribe el mismo ejemplo de raiz, pero en Java, metiendo ese método en una clase `Calculadora` y llama a dicho método desde el método `main`, mostrando cómo se puede controlar desde fuera.

```java
public class Calculadora {

    public static double raiz(double x) {
        if (x < 0) {
            throw new IllegalArgumentException(
                "No se puede calcular la raíz de un número negativo: " + x);
        }
        return Math.sqrt(x);
    }

    public static void main(String[] args) {
        try {
            double resultado = raiz(-4.0);
            System.out.println("Resultado: " + resultado);
        } catch (IllegalArgumentException e) {
            System.out.println("Error capturado: " + e.getMessage());
        }
        System.out.println("El programa continúa tras el catch.");
    }
}
```

El método `raiz` lanza una excepción si el argumento es negativo. El método `main` la captura en el bloque `catch`, informa al usuario con `e.getMessage()` y el programa **continúa** ejecutándose después del bloque `try-catch`. Si no se capturase, la excepción terminaría el programa mostrando la traza de error.

---

## 4. ¿Qué es **"lanzar"** una excepción? ¿Qué es **"controlar"** o **"capturar"** una excepción? ¿Qué es que se **"propague"** una excepción? ¿Qué le va ocurriendo a las funciones en la pila de llamadas por donde se va propagando la excepción? ¿Las funciones que no la controlan se reanudan después de alguna forma? Explica con el mismo ejemplo anterior en Java de la raíz cuadrada.

**Lanzar** (`throw`) una excepción consiste en crear un objeto que representa el error y entregárselo al sistema de ejecución, interrumpiendo inmediatamente el método donde ocurre. **Capturar** (`catch`) es interceptar esa excepción en un bloque `try-catch` para tratarla; el flujo continúa después del `catch`. **Propagarse** significa que, si un método no captura la excepción, ésta sube automáticamente al método que lo llamó, y así sucesivamente.

Cuando una excepción se propaga, cada método de la pila de llamadas que no la captura se **abandona inmediatamente**: no termina de ejecutarse, no devuelve ningún valor y su marco de pila se descarta. Esos métodos no se reanudan en ningún momento; simplemente dejan de existir en el flujo de ejecución.

Siguiendo el ejemplo anterior, si `main` llamase a un método intermedio `calcular` que a su vez llama a `raiz`:
```java
// raiz() lanza IllegalArgumentException
// calcular() no la captura → se propaga
// main() la captura en su try-catch

public static double calcular(double x) {
    return raiz(x); // No hay try-catch: la excepción sube
}

public static void main(String[] args) {
    try {
        double r = calcular(-9.0);
        System.out.println(r); // nunca se ejecuta
    } catch (IllegalArgumentException e) {
        System.out.println("Error en main: " + e.getMessage());
    }
}
```

`calcular` queda abortado a mitad; `main` recibe la excepción y la gestiona. La línea `println(r)` dentro del `try` jamás se ejecuta.

---

## 5. ¿Qué ventajas tiene frente a C, la **"propagación natural"** de las excepciones a través de la pila (*stack*) de llamadas?

En C, cada función de la cadena de llamadas debe comprobar manualmente el valor de retorno del nivel inferior y decidir si reenvía el error hacia arriba (normalmente devolviendo a su vez un valor especial). Esto obliga a escribir comprobaciones repetitivas en cada nivel y hace muy fácil olvidarse de una, lo que provoca que el error pase desapercibido.

Con la propagación automática de excepciones, el error "sube solo" sin que los niveles intermedios tengan que hacer nada. El código de manejo de errores puede situarse en el nivel que realmente sabe qué hacer con ese error (normalmente el más alto), mientras los niveles intermedios se mantienen limpios y centrados en su lógica de negocio. Además, si nadie captura la excepción, la JVM termina el programa con un mensaje de error claro y una traza de pila, en vez de continuar silenciosamente con datos corruptos como ocurriría en C.

---

## 6. En orientación a objetos, ¿las excepciones suelen ser objetos? ¿Qué ventajas tiene esto en términos de encapsulación? ¿Podemos entonces crear excepciones personalizadas?

En los lenguajes orientados a objetos, las excepciones son **objetos** que pertenecen a una jerarquía de clases. En Java, todas descienden de `Throwable`, luego de `Exception` o `Error`. Esto tiene ventajas claras de encapsulación: el objeto excepción puede llevar consigo toda la información sobre el error (mensaje, causa, traza de pila, datos adicionales) perfectamente encapsulada en sus atributos, y el manejador accede a ella a través de métodos públicos.

Al ser clases, es completamente posible **crear excepciones personalizadas** extendiendo `Exception` (controlada) o `RuntimeException` (no controlada). Así se pueden definir tipos de error propios del dominio del problema con su propio nombre, atributos y métodos:
```java
public class SaldoInsuficienteException extends Exception {
    private final double saldoActual;

    public SaldoInsuficienteException(double saldoActual) {
        super("Saldo insuficiente. Disponible: " + saldoActual);
        this.saldoActual = saldoActual;
    }

    public double getSaldoActual() { return saldoActual; }
}
```

---

## 7. En relación con las ventajas de la encapsulación, comparando el ejemplo en C con Java. ¿Qué **información esencial** lleva cualquier **objeto excepción** que es muy útil tener cuando se llega a un manejador?


Todo objeto excepción en Java incluye al menos dos piezas de información fundamentales. La primera es el **mensaje** (`getMessage()`), una cadena de texto legible que describe qué falló y, si se diseña bien, con qué valores. La segunda y más valiosa es la **traza de pila** (*stack trace*), que registra automáticamente la secuencia exacta de llamadas a métodos que llevaron al error, con nombres de clase, método y número de línea.

En C, cuando ocurre un error con valores de retorno, el programador solo sabe que la función devolvió −1; no sabe desde dónde se llamó, ni con qué argumentos, ni qué camino siguió la ejecución. El objeto excepción, en cambio, encapsula toda esa información de forma automática sin que el programador tenga que construirla manualmente. Esto reduce drásticamente el tiempo de depuración, especialmente en sistemas con muchos niveles de llamadas.

---

## 8. En Java, sobre el bloque **"try-catch"**, ¿se pueden tener más de un bloque `catch`? ¿cuántos bloques `catch` se ejecutan?

Sí, un bloque `try` puede ir seguido de **tantos `catch` como sean necesarios**, cada uno especializándose en un tipo de excepción distinto. Cuando se lanza una excepción, Java recorre los `catch` en orden y ejecuta **solo el primero** cuyo tipo coincida (o sea superclase del tipo lanzado). Los demás se ignoran.
```java
try {
    double r = Calculadora.raiz(Double.parseDouble("abc"));
} catch (NumberFormatException e) {
    System.out.println("El texto no es un número: " + e.getMessage());
} catch (IllegalArgumentException e) {
    System.out.println("Argumento inválido: " + e.getMessage());
} catch (Exception e) {
    System.out.println("Error genérico: " + e.getMessage());
}
```

El orden importa: los tipos más específicos deben ir antes que los más generales. Si se pusiera `Exception` primero, capturaría todo y los `catch` posteriores nunca se alcanzarían (de hecho, Java no compila ese código y lo señala como error).

---

## 9. Si las excepciones producen rupturas en el código llamador, ¿cómo podemos garantizar que se ejecuta siempre finalmente un código necesario para cierre de ficheros, liberacion de recursos, antes de que continúe propagándose la excepción? Pon un ejemplo en Java con `finally`, tanto con `catch` como sin él.

El bloque `finally` contiene código que **se ejecuta siempre**, tanto si el `try` terminó normalmente como si se lanzó una excepción (capturada o no). Es el lugar adecuado para cerrar ficheros, liberar conexiones de red o bases de datos, y cualquier recurso que deba liberarse independientemente de lo que haya ocurrido.
```java
// Con catch y finally
public static void leerFichero(String ruta) {
    BufferedReader br = null;
    try {
        br = new BufferedReader(new FileReader(ruta));
        System.out.println(br.readLine());
    } catch (IOException e) {
        System.out.println("Error de lectura: " + e.getMessage());
    } finally {
        if (br != null) {
            try { br.close(); } catch (IOException e) { /* ignorado */ }
        }
        System.out.println("finally ejecutado siempre");
    }
}

// Sin catch: la excepción se propaga, pero finally sí se ejecuta
public static void leerFichero(String ruta) throws IOException {
    BufferedReader br = new BufferedReader(new FileReader(ruta));
    try {
        System.out.println(br.readLine());
    } finally {
        br.close(); // se cierra siempre, aunque haya excepción
    }
}
```

---

## 10. En Java, el bloque `finally` puede ir sin `catch`? ¿Se ejecuta siempre tanto si ocurre como si no ocurre una excepción? ¿Y si hay un `return` en medio del `try`?

El bloque `finally` **puede usarse sin `catch`**, combinando `try-finally` directamente, como muestra el ejemplo anterior. Esta combinación es perfectamente válida y útil cuando no se quiere capturar la excepción sino solo garantizar la limpieza antes de que se propague.

El `finally` se ejecuta **siempre**, incluso si dentro del `try` hay un `return`. Java ejecuta el `finally` antes de efectuar el retorno. Si el propio `finally` contiene también un `return`, ese valor sobreescribe al del `try`, lo cual puede ser una fuente de bugs difíciles de detectar y debe evitarse. El único caso extremo donde `finally` no se ejecuta es si la JVM se termina abruptamente (por ejemplo, con `System.exit(0)` o un fallo de hardware).

---

## 11. En Java, qué son las excepciones **"controladas"** y las **"no controladas"**? ¿Qué papel juega `RuntimeException`? Pon un ejemplo de excepciones típicas controladas y no controladas que incluso nosotros mismos podríamos usar. Haz dos listas con 3 o 4 ejemplos de situación donde se suele preferir una excepción controlada y donde se suele preferir una excepción no controlada.

Las excepciones **controladas** (*checked*) son aquellas que el compilador obliga a gestionar: cualquier método que pueda lanzarlas debe o bien capturarlas con `try-catch` o bien declararlas en su firma con `throws`. Descienden de `Exception` pero **no** de `RuntimeException`. Las **no controladas** (*unchecked*) descienden de `RuntimeException` (o de `Error`) y el compilador no exige ningún tratamiento especial; pueden propagarse libremente sin declaración.

`RuntimeException` es la raíz de todas las excepciones no controladas que representan errores de programación: acceso nulo, índice fuera de rango, argumento ilegal… Se asume que este tipo de errores debería evitarse con código correcto, no capturarse.

**Ejemplos de excepciones controladas** (situaciones externas al programa):
- `IOException` — fallo al leer/escribir un fichero.
- `SQLException` — error en una consulta a base de datos.
- `FileNotFoundException` — el fichero indicado no existe.
- `SaldoInsuficienteException` — excepción personalizada de dominio.

**Ejemplos de excepciones no controladas** (errores de programación):
- `NullPointerException` — se usó una referencia nula.
- `ArrayIndexOutOfBoundsException` — índice fuera del array.
- `IllegalArgumentException` — argumento inválido pasado a un método.
- `IllegalStateException` — el objeto no está en el estado correcto para esa operación.

---

## 12. ¿Qué es y para qué se usa `throws`? ¿Por qué es alternativa a capturar una excepción controlada?

`throws` es una cláusula que se añade a la **firma de un método** para declarar que ese método puede lanzar una o más excepciones controladas sin capturarlas internamente. Es la alternativa a poner un `try-catch`: en lugar de gestionar la excepción, el método avisa a quien lo llame de que deberá gestionarla él.
```java
public double leerNumero(String ruta) throws IOException {
    // Si falla, la IOException sube al llamador
    BufferedReader br = new BufferedReader(new FileReader(ruta));
    return Double.parseDouble(br.readLine());
}
```

Si un método usa `throws`, el compilador traslada la responsabilidad de gestión al llamador: éste deberá a su vez capturar la excepción o también declararla con `throws`. Así la obligación de gestión se propaga explícitamente hacia arriba por la cadena de llamadas, lo cual es útil cuando los niveles intermedios no tienen suficiente contexto para tratar el error y prefieren delegarlo a un nivel superior.

---

## 13. Pon un ejemplo en Java de firma de método que incluya `throws`, de una función que abre un fichero pero que declara que no le interesa menejar la excepción de si el fichero no existe, sino que se propague hacia arriba. Eso sí, acuérdate del `finally`.

```java
import java.io.*;

public class LectorFichero {

    public static String leerPrimeraLinea(String ruta) throws FileNotFoundException, IOException {
        BufferedReader br = null;
        try {
            br = new BufferedReader(new FileReader(ruta));
            return br.readLine();
        } finally {
            if (br != null) {
                br.close();
            }
        }
    }

    public static void main(String[] args) {
        try {
            String linea = leerPrimeraLinea("datos.txt");
            System.out.println("Primera línea: " + linea);
        } catch (FileNotFoundException e) {
            System.out.println("Fichero no encontrado: " + e.getMessage());
        } catch (IOException e) {
            System.out.println("Error de lectura: " + e.getMessage());
        }
    }
}
```

`leerPrimeraLinea` no gestiona las excepciones: las declara en `throws` y deja que `main` decida qué hacer con cada una. El `finally` garantiza que el `BufferedReader` se cierra siempre, incluso aunque el `return` se ejecute antes de llegar al final del bloque `try`.

---

## 14. ¿Podemos poner en `throws` excepciones no controladas, como `RuntimeException`? ¿Debería el método llamador entonces poner `try-catch` en ese caso? ¿Qué sentido tendría?

Sí, Java lo permite sintácticamente: se puede escribir `throws RuntimeException` o cualquier subclase en la firma de un método. Sin embargo, **no es obligatorio** hacerlo y no genera ningún efecto en el compilador: el llamador no estará forzado a capturarla ni a declararla a su vez.

El uso principal que tiene sentido es **documentativo**: indicar en la firma que ese método puede lanzar cierta `RuntimeException` sirve como aviso explícito para quien lea la API, aunque el compilador no lo exija. Algunos frameworks y javadocs lo usan con este fin. No obstante, si el objetivo es simplemente informar, suele ser suficiente con documentarlo en el comentario `@throws` del Javadoc sin incluirlo en la firma, que es la práctica más habitual.

---

## 15. ¿Cuándo se recomienda usar excepciones controladas, como `IOException`, y cuándo no controladas como `IllegalArgumentException`? ¿Existen en todos los lenguajes ambas opciones? En los que sólo existe una opción, ¿cuál es la más habitual?

Las excepciones **controladas** se recomiendan cuando el error es una situación **externa y previsible** que un programa correcto debe anticipar y de la que puede recuperarse razonablemente: fichero no encontrado, conexión de red caída, base de datos no disponible. En estos casos tiene sentido obligar al llamador a tomar una decisión consciente.

Las excepciones **no controladas** se prefieren para errores que indican un **fallo de programación**: pasar `null` donde no está permitido, un índice fuera de rango, un estado de objeto inválido. No tiene sentido obligar a capturar estos errores porque la solución correcta es arreglar el código, no capturar el error en producción. También se prefieren en APIs de uso muy frecuente donde exigir `try-catch` en todas partes haría el código verboso e ilegible.

No todos los lenguajes distinguen entre ambos tipos. **Python**, **C#**, **Kotlin**, **C++** y **Ruby**, entre otros, solo tienen excepciones no controladas: no existe obligación de captura. Esta es, de hecho, la opción más extendida, y muchos desarrolladores de Java consideran que el modelo de excepciones controladas introduce demasiado ruido sintáctico.

---

## 16. ¿Tiene sentido lanzar excepciones dentro del `catch`? ¿Se puede relanzar la misma excepción capturada? ¿Cuándo tendría sentido hacer esto último? Pon ejemplos de ambos casos.

Sí, es una práctica habitual en dos variantes. La primera es lanzar una **excepción diferente** dentro del `catch`, normalmente para convertir una excepción de bajo nivel en una de más alto nivel o de dominio propio (ver pregunta 17). La segunda es **relanzar la misma excepción** capturada usando `throw e;`, lo que tiene sentido cuando se quiere ejecutar alguna acción (registrar el error en un log, liberar un recurso) pero sin absorber la excepción: el error debe seguir propagándose hacia arriba.
```java
// Lanzar una excepción diferente (encapsulación de bajo nivel en alto nivel)
try {
    conectarBaseDatos();
} catch (SQLException e) {
    throw new ServicioNoDisponibleException("BD no accesible", e);
}

// Relanzar la misma excepción tras registrar el error
try {
    raiz(-1.0);
} catch (IllegalArgumentException e) {
    logger.error("Argumento inválido detectado", e);
    throw e; // sigue propagándose
}
```

Relanzar la excepción original es especialmente útil en capas de infraestructura o middleware que quieren observar el error (para métricas, trazabilidad, logging) sin asumir la responsabilidad de resolverlo.

---

## 17. ¿En qué consiste que una excepción sea la **"causa"** de otra excepción? Pon un ejemplo en Java, donde capturemos una excepción de bajo nivel y la encapsulemos en otra personalizada de alto nivel. Cuando una excepción sale por pantalla y tiene una causa, ¿se ve?

En Java, cuando se encapsula una excepción dentro de otra, la original se denomina **causa** (*cause*). El constructor de `Exception` acepta un segundo parámetro de tipo `Throwable` que guarda esa causa internamente. Esto permite construir cadenas de excepciones que reflejan la relación entre un error de alto nivel (significativo para el llamador) y el error técnico de bajo nivel que lo originó, sin perder la información original.
```java
public class ServicioNoDisponibleException extends RuntimeException {
    public ServicioNoDisponibleException(String mensaje, Throwable causa) {
        super(mensaje, causa);
    }
}

public void obtenerDatos() {
    try {
        conectarBaseDatos();
    } catch (SQLException e) {
        throw new ServicioNoDisponibleException("No se pudo obtener datos", e);
    }
}
```

Cuando esta excepción llega a un manejador y se imprime (por ejemplo con `e.printStackTrace()`), la salida muestra **ambas excepciones**: primero la de alto nivel y debajo, etiquetada como `Caused by:`, la excepción original con su propia traza de pila. Esto hace que el diagnóstico sea completo sin que el llamador tenga que conocer los detalles internos de implementación.