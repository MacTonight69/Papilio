# Análisis del problema

## 1. Situación problemática

El proyecto plantea la necesidad de desarrollar una **plataforma web de comercio electrónico** que permita gestionar y exhibir productos mediante un catálogo interactivo, posibilitando que los usuarios seleccionen productos, los incorporen a un carrito de compras y generen una orden de compra. Paralelamente, el sistema debe proporcionar mecanismos para que un administrador gestione la información de los productos.

El proyecto establece como objetivo la construcción de una tienda en línea dinámica que contemple específicamente **catálogo, carrito de compras persistente y panel de administración**.

Por lo tanto, el problema central puede formularse como:

> **Se necesita un sistema de comercio electrónico que permita administrar y consultar un catálogo de productos, gestionar un carrito de compras que conserve su información durante la navegación y registrar las órdenes de compra, incorporando además una interfaz administrativa para mantener actualizados los productos.**

---

## 2. Problemas identificados

A partir de la consigna pueden identificarse varios problemas relacionados entre sí.

### 2.1. Gestión y consulta del catálogo

La tienda necesita disponer de un catálogo de productos que pueda ser consultado por los usuarios de manera dinámica. La información no debe estar simplemente incorporada de forma estática en las páginas, sino que debe ser obtenida desde una API de manera asincrónica.

Además, el catálogo debe contemplar **filtros de búsqueda**, por lo que el sistema debe permitir localizar productos de acuerdo con los criterios que se definan para dicha búsqueda. La consigna especifica la existencia de una operación `GET /api/productos` destinada a esta función.

El problema, entonces, consiste en mantener una fuente centralizada de información sobre los productos y proporcionar un mecanismo que permita al frontend consultar dicha información.

---

### 2.2. Gestión del carrito de compras

El usuario debe poder seleccionar productos del catálogo y agregarlos a un carrito.

El carrito presenta además un requisito particular: **debe ser persistente**. Esto significa que la información de los productos seleccionados no debe perderse simplemente porque el usuario recargue la página.

La consigna establece específicamente que esta persistencia debe implementarse mediante `LocalStorage` y que el sistema debe realizar automáticamente la suma de los montos correspondientes a los productos incorporados.

Por lo tanto, el problema comprende tanto el almacenamiento temporal de la selección del usuario como la actualización coherente de las cantidades y del importe total.

---

### 2.3. Registro de órdenes de compra

Una vez que el usuario ha conformado su carrito, el sistema necesita transformar esa selección en una **orden de compra que pueda ser almacenada**.

La consigna establece una API específica mediante la ruta:

`POST /api/pedidos`

cuya finalidad es guardar la orden de compra.

Esto introduce una separación entre dos conceptos:

- **Carrito:** representa la selección actual del usuario y permanece almacenado localmente.
- **Pedido:** representa la orden que debe registrarse en el backend.

El problema consiste, por lo tanto, en establecer correctamente el proceso mediante el cual los datos del carrito son enviados al servidor y transformados en una orden persistente.

---

### 2.4. Administración del catálogo

El catálogo no puede considerarse un conjunto de datos inmutable. El proyecto requiere un **panel CRUD** mediante el cual se puedan agregar, modificar y eliminar productos, incluyendo sus imágenes y datos.

La consigna especifica que dicho panel debe encontrarse en una **ruta protegida del frontend**.

El problema consiste entonces en proporcionar una interfaz diferenciada para las tareas administrativas y evitar que las operaciones de mantenimiento del catálogo estén disponibles de manera indiscriminada para cualquier usuario.

---

## 3. Actores involucrados

A partir de los requisitos pueden identificarse principalmente dos actores:

| Actor | Interacción con el sistema |
|---|---|
| **Cliente / usuario** | Consulta el catálogo, busca productos, agrega productos al carrito y genera una orden de compra. |
| **Administrador** | Accede al panel administrativo y mantiene los productos mediante operaciones de alta, modificación y eliminación. |

La consigna no especifica otros actores, como operadores de logística, medios de pago o proveedores, por lo que **no corresponde incorporarlos al análisis del problema como requisitos del sistema**.

---

## 4. Información que debe manejar el sistema

Del problema se desprenden, como mínimo, dos grupos principales de información.

### Productos

El sistema debe manejar información correspondiente a los productos, incluyendo:

- Datos del producto.
- Imagen o imágenes.
- Información necesaria para mostrarlo en el catálogo.
- Información necesaria para realizar operaciones de búsqueda.
- Información necesaria para calcular el importe del carrito.

La consigna menciona explícitamente la administración de **imágenes y datos de productos**.

### Pedidos

El sistema también debe manejar las órdenes generadas por los usuarios. Estas deben poder ser enviadas al backend y almacenadas mediante la API correspondiente.

La consigna confirma específicamente la necesidad de guardar la orden mediante `POST /api/pedidos`.

---

## 5. Procesos principales del sistema

Desde el punto de vista del análisis, el funcionamiento general puede dividirse en los siguientes procesos.

### Consulta de productos

```text
Usuario
   ↓
Catálogo
   ↓
Solicitud a la API
   ↓
Backend
   ↓
Base de datos
   ↓
Productos
   ↓
Catálogo mostrado al usuario