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

En Programación Orientada a Objetos (POO), la encapsulación busca agrupar datos (atributos) y comportamiento (métodos) relacionados en una misma clase, tratando al objeto como un módulo independiente. La ocultación de información pretende que los detalles internos de esa clase (atributos y métodos privados) no sean accesibles directamente desde fuera, sino a través de una interfaz controlada.

Algunas ventajas de la ocultación de información son:

Protección de la integridad de los datos, evitando estados inconsistentes.

Reducción del acoplamiento entre clases, ya que el resto del programa depende solo de la interfaz pública, no de la implementación interna.

Mayor facilidad de mantenimiento: se puede cambiar la implementación interna sin modificar el código que usa la clase.

Reutilización y modularidad: cada clase se convierte en un módulo claro y bien definido.


## 2. ¿Qué se entiende por la **interfaz pública** de un objeto o clase en POO? Describe brevemente cómo se relaciona con la ocultación de información.

La interfaz pública de un objeto o clase es el conjunto de métodos y, en algunos casos, atributos, que están accesibles desde el exterior y definen cómo se puede interactuar con ese objeto. Es, en cierto modo, la “fachada” que expone la clase al resto del programa.

La interfaz pública está directamente relacionada con la ocultación de información, porque:

Es la única manera de comunicarse con el objeto, mientras que los detalles internos (atributos privados, métodos auxiliares, etc.) se mantienen ocultos.

Permite que el resto del código conozca solo lo necesario para usar el objeto, sin saber cómo funciona internamente, lo que ayuda a reducir dependencias y errores.


## 3. Brevemente: ¿Por qué hay que ser conscientes y diseñar con cuidado la **interfaz pública** de una clase? ¿Es fácil cambiarla?

Hay que ser muy conscientes y cuidadosos al diseñar la interfaz pública porque:

Ella define cómo el resto del sistema usa la clase; si se cambia, puede afectar a muchas partes del programa.

Una interfaz mal diseñada provoca código difícil de entender, usar o mantener, y puede romper la abstracción buscada por la POO.

Cambiar la interfaz pública no es fácil:

Si otras clases ya dependen de esos métodos o atributos públicos, modificarlos puede romper el código existente o requerir grandes refactorizaciones.

Por eso, en POO se recomienda congelar la interfaz pública lo antes posible y, si se necesita cambiar, hacerlo mediante extensiones (nuevos métodos) en lugar de tocar los ya existentes.


## 4. ¿Qué son las **invariantes de clase** y por qué la ocultación de información nos ayuda?

Las invariantes de clase son condiciones o reglas que deben cumplirse siempre para que un objeto esté en un estado válido. Por ejemplo, un saldo bancario no puede ser negativo, o una fecha de nacimiento debe ser anterior a la fecha actual.

La ocultación de información ayuda a mantener estas invariantes porque:

Los atributos se declaran privados, de modo que no se pueden modificar directamente desde fuera, evitando que el objeto pase a un estado inconsistente.

Todo acceso se hace a través de métodos públicos (como getters y setters), donde se pueden incluir validaciones y controles antes de cambiar el estado interno.

Así, el código interno de la clase puede garantizar las invariantes sin exponer la lógica compleja al resto del programa.


## 5. Pon un ejemplo de una clase `Punto` en `Java`, con dos coordenadas, `x` e `y`, de tipo `double`, con un método `calcularDistanciaAOrigen`, y que haga uso de la ocultación de información. ¿Cuál es la interfaz pública de la clase `Punto`? ¿Qué significa `public` y `private`?

public class Punto {
    private double x;  // Oculto
    private double y;  // Oculto
    
    // Constructor público
    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }
    
    // Método público para calcular distancia al origen
    public double calcularDistanciaAOrigen() {
        return Math.sqrt(x * x + y * y);
    }
}
Interfaz pública de la clase Punto: Consta del constructor Punto(double x, double y) y del método calcularDistanciaAOrigen(). Estos son los únicos elementos accesibles desde fuera de la clase.

Significado de public y private:

public: El elemento es accesible desde cualquier clase en cualquier paquete.

private: El elemento solo es accesible desde dentro de la propia clase.




## 6. En Java, ¿A quiénes se pueden aplicar los modificadores `public` o `private`?

En Java, los modificadores public y private se pueden aplicar a:

Clases (top-level: solo public o por defecto; clases internas: todos).

Atributos (campos).

Métodos.

Constructores.

No se aplican directamente a paquetes (package), interfaces o parámetros de métodos.


## 7. En POO, la visibilidad puede ser pública o privada, pero ¿existen más tipos de visibilidad? ¿Qué ocurre en Java? ¿Y en otros lenguajes?

Sí, en POO existen más tipos de visibilidad además de pública (public) y privada (private):

Protegida (protected): Accesible en la clase, subclases y mismo paquete.

Paquete (por defecto, sin modificador): Accesible solo en clases del mismo paquete.

En Java: Hay cuatro niveles exactos: public, private, protected y paquete-private (default).

En otros lenguajes:

C#: Agrega internal (paquete) y protected internal.
​

C++: public, private, protected (similar a Java, sin paquete explícito)


## 8. Responde: Los miembros de instancia privados de un objeto están ocultos para (a) otras clases o (b) otras instancias, aunque sean de la misma clase. Pon un ejemplo añadiendo un método `calcularDistanciaAPunto(Punto otro)` y explica la respuesta.

Los miembros de instancia privados de un objeto están ocultos para (a) otras clases, pero NO para otras instancias de la misma clase.

Aquí va el ejemplo extendido de Punto con el método calcularDistanciaAPunto(Punto otro), que accede a los atributos privados x e y del parámetro otro (misma clase):
public class Punto {
    private double x;
    private double y;
    
    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }
    
    public double calcularDistanciaAOrigen() {
        return Math.sqrt(x * x + y * y);
    }
    
    // Nuevo método: accede a privados de 'otro' (misma clase)
    public double calcularDistanciaAPunto(Punto otro) {
        double deltaX = x - otro.x;  // Acceso directo a private de otro
        double deltaY = y - otro.y;  // Acceso directo a private de otro
        return Math.sqrt(deltaX * deltaX + deltaY * deltaY);
    }
}


## 9. ¿Qué son los métodos "getter" y "setter" en los lenguajes orientados a objetos?

Los métodos getter son funciones públicas que permiten leer el valor de un atributo privado de una clase, siguiendo la convención getNombreAtributo(). Los setter son métodos públicos que permiten modificar un atributo privado de forma controlada, con convención setNombreAtributo(tipo valor), incluyendo validaciones si es necesario. Ambos forman parte de la interfaz pública y facilitan la encapsulación al ocultar los detalles internos.


## 10. Cuando nos referimos a que la ocultación de información mejora la "seguridad" del programa, ¿nos referimos a que no pueda ser "hackeado"?

No, la "seguridad" por ocultación se refiere a la integridad del programa, protegiendo los datos contra modificaciones accidentales o inválidas que causen estados inconsistentes o errores. No impide hacking (que requiere medidas de ciberseguridad como cifrado), sino que garantiza que el objeto mantenga sus invariantes a través de accesos controlados.

## 11. ¿Qué diferencia hay entre **miembro de instancia** y **miembro de clase**? ¿Los miembros de clase también se pueden ocultar?

Un miembro de instancia (atributos o métodos con double x; o sin static) pertenece a cada objeto individual y tiene su propio valor por instancia. Un miembro de clase (static) pertenece a la clase misma, es compartido por todas las instancias y se accede vía nombre de clase. Sí, los miembros de clase también se pueden ocultar con private static, limitando su acceso solo dentro de la clase.


## 12. Brevemente: ¿Tiene sentido que los constructores sean privados?

Sí, tiene sentido: se usan en patrones como Singleton para controlar cuántas instancias se crean (solo una), o en clases de utilidad sin instanciación directa. Fuerza el uso de métodos fábrica públicos (static getInstance()) para crear objetos, manteniendo encapsulación y control sobre la creación.


## 13. ¿Cómo se indican los **miembros de clase** en Java? Pon un ejemplo, en la clase `Punto` definida anteriormente, para que incluya miembros de clase que permitan saber cuáles son los valores `x` e `y` máximos que se han establecido en todos los puntos que se hayan creado hasta el momento.

En Java, los miembros de clase se indican con la palabra clave static (pueden ser atributos static o métodos static), y pertenecen a la clase, no a las instancias.

Aquí está la clase Punto actualizada con dos miembros de clase privados maxX y maxY (inicializados a 0), que se actualizan en el constructor para rastrear los máximos valores de x e y en todas las instancias creadas:
public class Punto {
    private double x;
    private double y;
    
    // Miembros de clase (static)
    private static double maxX = Double.NEGATIVE_INFINITY;
    private static double maxY = Double.NEGATIVE_INFINITY;
    
    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
        if (x > maxX) maxX = x;  // Actualiza máximo
        if (y > maxY) maxY = y;  // Actualiza máximo
    }
    
    public double calcularDistanciaAOrigen() {
        return Math.sqrt(x * x + y * y);
    }
    
    // Getter para máximos (ejemplo de acceso)
    public static double getMaxX() {
        return maxX;
    }
    
    public static double getMaxY() {
        return maxY;
    }
}


## 14. Como sería un método factoría dentro de la clase `Punto` para construir un `Punto` a partir de dos coordenadas, pero que las redondee al entero más cercano. Escribe sólo el código del método, no toda la clase ¿Has usado `static`? 

public static Punto crearPuntoRedondeado(double x, double y) {
    double xRed = Math.round(x);
    double yRed = Math.round(y);
    return new Punto(xRed, yRed);
}
Sí, se usa static porque es un método de clase (fábrica), accesible sin instanciar Punto.
​


## 15. Cambia la implementación de `Punto`. En vez de dos `double`, emplea un array interno de dos posiciones, intentando no modificar la interfaz pública de la clase.

Aquí está la clase Punto modificada: atributos privados cambiados a un array double[] coordenadas = new double[2];, sin alterar la interfaz pública (constructor y métodos siguen iguales). La ocultación se mantiene.

public class Punto {
    private double[] coordenadas = new double[2];  // Array interno
    
    private static double maxX = Double.NEGATIVE_INFINITY;
    private static double maxY = Double.NEGATIVE_INFINITY;
    
    public Punto(double x, double y) {
        this.coordenadas[0] = x;  // x en índice 0
        this.coordenadas[1] = y;  // y en índice 1
        if (x > maxX) maxX = x;
        if (y > maxY) maxY = y;
    }
    
    public double calcularDistanciaAOrigen() {
        return Math.sqrt(coordenadas[0] * coordenadas[0] + coordenadas[1] * coordenadas[1]);
    }
    
    public static double getMaxX() { return maxX; }
    public static double getMaxY() { return maxY; }
    
    // Método fábrica (sin cambios)
    public static Punto crearPuntoRedondeado(double x, double y) {
        double xRed = Math.round(x);
        double yRed = Math.round(y);
        return new Punto(xRed, yRed);
    }
}



## 16. Si un atributo va a tener un método "getter" y "setter" públicos, ¿no es mejor declararlo público? ¿Cuál es la convención más habitual sobre los atributos, que sean públicos o privados? ¿Tiene esto algo que ver con las "invariantes de clase"?

No es mejor declarar público un atributo aunque tenga getter y setter públicos, porque los atributos privados permiten control mediante validación en setters y mantenimiento de invariantes de clase, como consistencia interna. La convención habitual en Java es declarar atributos privados (encapsulación) y usar getters/setters públicos. Las invariantes de clase (condiciones que deben cumplirse siempre, ej. edad >=0) se protegen con esta práctica, evitando accesos directos que las rompan


## 17. ¿Qué significa que una clase sea **inmutable**? ¿qué es un método modificador? ¿Un método modificador es siempre un "setter"? ¿Tiene ventajas que una clase sea inmutable?

Una clase inmutable tiene objetos cuyo estado no cambia tras crearse (sin setters, campos final). Un método modificador altera el estado (setters o métodos que cambian campos); no siempre es setter, puede ser cualquier mutador. Ventajas: thread-safety sin sincronización, predictibilidad, facilidad de uso en colecciones concurrentes y reducción de errores por mutación inesperada


## 18. ¿Es recomendable incluir métodos "setter" siempre y como convención?

No es recomendable incluir setters siempre; solo si el estado debe modificarse post-construcción y con validación. Prefiere constructores para inicializar y clases inmutables cuando posible, evitando setters innecesarios que rompen encapsulación.



## 19. ¿La clase `String` en Java es mutable o inmutable? ¿Qué ocurre al concatenar dos cadenas? ¿Qué debemos hacer si vamos a hacer una operación que implique concatenar muchas veces para construir paso a paso una cadena muy larga?

La clase String es inmutable. Al concatenar (+), se crea un nuevo objeto String, ineficiente para muchas operaciones por gasto de memoria/tiempo. Para cadenas largas con múltiples concatenaciones, usa StringBuilder (mutable, eficiente con append() y toString() final).


## 20. En POO ¿Cómo se comparan objetos de una misma clase? ¿Por su contenido o por su identidad? ¿Qué es el método equals en Java? ¿Qué hace por defecto? ¿Cómo se deben comparar dos cadenas en Java? 

En POO, por defecto se comparan por identidad (referencia en memoria, ==), no contenido. equals(Object obj) en Object compara por identidad por defecto; se sobrescribe para contenido lógico. Compara cadenas con str1.equals(str2), no == (salvo interned strings).


## 21. ¿Qué son las clases "wrapper" en un lenguaje de programación orientado a objetos? ¿Cómo se hace? ¿Es un proceso automático? ¿Qué ventajas tienen? ¿Todos los lenguajes orientados a objetos tienen tipos primitivos y necesitan wrappers? 

Clases wrapper encapsulan primitivos (ej. Integer para int, Double para double) como objetos. Se crean con constructores/autoboxing (automático en Java 5+); ventajas: uso en colecciones (no aceptan primitivos), nullables, métodos utilitarios (parse, valueOf). No todos lenguajes OO tienen primitivos (ej. Python no); Java sí los necesita por performance, wrappers para genéricos.


## 22. ¿En POO qué es un **tipo de dato enumerado**? ¿En Java, un tipo de dato enumerado es una clase? ¿Qué ventajas tienen en términos de encapsulación los enumerados en Java?

Un tipo enumerado (enum) define conjunto fijo de constantes con nombre, más seguro que constantes int. En Java, un enum es clase especial (hereda Enum), con constructor/atributos/métodos. Ventajas en encapsulación: campos privados, métodos controlados; previene valores inválidos y permite lógica asociada.




## 23. Crea un tipo enumerado en Java que se llame `Mes`, con doce posibles instancias y que además proporcione métodos para obtener cuántos días tiene ese mes, el ordinal de ese mes en el año (1-12), empleando atributos privados y constructores del tipo enumerado.

public enum Mes {
    ENERO(31, 1), FEBRERO(28, 2), MARZO(31, 3), ABRIL(30, 4),
    MAYO(31, 5), JUNIO(30, 6), JULIO(31, 7), AGOSTO(31, 8),
    SEPTIEMBRE(30, 9), OCTUBRE(31, 10), NOVIEMBRE(30, 11), DICIEMBRE(31, 12);

    private final int dias;
    private final int ordinalMes;

    Mes(int dias, int ordinalMes) {
        this.dias = dias;
        this.ordinalMes = ordinalMes;
    }

    public int getDias() { return dias; }
    public int getOrdinal() { return ordinalMes; }
}
Usa atributos privados y constructor para días/ordinal (1-12);


## 24. Añade a la clase `Mes` del ejercicio anterior cuatro métodos para devolver si ese mes tiene algunos días de invierno, primavera, verano u otoño, indicando con un booleano el hemisferio (norte o sur, parámetro `enHemisferioNorte`). Es decir: `esDePrimavera(boolean esHemisferioNorte)`, `esDeVerano(boolean esHemisferioNorte)`, `esDeOtoño(boolean esHemisferioNorte)`, `esDeInvierno(boolean esHemisferioNorte)`

public boolean esDeInvierno(boolean enHemisferioNorte) {
    if (enHemisferioNorte) return ordinalMes >= 12 || ordinalMes <= 2;
    return ordinalMes >= 6 && ordinalMes <= 8;
}

public boolean esDePrimavera(boolean enHemisferioNorte) {
    if (enHemisferioNorte) return ordinalMes >= 3 && ordinalMes <= 5;
    return ordinalMes >= 9 && ordinalMes <= 11;
}

public boolean esDeVerano(boolean enHemisferioNorte) {
    if (enHemisferioNorte) return ordinalMes >= 6 && ordinalMes <= 8;
    return ordinalMes >= 12 || ordinalMes <= 2;
}

public boolean esDeOtoño(boolean enHemisferioNorte) {
    if (enHemisferioNorte) return ordinalMes >= 9 && ordinalMes <= 11;
    return ordinalMes >= 3 && ordinalMes <= 5;
}

