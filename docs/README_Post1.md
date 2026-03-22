# Sistema de Pedidos - Patrones COR y Command con Undo

## Descripción del Proyecto

Este proyecto implementa un sistema backend de procesamiento de pedidos utilizando Spring Boot. El objetivo es modelar el flujo de validación de pedidos y la ejecución de acciones sobre estos, aplicando los patrones de comportamiento:

* **Chain of Responsibility**: para validar pedidos de forma secuencial.
* **Command con Undo**: para encapsular acciones sobre pedidos y permitir revertirlas.

---

## Estructura del Proyecto

```text
src/
 └── main/
     └── java/com/universidad/pedidos/
         ├── chain/
         │   ├── ValidadorPedido.java
         │   ├── ValidadorStock.java
         │   ├── ValidadorMonto.java
         │   └── ValidadorCredito.java
         ├── command/
         │   ├── Comando.java
         │   ├── ComandoConfirmar.java
         │   ├── ComandoAplicarDescuento.java
         │   └── HistorialComandos.java
         ├── model/
         │   └── Pedido.java
         └── PedidosApp.java

 └── test/
     └── java/com/universidad/pedidos/
         └── CadenaValidacionTest.java
```

---

## Patrón Chain of Responsibility

### Problema

El sistema requiere validar pedidos mediante múltiples reglas (stock, monto mínimo y crédito del cliente), manteniendo bajo acoplamiento entre las validaciones.

---

### Solución

Se implementa una cadena de validadores donde cada uno procesa el pedido y delega al siguiente si la validación es exitosa.

---

### Cadena de Validación

```text
Pedido → ValidadorStock → ValidadorMonto → ValidadorCredito → Pedido aprobado
```

---

### Funcionamiento

* `ValidadorStock`: verifica disponibilidad de productos
* `ValidadorMonto`: valida que el monto sea mayor a un mínimo
* `ValidadorCredito`: verifica el crédito del cliente

Cada validador:

* Procesa su condición
* Si falla → lanza excepción
* Si pasa → delega al siguiente

---

### Ejemplo de salida esperada

```text
Validando stock...
Validando monto mínimo...
Validando crédito...
Pedido aprobado
```

---

## Patrón Command con Undo

### Problema

Las acciones sobre un pedido (confirmar, aplicar descuento) deben ser reversibles y desacopladas del objeto que las ejecuta.

---

### Solución

Se encapsulan las acciones como comandos y se mantiene un historial para permitir deshacer la última operación.

---

### Componentes

* `Comando`: interfaz base
* `ComandoConfirmar`: confirma el pedido
* `ComandoAplicarDescuento`: aplica descuento
* `HistorialComandos`: almacena comandos ejecutados y permite undo

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

## Pruebas

El proyecto incluye pruebas unitarias para validar el comportamiento de la cadena de validación.

Ejecutar:

```bash
mvn test
```

---

## Evidencia de Ejecución

El proyecto incluye capturas de pantalla en la carpeta screenshots que muestran:

* Validación secuencial de pedidos (Chain of Responsibility)
* Ejecución de comandos y uso de undo (Command)
* Resultados de pruebas unitarias

