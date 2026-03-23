# Pedidos Comportamiento

Aplicación Java con Spring Boot que implementa patrones de diseño de comportamiento para gestionar pedidos:
- **Chain of Responsibility**: Validación de pedidos mediante una cadena de validadores
- **Command**: Ejecución y deshecho (undo) de operaciones sobre pedidos

## Descripción del Proyecto

La aplicación gestiona pedidos con validaciones encadenadas:
- **ValidadorStock**: Verifica que la cantidad no exceda el stock disponible
- **ValidadorMonto**: Valida que el monto sea mayor al mínimo requerido
- **ValidadorCredito**: Confirma que el cliente tenga crédito disponible

También permite ejecutar comandos sobre los pedidos:
- **ComandoConfirmar**: Cambia el estado del pedido a CONFIRMADO
- **ComandoAplicarDescuento**: Aplica un descuento al total del pedido

Todos los comandos pueden deshacerse (undo) mediante el **HistorialComandos**.

## Requisitos Previos

- **Java 17 o superior**
- **Maven 3.6+** (incluido el wrapper `mvnw` en el proyecto)

## Instalación y Ejecución

### 1. Clonar o descargar el proyecto

```bash
cd pedidos-comportamiento
```

### 2. Compilar el proyecto

```bash
./mvnw clean compile
```

O en Windows:
```bash
mvnw.cmd clean compile
```

### 3. Ejecutar la aplicación

```bash
./mvnw spring-boot:run
```

O en Windows:
```bash
mvnw.cmd spring-boot:run
```

La aplicación ejecutará automáticamente la clase `PedidosApp` que demuestra:
- Validación de pedidos válidos e inválidos
- Ejecución de comandos (confirmación y descuentos)
- Operaciones de undo para revertir cambios

### 4. Ejecutar las pruebas

```bash
./mvnw test
```

Las pruebas validan:
- La cadena de validación con pedidos válidos e inválidos
- El funcionamiento del comando Confirmar con undo
- El funcionamiento del comando Aplicar Descuento con undo

## Estructura del Proyecto

```
src/main/java/com/universidad/pedidos/
├── PedidosApp.java                    # Punto de entrada (aplicación principal)
├── modelo/
│   └── Pedido.java                    # Entidad de dominio
├── cor/                               # Chain of Responsibility
│   ├── ValidadorPedido.java           # Clase abstracta base
│   ├── ValidadorStock.java            # Validador de stock
│   ├── ValidadorMonto.java            # Validador de monto mínimo
│   └── ValidadorCredito.java          # Validador de crédito
└── command/                           # Command Pattern
    ├── Comando.java                   # Interfaz de comandos
    ├── ComandoConfirmar.java          # Comando de confirmación
    ├── ComandoAplicarDescuento.java   # Comando de descuento
    └── HistorialComandos.java         # Gestor del historial de comandos

src/test/java/com/universidad/pedidos/
├── CadenaValidacionTest.java          # Pruebas de validación
└── PedidosAppTests.java               # Pruebas de contexto Spring
```

## Ejemplo de Uso

La aplicación demuestra:

```java
// 1. Crear cadena de validación
ValidadorPedido cadena = new ValidadorStock();
cadena.setNext(new ValidadorMonto())
      .setNext(new ValidadorCredito());

// 2. Crear un pedido
Pedido pedido = new Pedido("P-001", "PROD-A", 3, 45000.0, true);

// 3. Validar
if (cadena.validar(pedido)) {
    // 4. Ejecutar comandos
    historial.ejecutar(new ComandoConfirmar(pedido));
    historial.ejecutar(new ComandoAplicarDescuento(pedido, 10));
    
    // 5. Deshacer si es necesario
    historial.deshacer();
}
```

## Parámetros de Validación

- **Stock máximo**: 50 unidades
- **Monto mínimo**: 1000.0
- **Crédito disponible**: Verificado en la propiedad `creditoOk` del pedido

## Salida Esperada

Al ejecutar `./mvnw spring-boot:run`, verá una salida similar a:

```
--- Validando pedido P-001 ---
Resultado validación: true
Estado actual: Pedido{id='P-001', estado='CONFIRMADO', total=40500.0}

--- Deshaciendo última acción ---
Estado después de undo: Pedido{id='P-001', estado='CONFIRMADO', total=45000.0}

--- Validando pedido P-002 (sin crédito) ---
...
```

## Notas Importantes

- El proyecto usa **Spring Boot 4.0.4** y **Java 17**
- Las validaciones se ejecutan en cadena: Stock → Monto → Crédito
- Si una validación falla, las siguientes no se ejecutan
- Los comandos se pueden deshacer en orden inverso (stack LIFO)

## Soporte

Para más información sobre los patrones de diseño implementados:
- [Chain of Responsibility Pattern](https://en.wikipedia.org/wiki/Chain-of-responsibility_pattern)
- [Command Pattern](https://en.wikipedia.org/wiki/Command_pattern)

