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

En C, sin excepciones, dos opciones comunes son códigos de retorno especiales y errno con valor especial. Ambas permiten informar error desde fuera sin detener programa.

Opción 1: Código retorno (-1 indica error).
#include <stdio.h>
#include <math.h>

double raiz(double x) {
    if (x < 0.0) return -1.0;  // Error especial
    return sqrt(x);
}

int main() {
    double x = -4.0;
    double r = raiz(x);
    if (r == -1.0) {
        printf("¡Error! Número negativo: %.2f\n", x);
    } else {
        printf("Raíz de %.2f = %.2f\n", x, r);
    }
    return 0;
}

Opción 2: errno + NAN.

#include <stdio.h>
#include <math.h>
#include <errno.h>

double raiz(double x) {
    errno = 0;
    if (x < 0.0) {
        errno = EDOM;  // Domain error
        return NAN;
    }
    return sqrt(x);
}

int main() {
    double x = -4.0;
    double r = raiz(x);
    if (errno == EDOM) {
        printf("¡Error! Raíz de negativo (%.2f): dominio inválido\n", x);
    } else if (!isnan(r)) {
        printf("Raíz de %.2f = %.2f\n", x, r);
    }
    return 0;
}



## 2. Brevemente ¿Qué es una **"excepción"**? ¿Con qué objetivo las usa un programador cuando implementa funciones o cuando las llama?

Una excepción es un objeto especial (hereda Throwable en Java) que representa una condición excepcional o error durante la ejecución normal del programa, como división por cero, acceso a fichero inexistente o argumentos inválidos. Interrumpe el flujo de control habitual (secuencial) y lo desvía hacia un manejador específico diseñado para ese tipo de error.

Objetivos principales que persigue el programador:

Separar lógica de errores: El código "feliz" (caso normal) queda limpio, sin if(error) en cada línea como en C. Los errores se tratan en bloques dedicados.

Propagación automática: La excepción "sube" por la pila de llamadas hasta encontrar un try-catch adecuado, sin que funciones intermedias deban verificar manualmente.

Recuperación selectiva: Permite decidir por tipo de error: reintentar (I/O temporal), usar valor por defecto, o propagar más arriba.

Información rica: Lleva mensaje, stack trace completo (línea/método/clase origen) y causas encadenadas para debugging preciso.

Centralización: Un solo catch maneja errores de múltiples subllamadas profundas.


## 3. Reescribe el mismo ejemplo de raiz, pero en Java, metiendo ese método en una clase `Calculadora` y llama a dicho método desde el método `main`, mostrando cómo se puede controlar desde fuera.

import java.util.Scanner;

class Calculadora {
    public static double raiz(double x) {
        if (x < 0) {
            throw new IllegalArgumentException(
                String.format("Número negativo: %.2f no tiene raíz real", x));
        }
        return Math.sqrt(x);
    }

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.print("Introduce número: ");
        double x = sc.nextDouble();
        
        try {
            double r = raiz(x);
            System.out.printf("Raíz de %.2f = %.2f%n", x, r);
        } catch (IllegalArgumentException e) {
            System.err.println("ERROR: " + e.getMessage());
        }
        sc.close();
    }
}



## 4. ¿Qué es **"lanzar"** una excepción? ¿Qué es **"controlar"** o **"capturar"** una excepción? ¿Qué es que se **"propague"** una excepción? ¿Qué le va ocurriendo a las funciones en la pila de llamadas por donde se va propagando la excepción? ¿Las funciones que no la controlan se reanudan después de alguna forma? Explica con el mismo ejemplo anterior en Java de la raíz cuadrada.

Lanzar: throw new Excepcion() crea y arroja objeto excepción.
Capturar/controlar: try-catch intercepta, maneja (log, recuperar).
Propagar: Si no capturada, sube pila automáticamente (stack unwinding).

Pila se "desenvuelve": funciones intermedias abortan prematuramente (no reanudan post-excepción), control pasa a llamador hasta manejador o JVM termina.

En ejemplo: main → raiz(-1) lanza → propaga directo a catch de main.


## 5. ¿Qué ventajas tiene frente a C, la **"propagación natural"** de las excepciones a través de la pila (*stack*) de llamadas?

La propagación natural por la pila de llamadas es una mejora drástica sobre el control manual de C. En C, cada función debe explicitamente verificar códigos de retorno o errno en cada nivel, poluyendo código:

// C: Verificación manual en CADA nivel
double nivel3() { return raiz(-1); }  // ¿Chequea?
double nivel2() { 
    double r = nivel3(); 
    if (r == -1) return -2;  // Propaga manual
    return r * 2; 
}
double nivel1() { 
    double r = nivel2(); 
    if (r == -2) return -3;  // Más manual
    return r + 1; 
}

Ventajas clave de Java:

Código limpio y legible: Funciones intermedias (nivel2, nivel3) no necesitan chequeos. Solo main decide:

java
try { resultado = nivel1(); } catch (Exception e) { /* handle */ }
Automático y sin olvidos: Error sube sola hasta manejador correcto. En C, fácil olvidar chequeo → bugs silenciosos.

Centralización: Un try-catch en main cubre toda la pila profunda sin modificar intermedias.

Tipado fuerte: catch (IOException e) distingue errores (I/O vs args inválidos) vs C (solo errno==EDOM).

Menos anidación: Evita pirámides if(error) { if(error2) { ... } }.

Debugging superior: Stack trace muestra ruta exacta del error vs adivinar en C.

## 6. En orientación a objetos, ¿las excepciones suelen ser objetos? ¿Qué ventajas tiene esto en términos de encapsulación? ¿Podemos entonces crear excepciones personalizadas?

Sí, todas son objetos que heredan java.lang.Throwable → jerarquía:
Ventajas encapsulación OO:

Métodos ricos: getMessage(), getCause(), printStackTrace() extraen info sin exponer internals.

Polimorfismo: catch (Exception e) maneja todas subclasses automáticamente.

Herencia personalizada:

java
class ValidacionException extends Exception {
    public ValidacionException(String campo, String valor) {
        super("Campo '" + campo + "' inválido: " + valor);
    }
}
Composición: Encapsula causas (new MiError("Alto nivel", new IOException())).

Estados internos: Puede llevar datos extra (código error, severity) privados.

Ejemplo creación personalizada:

java
class Calculadora { 
    public double dividir(double a, double b) throws ValidacionException {
        if (b == 0) 
            throw new ValidacionException("divisor", "0");
        return a / b;
    }
}

catch (ValidacionException e) sabe qué falló exactamente.

## 7. En relación con las ventajas de la encapsulación, comparando el ejemplo en C con Java. ¿Qué **información esencial** lleva cualquier **objeto excepción** que es muy útil tener cuando se llega a un manejador?

C limita a 1-2 enteros (errno=34, retorno=-1). Objeto Java lleva paquete completo:

Aspecto	C	Java Exception
Mensaje	❌ No	✅ "Fichero no encontrado: data.txt"
Ubicación	❌ Manual	✅ Stack trace: clase/método/línea
Causa raíz	❌ No	✅ "Caused by: PermissionDeniedException"
Datos extra	❌ No	✅ Timestamp, user ID, severity
Formato	❌ printf manual	✅ e.printStackTrace() automático
Ejemplo real printStackTrace():

text
java.io.FileNotFoundException: data.txt (No such file)
    at Calculadora.leer(Calculadora.java:25)
    at Main.procesar(Main.java:10)
Caused by: java.nio.AccessDenied: /tmp/data.txt
    at ... (5 frames)
Valor debug: En producción, log completo → reproduce error sin acceso código fuente.



## 8. En Java, sobre el bloque **"try-catch"**, ¿se pueden tener más de un bloque `catch`? ¿cuántos bloques `catch` se ejecutan?

Sí, múltiples catch EN ORDEN ESPECÍFICO (específico → general). Ejecuta SOLO el primero que coincida por isa:

java
try {
    // Puede lanzar FileNotFound, IOException, o ArithmeticException
    procesarFichero("data.txt");
} catch (FileNotFoundException e) {      // 1º: Más específico
    System.out.println("Fichero NO existe");
} catch (IOException e) {                // 2º: Superclase
    System.out.println("Error I/O genérico");
} catch (Exception e) {                  // 3º: Catch-all
    System.out.println("Error inesperado");
}
¡ERROR de compilación si orden inverso!

java
// ❌ COMPILA NO: IOException captura FileNotFound también
catch (IOException e) { ... }  
catch (FileNotFoundException e) { ... }  // NUNCA llega
Catch multi-excepción (Java 7+):

java
catch (FileNotFoundException | PermissionException e) {
    logger.warn("Acceso denegado: " + e.getMessage());
}
Flujo:

Lanza FileNotFoundException → solo 1er catch.

Resto catch se saltan completamente.

finally ejecuta siempre después.

## 9. Si las excepciones producen rupturas en el código llamador, ¿cómo podemos garantizar que se ejecuta siempre finalmente un código necesario para cierre de ficheros, liberacion de recursos, antes de que continúe propagándose la excepción? Pon un ejemplo en Java con `finally`, tanto con `catch` como sin él.

finally ejecuta SIEMPRE (excepción o no), ideal para cerrar recursos.

Con catch:

java
Scanner sc = null;
try {
    sc = new Scanner(new File("data.txt"));
    // procesar
} catch (FileNotFoundException e) {
    System.err.println("Fichero no hallado");
} finally {
    if (sc != null) sc.close();  // Siempre
}
Sin catch:

java
PrintWriter pw = null;
try {
    pw = new PrintWriter("out.txt");
    pw.println("datos");
} finally {
    if (pw != null) pw.close();
}


## 10. En Java, el bloque `finally` puede ir sin `catch`? ¿Se ejecuta siempre tanto si ocurre como si no ocurre una excepción? ¿Y si hay un `return` en medio del `try`?

Sí, válido sin catch. Ejecuta siempre (excepción/no, return/break). Con return en try, finally corre ANTES de retornar.

java
public int test() {
    try {
        return 42;  // Nota: se guarda temporal
    } finally {
        System.out.println("Finally siempre");  // Imprime PRIMERO
    }
    // Retorna 42 DESPUÉS
}

## 11. En Java, qué son las excepciones **"controladas"** y las **"no controladas"**? ¿Qué papel juega `RuntimeException`? Pon un ejemplo de excepciones típicas controladas y no controladas que incluso nosotros mismos podríamos usar. Haz dos listas con 3 o 4 ejemplos de situación donde se suele preferir una excepción controlada y donde se suele preferir una excepción no controlada.

Checked (Exception): Compilador obliga handle/throws (recuperables).
Unchecked (RuntimeException/Error): No obliga (bugs). RuntimeException base unchecked.

Checked típicas: IOException, SQLException, ParseException, ClassNotFoundException.
Unchecked típicas: NullPointerException, IllegalArgumentException, ArrayIndexOutOfBoundsException, NumberFormatException.

Cuándo checked: Errores externos/recuperables (I/O, DB).
Cuándo unchecked: Errores programador (args inválidos, nulls).


## 12. ¿Qué es y para qué se usa `throws`? ¿Por qué es alternativa a capturar una excepción controlada?

12. throws en firmas de métodos (extendida)
throws es una cláusula en la firma del método que declara explícitamente qué excepciones checked (controladas) puede propagar hacia el llamador, sin capturarlas internamente. Sintaxis: public void metodo() throws IOException, SQLException.

Propósito principal:

Documenta contrato API: Llamador sabe qué errores esperar y debe prepararse.

Obliga responsabilidad: Compilador fuerza llamador a elegir: capturar O declarar su propio throws.

Alternativa a try-catch interno: Método dice "no me encargo, tú decides".

Ejemplo detallado:

java
public class GestorBD {
    // Declara: "Puedo fallar por BD o timeout"
    public void conectar(String url, String user) 
            throws SQLException, SocketTimeoutException {
        
        // NO try-catch aquí → PROPAGA
        Connection conn = DriverManager.getConnection(url, user);
        // ...
    }
}
Llamador OBLIGADO a decidir:

java
public static void main(String[] args) {
    GestorBD gdb = new GestorBD();
    
    // OPCIÓN 1: Capturar
    try {
        gdb.conectar("jdbc:mysql://...", "root");
    } catch (SQLException e) {
        System.err.println("Error SQL: " + e.getMessage());
    } catch (SocketTimeoutException e) {
        System.err.println("Timeout conexión");
    }
    
    // OPCIÓN 2: Re-declarar throws (propagar más arriba)
    // public static void main() throws SQLException, SocketTimeoutException { ... }
}
Comparación:

Sin throws	Con throws
❌ Compilación falla si checked no capturada	✅ Documenta + obliga llamador
Método "oculta" errores posibles	Llamador SABE qué preparar


## 13. Pon un ejemplo en Java de firma de método que incluya `throws`, de una función que abre un fichero pero que declara que no le interesa menejar la excepción de si el fichero no existe, sino que se propague hacia arriba. Eso sí, acuérdate del `finally`.

Ejemplo throws con fichero + finally (extendida)
Método que abre/crea fichero pero NO maneja FileNotFoundException, la propaga. Usa finally para garantizar cierre:

java
import java.io.*;

public class ProcesadorFicheros {
    
    // Declara: "PUEDE fallar si fichero no accesible"
    public void escribirLog(String ruta, String mensaje) 
            throws FileNotFoundException {
        
        PrintWriter salida = null;
        try {
            salida = new PrintWriter(ruta);  // ¡PUNTO CRÍTICO!
            salida.println("[" + new java.util.Date() + "] " + mensaje);
            System.out.println("Log escrito en " + ruta);
            
        } finally {
            // SIEMPRE ejecuta, incluso si FileNotFound O excepción en println
            if (salida != null) {
                salida.close();
                System.out.println("Fichero cerrado correctamente");
            }
        }
    }
    
    public static void main(String[] args) {
        ProcesadorFicheros pf = new ProcesadorFicheros();
        
        // Main CAPTURA porque método declara throws
        try {
            pf.escribirLog("log.txt", "Usuario login exitoso");
        } catch (FileNotFoundException e) {
            System.err.println("¡ERROR! No se pudo crear/abrir '" + 
                             e.getMessage() + "'");
            System.err.println("Causa: " + e.getCause());
        }
    }
}
Salida si log.txt en dir protegida:

text
¡ERROR! No se pudo crear/abrir 'log.txt (Permission denied)'
Causa: null
Flujo finally: new PrintWriter() falla → try aborta → finally cierra null (if protege) → catch maneja.


## 14. ¿Podemos poner en `throws` excepciones no controladas, como `RuntimeException`? ¿Debería el método llamador entonces poner `try-catch` en ese caso? ¿Qué sentido tendría?

throws con RuntimeException (extendida)
SÍ posible, aunque innecesario (unchecked no obliga try-catch). Sintaxis válida:

java
public void procesarDatos(String[] datos) 
        throws IllegalArgumentException, NullPointerException {
    if (datos == null) 
        throw new NullPointerException("Array no puede ser null");
    // ...
}
¿Obliga try-catch al llamador? NO, unchecked se propagan libremente.

Sentidos prácticos de documentarlo:

Documentación explícita: throws IllegalArgumentException grita "¡CUIDADO! Revisa args".

IDE autocompletado: Tools muestran "Posibles errores" en tooltip.

Claridad API: Usuario API entiende qué inválido espera.

Contrato formal: Futuras refactorizaciones respetan declaración.

Buena práctica vs no declararlo:

java
// ✅ RECOMENDADO: Documenta expectativas
public void dividir(double a, double b) 
        throws IllegalArgumentException {
    if (b == 0) throw new IllegalArgumentException("Divisor cero");
    return a / b;
}

// ❌ Silencioso, llamador no sabe qué esperar
public void dividir(double a, double b) {
    if (b == 0) throw new IllegalArgumentException("Divisor cero");
}
Llamador NO cambia:

java
dividir(10, 0);  // Lanza unchecked → propaga libre

## 15. ¿Cuándo se recomienda usar excepciones controladas, como `IOException`, y cuándo no controladas como `IllegalArgumentException`? ¿Existen en todos los lenguajes ambas opciones? En los que sólo existe una opción, ¿cuál es la más habitual?

Tipo	Cuándo	Ejemplos	Recuperable?
Checked	Externos/recuperables
★ Llamador puede actuar
★ Recursos sist.
★ APIs externas	IOException
SQLException
ParseException
TimeoutException	✅ SÍ
Unchecked	Internos/no-recuperables
★ Bugs programador
★ Args inválidos
★ Estados imposibles	IllegalArgumentException
NullPointerException
IndexOutOfBoundsException
NumberFormatException	❌ NO
Regla práctica:

text
¿Puede el llamador REINTENTAR o usar ALTERNATIVA?
    → Checked (IOException: reintentar conexión)
¿Es BUG del programador que debe ARREGLARSE?
    → Unchecked (NullPointer: revisar null-check)
Otros lenguajes:

Python/JS/C++: Solo unchecked-like (flexible, menos verboso).

C#: Similar Java (checked opcional desde C# 6).

Rust: Result<T,E> enum (checked always, compilador fuerza).

Java híbrido mejor: Checked fuerza manejo recursos críticos, unchecked evita verbosidad bugs obvios.


## 16. ¿Tiene sentido lanzar excepciones dentro del `catch`? ¿Se puede relanzar la misma excepción capturada? ¿Cuándo tendría sentido hacer esto último? Pon ejemplos de ambos casos.
SÍ, 100% válido y común. Dos casos:

1. Lanzar NUEVA excepción (wrapper/translate)
java
public void transferirFondos(Cuenta origen, Cuenta destino, double cantidad) 
        throws ExcepcionNegocio {
    
    try {
        origen.retirar(cantidad);   // Puede lanzar SQLException
        destino.depositar(cantidad);
    } catch (SQLException e) {
        // TRADUCE: Error técnico → Error de negocio
        throw new ExcepcionNegocio(
            "Transferencia falló por error banco: " + e.getMessage(), e);
    }
}
2. Relanzar MISMA (log + rethrow)
java
public void procesarLote(String[] ficheros) {
    for (String f : ficheros) {
        try {
            procesarFichero(f);
        } catch (IOException e) {
            // LOG pero NO consume: propaga para llamador decida
            logger.error("Fallo procesando " + f, e);
            throw e;  // RELANZA ORIGINAL
        }
    }
}
Cuándo relanzar misma (throw e;):

Log parcial + cleanup + propagar.

Métricas/contadores + rethrow.

NO perder stack trace original (vs throw new Exception(e.getMessage())).

Sintaxis moderna Java 7+ (rethrow limpio):

java
try { ... }
catch (Exception e) {
    validar(e);
    throw e;  // Compilador sabe tipos originales
}


## 17. ¿En qué consiste que una excepción sea la **"causa"** de otra excepción? Pon un ejemplo en Java, donde capturemos una excepción de bajo nivel y la encapsulemos en otra personalizada de alto nivel. Cuando una excepción sale por pantalla y tiene una causa, ¿se ve?

"Causa" = excepción raíz envuelta en otra superior. Crea cadena jerárquica para debugging.

Jerarquía típica:

text
ExcepcionNegocio ("Saldo insuficiente")
└── SQLException ("Deadlock detectado")
    └── IOException ("Connection timeout")
Implementación completa:

java
// Excepción personalizada ALTO NIVEL
class ExcepcionNegocio extends Exception {
    public ExcepcionNegocio(String mensaje, Throwable causa) {
        super(mensaje, causa);  // Constructor chaining
    }
}

public class ServicioBancario {
    public void transferir(String cuentaOrigen, String cuentaDestino, 
                          double importe) throws ExcepcionNegocio {
        
        try {
            // Simula cadena real: IO → SQL → Negocio
            Connection conn = obtenerConexion();  // IOException
            PreparedStatement stmt = conn.prepareStatement(
                "UPDATE cuentas SET saldo=? WHERE num=?");
            stmt.setDouble(1, saldoOrigen - importe);
            stmt.executeUpdate();  // SQLException
        } catch (SQLException e) {
            // ENVUELVE: Bajo nivel → Alto nivel
            throw new ExcepcionNegocio(
                "Transferencia falló - saldo insuficiente cuenta " + cuentaOrigen, 
                e);
        } catch (IOException e) {
            throw new ExcepcionNegocio("Error conexión BD", e);
        }
    }
}
Salida printStackTrace() MUY útil:

text
ExcepcionNegocio: Transferencia falló - saldo insuficiente cuenta 12345
    at ServicioBancario.transferir(ServicioBancario.java:25)
Caused by: java.sql.SQLException: Deadlock detected
    at BDManager.execute(BDManager.java:15)
Caused by: java.io.IOException: Connection timeout
    at ConexionPool.get(ConexionPool.java:8)

