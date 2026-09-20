# Patrón Singleton

El **Singleton** es un patrón de diseño creacional que garantiza que una clase tenga una única instancia y proporciona un punto de acceso a ella.

Su propósito es controlar la creación de objetos cuando varias partes de una aplicación necesitan compartir la misma instancia.

## ¿Cómo funciona?

Una implementación habitual utiliza:

1. Un **constructor privado** para impedir que otras clases creen objetos directamente.
2. Un **campo estático** que almacena la única instancia.
3. Un **método estático** que devuelve esa instancia.

Cada llamada al método de acceso devuelve el mismo objeto.

## Código base en Java

La siguiente implementación crea la instancia la primera vez que se solicita:

```java
public final class MyClass {

    private static MyClass instance;

    private MyClass() {
    }

    public static MyClass getInstance() {
        if (instance == null) {
            instance = new MyClass();
        }
        return instance;
    }

}
```

## Ejemplos

### 1. Configuración compartida

Una aplicación puede centralizar datos de configuración disponibles para todos sus componentes:

```java
public final class Configuration {
    private static Configuration instance;

    private final String applicationName;

    private Configuration() {
        applicationName = "My application";
    }

    public static Configuration getInstance() {
        if (instance == null) {
            instance = new Configuration();
        }
        return instance;
    }

    public String getApplicationName() {
        return applicationName;
    }
}
```

```java
Configuration first = Configuration.getInstance();
Configuration second = Configuration.getInstance();

System.out.println(first == second); // true
System.out.println(first.getApplicationName()); // My application
```

### 2. Registro de eventos

Un registro único puede recibir mensajes desde distintas partes de la aplicación:

```java
public final class EventLogger {
    private static EventLogger instance;

    private EventLogger() {
    }

    public static EventLogger getInstance() {
        if (instance == null) {
            instance = new EventLogger();
        }
        return instance;
    }

    public void log(String message) {
        System.out.println("Event: " + message);
    }
}
```

```java
EventLogger primaryLogger = EventLogger.getInstance();
EventLogger secondaryLogger = EventLogger.getInstance();

primaryLogger.log("User authenticated");
System.out.println(primaryLogger == secondaryLogger); // true
```

Los tres fragmentos usan inicialización diferida. La versión base y los ejemplos no son seguros ante accesos concurrentes: dos hilos podrían crear instancias distintas al mismo tiempo.

## Formas de inicialización

- **Anticipada:** la instancia se crea al inicializar la clase. Es sencilla, pero puede crear el objeto aunque finalmente no se utilice.
- **Diferida:** la instancia se crea cuando se solicita por primera vez. Puede evitar trabajo innecesario, pero requiere una implementación que controle correctamente el acceso concurrente.

## ¿Cuándo utilizarlo?

Puede ser apropiado cuando existe un requisito real de tener una sola instancia dentro del ámbito de la aplicación, por ejemplo, un componente que exponga una configuración común.

Antes de aplicarlo, conviene comprobar si basta con crear un objeto y compartirlo mediante inyección de dependencias. Necesitar un objeto compartido no siempre implica necesitar el patrón Singleton.

## Ventajas

- Controla la creación de la instancia.
- Permite que distintos componentes accedan al mismo objeto.
- Puede evitar la creación repetida de un recurso costoso cuando una única instancia es suficiente.

## Desventajas

- El acceso global puede ocultar dependencias y aumentar el acoplamiento.
- Puede dificultar las pruebas, especialmente si conserva estado entre ellas.
- El estado mutable compartido requiere cuidado adicional en aplicaciones concurrentes.
- Reúne la lógica de la clase con la responsabilidad de gestionar su propia instancia.

## Consideraciones importantes

- La unicidad depende del entorno. En Java, normalmente se limita al cargador de clases que carga la clase; no garantiza una sola instancia entre varios procesos o servidores.
- Un constructor privado no cubre por sí solo todos los mecanismos especiales de creación de objetos, como la reflexión o la deserialización. Si estos intervienen, deben evaluarse medidas adicionales.
- Singleton no es lo mismo que una clase de métodos estáticos: el primero mantiene un objeto, que puede implementar interfaces y pasarse como dependencia.
- Si necesitas varias configuraciones, aislamiento entre pruebas o sustituciones frecuentes, suele ser más flexible gestionar el ciclo de vida del objeto mediante inyección de dependencias.
