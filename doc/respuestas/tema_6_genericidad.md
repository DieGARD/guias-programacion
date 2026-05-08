<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Genericidad". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: clases y objetos, encapsulación, excepciones, composición, herencia y polimorfismo.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 6. Genericidad

## 1. Empleando `void*` en C o `Object` en Java, pon un ejemplo de una estructura de datos, que empleando un array primitivo, permita alojar cualquier tipo de dato.

### Respuesta

En C se puede usar un array de `void *` para almacenar punteros a cualquier tipo de dato; en Java se puede emplear un array de `Object` para almacenar referencias a cualquier objeto. Ambos enfoques permiten heterogeneidad pero no ofrecen comprobación de tipos en tiempo de compilación, por lo que requieren conversiones (casts) al recuperar valores.

Ejemplo en C (muy básico):

```c
#include <stdlib.h>
#include <stdio.h>

typedef struct {
    void **data;
    size_t size;
    size_t capacity;
} Vector;

/* funciones para gestionar Vector (reserva, push, free) omisas por brevedad */

/* Uso: almacenar punteros a distintos tipos */
```

Ejemplo en Java:

```java
Object[] arr = new Object[10];
arr[0] = "texto"; // String
arr[1] = Integer.valueOf(42); // Integer
String s = (String) arr[0]; // cast necesario
```
## 2. Brevemente, ¿Qué significa la **programación genérica**? ¿Es el ejemplo anterior un ejemplo básico de programación genérica? 

### Respuesta

La programación genérica es la técnica que permite escribir componentes (clases, interfaces, funciones) parametrizados por tipos, de modo que el mismo código funcione con distintos tipos sin repetirlo. El ejemplo anterior con `void*`/`Object` es una forma rudimentaria de generar estructuras heterogéneas, pero no es verdadera programación genérica porque no proporciona verificación de tipos estática ni seguridad en el uso: los genéricos permiten declarar `List<String>` de forma segura y que el compilador verifique usos.

En resumen, el ejemplo con `void*`/`Object` es un acercamiento dinámico pero no aprovecha las ventajas de los genéricos que ofrecen comprobación y documentación de tipos en tiempo de compilación.
## 3. Indica los problemas respecto al chequeo de tipos, de emplear `void*` o `Object` cuando se crean estructuras de datos genéricas. 

### Respuesta

Usar `void*` (C) o `Object` (Java) en estructuras de datos trae problemas de chequeo de tipos: no hay verificación en compilación, por lo que se deben realizar casts al recuperar elementos y se pueden producir errores en tiempo de ejecución (segfault en C por cast incorrecto, ClassCastException en Java). En C además hay responsabilidad manual de gestión de memoria y ausencia de información de tamaño/aliances de tipo.

En Java, el uso de `Object` obliga a boxing/unboxing para tipos primitivos (pérdida de eficiencia) y elimina el beneficio de que el compilador detecte incompatibilidades entre tipos al insertar o leer elementos.

## 4. Vamos entonces con mecanismos de mejora de la programación genérica ¿Qué son los **parámetros de tipo**? 

### Respuesta

Los parámetros de tipo son identificadores que representan tipos en tiempo de compilación y que se usan para parametrizar clases, interfaces o métodos. En lugar de fijar un tipo concreto (por ejemplo `String`) se declara un parámetro `<T>` y el compilador lo sustituye o verifica según la instancia concreta: así se obtiene reutilización y seguridad de tipos.

Los parámetros de tipo permiten declarar contenedores y algoritmos que trabajan de forma genérica sobre tipos determinados solo cuando se instancian, manteniendo la comprobación estática del compilador.

## 5. En Java existe "generics", en C++ existen "templates". Pon un ejemplo de uso de programación genérica en ambos, instanciando una lista o vector dinámico que solo admite `String`. Introduce valores, y luego haz un recorrido de ellos mostrando cómo cada elemento es del tipo concreto con seguridad.

### Respuesta

En Java:

```java
import java.util.ArrayList;
import java.util.List;

List<String> lista = new ArrayList<>();
lista.add("hola");
lista.add("mundo");
for (String s : lista) {
    System.out.println(s.toUpperCase());
}
```

En C++ (templates):

```cpp
#include <vector>
#include <string>
#include <iostream>

std::vector<std::string> v;
v.push_back("hola");
v.push_back("mundo");
for (const std::string &s : v) std::cout << s << '\n';
```

Ambos ejemplos permiten recorridos donde cada elemento se trata ya como `String` con seguridad de tipo.

## 6. Sobre el funcionamiento de la programación genérica. ¿Qué hace el compilador cuando se instancia una clase que tiene parámetros de tipo? ¿Hace lo mismo C++ y Java? ¿Qué es el "type erasure" de Java y la "instanciación de plantillas" de C++?

### Respuesta

El comportamiento difiere entre C++ y Java. En C++ los templates son instanciados por el compilador para cada conjunto de parámetros de tipo (monomorfización): el compilador genera código específico para `Vector<int>` y para `Vector<double>`, lo que permite optimizaciones y código concreto por tipo.

Java implementa los generics mediante `type erasure`: la información de parámetros de tipo se usa en compilación para verificar y evitar casts, pero se elimina en la clase compilada (la implementación en tiempo de ejecución suele ser la misma que la del tipo sin parámetros). Por tanto, C++ crea instancias concretas en tiempo de compilación; Java mantiene una sola implementación genérica y aplica conversiones/casts automáticos según sea necesario.

## 7. Vamos a crear una nueva clase con parámetros de tipo. Define en Java una clase `Par`, que permite alojar dos valores de tipos diferentes. Incluye un constructor y un getter para cada tipo. Pon un ejemplo de uso de ese `Par`, por ejemplo para especificar el tipo de retorno de una función que devuelve en un `Par` la media y desviación típica de un array de `double`. 

### Respuesta

Definición de `Par` en Java y uso para devolver media y desviación típica:

```java
public class Par<A, B> {
    private final A primero;
    private final B segundo;

    public Par(A primero, B segundo) {
        this.primero = primero;
        this.segundo = segundo;
    }

    public A getPrimero() { return primero; }
    public B getSegundo() { return segundo; }
}

public static Par<Double, Double> estadisticas(double[] datos) {
    double suma = 0.0;
    for (double d : datos) suma += d;
    double media = datos.length == 0 ? 0 : suma / datos.length;
    double var = 0.0;
    for (double d : datos) var += Math.pow(d - media, 2);
    double desviacion = datos.length == 0 ? 0 : Math.sqrt(var / datos.length);
    return new Par<>(media, desviacion);
}
```

Así `Par<Double, Double>` expresa de forma clara el tipo devuelto y evita casts al usar los valores.

## 8. En Java, se pueden declarar parámetros de tipo también a nivel de método, no solo a nivel de clase. Pon un ejemplo con un método genérico `seleccionaUno`, que pasados dos objetos del mismo tipo, te devuelva aleatoriamente uno de ellos. Muestra la diferencia de definirlo con dos `Object`, a definirlo con dos parámetros de tipo, en terminos de (i) evitar downcasting y (ii) forzar que ambos objetos sean del mismo tipo. 

### Respuesta

Ejemplo de método genérico `seleccionaUno`:

```java
public static <T> T seleccionaUno(T a, T b) {
    return Math.random() < 0.5 ? a : b;
}

// Versión con Object (sin genéricos):
public static Object seleccionaUnoObj(Object a, Object b) {
    return Math.random() < 0.5 ? a : b;
}
```

Usar genéricos evita hacer `cast` al obtener el resultado y obliga a que `a` y `b` sean del mismo tipo en tiempo de compilación, mientras que la versión con `Object` permite mezclar tipos y fuerza `downcasting` en el call-site.

## 9. ¿Se pueden establecer restricciones en los parámetros de tipo? Por ejemplo, si quiero definir un tipo genérico `<T>`, ¿puedo decir que tenga que ser, al menos, un número para poder tratarlo como tal? Pon un ejemplo en Java de un `Punto` con dos coordenadas, metodos `getX`, `getY`, y una función `calcularDistanciaA` otro `Punto`. Permite que esas coordenadas sean cualquier tipo de número. Pon dos soluciones: una simplemente creando coordenadas de tipo `Number` y otra añadiendo generics para reforzar el chequeo de tipos y saber exactamente con qué tipo de número trabaja el `Punto`. En este caso y respecto al "type erasure", ¿cuál es el tipo final tras la compilación?

### Respuesta

Sí se pueden establecer restricciones con `extends` (y, en combinación, `super` en wildcards). Por ejemplo, para limitar a números se puede usar `T extends Number`. Dos soluciones para `Punto`:

1) Coordenadas como `Number`:

```java
public class PuntoNumber {
    private final Number x, y;
    public PuntoNumber(Number x, Number y) { this.x = x; this.y = y; }
    public Number getX() { return x; }
    public Number getY() { return y; }
    public double distanciaA(PuntoNumber p) {
        return Math.hypot(x.doubleValue() - p.x.doubleValue(), y.doubleValue() - p.y.doubleValue());
    }
}
```

2) Solución con generics:

```java
public class Punto<T extends Number> {
    private final T x, y;
    public Punto(T x, T y) { this.x = x; this.y = y; }
    public T getX() { return x; }
    public T getY() { return y; }
    public double distanciaA(Punto<T> p) {
        return Math.hypot(x.doubleValue() - p.x.doubleValue(), y.doubleValue() - p.y.doubleValue());
    }
}
```

Respecto al `type erasure`, en la versión genérica `Punto<T extends Number>` el compilador sustituye `T` por su límite superior (`Number`) en el bytecode; en tiempo de ejecución la información concreta de `T` no está presente.

## 10. Sobre las soluciones anteriores. Si bien ambas permiten trabajar con distintos tipos de número sin duplicar la clase `Punto`, reflexiona sobre el refuerzo del chequeo de tipos con generics. ¿Permiten ambas crear un punto con una coordenada de tipo entero y la otra coordenada de tipo real? ¿Qué tipo devuelve el `getX` con la solucion sin generics y qué tipo devuelve el que tiene la solución con generics?

### Respuesta

Con la primera solución (propiedades `Number`) es posible crear un `Punto` con una coordenada `Integer` y otra `Double` porque cada coordenada se almacena como `Number` y la verificación de tipos no impide esa mezcla. En la solución genérica `Punto<T>` se fuerza que ambas coordenadas sean del mismo tipo `T`, por lo que no se permite mezclar `Integer` y `Double` en una misma instancia.

`getX` en la solución sin genéricos devuelve `Number`; en la solución genérica devuelve `T` (el tipo concreto que se usó al instanciar la clase), lo que mejora el chequeo estático y evita casts en el código cliente.

## 11. Hagamos un ejemplo avanzado. El siguiente código, con interfaz `Punto`, que define un método `calcularDistanciaA(Punto p)`, junto con las implementaciones `Punto2D` y `Punto3D`. Añade generics para asegurarnos que la sobreescritura del método calcular distancia a otro `Punto` siempre es sobre un `Punto` del mismo tipo, evitando `instanceof` y el downcasting.
```java
public interface Punto { 
    public double distanciaA(Punto p); 
} 

public class Punto2D implements Punto { 
     private final double x, y; 
     public Punto2D(double x, double y) { 
        this.x = x; this.y = y; 
    } 

    @Override 
    public double distanciaA(Punto p) { 
        if (p instanceof Punto2D) { 
            Punto2D p2d = (Punto2D) p; 
            return Math.sqrt(Math.pow(x - p2d.x, 2) 
                    + Math.pow(y - p2d.y, 2)); 
        } else { 
            throw new RuntimeException("p debe ser Punto 2D"); 
        } 
    } 
} 
public class Punto3D implements Punto { 
    // Igual que Punto2D, pero con tres coordenadas
    ...
} 
```

### Respuesta

Una forma común de forzar que `distanciaA` reciba siempre el mismo subtipo es parametrizar la interfaz con un self-type genérico:

```java
public interface Punto<T extends Punto<T>> {
    double distanciaA(T p);
}

public class Punto2D implements Punto<Punto2D> {
    private final double x, y;
    public Punto2D(double x, double y) { this.x = x; this.y = y; }
    @Override
    public double distanciaA(Punto2D p) {
        return Math.hypot(x - p.x, y - p.y);
    }
}

public class Punto3D implements Punto<Punto3D> {
    private final double x, y, z;
    public Punto3D(double x, double y, double z) { this.x = x; this.y = y; this.z = z; }
    @Override
    public double distanciaA(Punto3D p) {
        return Math.sqrt(Math.pow(x - p.x,2) + Math.pow(y - p.y,2) + Math.pow(z - p.z,2));
    }
}
```

Con este patrón (similar al CRTP), el compilador garantiza en tiempo de compilación que `Punto2D.distanciaA` solo acepta `Punto2D`, evitando `instanceof` y `downcasts` en la implementación.

## 12. Dado que `String` es subtipo de `Object`, ¿significa eso que `List<String>` es subtipo de `List<Object>`? ¿Y que `String[]` es subtipo de `Object[]`? Razona por qué la respuesta es diferente en cada caso y qué problema en tiempo de ejecución puede aparecer con los arrays. A partir de estos ejemplos, define qué significa que un tipo genérico sea **covariante**, **contravariante** o **invariante** respecto a su parámetro de tipo.

### Respuesta

No: `List<String>` no es subtipo de `List<Object>` en Java; los genéricos son invariantes por defecto. En cambio los arrays son covariantes: `String[]` sí es subtipo de `Object[]`. La covarianza de arrays permite asignaciones como `Object[] a = new String[1];` pero esto puede fallar en tiempo de ejecución si se intenta escribir un `Integer` en ese array, provocando `ArrayStoreException`.

Definiciones: un tipo genérico es covariante respecto a su parámetro si `Tipo<Subtipo>` es subtipo de `Tipo<Suptipo>`; es contravariante si `Tipo<Supertipo>` es subtipo de `Tipo<Tipo>`; e invariante si no existe relación de subtipado derivada del parámetro. Java mantiene invariancia en `List<T>` para evitar errores de tipo en tiempo de ejecución.

## 13. Java permite recuperar covarianza y contravarianza en tipos genéricos de forma controlada mediante **wildcards**. ¿Qué es un wildcard (`?`)? Muestra la diferencia entre `List<? extends T>` y `List<? super T>`, indicando en qué casos se usa cada uno. Pon dos ejemplos: (i) un método que reciba una lista de números y calcule su suma, usando `? extends`; (ii) un método que reciba una lista y le añada varios números enteros, usando `? super`.

### Respuesta

Un wildcard `?` representa un tipo desconocido. `List<? extends T>` indica una lista de algún subtipo de `T` (lectura segura: se pueden leer `T` o sus supertipos), mientras que `List<? super T>` indica una lista de algún supertipo de `T` (escribir `T` en la lista es seguro).

Ejemplos:

1) Sumar números con `? extends Number`:

```java
public static double suma(List<? extends Number> nums) {
    double s = 0.0;
    for (Number n : nums) s += n.doubleValue();
    return s;
}
```

2) Añadir enteros con `? super Integer`:

```java
public static void anadeEnteros(List<? super Integer> lista) {
    lista.add(1);
    lista.add(2);
}
```

El `? extends` se usa cuando la colección produce valores (se quiere leer), y `? super` cuando la colección consume valores (se quiere escribir).
