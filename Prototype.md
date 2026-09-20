# Patrón Prototype

El **Prototype** es un patrón de diseño creacional que permite crear objetos nuevos copiando una instancia existente, llamada prototipo, en lugar de construirlos desde cero.

Su propósito es reducir el costo o la complejidad de crear objetos cuando ya existe otro objeto con una configuración similar.

## ¿Cómo funciona?

Una implementación habitual utiliza:

1. Una clase que puede crear una copia de sí misma mediante un método como `clone()`.
2. Un objeto prototipo que contiene el estado inicial que se quiere reutilizar.
3. Clientes que solicitan una copia del prototipo y modifican solo los valores que necesitan cambiar.

Cada copia debe ser un objeto independiente. Por ello, es importante decidir si los objetos que contiene el prototipo también deben copiarse o pueden compartirse.

## Código base en Java

La siguiente implementación usa un constructor de copia para crear una nueva instancia a partir de otra existente:

```java
public class MyClass {

    private String name;

    public MyClass(String name) {
        this.name = name;
    }

    public MyClass(MyClass source) {
        this.name = source.name;
    }

    public MyClass copy() {
        return new MyClass(this);
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

}
```

## Ejemplos

### 1. Plantillas de documentos

**Problema:** una aplicación genera documentos que comparten la mayor parte de su contenido y estilo, como el tipo de letra, los márgenes y una estructura inicial. Crear y configurar cada documento desde cero repite el mismo trabajo y hace más probable que las plantillas queden inconsistentes.

**Solución:** `Document` actúa como prototipo. La aplicación crea una copia de la plantilla y modifica únicamente el título y el contenido necesarios para cada documento:

```java
public class Document {
    private String font;
    private int margin;
    private String title;

    public Document(String font, int margin, String title) {
        this.font = font;
        this.margin = margin;
        this.title = title;
    }

    public Document(Document source) {
        this.font = source.font;
        this.margin = source.margin;
        this.title = source.title;
    }

    public Document copy() {
        return new Document(this);
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public String getTitle() {
        return title;
    }
}
```

```java
Document template = new Document("Arial", 20, "Untitled");
Document report = template.copy();
Document invoice = template.copy();

report.setTitle("Monthly report");
invoice.setTitle("Invoice 2026-001");

System.out.println(template.getTitle()); // Untitled
System.out.println(report.getTitle()); // Monthly report
System.out.println(invoice.getTitle()); // Invoice 2026-001
```

### 2. Personajes en un juego

**Problema:** un juego puede necesitar crear muchos personajes del mismo tipo con atributos iniciales iguales, como salud, velocidad y apariencia. Configurar todos esos atributos para cada personaje nuevo aumenta la repetición y dificulta cambiar los valores predeterminados del tipo de personaje.

**Solución:** `GameCharacter` se usa como prototipo de un tipo de personaje. Cada copia hereda los atributos iniciales y puede personalizar su nombre sin afectar a los demás personajes:

```java
public class GameCharacter {
    private String type;
    private int health;
    private int speed;
    private String name;

    public GameCharacter(String type, int health, int speed, String name) {
        this.type = type;
        this.health = health;
        this.speed = speed;
        this.name = name;
    }

    public GameCharacter(GameCharacter source) {
        this.type = source.type;
        this.health = source.health;
        this.speed = source.speed;
        this.name = source.name;
    }

    public GameCharacter copy() {
        return new GameCharacter(this);
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }
}
```

```java
GameCharacter archerPrototype = new GameCharacter("Archer", 100, 8, "Unknown");
GameCharacter firstArcher = archerPrototype.copy();
GameCharacter secondArcher = archerPrototype.copy();

firstArcher.setName("Robin");
secondArcher.setName("Marian");

System.out.println(archerPrototype.getName()); // Unknown
System.out.println(firstArcher.getName()); // Robin
System.out.println(secondArcher.getName()); // Marian
```

Los constructores de copia de estos ejemplos realizan una **copia superficial** porque sus atributos son valores primitivos o referencias inmutables como `String`. Si el objeto contiene colecciones u otros objetos mutables, se debe evaluar una **copia profunda** para impedir que el original y la copia compartan estado modificable.

## ¿Cuándo utilizarlo?

Prototype puede ser apropiado cuando crear un objeto requiere una configuración extensa, una operación costosa o valores iniciales que se repiten con frecuencia. También es útil cuando el código cliente debe crear objetos sin depender de sus clases concretas.

No es la mejor opción si construir el objeto es simple o si copiarlo es más complejo que inicializarlo directamente.

## Ventajas

- Reduce la repetición al reutilizar una configuración existente.
- Puede evitar operaciones costosas de inicialización.
- Permite crear variantes sin conocer la clase concreta del objeto.
- Facilita agregar nuevos prototipos configurados en tiempo de ejecución.

## Desventajas

- Definir correctamente la copia puede ser difícil cuando existen referencias mutables o ciclos entre objetos.
- Una copia superficial puede producir cambios inesperados al compartir estado interno.
- Puede aumentar la cantidad de clases o prototipos que la aplicación debe gestionar.

## Consideraciones importantes

- En Java, implementar `Cloneable` y sobrescribir `clone()` es posible, pero los constructores de copia o métodos como `copy()` suelen ser más explícitos y fáciles de controlar.
- Define con claridad qué objetos internos se comparten y cuáles se copian de forma independiente.
- Si los prototipos se registran y se reutilizan globalmente, protege su estado para evitar que una modificación accidental afecte a futuras copias.
