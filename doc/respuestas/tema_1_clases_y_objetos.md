<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Clases y Objetos". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: ninguno.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->

# TEMA 1. Clases y objetos

## 1. ¿Cuáles son las cuatro características básicas de la programación orientada a objetos? Describe brevemente cada una

Las cuatro características esenciales suelen identificarse como abtracción, encapsulación, herencia y polimorfismo. 

La abstracción permite modelar un problema identificando solo los elementos relevantes, ocultando los detalles innecesarios. En Java, esto se consigue creando clases que representan conceptos del mundo real o del dominio del problema.

La encapsulación consiste en ocultar la implementación interna de los objetos y exponer solo lo necesario mediante una interfaz. Esto reduce errores y facilita modificar un componente sin afectar al resto del sistema. En Java se implementa mediante modificadores como public, private y protected.

La herencia permite crear nuevas clases basándose en otras existentes, reutilizando tanto la interfaz como la implementación. La clase derivada añade o modifica comportamiento, permitiendo representar jerarquías como “un Círculo es un Polígono”.

El polimorfismo hace posible tratar objetos de diferentes clases derivadas como si fuesen del mismo tipo base y que ejecuten el comportamiento correcto en tiempo de ejecución. Esto permite escribir código más genérico, flexible y extensible.

## 2. Cita cuatro lenguajes populares que permitan la programación orientada a objetos

Existen muchos lenguajes que incorporan características orientadas a objetos, pero cuatro de los más conocidos son Java, C++, Python y C#.

## 3. Los paradigmas anteriores a la POO, ¿Qué es la **programación estructurada**? y, todavía mejor, ¿Qué es la **programación modular**?

La programación estructurada se basa en dividir un programa en funciones y en controlar el flujo mediante estructuras claras como if, while o for.

La programación modular va un paso más allá separando el programa en módulos independientes, cada uno con sus datos y funciones relacionadas. Esto mejora la mantenibilidad y permite trabajar de forma más organizada.

## 4. ¿Qué tres elementos definen a un objeto en programación orientada a objetos?

Un objeto se define principalmente por su estado, su comportamiento y su identidad. 

El estado corresponde a los datos que almacena internamente, usualmente representados por atributos o campos.

El comportamiento es el conjunto de operaciones que puede realizar, descrito mediante métodos que se pueden invocar desde otros objetos. 

Por último, la identidad permite diferenciar un objeto de otro, incluso aunque ambos tengan el mismo estado. Esta identidad se materializa en Java mediante la referencia que apunta al objeto en memoria.

## 5. ¿Qué es una clase? ¿Es lo mismo que un objeto? ¿Qué es una instancia? ¿Todos los lenguajes orientados a objetos manejan el concepto de clase?

Una clase es un tipo que define qué datos y qué operaciones tendrán los objetos creados a partir de ella. Funciona como un molde o plantilla. 

Un objeto, en cambio, es una instancia concreta de esa clase, es decir, una copia real creada en memoria con valores propios.

Una instancia es simplemente un objeto concreto creado a partir de una clase. 

Aunque en la mayor parte de lenguajes orientados a objetos se trabaja con clases, no todos utilizan este concepto estrictamente.

## 6. ¿Dónde se almacenan en memoria los objetos? ¿Es igual en todos los lenguajes? ¿Qué es la **recolección de basura**? 

En Java, todos los objetos se almacenan en una zona especial de memoria llamada heap (montículo). Cuando se usa new, Java reserva espacio en esa zona y coloca allí el objeto. Esto significa que su tamaño y su duración no necesitan conocerse en tiempo de compilación, sino que se deciden mientras el programa se ejecuta.
Para acceder a un objeto del heap, Java utiliza referencias, que son variables que se guardan en la pila (stack). Estas referencias apuntan al objeto real. Lo importante es que la referencia vive en el stack, pero el objeto vive en el heap.

Ejemplo sencillo:
Punto p = new Punto();
– p → vive en el stack
– el objeto new Punto() → vive en el heap

No. Esto depende del lenguaje:

En C++, los objetos pueden:

-Crearse en el stack
Punto p;  // vive en el stack

-O en el heap usando new
Punto* p = new Punto();  // vive en el heap

Esto permite optimizar rendimiento, pero también implica mayor responsabilidad: el programador debe decidir dónde se almacena el objeto y cuándo destruirlo.
En cambio, Java siempre crea objetos en el heap y gestiona su liberación automáticamente.

La recolección de basura (garbage collection) es un mecanismo automático de Java que:

-Detecta objetos que ya no están siendo usados (nadie tiene referencias hacia ellos).
-Libera su memoria automáticamente.

## 7. ¿Qué es un método? ¿Qué es la **sobrecarga de métodos**? 

Un método es una función asociada a un objeto o clase, encargada de definir acciones que ese objeto puede realizar. En Java forman parte fundamental de la interfaz que cada clase ofrece a otros componentes.

La sobrecarga de métodos consiste en definir varios métodos con el mismo nombre pero con firmas distintas, es decir, diferentes parámetros. Esto permite ampliar el comportamiento sin tener que inventar nuevos nombres y favorece una interfaz más coherente.

## 8. Ejemplo mínimo de clase en Java, que se llame Punto, con dos atributos, x e y, con un método que se llame `calculaDistanciaAOrigen`, que calcule la distancia a la posición 0,0. Por sencillez, los atributos deben tener visibilidad por defecto. Crea además un ejemplo de uso con una instancia y uso del método

class Punto {
    int x;
    int y;

    double calculaDistanciaAOrigen() {
        return Math.sqrt(x * x + y * y);
    }
}

class EjemploUso {
    public static void main(String[] args) {
        Punto p = new Punto();
        p.x = 3;
        p.y = 4;
        System.out.println(p.calculaDistanciaAOrigen());
    }
}


## 9. ¿Cuál es el punto de entrada en un programa en Java? ¿Qué es `static` y para qué vale? ¿Sólo se emplea para ese método `main`? ¿Para qué se combina con `final`?

En Java, todo programa comienza su ejecución en un método muy concreto:

public static void main(String[] args)

Este método funciona como punto de entrada, es decir, es el primer método que la Máquina Virtual de Java (JVM) ejecuta cuando se inicia un programa. Sin este método, la JVM no sabría dónde empezar.

La palabra static indica que un método o atributo pertenece a la clase, no a un objeto concreto.
Esto significa que se puede usar sin crear una instancia.
Por eso el main debe ser static:
La JVM necesita poder ejecutarlo sin construir primero un objeto de la clase que lo contiene.

No. static es útil en muchas otras situaciones:

-Para crear métodos de utilidad (como en Math.sqrt()).
-Para crear atributos compartidos por todas las instancias de una clase.
-Para definir bloques estáticos que se ejecutan una sola vez cuando se carga la clase.

Es decir, static sirve para cualquier elemento que pertenezca al concepto general de la clase y no a un objeto concreto.

La combinación: (static final) se usa para declarar constantes, es decir, valores que:

-pertenecen a la clase (por static).
-no cambian nunca (por final).

## 10. Intenta ejecutar un poco de Java de forma básica, con los comandos `javac` y `java`. ¿Cómo podemos compilar el programa y ejecutarlo desde linea de comandos? ¿Java es compilado? ¿Qué es la **máquina virtual**? ¿Qué es el *byte-code* y los ficheros `.class`?

Para compilar un archivo Java desde la línea de comandos se utiliza:

javac MiPrograma.java

Esto produce un archivo .class. Para ejecutarlo:

java MiPrograma

Java emplea un proceso híbrido: compila a un formato intermedio llamado bytecode, que será interpretado y optimizado en tiempo de ejecución por la Máquina Virtual de Java (JVM). Esta máquina virtual permite la portabilidad del código entre diferentes plataformas.

## 11. En el código anterior de la clase `Punto` ¿Qué es `new`? ¿Qué es un **constructor**? Pon un ejemplo de constructor en una clase `Empleado` que tenga DNI, nombre y apellidos

La palabra clave new crea un objeto en memoria y devuelve una referencia al mismo. Asociado a esta creación se invoca un constructor, que es un método especial cuya función es inicializar el objeto recién creado.

class Empleado {
    String dni;
    String nombre;
    String apellidos;

    Empleado(String dni, String nombre, String apellidos) {
        this.dni = dni;
        this.nombre = nombre;
        this.apellidos = apellidos;
    }
}

## 12. ¿Qué es la referencia `this`? ¿Se llama igual en todos los lenguajes? Pon un ejemplo del uso de `this` en la clase `Punto`

La referencia this señala al objeto actual dentro de un método o constructor. Se utiliza para distinguir atributos de parámetros con el mismo nombre o para encadenar constructores.

Muchos lenguajes orientados a objetos incorporan una referencia equivalente, aunque puede variar su nombre. En C++ es también this, mientras que en Python se utiliza self. Ejemplo en Punto:

double distanciaA(Punto otro) {
    return Math.sqrt(
        (this.x - otro.x) * (this.x - otro.x) +
        (this.y - otro.y) * (this.y - otro.y)
    );
}

## 13. Añade ahora otro nuevo método que se llame `distanciaA`, que reciba un `Punto` como parámetro y calcule la distancia entre `this` y el punto proporcionado

class Punto {
    int x;
    int y;

    double distanciaA(Punto otro) {
        int dx = this.x - otro.x;
        int dy = this.y - otro.y;
        return Math.sqrt(dx * dx + dy * dy);
    }
}

## 14. El paso del `Punto` como parámetro a un método, es **por copia** o **por referencia**, es decir, si se cambia el valor de algún atributo del punto pasado como parámetro, dichos cambios afectan al objeto fuera del método? ¿Qué ocurre si en vez de un `Punto`, se recibiese un entero (`int`) y dicho entero se modificase dentro de la función? 

En Java, todo se pasa por valor, pero cuando se trata de objetos, lo que se pasa “por valor” es la referencia. Esto implica que si se modifica un atributo del objeto dentro de un método, el cambio será visible desde fuera, pues ambos apuntan al mismo objeto.
En cambio, los tipos primitivos como int se pasan por copia del valor real. Por tanto, modificar el parámetro dentro de un método no afecta al original.

## 15. ¿Qué es el método `toString()` en Java? ¿Existe en otros lenguajes? Pon un ejemplo de `toString()` en la clase `Punto` en Java

El método toString() forma parte de la clase base Object y devuelve una representación textual del objeto. Sobrescribirlo facilita la depuración y la impresión de objetos.

Otros lenguajes también incorporan mecanismos similares, como __str__ en Python o toString() en C#.

Ejemplo:

@Override
public String toString() {
    return "(" + x + ", " + y + ")";
}

## 16. Reflexiona: ¿una clase es como un `struct` en C? ¿Qué le falta al `struct` para ser como una clase y las variables de ese tipo ser instancias?

Un struct de C es conceptualmente similar solo en que agrupa datos, pero carece de la capacidad de integrar funciones, control de accesos, herencia, polimorfismo y otros mecanismos propios de la POO. Además, un struct no puede definir comportamiento ni gestionar su propia inicialización o destrucción. Por tanto, aunque ambos agrupan datos, la clase es mucho más completa como unidad de abstracción.

## 17. Quitemos un poco de magia a todo esto: ¿Como se podría “emular”, con `struct` en C, la clase `Punto`, con su función para calcular la distancia al origen? ¿Qué ha pasado con `this`?

En C se puede simular una clase utilizando un struct junto con funciones externas que operan sobre él. Sin embargo, no existe un equivalente directo a this, por lo que debe pasarse explícitamente un puntero al objeto.

Ejemplo aproximado:

typedef struct {
    int x;
    int y;
} Punto;

double distanciaAOrigen(Punto* p) {
    return sqrt(p->x * p->x + p->y * p->y);
}

Aquí, el puntero p actúa como sustituto manual de this.
