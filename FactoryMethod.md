# Patrón Factory Method

El **Factory Method** es un patrón de diseño creacional que define una operación para crear objetos, pero deja que las subclases decidan qué clase concreta se debe instanciar.

Su propósito es separar el código que utiliza un producto de la decisión sobre qué implementación concreta crear.

## ¿Cómo funciona?

Una implementación habitual utiliza:

1. Una interfaz o clase abstracta para el producto que utilizará el código cliente.
2. Un creador abstracto que declara el método fábrica, normalmente llamado `createProduct()`.
3. Creadores concretos que sobrescriben el método fábrica para devolver un producto concreto.
4. Código cliente que trabaja mediante el tipo abstracto del producto, sin depender de sus implementaciones concretas.

El creador puede contener lógica común que usa el producto creado por el método fábrica. Las subclases solo cambian la decisión de creación.

## Código base en Java

La siguiente implementación permite que cada creador elija qué producto construir:

Cada bloque siguiente corresponde a un archivo Java independiente:

`Product.java`

```java
public interface Product {
    String use();
}
```

`ConcreteProductA.java`

```java
public class ConcreteProductA implements Product {
    @Override
    public String use() {
        return "Using product A";
    }
}
```

`Creator.java`

```java
public abstract class Creator {
    public abstract Product createProduct();

    public String processProduct() {
        Product product = createProduct();
        return product.use();
    }
}
```

`ConcreteCreatorA.java`

```java
public class ConcreteCreatorA extends Creator {
    @Override
    public Product createProduct() {
        return new ConcreteProductA();
    }
}
```

`Main.java`

```java
public class Main {
    public static void main(String[] args) {
        Creator creator = new ConcreteCreatorA();
        System.out.println(creator.processProduct()); // Using product A
    }
}
```

## Ejemplos

### 1. Envío de notificaciones

**Problema:** una aplicación debe notificar a sus usuarios por distintos canales, como correo electrónico o mensajes SMS. Si el servicio principal crea directamente objetos `EmailNotification` o `SmsNotification`, debe conocer cada clase concreta y cambiar cada vez que se incorpora un canal nuevo.

**Solución:** `NotificationService` declara `createNotification()` como Factory Method. Cada servicio concreto decide qué canal crear, mientras que el envío usa solamente la interfaz `Notification`:

Cada bloque siguiente corresponde a un archivo Java independiente:

`Notification.java`

```java
public interface Notification {
    String send(String message);
}
```

`EmailNotification.java`

```java
public class EmailNotification implements Notification {
    @Override
    public String send(String message) {
        return "Sending email: " + message;
    }
}
```

`SmsNotification.java`

```java
public class SmsNotification implements Notification {
    @Override
    public String send(String message) {
        return "Sending SMS: " + message;
    }
}
```

`NotificationService.java`

```java
public abstract class NotificationService {
    public abstract Notification createNotification();

    public String notifyUser(String message) {
        Notification notification = createNotification();
        return notification.send(message);
    }
}
```

`EmailNotificationService.java`

```java
public class EmailNotificationService extends NotificationService {
    @Override
    public Notification createNotification() {
        return new EmailNotification();
    }
}
```

`SmsNotificationService.java`

```java
public class SmsNotificationService extends NotificationService {
    @Override
    public Notification createNotification() {
        return new SmsNotification();
    }
}
```

`Main.java`

```java
public class Main {
    public static void main(String[] args) {
        NotificationService emailService = new EmailNotificationService();
        NotificationService smsService = new SmsNotificationService();

        System.out.println(emailService.notifyUser("Your order has been shipped"));
        System.out.println(smsService.notifyUser("Your verification code is 123456"));
    }
}
```

### 2. Generación de documentos

**Problema:** una herramienta de informes puede exportar el mismo contenido a formatos distintos, como PDF y DOC. Si la lógica que prepara el informe instancia cada exportador directamente, queda acoplada a los formatos disponibles y se vuelve más difícil agregar uno nuevo, como HTML.

**Solución:** `ReportGenerator` prepara el contenido y delega la creación del exportador en `createExporter()`. Cada generador concreto entrega el exportador apropiado, mientras que la lógica de generación trabaja con `ReportExporter`:

Cada bloque siguiente corresponde a un archivo Java independiente:

`ReportExporter.java`

```java
public interface ReportExporter {
    String export(String content);
}
```

`PdfReportExporter.java`

```java
public class PdfReportExporter implements ReportExporter {
    @Override
    public String export(String content) {
        return "Exporting PDF: " + content;
    }
}
```

`DocReportExporter.java`

```java
public class DocReportExporter implements ReportExporter {
    @Override
    public String export(String content) {
        return "Exporting DOC: " + content;
    }
}
```

`ReportGenerator.java`

```java
public abstract class ReportGenerator {
    public abstract ReportExporter createExporter();

    public String generate(String content) {
        ReportExporter exporter = createExporter();
        return exporter.export(content);
    }
}
```

`PdfReportGenerator.java`

```java
public class PdfReportGenerator extends ReportGenerator {
    @Override
    public ReportExporter createExporter() {
        return new PdfReportExporter();
    }
}
```

`DocReportGenerator.java`

```java
public class DocReportGenerator extends ReportGenerator {
    @Override
    public ReportExporter createExporter() {
        return new DocReportExporter();
    }
}
```

`Main.java`

```java
public class Main {
    public static void main(String[] args) {
        ReportGenerator pdfGenerator = new PdfReportGenerator();
        ReportGenerator docGenerator = new DocReportGenerator();

        System.out.println(pdfGenerator.generate("Monthly sales report"));
        System.out.println(docGenerator.generate("Monthly sales report"));
    }
}
```

## ¿Cuándo utilizarlo?

Factory Method puede ser apropiado cuando una clase necesita trabajar con productos, pero no debe depender de una implementación concreta. También resulta útil cuando se quiere permitir que subclases o extensiones de una aplicación elijan el tipo de objeto que se creará.

No suele ser necesario cuando solo existe un tipo de producto, no se espera agregar otros y la creación no tiene variaciones relevantes.

## Ventajas

- Reduce el acoplamiento entre el código cliente y las clases concretas de los productos.
- Centraliza la lógica común del creador y delega la decisión de creación a las subclases.
- Facilita incorporar nuevos productos y creadores sin modificar el código que trabaja con la abstracción.
- Permite probar el creador con implementaciones alternativas del producto.

## Desventajas

- Puede aumentar la cantidad de clases, porque cada tipo de producto suele requerir un creador concreto.
- Introduce una jerarquía de herencia incluso cuando una composición simple podría ser suficiente.
- Si se necesita seleccionar productos con muchas condiciones en tiempo de ejecución, una fábrica simple o una estrategia puede ser más adecuada.

## Consideraciones importantes

- El método fábrica puede ser abstracto, como en los ejemplos, o tener una implementación predeterminada que las subclases pueden sobrescribir.
- Factory Method se basa habitualmente en herencia: una subclase cambia el producto que crea. Si la elección debe variar dinámicamente en una misma instancia, considera inyectar una fábrica o una estrategia.
- No debe confundirse con Abstract Factory: Factory Method crea normalmente una familia de un solo producto por método; Abstract Factory coordina la creación de familias completas de productos relacionados.
