<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Polimorfismo". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación, Excepciones, Composición y Herencia.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# Tema 5. Polimorfismo

## 1. Brevemente, ¿qué es el **"polimorfismo"** y para qué sirve en programación orientada a objetos? ¿qué es la **"sobreescritura"** de métodos?

### Respuesta

El polimorfismo es la capacidad de un mismo nombre de método o mensaje para ejecutar comportamientos distintos según el tipo concreto del objeto que lo recibe. En programación orientada a objetos permite escribir código que opere sobre referencias a la clase base (o a una interfaz) y que, en tiempo de ejecución, invoque la implementación concreta del subtipo real, facilitando la reutilización y la extensión sin cambiar el código cliente.

La sobreescritura (overriding) es el mecanismo por el cual una subclase proporciona una implementación concreta para un método ya definido en la clase base. Al sobrescribir, la firma del método debe ser compatible y la llamada polimórfica dirigirá la invocación a la versión de la subclase cuando el objeto real sea de esa subclase.

## 2. ¿En qué consiste la **"ligadura dinámica"** o **"enlace tardío"**? ¿qué relación tiene con el polimorfismo? ¿hay que indicarlos explícitamente al programar o depende esto del lenguaje? Compara C++ y Java. Indicalo después también para Python.

### Respuesta

La ligadura dinámica (o enlace tardío) es el proceso por el que la decisión de qué implementación concreta ejecutar se pospone hasta el tiempo de ejecución, en lugar de resolverse en tiempo de compilación. Esta característica es la base del polimorfismo en OO: la llamada a un método en una referencia de tipo general se resuelve al método del objeto real en tiempo de ejecución.

Su necesidad de declaración explícita depende del lenguaje. En C++ el enlace tardío se activa declarando métodos como `virtual`; sin `virtual` las llamadas se enlazan estáticamente. En Java, los métodos de instancia son, por defecto, de enlace dinámico (son virtuales), salvo casos como `private`, `static` o `final` donde no hay sobreescritura. En Python, el enlace es siempre dinámico: la resolución de atributos y métodos ocurre en tiempo de ejecución sin necesidad de anotaciones.

## 3. Pon un ejemplo sencillo en Java, de un `Soldado`, con un método `saluda`, con dos subclases: `Zapador` y `Artillero`, donde `Zapador` sobreescribe el método `saludar`, sustituyendo por completo su comportamiento. Ilustra el funcionamiento del polimorfismo creando un array de `Soldados` de dos tipos y luego recorriéndolo empleando referencias de tipo `Soldado` y llamando a `saludar`.

### Respuesta

A continuación se muestra un ejemplo sencillo en Java que ilustra polimorfismo con `Soldado`, `Zapador` y `Artillero`. Cada subclase puede sobreescribir `saludar` y las llamadas realizadas sobre una referencia de tipo `Soldado` invocan la implementación correspondiente al objeto real.

```java
public class Soldado {
	public void saludar() {
		System.out.println("Soldado: Buenos días, señor.");
	}
}

class Zapador extends Soldado {
	@Override
	public void saludar() {
		System.out.println("Zapador: Preparado para desactivar explosivos.");
	}
}

class Artillero extends Soldado {
	@Override
	public void saludar() {
		System.out.println("Artillero: Listo para fuego.");
	}
}

public class Demo {
	public static void main(String[] args) {
		Soldado[] escuadra = new Soldado[] { new Zapador(), new Artillero(), new Soldado() };
		for (Soldado s : escuadra) {
			s.saludar(); // Llamada polimórfica
		}
	}
}
```

Al recorrer el array con referencias de tipo `Soldado`, cada `saludar()` ejecuta la versión del subtipo concreto.

## 4. Si sobreescribo un método, ¿puedo invocar el método base para trabajar a partir de su resultado? Haz que zapador cambie ligeramente la forma de saludar, que salude de forma normal, tal cual hace el soldado base, pero que además añada un "ZAPADOR A SUS ORDENES" ¿qué palabra clave del lenguaje has usado para invocar al método de la clase base?

### Respuesta

Sí; desde una subclase se puede invocar la implementación de la clase base y, a partir de su resultado, completar o modificar el comportamiento. En Java se usa la palabra clave `super` para referirse al miembro de la clase padre. De este modo una subclase puede reutilizar la lógica común y añadir sus propias acciones.

Ejemplo corto en Java donde `Zapador` reutiliza el saludo base y añade su firma:

```java
class Zapador extends Soldado {
	@Override
	public void saludar() {
		super.saludar(); // Invoca el saludo de la clase base
		System.out.println("ZAPADOR A SUS ORDENES");
	}
}
```

La palabra clave usada para invocar el método de la clase base es `super`.

## 5. Al sobreescribir un método en Java, ¿qué restricciones existen sobre los tipos de los parámetros y el tipo de retorno? ¿Qué diferencia hay entre sobreescritura (*overriding*) y sobrecarga (*overloading*)? ¿Para qué sirve la anotación `@Override` y por qué es recomendable usarla siempre?

### Respuesta

Al sobreescribir en Java los parámetros deben mantener la misma firma (mismos tipos y orden) que el método original; no se considera sobreescritura si cambia la lista de parámetros (eso sería sobrecarga). El tipo de retorno debe ser compatible: Java permite retornos covariantes, es decir, la subclase puede devolver un subtipo del tipo de retorno declarado en la superclase. Además, el modificador de acceso no puede ser más restrictivo y las excepciones comprobadas no pueden ampliarse en la subclase (solo se pueden lanzar las mismas o subtipos declarados).

La sobreescritura (overriding) consiste en redefinir el comportamiento de un método heredado; la sobrecarga (overloading) consiste en definir varios métodos con el mismo nombre pero diferentes firmas dentro de la misma clase o en la jerarquía. La anotación `@Override` sirve para pedirle al compilador que verifique que el método realmente está sobrescribiendo otro; es recomendable usarla siempre porque detecta errores (por ejemplo, firmas mal escritas) en tiempo de compilación.

## 6. Entonces, cuando se estudia Java, ¿se emplea el polimorfismo desde el principio? Por ejemplo, sobreescribiendo `toString` o sobreescribiendo `equals`, ¿ya estoy usando polimorfismo?

### Respuesta

Sí. Cuando se estudia Java se emplea polimorfismo desde el principio: al sobrescribir `toString()` o `equals(Object)` se está usando polimorfismo porque el llamado se realiza a través de la referencia con tipo base (`Object`) y la implementación concreta se decide en tiempo de ejecución según la clase real del objeto.

En la práctica, cualquier redefinición de métodos heredados que sea invocada a través de referencias generales es un uso directo del polimorfismo.

## 7. ¿Qué es una **"clase abstracta"**? ¿Qué es un **"método abstracto"**? ¿Puedo crear instancias de una clase abstracta? Pongamos un ejemplo en Java: Redefinamos `Soldado`, hagamos que, además del método `saluda` que ya tenía, tenga un método `atacar`, que sea abstracto y que cada tipo de soldado haga su acción cuando se le pida atacar. ¿Donde debemos poner `abstract`?

### Respuesta

Una clase abstracta es una clase que puede contener tanto métodos con implementación como métodos sin implementación (abstractos) y que no puede ser instanciada por sí misma. Un método abstracto declara la firma pero no la implementación; las subclases concretas deben implementarlo. No es posible crear instancias de una clase marcada como `abstract`.

En Java se coloca `abstract` en la declaración de la clase y/o del método. Ejemplo:

```java
public abstract class Soldado {
	public void saludar() { System.out.println("Soldado: Preparado."); }
	public abstract void atacar();
}

class Zapador extends Soldado {
	@Override
	public void atacar() { System.out.println("Zapador: Ataca con explosivos controlados."); }
}
```

Aquí `Soldado` es abstracta y obliga a implementar `atacar()` en las subclases.

## 8. ¿Qué efecto tiene la palabra clave `final` sobre métodos y clases en Java? ¿Cómo se relaciona con el polimorfismo? ¿Conoces algún ejemplo de clase `final` en la propia API estándar de Java?

### Respuesta

La palabra `final` aplicada a un método impide que ese método sea sobrescrito en subclases; aplicada a una clase impide que la clase sea heredada. En relación con el polimorfismo, un método `final` no se puede redefinir, por lo que su comportamiento queda inalterable y no participa en la sobreescritura.

Un ejemplo conocido de clase `final` en la API de Java es `java.lang.String`, que no puede ser extendida para preservar su inmutabilidad y comportamiento garantizado.

## 9. En Java, qué son las **"interfaces"**? ¿Son como clases abstractas? ¿Una clase puede implementar más de una interfaz?

### Respuesta

Una interfaz en Java es un contrato que declara métodos que las clases pueden implementar. A diferencia de una clase abstracta, una interfaz no mantiene estado de instancia y permite que una misma clase implemente múltiples interfaces, lo que facilita la composición de comportamientos. Desde Java 8 las interfaces pueden contener métodos `default` y `static` con implementación.

Una clase puede implementar más de una interfaz; esta característica proporciona una forma segura de herencia múltiple de comportamiento sin las complicaciones de la herencia múltiple de implementación.

## 10. Vamos a poner un ejemplo nuevo con polimorfismo. Queremos implementar una clase `Punto`, con un método `calcularDistanciaA`, que permite calcular la distancia a otro `Punto`. Sin embargo, como queremos trabajar con puntos 2D y 3D, haz que ese método sea abstracto y haya dos implementaciones de ese cálculo de distancia. Emplea `instanceof` y *downcasting* para verificar que se recibe un punto compatible y poder calcular correctamente la distancia siempre entre puntos del mismo subtipo. Aprovecha este diseño para crear ahora una clase `Linea`, que acepta `Punto`, sin saber de qué tipo es, y es capaz de dar su longitud independientemente de las dimensiones de sus puntos (las cuales desconoce).

### Respuesta

Se puede diseñar una jerarquía donde `Punto` sea abstracto y `calcularDistanciaA` sea abstracto, y luego proporcionar implementaciones para 2D y 3D usando `instanceof` y downcasting para comprobar compatibilidad. Un ejemplo práctico:

```java
public abstract class Punto {
	public abstract double calcularDistanciaA(Punto p);
}

public class Punto2D extends Punto {
	private final double x, y;
	public Punto2D(double x, double y) { this.x = x; this.y = y; }

	@Override
	public double calcularDistanciaA(Punto p) {
		if (p instanceof Punto2D) {
			Punto2D o = (Punto2D) p;
			return Math.hypot(x - o.x, y - o.y);
		}
		throw new IllegalArgumentException("Punto incompatible: se esperaba Punto2D");
	}
}

public class Punto3D extends Punto {
	private final double x, y, z;
	public Punto3D(double x, double y, double z) { this.x = x; this.y = y; this.z = z; }

	@Override
	public double calcularDistanciaA(Punto p) {
		if (p instanceof Punto3D) {
			Punto3D o = (Punto3D) p;
			return Math.sqrt(Math.pow(x - o.x, 2) + Math.pow(y - o.y, 2) + Math.pow(z - o.z, 2));
		}
		throw new IllegalArgumentException("Punto incompatible: se esperaba Punto3D");
	}
}

public class Linea {
	private final Punto a, b;
	public Linea(Punto a, Punto b) { this.a = a; this.b = b; }
	public double longitud() { return a.calcularDistanciaA(b); }
}
```

`Linea` no necesita conocer la dimensión concreta; delega el cálculo a los subtipos de `Punto`.

## 11. ¿Qué es la **"herencia de interfaces"** en Java? ¿Existe **"herencia múltiple de interfaces"**? Pon un ejemplo de una interfaz `Fichero` que tenga un método para leer su contenido en forma de `String` y luego dicha interfaz sea extendida por otra que sea `FicheroEscribible` que permita enviar contenido e incluso eliminar el fichero.

### Respuesta

La herencia de interfaces permite que una interfaz extienda otra, heredando sus métodos abstractos. Sí existe herencia múltiple de interfaces: una interfaz puede extender varias interfaces y una clase puede implementar múltiples interfaces.

Ejemplo:

```java
public interface Fichero {
	String leerContenido();
}

public interface FicheroEscribible extends Fichero {
	void escribirContenido(String contenido);
	void eliminar();
}
```

Una clase que implemente `FicheroEscribible` debe proporcionar `leerContenido`, `escribirContenido` y `eliminar`.
