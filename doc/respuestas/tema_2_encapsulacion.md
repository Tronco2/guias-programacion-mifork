<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Encapsulación". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 2. Encapsulación

## 1. En Programación Orientada a Objetos (POO), ¿Qué buscan la **encapsulación** y **la ocultación** de información? Enumera brevemente algunas ventajas de la ocultación de información.

Encapsulación es el principio de agrupar datos (estado) y los métodos que operan sobre ellos dentro de una misma unidad (clase u objeto), de modo que el objeto actúe como un módulo autónomo con una interfaz pública bien definida. 

Ocultamiento de información (information hiding) consiste en restringir el acceso directo al estado interno de esa unidad, exponiendo sólo lo necesario a través de la interfaz pública. El objetivo conjunto es separar qué hace una clase (su interfaz) de cómo lo hace (su implementación), permitiendo cambiar la implementación sin afectar a los usuarios de la clase.

Ventajas de la ocultación de información:

-Mantenimiento más sencillo: cambiar la implementación interna no obliga a modificar el código cliente si la interfaz pública se mantiene.

-Reducción del acoplamiento: menos dependencias entre módulos; facilita la reutilización y la evolución del sistema.

-Protección de invariantes: la clase puede garantizar condiciones internas (por ejemplo, saldo nunca negativo) porque controla todas las modificaciones.

-Mejor seguridad y control de acceso: evita lecturas/escrituras indebidas desde fuera de la clase; útil para datos sensibles.

-Interfaz más clara y estable: los usuarios ven sólo lo necesario; la API es más fácil de entender y menos propensa a uso incorrecto. 

-Facilita pruebas y depuración: al tener puntos de acceso controlados (métodos), es más sencillo instrumentar, validar y aislar comportamientos. 


## 2. ¿Qué se entiende por la **interfaz pública** de un objeto o clase en POO? Describe brevemente cómo se relaciona con la ocultación de información.

La interfaz pública de una clase es el conjunto de miembros (métodos, propiedades y eventos) que la clase expone intencionadamente para que otros objetos o módulos puedan interactuar con ella; incluye nombres, parámetros, tipos de retorno y las reglas de uso (contratos) que definen qué se puede hacer, pero no cómo está implementado internamente.

La interfaz pública es la cara visible que permite usar un objeto; el ocultamiento de información (information hiding) es la práctica de mantener privados los detalles internos que no forman parte de esa cara. Juntas separan qué hace la clase de cómo lo hace, de modo que la implementación pueda cambiar sin romper a los clientes. 


## 3. Brevemente: ¿Por qué hay que ser conscientes y diseñar con cuidado la **interfaz pública** de una clase? ¿Es fácil cambiarla?

La interfaz pública es el contrato que otros módulos usan para interactuar con tu clase; por eso su diseño condiciona la estabilidad, mantenibilidad y evolución del sistema. Una interfaz mal pensada obliga a cambios frecuentes en código cliente, aumenta el acoplamiento y eleva el coste de mantenimiento.

No suele ser fácil cambiarla cuando la interfaz ya tiene consumidores. Si la clase forma parte de una API usada por muchos módulos o equipos, cualquier cambio requiere coordinación, pruebas y a menudo versiones nuevas. Cambios pequeños y bien planificados pueden hacerse, pero los cambios incompatibles son costosos y deben evitarse o gestionarse mediante estrategias de evolución. 


## 4. ¿Qué son las **invariantes de clase** y por qué la ocultación de información nos ayuda?

Una invariante de clase es un conjunto de condiciones o propiedades que deben ser siempre verdaderas para el estado interno de cada objeto de una clase.

Ejemplos sencillos

Cuenta bancaria: saldo >= 0 y moneda no nula.

Rango: min <= max.

La ocultación de información ayuda: 

-Control de cambios: si los atributos son privados, sólo los métodos de la clase pueden modificarlos, por tanto todas las comprobaciones necesarias se hacen en un único lugar.

-Evita cambios externos inválidos: código fuera de la clase no puede poner los datos en un estado incorrecto porque no tiene acceso directo a los campos.

-Operaciones seguras: la clase expone métodos que realizan validaciones antes de cambiar el estado, de modo que cualquier uso legítimo respeta las reglas internas.


## 5. Pon un ejemplo de una clase `Punto` en `Java`, con dos coordenadas, `x` e `y`, de tipo `double`, con un método `calcularDistanciaAOrigen`, y que haga uso de la ocultación de información. ¿Cuál es la interfaz pública de la clase `Punto`? ¿Qué significa `public` y `private`?

public class Punto {
    private double x; // coordenada x oculta
    private double y; // coordenada y oculta

    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    public double getX() { return x; }
    public double getY() { return y; }

    public void setX(double x) { this.x = x; }
    public void setY(double y) { this.y = y; }

    public double calcularDistanciaAOrigen() {
        return Math.hypot(x, y); // sqrt(x^2 + y^2) de forma segura
    }

    @Override
    public String toString() {
        return String.format("Punto(%.3f, %.3f)", x, y);
    }
}



## 6. En Java, ¿A quiénes se pueden aplicar los modificadores `public` o `private`?

En Java, los modificadores de acceso pueden aplicarse a clases (incluyendo clases anidadas), atributos, métodos y constructores. Es decir, prácticamente a cualquier miembro que se declare dentro de una clase, además de a la propia clase en sí.Cuando se aplica public a una clase de nivel superior (top-level), ésta es accesible desde cualquier parte del programa. Cuando se aplica a un atributo, método o constructor, indica que puede ser usado desde cualquier otra clase. Por su parte, private restringe el acceso exclusivamente al interior de la propia clase donde se declara: ningún código exterior puede referenciarlo directamente, independientemente del paquete o proyecto al que pertenezca.


## 7. En POO, la visibilidad puede ser pública o privada, pero ¿existen más tipos de visibilidad? ¿Qué ocurre en Java? ¿Y en otros lenguajes?

En Java existen cuatro niveles de visibilidad, no solo dos. De menor a mayor exposición son: private (solo la propia clase), package-private (sin modificador explícito, accesible para todas las clases del mismo paquete), protected (package-private más las subclases, concepto de herencia que se verá más adelante) y public (accesible desde cualquier lugar).En otros lenguajes la situación varía. C++ ofrece private, protected y public, y además la palabra clave friend, que permite a una clase o función concreta acceder a los miembros privados de otra. C# añade internal (visible dentro del mismo ensamblado), protected internal y private protected, que combinan herencia y ensamblado. Python no tiene modificadores formales: usa convención de nombres (_nombre sugiere "interno" y __nombre aplica name mangling para dificultar el acceso externo, pero no lo impide de verdad). La idea central de visibilidad es común a todos, aunque los mecanismos concretos difieren.


## 8. Responde: Los miembros de instancia privados de un objeto están ocultos para (a) otras clases o (b) otras instancias, aunque sean de la misma clase. Pon un ejemplo añadiendo un método `calcularDistanciaAPunto(Punto otro)` y explica la respuesta.

La privacidad en Java es a nivel de clase, no a nivel de instancia. Esto significa que un objeto de tipo Punto puede acceder directamente a los atributos privados de otro objeto que también sea de tipo Punto, siempre que el acceso se realice desde código dentro de la propia clase Punto. Lo que private prohíbe es el acceso desde otras clases, no desde otras instancias de la misma.Esto resulta muy útil en métodos que operan sobre dos objetos de la misma clase, como el cálculo de distancia entre dos puntos. A continuación se muestra el ejemplo:

### Ejemplo:
    public double calcularDistanciaAPunto(Punto otro) {
        // Se accede a otro.x y otro.y directamente porque estamos
        // dentro de la clase Punto, aunque "otro" sea una instancia distinta.
        double dx = this.x - otro.x;
        double dy = this.y - otro.y;
        return Math.hypot(dx, dy);
    }
Si los atributos x e y fueran de otra clase (por ejemplo, Figura), el acceso directo estaría prohibido y habría que usar los getters. Pero al ser Punto quien accede a campos privados de Punto, Java lo permite sin restricciones.


## 9. ¿Qué son los métodos "getter" y "setter" en los lenguajes orientados a objetos?

Los métodos getter y setter son métodos públicos que actúan como punto de acceso controlado a los atributos privados de una clase. Un getter (getX(), getNombre()…) devuelve el valor de un atributo; un setter (setX(double x), setNombre(String n)…) lo modifica. Ambos siguen por convención la nomenclatura get/set seguida del nombre del atributo con la primera letra en mayúscula.
La ventaja frente a declarar el atributo directamente como público es que estos métodos permiten añadir lógica adicional: el getter puede devolver una copia defensiva del valor o calcularlo sobre la marcha; el setter puede validar que el nuevo valor cumple las invariantes antes de asignarlo (por ejemplo, rechazar un radio negativo). De este modo, la clase conserva el control total sobre su estado interno aunque exponga una vía de acceso al exterior.


## 10. Cuando nos referimos a que la ocultación de información mejora la "seguridad" del programa, ¿nos referimos a que no pueda ser "hackeado"?

No. En el contexto de la POO, "seguridad" se refiere a la integridad y corrección del estado interno de los objetos, no a la ciberseguridad o protección frente a ataques externos. La ocultación evita que otras partes del programa coloquen un objeto en un estado inválido por accidente o por desconocimiento de sus invariantes.
La seguridad frente a ataques es una disciplina completamente distinta que depende de factores como la arquitectura del sistema, la gestión de credenciales, el cifrado o la validación de entradas. La encapsulación contribuye indirectamente a un código más robusto y predecible, lo que facilita auditar y razonar sobre él, pero por sí sola no protege contra vulnerabilidades de red, inyecciones u otros vectores de ataque típicos de la seguridad informática.


## 11. ¿Qué diferencia hay entre **miembro de instancia** y **miembro de clase**? ¿Los miembros de clase también se pueden ocultar?

Un miembro de instancia (atributo o método sin static) existe de forma independiente en cada objeto creado: cada Punto tiene su propia x y su propia y. Un miembro de clase (declarado con static) es compartido por todos los objetos de esa clase y existe aunque no se haya creado ninguna instancia; pertenece a la clase en sí, no a los objetos individuales. En C, el equivalente más cercano sería una variable static local a un fichero.
Los miembros de clase también admiten modificadores de acceso, por lo que pueden ser public, private o cualquier otro nivel. No hay ningún motivo para exponerlos todos: un atributo static que almacena un contador interno, o una constante de configuración que solo usa la propia clase, deben declararse private static igual que se haría con cualquier dato de implementación.


## 12. Brevemente: ¿Tiene sentido que los constructores sean privados?

Sí, tiene sentido en varios escenarios concretos. El más conocido es el patrón Singleton, donde se quiere garantizar que solo existe una única instancia de la clase; el constructor privado impide que nadie externo cree objetos directamente, y se ofrece en su lugar un método estático (getInstance()) que gestiona esa única instancia. También es habitual en clases utilitarias que solo contienen métodos estáticos (como Math en Java) y que no deben instanciarse en absoluto.
Otro caso frecuente es el patrón Factory Method: la clase tiene varios constructores privados y expone métodos estáticos públicos con nombres descriptivos que deciden internamente cuál usar. Esto ofrece más control y legibilidad que sobrecargar el constructor. En resumen, un constructor privado es una herramienta deliberada de diseño que restringe o delega la creación de objetos.


## 13. ¿Cómo se indican los **miembros de clase** en Java? Pon un ejemplo, en la clase `Punto` definida anteriormente, para que incluya miembros de clase que permitan saber cuáles son los valores `x` e `y` máximos que se han establecido en todos los puntos que se hayan creado hasta el momento.

Los miembros de clase se declaran con la palabra clave static. Al ser parte de la clase y no de cada objeto, se accede a ellos normalmente a través del nombre de la clase (Punto.getMaxX()), aunque Java también permite acceder a través de una instancia (práctica desaconsejada porque induce a confusión).
A continuación se muestra la clase Punto ampliada con miembros static que rastrean los valores máximos de x e y registrados en todos los puntos creados:

### 
    public class Punto {
        private double x;
        private double y;

        // Miembros de clase (static): compartidos por todos los objetos Punto
        private static double maxX = Double.NEGATIVE_INFINITY;
        private static double maxY = Double.NEGATIVE_INFINITY;

        public Punto(double x, double y) {
            this.x = x;
            this.y = y;
            // Se actualizan los máximos globales al crear cada punto
            if (x > maxX) maxX = x;
            if (y > maxY) maxY = y;
        }

        public double getX() { return x; }
        public double getY() { return y; }

        public void setX(double x) {
            this.x = x;
            if (x > maxX) maxX = x;
        }

        public void setY(double y) {
            this.y = y;
            if (y > maxY) maxY = y;
        }

        // Getters estáticos: se accede con Punto.getMaxX()
        public static double getMaxX() { return maxX; }
        public static double getMaxY() { return maxY; }

        public double calcularDistanciaAOrigen() {
            return Math.hypot(x, y);
        }

        public double calcularDistanciaAPunto(Punto otro) {
            return Math.hypot(this.x - otro.x, this.y - otro.y);
        }

        @Override
        public String toString() {
            return String.format("Punto(%.3f, %.3f)", x, y);
        }
    }

Los atributos maxX y maxY son private static: son de clase (compartidos) pero ocultos. Solo se exponen mediante los getters estáticos públicos. Nótese que se inicializan a Double.NEGATIVE_INFINITY para que cualquier valor real los supere en la primera comparación.

## 14. Como sería un método factoría dentro de la clase `Punto` para construir un `Punto` a partir de dos coordenadas, pero que las redondee al entero más cercano. Escribe sólo el código del método, no toda la clase ¿Has usado `static`? 

Un método factoría es un método estático que construye y devuelve un objeto de la propia clase, ofreciendo una alternativa o complemento al constructor. En este caso, recibe dos double, los redondea al entero más cercano y crea el Punto resultante:

###
    public static Punto desdeCoordenadасRedondeadas(double x, double y) {
        return new Punto(Math.round(x), Math.round(y));
    }

Sí, se usa static: el método debe poder invocarse sin que exista todavía ningún objeto Punto (precisamente su función es crear uno). Math.round devuelve un long, que se convierte implícitamente a double al pasarlo al constructor. El uso sería Punto p = Punto.desdeCoordenadасRedondeadas(3.7, -1.2);, obteniendo Punto(4.0, -1.0).


## 15. Cambia la implementación de `Punto`. En vez de dos `double`, emplea un array interno de dos posiciones, intentando no modificar la interfaz pública de la clase.

La clave de la ocultación de información es precisamente esta: se puede sustituir la implementación interna por completo sin que el código que usa la clase tenga que cambiar nada, siempre que la interfaz pública permanezca intacta.

###
    public class Punto {
        // Nueva implementación interna: array en vez de dos doubles
        private double[] coords; // coords[0] = x, coords[1] = y

        private static double maxX = Double.NEGATIVE_INFINITY;
        private static double maxY = Double.NEGATIVE_INFINITY;

        public Punto(double x, double y) {
            coords = new double[]{x, y};
            if (x > maxX) maxX = x;
            if (y > maxY) maxY = y;
        }

        public double getX() { return coords[0]; }
        public double getY() { return coords[1]; }

        public void setX(double x) {
            coords[0] = x;
            if (x > maxX) maxX = x;
        }

        public void setY(double y) {
            coords[1] = y;
            if (y > maxY) maxY = y;
        }

        public static double getMaxX() { return maxX; }
        public static double getMaxY() { return maxY; }

        public double calcularDistanciaAOrigen() {
            return Math.hypot(coords[0], coords[1]);
        }

        public double calcularDistanciaAPunto(Punto otro) {
            return Math.hypot(this.coords[0] - otro.coords[0],
                            this.coords[1] - otro.coords[1]);
        }

        public static Punto desdeCoordenadасRedondeadas(double x, double y) {
            return new Punto(Math.round(x), Math.round(y));
        }

        @Override
        public String toString() {
            return String.format("Punto(%.3f, %.3f)", coords[0], coords[1]);
        }
    }

Todo el código cliente que usaba Punto (llamadas a getX(), calcularDistanciaAOrigen(), etc.) sigue funcionando sin modificación. Solo ha cambiado lo que está dentro de la clase: eso es exactamente la promesa de la ocultación de información.


## 16. Si un atributo va a tener un método "getter" y "setter" públicos, ¿no es mejor declararlo público? ¿Cuál es la convención más habitual sobre los atributos, que sean públicos o privados? ¿Tiene esto algo que ver con las "invariantes de clase"?

No, aunque pueda parecer equivalente. La diferencia fundamental es que los métodos permiten interponer lógica entre el exterior y el dato: el setter puede validar el valor antes de asignarlo, registrar el cambio, notificar a otros componentes o calcular un valor derivado; el getter puede devolver una copia, formatear el dato o calcularlo al vuelo. Si el atributo es público, no existe ese punto de control y cualquier modificación es directa e incontrolada.
La convención ampliamente aceptada es declarar todos los atributos como private y exponer solo los accesos que sean realmente necesarios. Esto tiene relación directa con las invariantes de clase: un setter puede rechazar valores que violarían una invariante (por ejemplo, impedir que el radio de un círculo sea negativo), algo imposible si el atributo es público.


## 17. ¿Qué significa que una clase sea **inmutable**? ¿qué es un método modificador? ¿Un método modificador es siempre un "setter"? ¿Tiene ventajas que una clase sea inmutable?

Una clase inmutable es aquella cuyos objetos no pueden cambiar de estado después de ser creados. Todos sus atributos se fijan en el constructor y no se ofrecen mecanismos para alterarlos posteriormente. La clase String de Java es el ejemplo canónico.
Un método modificador es cualquier método que altera el estado interno de un objeto, no necesariamente un setter con ese nombre. Por ejemplo, StringBuilder.append(...) es un método modificador aunque no se llame set. En una clase inmutable no existen métodos modificadores: los métodos que en otras clases modificarían el objeto aquí devuelven un nuevo objeto con el resultado, dejando el original intacto.
Las ventajas de la inmutabilidad son relevantes: los objetos inmutables son inherentemente seguros en entornos concurrentes (no hay condiciones de carrera si nadie puede modificarlos), son fáciles de razonar sobre ellos, pueden compartirse sin riesgo de efectos colaterales y se pueden usar como claves en colecciones basadas en hash sin problemas.


## 18. ¿Es recomendable incluir métodos "setter" siempre y como convención?

No. Añadir setters de forma indiscriminada introduce mutabilidad innecesaria y puede comprometer las invariantes de la clase. La recomendación general es incluir un setter solo cuando exista una razón de diseño clara para que ese atributo pueda modificarse tras la construcción del objeto.
Si una clase no necesita modificar un atributo una vez creado, lo correcto es no ofrecer el setter y declarar el atributo además como final. Exponer setters por costumbre hace que la clase sea más difícil de razonar, más propensa a errores y más difícil de usar en contextos concurrentes. La mutabilidad debe ser una decisión consciente, no la opción predeterminada.


## 19. ¿La clase `String` en Java es mutable o inmutable? ¿Qué ocurre al concatenar dos cadenas? ¿Qué debemos hacer si vamos a hacer una operación que implique concatenar muchas veces para construir paso a paso una cadena muy larga?

String en Java es inmutable: una vez creado un objeto String, su contenido no puede cambiar. Cuando se aplica el operador + para concatenar cadenas, no se modifica ninguna de las cadenas originales; lo que ocurre es que Java crea un nuevo objeto String con el resultado. Esto significa que una concatenación en bucle de n iteraciones puede generar hasta n objetos temporales, lo cual es muy ineficiente en memoria y tiempo.
Cuando se necesita construir una cadena de forma incremental (por ejemplo, leer líneas y acumularlas), la solución es usar StringBuilder, que sí es mutable y realiza las operaciones en un buffer interno sin crear objetos intermedios. Al terminar, se convierte a String con toString(). Su uso es significativamente más eficiente para operaciones que implican muchas concatenaciones:

###
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i < 10000; i++) {
        sb.append("línea ").append(i).append("\n");
    }
    String resultado = sb.toString();


## 20. En POO ¿Cómo se comparan objetos de una misma clase? ¿Por su contenido o por su identidad? ¿Qué es el método equals en Java? ¿Qué hace por defecto? ¿Cómo se deben comparar dos cadenas en Java? 

En Java, el operador == aplicado a referencias de objeto compara identidad: devuelve true solo si las dos variables apuntan exactamente al mismo objeto en memoria, no si dos objetos distintos tienen el mismo contenido. Esto contrasta con C, donde == sobre punteros también compara direcciones, de forma análoga.
El método equals(Object o), heredado de la clase Object, está pensado para comparar contenido (equivalencia lógica). Sin embargo, su implementación por defecto en Object hace exactamente lo mismo que == (compara referencias). Para que sea útil, cada clase debe sobreescribirlo con su propio criterio de igualdad. Por ejemplo, en Punto se consideraría que dos puntos son iguales si tienen las mismas coordenadas:

###
    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (!(obj instanceof Punto)) return false;
        Punto otro = (Punto) obj;
        return Double.compare(this.getX(), otro.getX()) == 0 &&
            Double.compare(this.getY(), otro.getY()) == 0;
    }

Las cadenas en Java deben compararse siempre con .equals(), nunca con ==. Dos objetos String con el mismo texto pueden ser objetos distintos en memoria, por lo que == devolvería false aunque el contenido sea idéntico. El método equals de String sí compara el contenido carácter a carácter.


## 21. ¿Qué son las clases "wrapper" en un lenguaje de programación orientado a objetos? ¿Cómo se hace? ¿Es un proceso automático? ¿Qué ventajas tienen? ¿Todos los lenguajes orientados a objetos tienen tipos primitivos y necesitan wrappers? 

Las clases wrapper (o clases envoltorio) son clases que encapsulan un tipo primitivo dentro de un objeto, permitiendo tratarlo como tal. En Java, los tipos primitivos (int, double, boolean, char…) no son objetos y no pueden usarse en contextos que requieran un objeto (por ejemplo, colecciones genéricas como ArrayList<Integer>). Para eso existen Integer, Double, Boolean, Character, etc.
El proceso de conversión entre primitivo y wrapper es en gran parte automático en Java gracias al mecanismo de autoboxing (primitivo → objeto) y unboxing (objeto → primitivo), introducido en Java 5. El compilador inserta las conversiones necesarias de forma transparente: se puede escribir Integer n = 42; y Java genera internamente Integer.valueOf(42).
Las ventajas son varias: permiten usar primitivos en colecciones genéricas, ofrecen métodos de utilidad (Integer.parseInt, Double.isNaN, Integer.MAX_VALUE…) y permiten representar la ausencia de valor mediante null. No todos los lenguajes orientados a objetos necesitan wrappers: en Python, Ruby o Kotlin, todo (incluso los números) es un objeto desde el principio, por lo que esta distinción no existe.


## 22. ¿En POO qué es un **tipo de dato enumerado**? ¿En Java, un tipo de dato enumerado es una clase? ¿Qué ventajas tienen en términos de encapsulación los enumerados en Java?

Un tipo enumerado (enum) es un tipo de dato que solo puede tomar un conjunto fijo y predefinido de valores. En lugar de usar constantes enteras (int MES_ENERO = 1;) con todos sus riesgos (se puede pasar cualquier entero por error), un enumerado garantiza en tiempo de compilación que el valor pertenece al conjunto válido.
En Java, un enum es efectivamente una clase especial. Cada valor del enumerado es una instancia única (singleton) de esa clase, y la clase puede tener atributos privados, constructores (siempre privados o package-private) y métodos como cualquier otra clase. Esto lo diferencia profundamente de los enum de C, que son solo alias de enteros.
Las ventajas en términos de encapsulación son notables: el comportamiento asociado a cada valor puede vivir dentro del propio enumerado (métodos propios por valor mediante cuerpos de clase anónimos), los datos internos pueden ocultarse con private, y la interfaz pública define exactamente qué se puede hacer con cada valor. Todo el conocimiento sobre un "mes" o un "estado" queda centralizado en su enum, sin dispersarse por el código.


## 23. Crea un tipo enumerado en Java que se llame `Mes`, con doce posibles instancias y que además proporcione métodos para obtener cuántos días tiene ese mes, el ordinal de ese mes en el año (1-12), empleando atributos privados y constructores del tipo enumerado.

### 
    public enum Mes {

        ENERO  (31,  1),
        FEBRERO(28,  2),   // Se simplifica: 28 días (sin gestión de año bisiesto)
        MARZO  (31,  3),
        ABRIL  (30,  4),
        MAYO   (31,  5),
        JUNIO  (30,  6),
        JULIO  (31,  7),
        AGOSTO (31,  8),
        SEPTIEMBRE(30, 9),
        OCTUBRE   (31, 10),
        NOVIEMBRE (30, 11),
        DICIEMBRE (31, 12);

        private final int diasDelMes;
        private final int ordinalDelMes;

        // El constructor de un enum siempre es implícitamente privado
        Mes(int diasDelMes, int ordinalDelMes) {
            this.diasDelMes   = diasDelMes;
            this.ordinalDelMes = ordinalDelMes;
        }

        public int getDiasDelMes()    { return diasDelMes; }
        public int getOrdinalDelMes() { return ordinalDelMes; }
    }

El constructor de un enum en Java no puede ser public: es implícitamente private (o, a lo sumo, package-private). Java impide que nadie externo cree nuevas instancias: las únicas instancias posibles son las doce declaradas. Los atributos diasDelMes y ordinalDelMes se declaran final porque no tienen sentido modificarlos tras la creación.
Nótese que ordinalDelMes (1–12) es distinto del método ordinal() que hereda todo enum de Java, el cual devuelve la posición en la declaración empezando en 0. Usar un atributo propio evita la confusión y hace la interfaz más explícita.


## 24. Añade a la clase `Mes` del ejercicio anterior cuatro métodos para devolver si ese mes tiene algunos días de invierno, primavera, verano u otoño, indicando con un booleano el hemisferio (norte o sur, parámetro `enHemisferioNorte`). Es decir: `esDePrimavera(boolean esHemisferioNorte)`, `esDeVerano(boolean esHemisferioNorte)`, `esDeOtoño(boolean esHemisferioNorte)`, `esDeInvierno(boolean esHemisferioNorte)`

Se añaden cuatro métodos al enumerado del ejercicio anterior. En el hemisferio norte las estaciones son: invierno (dic–feb), primavera (mar–may), verano (jun–ago) y otoño (sep–nov). En el hemisferio sur están desplazadas seis meses:

###
    public boolean esDeInvierno(boolean esHemisferioNorte) {
        if (esHemisferioNorte) {
            return this == DICIEMBRE || this == ENERO || this == FEBRERO;
        } else {
            return this == JUNIO || this == JULIO || this == AGOSTO;
        }
    }

    public boolean esDePrimavera(boolean esHemisferioNorte) {
        if (esHemisferioNorte) {
            return this == MARZO || this == ABRIL || this == MAYO;
        } else {
            return this == SEPTIEMBRE || this == OCTUBRE || this == NOVIEMBRE;
        }
    }

    public boolean esDeVerano(boolean esHemisferioNorte) {
        if (esHemisferioNorte) {
            return this == JUNIO || this == JULIO || this == AGOSTO;
        } else {
            return this == DICIEMBRE || this == ENERO || this == FEBRERO;
        }
    }

    public boolean esDeOtoño(boolean esHemisferioNorte) {
        if (esHemisferioNorte) {
            return this == SEPTIEMBRE || this == OCTUBRE || this == NOVIEMBRE;
        } else {
            return this == MARZO || this == ABRIL || this == MAYO;
        }
    }

Dentro del enum, this hace referencia a la instancia concreta sobre la que se invoca el método (por ejemplo, JULIO), de modo que las comparaciones this == JULIO son completamente correctas: al ser singletons, la igualdad de instancia y la igualdad de contenido coinciden. El modelo simplifica la realidad (los cambios de estación ocurren a mediados de mes, no el día 1), pero es adecuado para representar si un mes "pertenece" a una estación.
