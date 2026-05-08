<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Aspectos funcionales". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: clases y objetos, encapsulación, excepciones, composición, herencia, polimorfismo y genericidad.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->

# TEMA 7. Aspectos funcionales

## 1. ¿Qué es un puntero a una función? Pon un ejemplo de código en C, donde se define una función y que reciba una cadena de caracteres como parámetro y devuelva la cadena en mayúsculas. Crea un puntero en una variable local a dicha función llamado `aMayusculas` e invócala con el puntero.

### Respuesta

Un puntero a función es una variable que almacena la dirección de una función, permitiendo invocarla indirectamente. En C permite pasar comportamiento como argumento, almacenar callbacks o seleccionar funciones en tiempo de ejecución.

Ejemplo en C que recibe una cadena y devuelve una nueva cadena en mayúsculas; se crea un puntero `aMayusculas` y se invoca a través de él:

```c
#include <ctype.h>
#include <stdlib.h>
#include <string.h>
#include <stdio.h>

char *to_upper(const char *s) {
	size_t n = strlen(s);
	char *out = malloc(n + 1);
	if (!out) return NULL;
	for (size_t i = 0; i < n; ++i) out[i] = toupper((unsigned char)s[i]);
	out[n] = '\0';
	return out;
}

int main(void) {
	/* puntero a función: toma const char* y devuelve char* */
	char *(*aMayusculas)(const char *) = &to_upper;
	char *r = aMayusculas("hola mundo");
	if (r) {
		printf("%s\n", r);
		free(r);
	}
	return 0;
}
```

El tipo del puntero refleja la firma de la función apuntada y se usa para invocarla.

## 2. ¿Qué es una **función lambda** en un lenguaje de programación? Pon un ejemplo similar al anterior en Javascript y otro en Java con funciones lambda. Usa una variable local `aMayusculas` para apuntar a la función lambda. Por simplicidad, en Java, emplea `Function<String, String>` para el tipo de la referencia a la función lambda.

### Respuesta

Una función lambda es una función anónima y concisa que puede definirse en línea, ser asignada a variables, pasarse como argumento y retornarse. Las lambdas facilitan la programación funcional y el paso de comportamiento sin definir clases explícitas.

Ejemplos:

JavaScript:

```javascript
const aMayusculas = s => s.toUpperCase();
console.log(aMayusculas('hola'));
```

Java (usando `Function<String,String>`):

```java
import java.util.function.Function;

Function<String, String> aMayusculas = s -> s.toUpperCase();
System.out.println(aMayusculas.apply("hola"));
```

En ambos casos `aMayusculas` es una variable que referencia la función lambda.

## 3. ¿Qué es el **paradigma funcional**? ¿Por qué a algunos lenguajes orientados a objetos como Java 8, se les llama multi-paradigma? ¿Qué quiere decir que las funciones son "ciudadanos de primera clase"?

### Respuesta

El paradigma funcional es un estilo de programación que trata las funciones como valores de primera clase, favorece la inmutabilidad, evita efectos secundarios y utiliza composición de funciones y funciones puras. Los lenguajes multi-paradigma, como Java desde la versión 8, soportan características funcionales (lambdas, streams) junto con la programación orientada a objetos.

Decir que las funciones son "ciudadanos de primera clase" significa que pueden asignarse a variables, pasarse como parámetros y devolverse desde otras funciones, con la misma flexibilidad que otros tipos de datos.

## 4. Explica la sintaxis básica de una función lambda en Java.

### Respuesta

La sintaxis básica de una lambda en Java es `(parametros) -> expresión` o `(parametros) -> { instrucciones; }`. Si la lambda tiene un solo parámetro puede omitirse el paréntesis; el tipo puede inferirse y no es necesario declararlo explícitamente cuando el contexto lo permite.

Ejemplos:

```java
// expresión simple
Function<String, String> f1 = s -> s.toUpperCase();

// bloque de instrucciones
Function<String, String> f2 = s -> {
	String r = s.trim();
	return r.toUpperCase();
};
```

## 5. Ahora recibamos una función como parámetro a un método y la llamaremos desde dentro. Amplia los ejemplos anteriores de Java y JavaScript con un método llamado `transformar`, que reciba un `String` como parámetro y luego una función transformadora como lo es `aMayúsculas` y la invoque desde dentro.

### Respuesta

Se puede recibir una función como parámetro y llamarla desde dentro del método. En Java se usa una interfaz funcional (`Function` o `Consumer`, etc.), y en JavaScript se pasa simplemente una función.

JavaScript:

```javascript
function transformar(s, f) { return f(s); }
const aMayusculas = str => str.toUpperCase();
console.log(transformar('hola', aMayusculas));
```

Java:

```java
import java.util.function.Function;

public static String transformar(String s, Function<String, String> f) {
	return f.apply(s);
}

// uso:
// transformar("hola", s -> s.toUpperCase());
```

## 6. Ahora, invoca `transformar`, con una nueva función lambda directamente en la llamada a `transformar`, por ejemplo, una función lambda que invierta la cadena. Define la función de inversión justo cuando la estás pasando como parámetro.

### Respuesta

Se puede pasar la lambda directamente en la llamada a `transformar`, definiéndola en el sitio donde se necesita. Ejemplo que invierte la cadena:

JavaScript:

```javascript
console.log(transformar('hola', s => s.split('').reverse().join('')));
```

Java:

```java
System.out.println(transformar("hola", s -> new StringBuilder(s).reverse().toString()));
```

Así la función de inversión se crea y se usa inline sin necesidad de una variable intermedia.

## 7. ¿Qué se entiende por cierre o "closure" en el contexto de las funciones lambda? Pon un ejemplo en Java de cómo una función lambda es capaz de acceder a una variable local en el contexto donde fue definida. Modifica el ejemplo anterior, creando otra función lambda para transformar una cadena, pero que lo que haga es concatenar a la cadena de entrada otra cadena que está en una variable local definida fuera de la función lambda.

### Respuesta

Un closure (cierre) es una función que captura y puede usar variables del contexto donde fue definida, manteniendo acceso a ellas incluso cuando ese contexto ha terminado su ejecución. En Java las lambdas pueden capturar variables locales siempre que sean efectivamente finales (no reasignadas).

Ejemplo en Java que concatena un sufijo capturado:

```java
import java.util.function.Function;

String sufijo = "!!!"; // efectivamente final
Function<String,String> añadeSufijo = s -> s + sufijo;
System.out.println(añadeSufijo.apply("hola")); // imprime "hola!!!"
```

Aquí la lambda accede a `sufijo` definido fuera de ella; esa captura es el cierre.

## 8. Reflexiona: ¿en qué se diferencia entonces una función lambda de los punteros a funciones que hay en C?

### Respuesta

Las diferencias principales son que los punteros a función en C solo guardan una dirección de código y no capturan contexto; no contienen cierre ni estado asociado. Las lambdas modernas pueden capturar variables (closures), ser objetos con tipo seguro y participar en el sistema de tipos del lenguaje (por ejemplo, implementan interfaces en Java), mientras que un puntero a función es simplemente una referencia a código sin capacidad para retener variables del entorno.

Además, en lenguajes como Java la lambda se traduce a un objeto que implementa una interfaz funcional y está sujeta a comprobación de tipos en tiempo de compilación; en C el puntero es más débil en seguridad de tipos y no ofrece captura de estado.

## 9. Devolvamos ahora funciones. Creemos ahora una función que sea capaz de crear funciones "descuento". Una función "descuento", decrementa un porcentaje pasado como parámetro. Por simplicidad, usa `Function<Double, Double>` para su tipo. La función `crearDescuento(porcentaje)`, recibe solo el porcentaje de descuento a aplicar y devuelve la función de descuento. Prueba a crear dos descuentos distintos y aplicarlos a una cantidad. Explica la closure en la función descuento.

### Respuesta

Se puede implementar una función que devuelva otra función (factory de descuentos). La función retornada captura el porcentaje y lo aplica cuando se la invoca.

Java:

```java
import java.util.function.Function;

public static Function<Double, Double> crearDescuento(double porcentaje) {
	return precio -> precio * (1.0 - porcentaje / 100.0);
}

// Uso:
Function<Double, Double> d10 = crearDescuento(10);
Function<Double, Double> d25 = crearDescuento(25);
System.out.println(d10.apply(100.0)); // 90.0
System.out.println(d25.apply(100.0)); // 75.0
```

La lambda devuelta captura `porcentaje`: ese valor forma parte del closure y permanece disponible cuando la función se invoca.

## 10. En Java, que es un lenguaje con comprobación estática de tipos, donde los tipos se declaran, toda función lambda tiene un tipo, que se conoce como **interfaz funcional**. ¿Qué es una **interfaz funcional**? ¿Qué requisitos tiene?

### Respuesta

Una interfaz funcional es una interfaz que declara exactamente un método abstracto (aunque puede contener métodos `default` o `static`). Esa única operación describe el tipo de la lambda que puede asignarse a esa interfaz. Es habitual usar la anotación `@FunctionalInterface` para documentar la intención y que el compilador verifique la restricción.

Requisitos: un único método abstracto (SAM, single abstract method), pudiendo coexistir métodos `default` o `static` sin afectar la regla.

## 11. Creemos una interfaz funcional a mano. Por ejemplo, define la interfaz funcional del ejemplo que transforma la cadena en otra. Llámale `Transformador`, que define una función que convierte una cadena de texto (`String`) en otra (`String`).

### Respuesta

Definición manual de la interfaz funcional `Transformador` para cadenas:

```java
@FunctionalInterface
public interface Transformador {
	String transformar(String s);
}

// Uso:
Transformador rev = s -> new StringBuilder(s).reverse().toString();
System.out.println(rev.transformar("hola"));
```

La interfaz permite usar lambdas que coincidan con la firma `String -> String`.

## 12. Ahora hagamos la interfaz funcional algo más genérica y empleando generics, para que permita definir un `Transformador` de un tipo en otro. Pon un ejemplo de un transformador que redondea un `Double` en un `Integer`.

### Respuesta

Versión genérica de `Transformador` y ejemplo que convierte `Double` a `Integer` redondeando:

```java
@FunctionalInterface
public interface Transformador<T,R> {
	R transformar(T t);
}

Transformador<Double, Integer> round = d -> (int) Math.round(d);
System.out.println(round.transformar(3.6)); // 4
```

La genericidad permite reutilizar la interfaz para distintos pares de tipos.

## 13. `Transformador`, en su versión genérica, parece muy útil y reutilizable, hasta el punto de que es igual a una interfaz funcional que ya hay, que es `Function<T, R>`. Muestra las interfaces funcionales predefinidas que hay en Java.

### Respuesta

Java define varias interfaces funcionales predefinidas en `java.util.function`: `Function<T,R>`, `Consumer<T>`, `Supplier<T>`, `Predicate<T>`, `UnaryOperator<T>`, `BinaryOperator<T>`, `BiFunction<T,U,R>`, `BiConsumer<T,U>`, entre otras. Estas cubren casos comunes de transformación, consumo, suministro y predicado, evitando definir interfaces ad-hoc.

Por ejemplo `Function<T,R>` representa una función que recibe `T` y devuelve `R`, y `Consumer<T>` representa una operación que recibe `T` y no devuelve valor.

## 14. Vamos a ver ejemplos expresivos de funcional en Java. Estudiemos el `List.forEach`, como versión funcional del bucle `for`. Emplea el `forEach` para recorrer una lista de `Integer` y que muestre un mensaje si el entero es positivo.

### Respuesta

Ejemplo de uso de `forEach` para recorrer una lista de `Integer` y mostrar un mensaje si el entero es positivo:

```java
import java.util.Arrays;
import java.util.List;

List<Integer> nums = Arrays.asList(-1, 2, 0, 5);
nums.forEach(n -> { if (n > 0) System.out.println("Positivo: " + n); });
```

`forEach` recibe un `Consumer` y permite expresar la operación en forma funcional, más concisa que un bucle `for` explícito.
## 15. Repasando el tema de genericidad, fíjate en la firma de `forEach`, ¿por qué se usa `Consumer<? super T>` y no `Consumer<T>`? Explica qué significa **PECS**, y explícalo para el caso de mejorar el ejemplo del método `transformar` la hora de definir el tipo de la función transformadora.

### Respuesta

La firma `forEach(Consumer<? super T> action)` usa `? super T` porque el `Consumer` consume valores de tipo `T`: aplicar `PECS` (Producer Extends, Consumer Super) indica que para consumir `T` se puede aceptar un `Consumer` parametrizado con cualquier supertipo de `T`. Así se facilita la llamada con consumidores más generales.

Aplicado al método `transformar`, si se quisiera permitir que la función transformadora acepte supertipos del tipo de entrada o devuelva subtipos del tipo de salida, conviene usar wildcards y generics (`Function<? super A, ? extends B>`) para maximizar flexibilidad sin perder seguridad de tipos.
## 16. Referencias a métodos. Podemos obtener una referencia a métodos de objetos o clases. Pon un ejemplo en JavaScript y en Java, de una clase `Persona` con un método `saludar`. En el código principal, crea una `Persona` con un nombre, y obtén una referencia a su método `saludar` en una variable local. Invoca `saludar` con esa referencia a su método `saludar`.

### Respuesta

JavaScript (referencia a método con `bind`) y Java (referencia de método):

JavaScript:

```javascript
class Persona {
	constructor(nombre) { this.nombre = nombre; }
	saludar() { console.log('Hola, soy ' + this.nombre); }
}

const p = new Persona('Ana');
const ref = p.saludar.bind(p); // referencia al método ligada a la instancia
ref(); // Hola, soy Ana
```

Java:

```java
public class Persona {
	private final String nombre;
	public Persona(String nombre) { this.nombre = nombre; }
	public void saludar() { System.out.println("Hola, soy " + nombre); }
}

Persona p = new Persona("Ana");
Runnable r = p::saludar; // referencia a método de instancia ligada a 'p'
r.run();
```

En JavaScript hay que cuidar el `this` y a menudo usar `bind` para ligar el contexto; en Java las referencias a método se traducen a objetos funcionales con tipado estático.

## 17. ¿Qué tipos de referencias a método se pueden hacer en Java? Pon un ejemplo de referencia a método estático, a constructor, a método de instancia de una instancia concreta y a método de instancia sobre cualquier instancia.

### Respuesta

En Java existen varias formas de referencia a método: referencia a método estático (`Clase::metodoEstatico`), referencia a constructor (`Clase::new`), referencia a método de instancia de una instancia concreta (`instancia::metodo`) y referencia a método de instancia sobre cualquier instancia del tipo (`Clase::metodoInstancia`). Ejemplos:

```java
// referencia a método estático
Comparator<String> comp = String::compareToIgnoreCase;

// referencia a constructor
Supplier<ArrayList<String>> sup = ArrayList::new;

// referencia a método de instancia de una instancia concreta
Persona p = new Persona("Ana");
Runnable r = p::saludar; // ligado a la instancia p

// referencia a método de instancia sobre cualquier instancia (unbound)
Function<Persona, String> nameGetter = Persona::toString; // recibe la instancia como primer argumento
```

Cada forma tiene su uso y se adapta al contexto del tipo funcional esperado.

## 18. Otro ejemplo expresivo. Ordena una lista de `Persona`, cada persona tiene un nombre y una edad (de tipo entero). Ordena la lista de `Persona` con `Collections.sort`, pasándole como comparador una expresión lambda que compare la edad de ambas personas y si tienen la misma edad, se ordene por orden alfabético del nombre. Crea dos versiones: Una con la función de comparación hecha manualmente, y otra empleando `Comparator`.

### Respuesta

Ejemplo de `Persona` y ordenación por edad y nombre. Versión manual con lambda y versión usando `Comparator`:

```java
import java.util.*;

class Persona {
	private final String nombre;
	private final int edad;
	public Persona(String nombre, int edad) { this.nombre = nombre; this.edad = edad; }
	public String getNombre() { return nombre; }
	public int getEdad() { return edad; }
	public String toString() { return nombre + " (" + edad + ")"; }
}

List<Persona> lista = new ArrayList<>();
lista.add(new Persona("Ana", 30));
lista.add(new Persona("Luis", 25));
lista.add(new Persona("Pedro", 25));

// 1) Lambda manual
Collections.sort(lista, (a, b) -> {
	int cmp = Integer.compare(a.getEdad(), b.getEdad());
	if (cmp != 0) return cmp;
	return a.getNombre().compareTo(b.getNombre());
});

// 2) Usando Comparator
lista.sort(Comparator.comparing(Persona::getEdad).thenComparing(Persona::getNombre));
```

Ambas versiones producen el mismo orden: primero por edad y, en empate, por orden alfabético del nombre.
