# Sistema de Pedidos - Patrones Observer y Strategy

## Descripción del Proyecto

Este proyecto extiende el sistema de pedidos implementado previamente, incorporando dos nuevos patrones de comportamiento:

* **Observer**: para gestionar eventos del sistema (confirmación y cancelación de pedidos) mediante el uso de Spring Events.
* **Strategy**: para aplicar diferentes estrategias de descuento de forma flexible y desacoplada.

El sistema permite reaccionar automáticamente a cambios en el estado del pedido y calcular descuentos dinámicamente según el contexto.

---

## Estructura del Proyecto

```text
src/
 └── main/
     └── java/com/universidad/pedidos/
         ├── observer/
         │   ├── PedidoConfirmadoEvent.java
         │   ├── PedidoCanceladoEvent.java
         │   ├── GestorPedidosService.java
         │   ├── EmailNotifier.java
         │   ├── InventarioUpdater.java
         │   └── AuditoriaLogger.java
         ├── strategy/
         │   ├── EstrategiaDescuento.java
         │   ├── DescuentoVIP.java
         │   ├── DescuentoTemporada.java
         │   ├── DescuentoCupon.java
         │   └── CarritoService.java
```

---

## Patrón Observer

### Problema

El sistema debe reaccionar automáticamente cuando ocurre un evento sobre un pedido (confirmación o cancelación), notificando a distintos subsistemas sin acoplarlos directamente.

---

### Solución

Se implementa el patrón Observer utilizando el sistema de eventos de Spring, donde un componente publica eventos y múltiples suscriptores reaccionan a ellos.

---

### Flujo de eventos

```text
GestorPedidosService (Publisher)
        ↓
Publica evento (PedidoConfirmadoEvent / PedidoCanceladoEvent)
        ↓
-----------------------------------------
↓                ↓                ↓
EmailNotifier   InventarioUpdater   AuditoriaLogger
(Subscriptor)   (Subscriptor)       (Subscriptor)
```

---

### Componentes

* **Publisher**:

    * `GestorPedidosService`: publica eventos cuando cambia el estado del pedido.

* **Eventos**:

    * `PedidoConfirmadoEvent`
    * `PedidoCanceladoEvent`

* **Suscriptores**:

    * `EmailNotifier`: envía notificaciones al cliente
    * `InventarioUpdater`: actualiza el stock
    * `AuditoriaLogger`: registra la operación

---

### ¿Cómo agregar un nuevo suscriptor?

Para añadir un nuevo comportamiento ante eventos, se debe crear una clase anotada con @Component o @Service y definir métodos con @EventListener para reaccionar a los eventos publicados.

Ejemplo de un suscriptor real del sistema: 

```java
@Component
public class EmailNotifier {

    @EventListener public void onConfirmado(PedidoConfirmadoEvent e) { 
        System.out.println("[EMAIL] Confirmación enviada para pedido: " + 
                e.getPedido().getId()); 
    }

    @EventListener public void onCancelado(PedidoCanceladoEvent e) { 
        System.out.println("[EMAIL] Aviso de cancelación enviado para: " + 
                e.getPedido().getId()); 
    } 
}
```

Cada método anotado con @EventListener se ejecuta automáticamente cuando ocurre el evento correspondiente.

Esto permite que múltiples suscriptores reaccionen a los mismos eventos sin que el publicador tenga conocimiento de ellos, manteniendo un bajo acoplamiento en el sistema.

---

## Patrón Strategy

### Problema

El sistema necesita aplicar diferentes tipos de descuentos según el contexto (cliente VIP, temporada o cupón), evitando condicionales complejos.

---

### Solución

Se implementa el patrón Strategy para encapsular cada algoritmo de descuento en clases independientes.

---

### Componentes

* `EstrategiaDescuento`: interfaz base

* `DescuentoVIP`: descuento para clientes VIP

* `DescuentoTemporada`: descuento por promociones

* `DescuentoCupon`: descuento por cupón

* `CarritoService`: selecciona y aplica la estrategia adecuada

---

### Flujo de ejecución

```text
Seleccionar estrategia → Aplicar descuento → Retornar monto final
```

---

### ¿Cómo activar cada estrategia?

Las estrategias pueden seleccionarse dinámicamente desde el servicio:

```java
double total = carritoService.aplicarDescuento("VIP", 100000);
```

Ejemplos:

* `"VIP"` → DescuentoVIP
* `"TEMPORADA"` → DescuentoTemporada
* `"CUPON"` → DescuentoCupon

Esto permite cambiar el comportamiento sin modificar la lógica principal.

---

## Ejecución del Proyecto

### Requisitos

* Java 17 o superior
* Maven

---

### Compilar

```bash
mvn clean package
```

---

### Ejecutar

```bash
mvn spring-boot:run
```

---

## Evidencia de Ejecución

El proyecto incluye capturas de pantalla que muestran:

* Pruebas unitarias probando el funcionamiento de los patrones implementados
* Ejecucion del programa despues de la implementacion


