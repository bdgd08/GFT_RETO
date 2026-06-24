# GFT_RETO


# ProductOpening_HTTP

## Descripción General

**ProductOpening_HTTP** es una aplicación desarrollada en IBM App Connect Enterprise (ACE) 12 que expone un servicio HTTP para el recibimiento de solicitudes de apertura de servicios financieros.

La aplicación recibe solicitudes en formato JSON, determina el tipo de producto solicitado y enruta la petición al subflujo correspondiente para su procesamiento.

Todos los servicios internos consumidos son servicios REST y la mensajería se maneja exclusivamente en formato JSON.

---

## Componentes de la Solución

### Flujo Principal

La aplicación contiene un flujo fachada encargado de recibir y enrutar las solicitudes:

* **ProductOpening_HTTP.msgflow**

Este flujo expone el servicio principal y direcciona la solicitud al subflujo correspondiente según el tipo de producto solicitado.

---

## Endpoint Expuesto

El servicio principal se encuentra disponible mediante el siguiente endpoint HTTP:

```http
POST /product-requests
```

Formato de intercambio:

* Request: JSON
* Response: JSON

---

## Subflujos de Negocio

### 1. ProductOpening_CDT.subflow

Subflujo encargado de la creación de productos tipo CDT.
Subflujo encargado de transformar la mensajeria al formato que espera el legado.

#### Endpoint Sistema Legado consumido

```http
http://127.0.0.1:9094/legacy/CDT
```

#### Método

```http
POST
```

---

### 2. ProductOpening_Checkings.subflow

Subflujo encargado de la creación de cuentas corrientes.
Subflujo encargado de transformar la mensajeria al formato que espera el legado.

#### Endpoint Sistema Legado consumido

```http
http://127.0.0.1:9093/legacy/Checking
```

#### Método

```http
POST
```

---

### 3. ProductOpening_Savings.subflow

Subflujo encargado de la creación de cuentas de ahorro.
Subflujo encargado de transformar la mensajeria al formato que espera el legado.

#### Endpoint Sistema Legado consumido

```http
http://127.0.0.1:9092/legacy/Savings
```

#### Método

```http
POST
```

---

## Integración con IBM MQ

Cada subflujo implementa procesos de auditoría y notificación mediante IBM MQ.

### Cola de Registro Exitoso

```text
MQ_NOTIFY
```

Utilizada para registrar eventos relacionados con la creación exitosa de productos.

### Cola de Registro de Errores

```text
MQ_AUDIT_ERROR
```

Utilizada para registrar errores funcionales o técnicos generados durante el procesamiento de las solicitudes.

---

## Persistencia y Homologación de Datos

La solución utiliza una base de datos para realizar homologaciones de información y registrar auditoría de las transacciones procesadas.

### Tablas utilizadas

#### Homologación de Productos

```sql
ace.homologacion_productos
```

Contiene la homologación de códigos y tipos de producto.

#### Homologación de Rutas

```sql
ace.homologacion_productos_ruta
```

Contiene la relación entre tipos de producto y rutas de procesamiento.

#### Auditoría

```sql
ace.auditoria
```

Almacena información de las solicitudes procesadas, incluyendo datos relevantes para trazabilidad y monitoreo.

#### Registro de Errores

```sql
ace.errores
```

Almacena información asociada a errores funcionales y técnicos ocurridos durante la ejecución de los flujos.

---

## Consideraciones Técnicas

* Plataforma: IBM App Connect Enterprise 12 (ACE 12).
* Protocolo de exposición: HTTP.
* Protocolo de integración: REST.
* Formato de mensajes: JSON.
* Integración asíncrona: IBM MQ.
* Persistencia: Base de datos relacional.
* Manejo de respuestas HTTP alineado con principios REST.
* Implementación de homologaciones mediante tablas de parametrización.
* Implementación de auditoría y trazabilidad para operaciones exitosas y fallidas.
