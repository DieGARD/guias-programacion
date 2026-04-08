<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Herencia". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación, Excepciones y Composición.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
## 1. En orientación a objetos, ¿qué es la **herencia** y su relación con "A es-un B"?. Explica las dos implicaciones principales: (1) **compatibilidad de tipos** y (2) **herencia de estado y comportamiento**. Pon un ejemplo en Java muy sencillo, donde un `Soldado` tiene un `nombre` (privado) y un método `saludar()` que muestra su nombre. Hay dos subtipos: un `Artillero`, que es capaz de disparar cohetes y un `Zapador` que pone minas, ambos heredan el atributo nombre y la capacidad de saludar. Además, y de forma específica, el artillero tiene un número de cohetes y el zapador un número de minas, accesibles mediante "getters" específicos. Respecto a la compatibilidad de tipos, aprovechémosla: crea un array de `Soldado`, mete varios de distinto tipo (son todos compatibles con `Soldado`). Recórrela y que todos te saluden.

### Respuesta
La herencia es la relación por la que una clase más específica reutiliza y amplía a otra más general. La idea de "A es un B" significa que un `Artillero` es un `Soldado`, y por eso se le puede tratar como tal cuando haga falta. De esa relación se derivan dos consecuencias importantes: por un lado, la compatibilidad de tipos, que permite usar objetos de la subclase donde se espera un objeto de la superclase; por otro, la herencia de estado y comportamiento, que hace que la subclase reciba los atributos y métodos de la superclase y añada los suyos propios.

En el ejemplo, `Soldado` aporta el `nombre` y el método `saludar()`, mientras que `Artillero` añade el número de cohetes y `Zapador` añade el número de minas. El atributo `nombre` se mantiene privado en la superclase, pero sigue formando parte de los objetos de las subclases porque procede de `Soldado`. Gracias a la compatibilidad de tipos, se puede construir un array de `Soldado` y guardar en él objetos de `Artillero` y `Zapador` sin problema, recorriéndolo después con un único código para que todos saluden.

```java
class Soldado {
	private String nombre;

	public Soldado(String nombre) {
		this.nombre = nombre;
	}

	public void saludar() {
		System.out.println("Hola, soy " + nombre);
	}
}

class Artillero extends Soldado {
	private int numeroCohetes;

	public Artillero(String nombre, int numeroCohetes) {
		super(nombre);
		this.numeroCohetes = numeroCohetes;
	}

	public int getNumeroCohetes() {
		return numeroCohetes;
	}
}

class Zapador extends Soldado {
	private int numeroMinas;

	public Zapador(String nombre, int numeroMinas) {
		super(nombre);
		this.numeroMinas = numeroMinas;
	}

	public int getNumeroMinas() {
		return numeroMinas;
	}
}

public class DemoSoldados {
	public static void main(String[] args) {
		Soldado[] soldados = {
			new Artillero("Ana", 5),
			new Zapador("Luis", 3),
			new Artillero("Marta", 8)
		};

		for (Soldado soldado : soldados) {
			soldado.saludar();
		}
	}
}
```


## 2. Al crear los soldados concretos, ¿cuántos constructores se ejecutan y en qué orden? ¿Qué significa `super` dentro de un constructor? Si la clase base no tiene visible el constructor sin parámetros, ¿debo llamar a `super` siempre? 

### Respuesta
Al construir un objeto de una subclase se ejecutan tantos constructores como niveles de herencia haya, siempre desde la clase base hacia la clase más concreta. Primero se entra en el constructor de la superclase, luego en el de la subclase, y así sucesivamente hasta terminar en el constructor de la clase real que se está instanciando. Esa secuencia garantiza que la parte común del objeto quede inicializada antes de añadir los datos específicos.

Dentro de un constructor, `super(...)` sirve para llamar explícitamente al constructor de la superclase. En Java, esa llamada debe aparecer como primera instrucción del constructor. Si la clase base no tiene visible un constructor sin parámetros, entonces sí es obligatorio invocar alguno de sus constructores con argumentos mediante `super(...)`; si no se hace, el compilador intentará insertar `super()` por defecto y fallará. Si existe un constructor sin parámetros accesible, la llamada puede omitirse y Java la insertará de forma implícita.

```java
class Soldado {
	private String nombre;

	public Soldado(String nombre) {
		this.nombre = nombre;
	}
}

class Artillero extends Soldado {
	public Artillero(String nombre) {
		super(nombre);
	}
}
```

En ese ejemplo, al crear un `Artillero` se ejecuta primero `Soldado(String nombre)` y después el constructor de `Artillero`. Esa cadena de construcción es la que hace que la parte heredada quede preparada antes de usar el comportamiento propio de la subclase.

## 3. Respecto a los objetos de subclases en memoria, los atributos privados de la superclase, ¿forman parte de una instancia de la subclase en memoria? En caso afirmativo ¿implica que se puedan usar desde el código de la subclase? Explícalo con el ejemplo de `Soldado` y alguna de sus subclases.

### Respuesta
Sí, los atributos privados de la superclase forman parte de la memoria de la instancia de la subclase. Un objeto de `Artillero` no contiene solo sus atributos propios, sino también la porción heredada de `Soldado`, con todo su estado. Por eso se dice que la subclase amplía la representación interna de la superclase en lugar de construir un objeto totalmente independiente.

Eso no significa que el código de la subclase pueda acceder directamente a esos atributos privados. La visibilidad sigue mandando: si `nombre` es `private` en `Soldado`, `Artillero` o `Zapador` no pueden escribir `nombre` directamente. Para usar ese dato se necesitan métodos de acceso de la superclase, como `getNombre()`, o un método heredado que ya haga uso de él. La memoria contiene el dato, pero el acceso continúa restringido por el encapsulamiento.

```java
class Soldado {
	private String nombre;

	public Soldado(String nombre) {
		this.nombre = nombre;
	}

	protected String getNombre() {
		return nombre;
	}
}

class Zapador extends Soldado {
	public Zapador(String nombre) {
		super(nombre);
	}

	public void ponerMina() {
		System.out.println(getNombre() + " coloca una mina");
	}
}
```

En ese caso, el nombre existe dentro del objeto `Zapador`, pero no se accede a él por ser un campo privado de `Soldado`; se accede a través del método heredado. Esa distinción entre presencia en memoria y acceso desde el código es clave para entender la herencia en Java.

## 4. ¿Qué implica en términos de **extensibilidad** de código el hecho de que sean compatibles a nivel de tipos? Ilustra esto añadiendo un nuevo tipo de `Soldado` y demostrando que el código para pedir el saludo a todos los soldados no se modifica.

### Respuesta
La compatibilidad de tipos permite que el código se escriba contra la superclase y no contra cada tipo concreto. Eso hace que el programa sea extensible, porque añadir una nueva subclase no obliga a tocar el código que ya trabaja con la abstracción general. En la práctica, el bucle que recorre soldados no necesita saber si delante tiene un `Artillero`, un `Zapador` o cualquier otro subtipo.

La consecuencia directa es que se reduce el acoplamiento y se favorece el mantenimiento. Si se incorpora un nuevo `Sanitario`, basta con definir la clase y añadir objetos de ese tipo al array o colección. El código que saluda a los soldados sigue siendo el mismo, porque solo depende de la operación común `saludar()` definida en `Soldado`.

```java
class Sanitario extends Soldado {
	public Sanitario(String nombre) {
		super(nombre);
	}
}

Soldado[] soldados = {
	new Artillero("Ana", 5),
	new Zapador("Luis", 3),
	new Sanitario("Carmen")
};

for (Soldado soldado : soldados) {
	soldado.saludar();
}
```

En ese escenario no se modifica el bucle, ni el método `saludar()`, ni la lógica general del programa. Solo se amplía el conjunto de tipos compatibles, que es precisamente la ventaja más útil de la herencia bien usada.


## 5. En Java, cuando trabajo con referencias y herencia. ¿Puedo tener una referencia del supertipo que apunte a objetos reales de un subtipo? ¿Puedo invocar con la referencia del supertipo a métodos públicos del subtipo? ¿En qué consiste el **"upcasting"** y el **"downcasting"**? ¿Qué es el `instanceof`? Pon un ejemplo de recorrido de un array de `Soldado`, comprobando que, si el objeto real es un `Artillero`, solicite el número de cohetes que tiene y los imprima.

### Respuesta
Sí, una referencia del supertipo puede apuntar a un objeto real de un subtipo. Eso es una de las bases del polimorfismo en Java: una variable de tipo `Soldado` puede referirse a un `Artillero` o a un `Zapador`. Sin embargo, con esa referencia solo se pueden invocar, en tiempo de compilación, los métodos que el tipo de la referencia conoce, es decir, los declarados en `Soldado`. Si el subtipo añade métodos nuevos, no serán visibles sin un cambio de tipo.

El `upcasting` es la conversión implícita desde un subtipo a su supertipo, como cuando un `Artillero` se guarda en una variable `Soldado`. El `downcasting` es la conversión inversa, desde el supertipo al subtipo, y debe hacerse de forma explícita porque puede fallar si el objeto real no es del tipo esperado. Para evitar errores, `instanceof` permite comprobar primero el tipo real del objeto antes de hacer el cast.

```java
Soldado[] soldados = {
	new Artillero("Ana", 5),
	new Zapador("Luis", 3)
};

for (Soldado soldado : soldados) {
	soldado.saludar();

	if (soldado instanceof Artillero) {
		Artillero artillero = (Artillero) soldado;
		System.out.println("Cohetes: " + artillero.getNumeroCohetes());
	}
}
```

En ese recorrido, el método `saludar()` se invoca sin problema porque pertenece a `Soldado`, mientras que el acceso a `getNumeroCohetes()` exige confirmar primero que el objeto real es un `Artillero`. Esa comprobación evita un `ClassCastException` y hace que el código sea seguro.


## 6. Respecto a la ocultación de información y herencia, ¿qué significa acceso **"protegido"** de métodos y/o atributos? ¿Cómo se implementa en Java? Pon un ejemplo de uso de en la clase `Soldado` para que su nombre sea protegido y pueda usarse en el método de poner bombas del `Zapador`.

### Respuesta
El acceso protegido permite que un atributo o método sea visible para la propia clase, para sus subclases y, en Java, también para las clases del mismo paquete. Es un punto intermedio entre `private` y `public`: expone más que un miembro privado, pero menos que uno público. Se usa cuando una parte del estado o del comportamiento debe ser reutilizable por la herencia sin quedar abierta a cualquiera.

En Java se implementa con la palabra clave `protected`. En el ejemplo, si `nombre` se declara `protected` en `Soldado`, `Zapador` puede utilizarlo directamente en su propio método para colocar bombas. Aun así, conviene recordar que esta decisión reduce el encapsulamiento respecto a `private`, así que debe aplicarse solo cuando exista una razón clara de diseño.

```java
class Soldado {
	protected String nombre;

	public Soldado(String nombre) {
		this.nombre = nombre;
	}
}

class Zapador extends Soldado {
	public Zapador(String nombre) {
		super(nombre);
	}

	public void ponerBombas() {
		System.out.println(nombre + " coloca bombas");
	}
}
```

En ese caso, `Zapador` puede usar `nombre` sin necesidad de getter porque el campo ha quedado accesible para la jerarquía. Esa facilidad existe, pero debe equilibrarse con el riesgo de acoplar demasiado a la subclase con la estructura interna de la superclase.


## 7. En los lenguajes orientados a objetos ¿hay una **clase base** para todos los objetos? ¿Ocurre en todos los lenguajes? ¿Qué ocurre en Java?

### Respuesta
No todos los lenguajes orientados a objetos tienen una clase base universal para todos los objetos. En algunos casos sí existe un ancestro común muy claro; en otros, el modelo es distinto o está más mezclado con tipos primitivos, estructuras o sistemas de herencia especiales. Por eso no puede afirmarse que haya una regla idéntica para todos los lenguajes.

En Java sí existe una clase raíz para los objetos: `java.lang.Object`. Todas las clases heredan directa o indirectamente de ella, y por eso comparten métodos comunes como `toString()`, `equals()`, `hashCode()` o `getClass()`. Los tipos primitivos no heredan de `Object`, pero sus envoltorios, como `Integer` o `Double`, sí lo hacen. También los arrays son objetos y, por tanto, descienden de `Object`.


## 8. ¿Qué es la **"herencia múltiple"**? ¿Existe en Java herencia múltiple?

### Respuesta
La herencia múltiple es la posibilidad de que una clase herede directamente de más de una clase base. A primera vista resulta atractiva porque parece permitir reutilizar más código, pero también introduce problemas de ambigüedad, como el conocido problema del diamante, en el que una clase recibe dos versiones distintas de un mismo miembro heredado.

En Java no existe herencia múltiple de clases. Una clase solo puede extender una clase concreta mediante `extends`. Sin embargo, sí puede implementar varias interfaces con `implements`, lo que cubre parte de la necesidad de combinar contratos sin arrastrar múltiples estados heredados. Esa restricción simplifica el modelo y evita muchas ambigüedades de diseño.


## 9. Las excepciones en los lenguajes orientados a objetos son objetos. Por tanto, se pueden crear excepciones personalizadas. Pon un ejemplo en Java de una excepción personalizada (`UsuarioNoEncontradoException`), que sea *no controlada* y que además este compuesto con un `Usuario`, para saber qué `Usuario` dio el problema. Permite además que se pueda incluir la causa, es decir, sobrecarga el constructor para tener una versión que permita añadir la causa subyacente. 

### Respuesta
Las excepciones personalizadas permiten expresar mejor los errores del dominio porque la excepción puede llevar información útil, no solo un mensaje genérico. Si la excepción es no controlada, debe extender `RuntimeException`, de modo que no obligue a capturarla o declararla explícitamente. Además, si se compone con un `Usuario`, la excepción puede guardar exactamente qué usuario provocó el problema.

También conviene sobrecargar el constructor para admitir una causa original. Así, cuando la excepción se lanza a partir de otro fallo interno, se conserva la trazabilidad completa. Ese patrón hace que el error sea más fácil de depurar sin perder el contexto funcional que aporta el objeto `Usuario`.

```java
class Usuario {
	private final String nombre;

	public Usuario(String nombre) {
		this.nombre = nombre;
	}

	@Override
	public String toString() {
		return nombre;
	}
}

class UsuarioNoEncontradoException extends RuntimeException {
	private final Usuario usuario;

	public UsuarioNoEncontradoException(Usuario usuario) {
		super("No se ha encontrado el usuario: " + usuario);
		this.usuario = usuario;
	}

	public UsuarioNoEncontradoException(Usuario usuario, Throwable causa) {
		super("No se ha encontrado el usuario: " + usuario, causa);
		this.usuario = usuario;
	}

	public Usuario getUsuario() {
		return usuario;
	}
}
```

Con esa definición, la excepción no solo informa de que el usuario falta, sino también de cuál era ese usuario y de cuál fue la causa subyacente si se proporciona. Eso la convierte en una pieza de información mucho más rica que una excepción genérica.


## 10. Herencia vs. Composición. Se dice que no se debe emplear herencia simplemente por reutilizar código, es decir, que si quiero reutilizar código simplemente, no debo pensar en herencia como primera opción ¿por qué?

### Respuesta
No se debe usar herencia solo para reutilizar código porque la herencia no es un mecanismo de copia de código, sino una relación de especialización real. Si no existe un verdadero "es-un", la jerarquía resultante queda forzada y el diseño se vuelve frágil. El problema no es solo estético: una relación de herencia incorrecta tiende a romper expectativas y a dificultar el mantenimiento.

Además, la herencia crea un acoplamiento fuerte con la superclase. La subclase depende de la forma en que está construida la clase base y de cómo evolucionan sus métodos y atributos. Si el único objetivo es reutilizar unas pocas operaciones, suele ser más seguro extraer esa lógica a una clase auxiliar, a un servicio o a un componente y usar composición o delegación.


## 11. Herencia vs. Composición. Se dice que se debe *"favorecer la composición frente a la herencia"*, ¿por qué?

### Respuesta
Se recomienda favorecer la composición porque ofrece más flexibilidad y menos acoplamiento. Con composición, una clase contiene a otra y delega parte de su comportamiento, lo que permite cambiar esa dependencia sin romper una jerarquía entera. Si en el futuro cambia la necesidad, el componente puede sustituirse o combinarse con otros sin reescribir la estructura principal.

También se preserva mejor el encapsulamiento. La clase compuesta no necesita conocer tantos detalles internos de la clase contenida como ocurre a menudo con la herencia. Eso reduce el riesgo de que pequeños cambios en una clase base provoquen efectos colaterales en muchas subclases, algo especialmente importante en sistemas grandes o muy evolucionados.


## 12. Herencia vs. Composición. Se dice que la *"herencia rompe la encapsulación"*, ¿a qué se refiere esto?

### Respuesta
Se dice que la herencia rompe la encapsulación porque la subclase acaba dependiendo, no solo del comportamiento público de la superclase, sino también de detalles de su implementación interna. En lugar de ver a la superclase como una caja negra, la subclase suele apoyarse en atributos `protected`, métodos heredados y reglas internas que quizá no deberían formar parte de la interfaz pública.

Eso convierte muchos cambios internos en cambios peligrosos. Si se modifica cómo la superclase guarda un dato, cómo ordena una operación o qué precondiciones espera un método, la subclase puede dejar de funcionar correctamente aunque su interfaz no haya cambiado. Por eso la herencia tiende a hacer más visible la estructura interna de la clase base y debilita la abstracción.


## 13. Pongamos un ejemplo de dos alternativas para lo mismo. Tenemos un `Estudiante` y un `Trabajador`, ambos tienen datos en común: el DNI y el nombre. Modelemos esto de dos formas: uno por herencia, con una superclase `Persona`, y otro con composición, con una clase `DatosPersonales`. Se debe recibir una instancia de `DatosPersonales` en el constructor de la clase `Estudiante` y `Trabajador`.

### Respuesta
En la solución por herencia, `Estudiante` y `Trabajador` extienden de `Persona`, de modo que ambas clases comparten directamente el DNI y el nombre. Esta opción expresa una relación fuerte entre los tipos y permite tratar a estudiantes y trabajadores como personas. Es una solución natural si se quiere modelar una verdadera generalización común.

En la solución por composición, en cambio, `Estudiante` y `Trabajador` contienen un objeto `DatosPersonales`. Esa alternativa separa mejor los datos compartidos del resto del comportamiento y hace más fácil reutilizar o sustituir la información personal sin depender de una jerarquía de herencia. Además, se ajusta bien cuando solo interesa compartir datos, no identidad de tipo.

```java
// Opción 1: herencia
class Persona {
	private final String dni;
	private final String nombre;

	public Persona(String dni, String nombre) {
		this.dni = dni;
		this.nombre = nombre;
	}
}

class Estudiante extends Persona {
	public Estudiante(String dni, String nombre) {
		super(dni, nombre);
	}
}

class Trabajador extends Persona {
	public Trabajador(String dni, String nombre) {
		super(dni, nombre);
	}
}

// Opción 2: composición
class DatosPersonales {
	private final String dni;
	private final String nombre;

	public DatosPersonales(String dni, String nombre) {
		this.dni = dni;
		this.nombre = nombre;
	}
}

class EstudianteCompuesto {
	private final DatosPersonales datosPersonales;

	public EstudianteCompuesto(DatosPersonales datosPersonales) {
		this.datosPersonales = datosPersonales;
	}
}

class TrabajadorCompuesto {
	private final DatosPersonales datosPersonales;

	public TrabajadorCompuesto(DatosPersonales datosPersonales) {
		this.datosPersonales = datosPersonales;
	}
}
```

En ambos casos se comparte el mismo estado básico, pero la intención del diseño es distinta. La herencia expresa que ambos tipos son personas; la composición expresa que ambos tipos tienen datos personales. Elegir una u otra depende de si se quiere modelar una especialización real o solo reutilizar información común.
