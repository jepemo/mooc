# Principio de Abierto/Cerrado
* Abierto para extension y cerrado para modificacion
* Permite no depender de implementaciones especificas. Por ejemplo:
  * Si tengo un switch para tratar cada tipologia de una clase
  * Cambiarlo para que cada caso lo trate una clase diferente
* Facilita añadir nuevos casos de uso

### Ejemplo

Este caso es incorrecto, ya que para recoger el valor de "SentLengthPercentage" tendriamos que tener un switch para cada clase y en cada caso llamar al metodo que tocara. Esto complica la extensibilidad (anyadir nuevas clases).

```java
final class Song {
  private Double totalLength;
  private Double sentLength;

  public Double getSentLengthPercentage() {
    return sentLength * 100 / totalLength;
  }
}

final class File {
  private Double totalLength;
  private Double sentLength;

  public Double getSentLengthPercentage() {
    return sentLength * 100 / totalLength;
  }
}
```

Por el contrato, si extraemos los que vamos a consultar con una interfaz y además sacamos el calculo a otra clase, se desacopla.

```java
interface Measurable {
  public Double getTotalLength();
  public Double getSentLength();
}

final class Song implements Measurable {
    private Double totalLength;
    private Double sentLength;
    
    @Override
    public Double getTotalLength() {
        return totalLength;
    }
    
    @Override
    public Double getSentLength() {
        return sentLength;
    }
}

final class Progress {
    public Double getSentLengthPercentage(Measurable measurable) {
        return measurable.getSentLength() * 100 / measurable.getTotalLength();
    }
}
```

Si utilizamos clases abstractas en vez de interfaces:

```java
abstract class Measurable {
    abstract Double getTotalLength();
    abstract Double getSentLength();
    
    public Double getSentLengthPercentage() {
        return getSentLength() * 100 / getTotalLength();
    }
}

final class Song extends Measurable {
    @Override
    public Double getTotalLength() {
        // ...
    }

    @Override
    public Double getSentLength() {
        // ...
    } 
}

final class Progress {
    public Double getSentLengthPercentage(Measurable measurable) {
        return measurable.getSentLengthPercentage();
    }
}
```

* Interfaces vs Clases abstractas
* Interaces:
  * No modifica arbol de jerarquia
  * Permite implementar n interfaces
  * Se utiliza mucho para desacoplar entre capas.
* Clases abstractas:
  * Permite patron template method.
    * Este patron se puede ver en el ultimo ejemplo de codigo. En la clase padre se implementa la secuencia de pasos que conforman el template (getSentLengthPercentage), pero en los hijos se implementan los huecos de ese template (getSentLength, getTotalLength).  
  * Permite tener getters (metodos) privados. Es decir, metodos ya implementados heredables.
  * Se suele utilizar solamente en modelos de dominio.
  
## Keep it Real!
* Por ejemplo en la clase VideoCreator, que inicialmente hace varias acciones al "crear" un video: GuardaDB, notifica subs, envia correo, etc.
  * Al final nos damos cuenta de que hay una accion principal y otras adicionales. En este caso guardar en BD es la principal. Luego publico un evento de dominio: "Video Creado".
  * Sobre este evento ya se pueden hacer las otras acciones, para los servicios que lo esten escuchando.
* Al separar cada "accion" en una clase diferente, que reacciona por eventos, estos son:
  * Se mejora la mantenibilidad
  * Testabilidad
  * Aumenta tolerancia al cambio
  
## Test

El Principio de Abierto/Cerrado (OCP) hace referencia a...
- [ ] Que los Getters de una clase deben ser públicos y los Setters privados
- [x] Que nuestro código esté abierto a extensión y cerrado a modificación
- [ ] Ninguna de las anteriores es correcta

Integrar un sistema de colas para la publicación/consumo de Eventos de Dominio nos brinda..
- [x] Un código más testable y mayor tolerancia al cambio
- [ ] Un código menos complejo y menor tolerancia al cambio
- [ ] Un código más testable y mayor acoplamiento entre clases
