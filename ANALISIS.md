# Análisis del problema

## 1. Situación problemática

El proyecto plantea la necesidad de desarrollar una **plataforma web de comercio electrónico** que permita gestionar y exhibir productos mediante un catálogo interactivo, posibilitando que los usuarios seleccionen productos, los incorporen a un carrito de compras y generen una orden de compra. Paralelamente, el sistema debe proporcionar mecanismos para que un administrador gestione la información de los productos.

El proyecto establece como objetivo la construcción de una tienda en línea dinámica que contemple específicamente **catálogo, carrito de compras persistente y panel de administración**.

El sistema está concebido para funcionar bajo un modelo de **comercio electrónico de un único vendedor**. Es decir, todos los productos ofrecidos en el catálogo pertenecen al mismo vendedor o comercio. No se contempla un modelo de marketplace en el que diferentes vendedores puedan registrar sus propios productos, administrar sus catálogos o recibir órdenes de manera independiente.

Por lo tanto, el sistema debe gestionar un único catálogo centralizado y un único contexto administrativo correspondiente al vendedor.

El problema central puede formularse como:

> **Se necesita un sistema de comercio electrónico para un único vendedor que permita administrar y consultar un catálogo de productos, gestionar un carrito de compras que conserve su información durante la navegación y registrar las órdenes de compra, incorporando además una interfaz administrativa para mantener actualizados los productos.**

---

## 2. Problemas identificados

A partir de la consigna pueden identificarse varios problemas relacionados entre sí.

### 2.1. Gestión y consulta del catálogo

La tienda necesita disponer de un catálogo de productos que pueda ser consultado por los usuarios de manera dinámica. La información no debe estar simplemente incorporada de forma estática en las páginas, sino que debe ser obtenida desde una API de manera asincrónica.

Además, el catálogo debe contemplar **filtros de búsqueda**, por lo que el sistema debe permitir localizar productos de acuerdo con los criterios que se definan para dicha búsqueda. La consigna especifica la existencia de una operación `GET /api/productos` destinada a esta función.

El problema, entonces, consiste en mantener una fuente centralizada de información sobre los productos y proporcionar un mecanismo que permita al frontend consultar dicha información.

Debido a que el sistema está destinado a un único vendedor, no es necesario contemplar la separación del catálogo en diferentes vendedores o tiendas. Todos los productos administrados forman parte del mismo catálogo comercial.

---

### 2.2. Gestión del carrito de compras

El usuario debe poder seleccionar productos del catálogo y agregarlos a un carrito.

El carrito presenta además un requisito particular: **debe ser persistente**. Esto significa que la información de los productos seleccionados no debe perderse simplemente porque el usuario recargue la página.

La consigna establece específicamente que esta persistencia debe implementarse mediante `LocalStorage` y que el sistema debe realizar automáticamente la suma de los montos correspondientes a los productos incorporados.

Por lo tanto, el problema comprende tanto el almacenamiento temporal de la selección del usuario como la actualización coherente de las cantidades y del importe total.

Como todos los productos pertenecen al mismo vendedor, el carrito no necesita diferenciar productos según vendedores ni gestionar carritos separados para distintas tiendas.

---

### 2.3. Registro de órdenes de compra

Una vez que el usuario ha conformado su carrito, el sistema necesita transformar esa selección en una **orden de compra que pueda ser almacenada**.

La consigna establece una API específica mediante la ruta:

`POST /api/pedidos`

cuya finalidad es guardar la orden de compra.

Esto introduce una separación entre dos conceptos:

* **Carrito:** representa la selección actual del usuario y permanece almacenado localmente.
* **Pedido:** representa la orden que debe registrarse en el backend.

El problema consiste, por lo tanto, en establecer correctamente el proceso mediante el cual los datos del carrito son enviados al servidor y transformados en una orden persistente.

Al tratarse de una plataforma de un único vendedor, una orden no necesita contemplar la posibilidad de distribuir sus productos entre diferentes vendedores.

---

### 2.4. Administración del catálogo

El catálogo no puede considerarse un conjunto de datos inmutable. El proyecto requiere un **panel CRUD** mediante el cual se puedan agregar, modificar y eliminar productos, incluyendo sus imágenes y datos.

La consigna especifica que dicho panel debe encontrarse en una **ruta protegida del frontend**.

El problema consiste entonces en proporcionar una interfaz diferenciada para las tareas administrativas y evitar que las operaciones de mantenimiento del catálogo estén disponibles de manera indiscriminada para cualquier usuario.

Debido al modelo de un único vendedor, el panel administrativo estará orientado a la gestión del catálogo perteneciente a ese vendedor y no a la administración de múltiples tiendas o cuentas comerciales independientes.

---

## 3. Actores involucrados

A partir de los requisitos pueden identificarse principalmente dos actores:

| Actor                        | Interacción con el sistema                                                                                                   |
| ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| **Cliente / usuario**        | Consulta el catálogo, busca productos, agrega productos al carrito y genera una orden de compra.                             |
| **Administrador / vendedor** | Accede al panel administrativo y mantiene el catálogo mediante operaciones de alta, modificación y eliminación de productos. |

El sistema está diseñado para un **único vendedor**, por lo que no se contempla la existencia de múltiples vendedores independientes dentro de la plataforma. El administrador representa al propietario o responsable del único catálogo gestionado por el sistema.

La consigna no especifica otros actores, como operadores de logística, medios de pago o proveedores, por lo que **no corresponde incorporarlos al análisis del problema como requisitos del sistema**.

---

## 4. Información que debe manejar el sistema

Del problema se desprenden, como mínimo, dos grupos principales de información.

### Productos

El sistema debe manejar información correspondiente a los productos, incluyendo:

* Datos del producto.
* Imagen o imágenes.
* Información necesaria para mostrarlo en el catálogo.
* Información necesaria para realizar operaciones de búsqueda.
* Información necesaria para calcular el importe del carrito.

La consigna menciona explícitamente la administración de **imágenes y datos de productos**.

Debido a que existe un único vendedor, los productos pertenecen al mismo catálogo y no es necesario contemplar información destinada a identificar diferentes vendedores.

### Pedidos

El sistema también debe manejar las órdenes generadas por los usuarios. Estas deben poder ser enviadas al backend y almacenadas mediante la API correspondiente.

La consigna confirma específicamente la necesidad de guardar la orden mediante `POST /api/pedidos`.

Las órdenes estarán compuestas por productos pertenecientes al catálogo del único vendedor.

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
```

El catálogo constituye una fuente centralizada de información correspondiente al único vendedor.

### Gestión del carrito

```text
Usuario selecciona producto
          ↓
     Agregar al carrito
          ↓
       LocalStorage
          ↓
Actualización de cantidades
          ↓
 Cálculo del importe total
```

### Generación del pedido

```text
Carrito
   ↓
Confirmación de compra
   ↓
Envío de datos
   ↓
POST /api/pedidos
   ↓
Backend
   ↓
Base de datos
```

### Administración de productos

```text
Administrador / vendedor
          ↓
    Panel protegido
          ↓
      Operación CRUD
          ↓
      Backend / API
          ↓
      Base de datos
          ↓
    Catálogo actualizado
```

---

## 6. Límites del problema

Es importante diferenciar **lo que la consigna exige** de funcionalidades que podrían pertenecer a un comercio electrónico real, pero que no están especificadas.

El sistema debe abarcar:

* Consulta del catálogo.
* Búsqueda/filtros de productos.
* Gestión del carrito.
* Persistencia del carrito mediante `LocalStorage`.
* Cálculo automático de los montos.
* Registro de pedidos.
* Administración CRUD de productos.
* Protección de la ruta administrativa.
* Gestión de un único catálogo correspondiente a un único vendedor.

### Modelo de negocio

El sistema corresponde a una plataforma de comercio electrónico de **un único vendedor**.

Esto implica que:

* Existe un único catálogo de productos.
* Los productos pertenecen al mismo vendedor.
* El panel administrativo gestiona un único catálogo.
* Las órdenes de compra se realizan sobre productos pertenecientes a ese único vendedor.
* No es necesario asociar cada producto a diferentes vendedores.
* No se contempla la administración de múltiples tiendas o cuentas de vendedores.
* No se contempla la distribución de una misma orden entre diferentes vendedores.

Por lo tanto, el problema se diferencia de un modelo de **marketplace**, donde múltiples vendedores independientes ofrecen productos dentro de una misma plataforma.

### Funcionalidades fuera del alcance

La consigna **no especifica** funcionalidades como:

* Sistema de pagos.
* Gestión de cuentas de clientes.
* Registro/login de usuarios.
* Seguimiento de pedidos.
* Gestión de stock.
* Facturación.
* Envíos.
* Notificaciones.
* Integración con proveedores externos.
* Administración de múltiples vendedores.
* Creación de múltiples tiendas dentro de la plataforma.
* Distribución de pedidos entre distintos vendedores.

Por lo tanto, estas funcionalidades no deberían considerarse parte del problema a resolver salvo que posteriormente se agreguen como requisitos.

---

## 7. Restricciones tecnológicas identificadas

El problema no se limita a definir qué debe hacer el sistema, sino que también establece una arquitectura tecnológica determinada.

La solución debe utilizar **JavaScript o TypeScript**, con **React** para el frontend, **Node.js con Express** para el backend y **MongoDB o PostgreSQL** como sistema de almacenamiento.

La estructura resultante queda conceptualmente dividida en:

```text
                 SISTEMA E-COMMERCE
                        │
          ┌─────────────┴─────────────┐
          │                           │
       FRONTEND                    BACKEND
        React                 Node.js + Express
          │                           │
          │                      API REST
          │                           │
          └──────────────┬────────────┘
                         │
                    Base de datos
                  MongoDB/PostgreSQL
```

El frontend también debe comunicarse con la API de forma asincrónica mediante `fetch` o `axios`.

La arquitectura se plantea para gestionar la información correspondiente a un único vendedor, por lo que no requiere una estructura orientada a múltiples comercios independientes.

---

## 8. Síntesis del problema

Desde una perspectiva de **análisis de sistemas**, el problema puede sintetizarse de la siguiente manera:

> Se requiere desarrollar una plataforma web de comercio electrónico para un **único vendedor**, capaz de centralizar la información de sus productos y ponerla a disposición de los usuarios mediante un catálogo dinámico y consultable. El usuario debe poder seleccionar productos, mantener dicha selección en un carrito persistente y obtener automáticamente el importe correspondiente. Una vez finalizada la selección, el sistema debe permitir registrar la orden de compra mediante el backend.
>
> Paralelamente, el sistema debe resolver la necesidad de mantenimiento del catálogo mediante un panel administrativo protegido que permita realizar operaciones de alta, modificación y eliminación de productos, incluyendo sus imágenes y datos.
>
> El sistema no contempla un modelo de marketplace ni la administración de múltiples vendedores. Todos los productos pertenecen al mismo vendedor y las órdenes se generan sobre productos pertenecientes a ese único catálogo.
>
> Para resolver el problema se plantea una arquitectura dividida entre frontend, backend y base de datos, utilizando React, Node.js/Express y MongoDB o PostgreSQL, con comunicación mediante una API REST.

Este análisis se mantiene deliberadamente en el **planteamiento y delimitación del problema**. No incluye todavía diseño de clases, diagramas UML, diseño de base de datos, casos de uso detallados, arquitectura interna ni implementación, ya que esos corresponden a etapas posteriores del análisis y diseño del sistema.
