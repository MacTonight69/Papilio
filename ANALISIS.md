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

Además, el catálogo debe contemplar filtros por fecha de incorporación al stock (más reciente o más antiguo), precio (mayor o menor), nombre y categoría. Para ordenar por incorporación, cada producto debe conservar la fecha en que fue agregado al stock. La consigna especifica la existencia de una operación `GET /api/productos` destinada a esta función.

El problema, entonces, consiste en mantener una fuente centralizada de información sobre los productos y proporcionar un mecanismo que permita al frontend consultar dicha información.

Debido a que el sistema está destinado a un único vendedor, no es necesario contemplar la separación del catálogo en diferentes vendedores o tiendas. Todos los productos administrados forman parte del mismo catálogo comercial.

---

### 2.2. Gestión del carrito de compras

El cliente debe poder seleccionar productos del catálogo, indicar la cantidad deseada en la unidad de medida correspondiente y agregarlos a un carrito. El carrito debe conservarse durante la navegación mediante `LocalStorage`.

El sistema debe calcular automáticamente el subtotal de cada línea y el precio final del carrito. No se aplicarán descuentos. Al confirmar el pedido, el backend debe validar que haya stock suficiente para cada producto y evitar que el stock quede negativo.

El carrito representa una selección pendiente; el pedido es el registro persistente generado al confirmarla. El sistema enviará por correo electrónico la información de la compra. No procesará pagos ni gestionará envíos.

---

### 2.3. Registro de órdenes de compra

Los clientes registrados deben poder transformar el carrito en una **orden de compra que pueda ser almacenada**. La orden debe incluir los productos, las cantidades solicitadas, los precios usados para calcular el total y el precio final, sin descuento.

La consigna establece una API específica mediante la ruta:

`POST /api/pedidos`

cuya finalidad es guardar la orden de compra.

Esto introduce una separación entre dos conceptos:

* **Carrito:** representa la selección actual del usuario y permanece almacenado localmente.
* **Pedido:** representa la orden que debe registrarse en el backend.

El backend debe volver a consultar los precios y el stock vigentes antes de guardar el pedido, en lugar de confiar en los importes enviados por el navegador. Al registrar la orden, debe actualizar el stock de los productos y enviar al cliente por correo electrónico la información del pedido. La política para pedidos con stock insuficiente debe informarse al cliente.

Al tratarse de una plataforma de un único vendedor, una orden no necesita contemplar la posibilidad de distribuir sus productos entre diferentes vendedores.

---

### 2.4. Administración del catálogo

El catálogo no puede considerarse un conjunto de datos inmutable. El proyecto requiere un **panel CRUD** mediante el cual se puedan agregar, modificar y eliminar productos, incluyendo sus imágenes y datos.

La consigna especifica que dicho panel debe encontrarse en una **ruta protegida** y que las operaciones deben estar autorizadas también en el backend. El producto solo podrá crearse si todos sus campos requeridos están completos: nombre, categoría, cantidad en stock, precio por unidad, unidad de medida, imagen o foto y descripción. Las imágenes se cargarán en el subdirectorio `imgs` del proyecto; la base de datos conservará la ruta relativa del archivo. También se registrará la fecha de incorporación al stock para permitir ordenar el catálogo por más reciente o más antiguo.

El problema consiste entonces en proporcionar una interfaz diferenciada para las tareas administrativas y evitar que las operaciones de mantenimiento del catálogo estén disponibles de manera indiscriminada para cualquier usuario.

Debido al modelo de un único vendedor, el panel administrativo estará orientado a la gestión del catálogo perteneciente a ese vendedor y no a la administración de múltiples tiendas o cuentas comerciales independientes.

### 2.5. Cuentas y administración de usuarios

Los clientes deberán registrarse e iniciar sesión con correo electrónico y contraseña. El registro solicita nombre, apellido, correo electrónico y contraseña. El sistema enviará a esa dirección un correo con un token de validación. Los datos se guardarán definitivamente en MongoDB solo después de que el usuario valide el token; antes de esa confirmación, el registro todavía no será una cuenta activa.

El usuario podrá eliminar su propia cuenta desde el apartado de configuración. Además, el administrador root podrá eliminar cuentas e informar por correo la justificación correspondiente.

Se creará por defecto una cuenta administrativa identificada como usuario 0, con nombre y contraseña, que representa al administrador root. Su tipo y permisos son exclusivos y no podrán asignarse ni replicarse en ninguna otra cuenta. El administrador podrá gestionar el catálogo y eliminar cuentas de clientes. Al eliminar una cuenta, el sistema deberá enviar un correo al usuario con la justificación de la eliminación.

Las contraseñas, incluida la del administrador root, se almacenarán como hashes generados con `bcryptjs`; nunca en texto plano. Al validar las credenciales, el backend emitirá un JWT que se utilizará para autenticar las solicitudes posteriores. La denominación usuario 0 identifica la cuenta root; MongoDB puede mantener su identificador interno habitual y guardar el tipo de cuenta como un atributo protegido.

---

## 3. Actores involucrados

A partir de los requisitos pueden identificarse principalmente dos actores:

| Actor                        | Interacción con el sistema                                                                                                   |
| ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| **Cliente / usuario**        | Se registra y confirma su correo, inicia sesión, consulta y filtra el catálogo, administra su carrito, genera pedidos y puede eliminar su cuenta desde configuración. |
| **Administrador root (usuario 0)** | Gestiona productos y cuentas desde una ruta protegida; sus permisos exclusivos no pueden replicarse. Al eliminar una cuenta, informa la justificación por correo electrónico. |

El sistema está diseñado para un **único vendedor**, por lo que no se contempla la existencia de múltiples vendedores independientes dentro de la plataforma. El administrador representa al propietario o responsable del único catálogo gestionado por el sistema.

No se requieren actores de logística ni medios de pago, ya que los envíos y pagos quedan fuera del alcance del proyecto.

---

## 4. Información que debe manejar el sistema

Del problema se desprenden, como mínimo, tres grupos principales de información.

### Productos

El sistema debe manejar información correspondiente a los productos, incluyendo:

* Nombre.
* Categoría.
* Cantidad en stock.
* Precio por unidad.
* Unidad de medida.
* Imagen o foto, almacenada como archivo en `imgs` y referenciada desde la base de datos.
* Descripción.
* Fecha de incorporación al stock, para ordenar el catálogo por antigüedad.

La consigna menciona explícitamente la administración de **imágenes y datos de productos**.

Debido a que existe un único vendedor, los productos pertenecen al mismo catálogo y no es necesario contemplar información destinada a identificar diferentes vendedores.

### Pedidos

El sistema también debe manejar las órdenes generadas por los usuarios. Estas deben poder ser enviadas al backend y almacenadas mediante la API correspondiente.

La consigna confirma específicamente la necesidad de guardar la orden mediante `POST /api/pedidos`.

Cada pedido debe estar asociado al cliente que inició sesión e incluir los productos elegidos, sus cantidades, el precio utilizado y el precio final, sin descuentos. Los pedidos estarán compuestos por productos del único catálogo. La información de la compra se enviará por correo electrónico. No se procesarán pagos ni envíos.

### Usuarios

Los datos de registro de los clientes (nombre, apellido y correo electrónico) se guardarán definitivamente en MongoDB después de validar el token enviado por email. También se almacenará la información segura necesaria para autenticarles y la cuenta administrativa inicial identificada como usuario 0. Los usuarios podrán eliminar su propia cuenta desde configuración; el administrador también podrá eliminarlos y el sistema deberá notificarles por correo con la justificación correspondiente.

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
 Indica cantidad y unidad
          ↓
     Agregar al carrito
          ↓
       LocalStorage
          ↓
Validar disponibilidad de stock
         ↓
      Calcular subtotal y total sin descuentos
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
Validar cliente, precios y stock
   ↓
Guardar pedido y actualizar stock
   ↓
Base de datos MongoDB
   ↓
Enviar resumen de la compra por email
```

### Administración de productos

```text
Administrador root (usuario 0)
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

### Registro, acceso y administración de usuarios

```text
Cliente envía nombre, apellido y correo
   ↓
Sistema envía token de validación al correo
   ↓
Cliente valida el token
   ↓
Datos de la cuenta se guardan en MongoDB
   ↓
Cliente inicia sesión y accede al sistema
   ↓
Puede eliminar su cuenta desde configuración

Administrador root (usuario 0; permisos exclusivos)
        ↓
Ruta protegida y autorización en backend
        ↓
Elimina cuenta indicando justificación
        ↓
Sistema envía correo de notificación
```

---

## 6. Límites del problema

Es importante diferenciar **lo que la consigna exige** de funcionalidades que podrían pertenecer a un comercio electrónico real, pero que no están especificadas.

El sistema debe abarcar:

* Consulta del catálogo.
* Búsqueda/filtros de productos.
* Gestión del carrito.
* Persistencia del carrito mediante `LocalStorage`.
* Cálculo automático del subtotal y precio final, sin descuentos.
* Registro de pedidos.
* Administración CRUD de productos.
* Protección de la ruta administrativa.
* Registro e inicio de sesión de clientes.
* Confirmación de correo mediante token antes de guardar definitivamente la cuenta en MongoDB.
* Eliminación de la propia cuenta desde la configuración del usuario.
* Administración de cuentas por el usuario administrador 0, incluida la notificación por correo al eliminar una cuenta.
* Control del stock almacenado en MongoDB y validación de disponibilidad al confirmar un pedido.
* Carga de imágenes al subdirectorio `imgs`.
* Filtros por fecha de incorporación al stock (más reciente o más antiguo), precio (mayor o menor), nombre y categoría.
* Envío por email de la información de la compra.
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

### Funcionalidades fuera del alcance

* Descuentos en las compras.
* Procesamiento de pagos; solo se enviará por email la información de la compra.
* Gestión de envíos y seguimiento logístico.
* Facturación e integración con proveedores externos.

### Autenticación y correo

* `bcryptjs` se utilizará para generar y verificar hashes de contraseñas.
* JWT se utilizará para autenticar sesiones y solicitudes a rutas protegidas.
* Mailtrap Sandbox se utilizará para probar el envío de tokens de validación, resúmenes de compra y notificaciones de eliminación. Los mensajes de Sandbox son de prueba y no equivalen a entregas reales; el envío a usuarios finales requerirá un servicio de correo de producción.

Por lo tanto, el problema se diferencia de un modelo de **marketplace**, donde múltiples vendedores independientes ofrecen productos dentro de una misma plataforma.

---

## 7. Restricciones tecnológicas identificadas

El problema no se limita a definir qué debe hacer el sistema, sino que también establece una arquitectura tecnológica determinada.

La solución debe utilizar **JavaScript o TypeScript**, con **React** para el frontend, **Node.js con Express** para el backend y **MongoDB** como sistema de almacenamiento.

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
                                 MongoDB
```

El frontend también debe comunicarse con la API de forma asincrónica mediante `fetch` o `axios`.

La arquitectura se plantea para gestionar la información correspondiente a un único vendedor, por lo que no requiere una estructura orientada a múltiples comercios independientes.

---

## 8. Síntesis del problema

Desde una perspectiva de **análisis de sistemas**, el problema puede sintetizarse de la siguiente manera:

> Se requiere desarrollar una plataforma web de comercio electrónico para un **único vendedor**, con frontend React, backend Node.js/Express y base de datos MongoDB. Los clientes deberán registrarse con nombre, apellido, correo y contraseña; validarán su correo mediante un token antes de que la cuenta se guarde definitivamente. El inicio de sesión utilizará `bcryptjs` para verificar contraseñas y JWT para autenticar solicitudes. El cliente podrá consultar el catálogo, agregar productos y cantidades a un carrito persistido en `LocalStorage`, y generar pedidos. El catálogo se podrá ordenar por fecha de incorporación al stock y precio, y filtrar por nombre y categoría. El sistema calculará el precio final sin aplicar descuentos, validará el stock, almacenará los pedidos y enviará por email la información de la compra mediante Mailtrap Sandbox durante las pruebas. No procesará pagos.
>
> El catálogo incluirá nombre, categoría, cantidad en stock, precio por unidad, unidad de medida, imagen o foto y descripción. El administrador inicial, denominado usuario 0, será el root y tendrá tipo y permisos exclusivos que no podrán replicarse. Gestionará productos desde una ruta protegida; para crearlos, todos esos campos deberán estar completos. Las imágenes se guardarán en el subdirectorio `imgs` del proyecto y MongoDB almacenará sus referencias.
>
> El administrador también podrá eliminar cuentas de clientes; el sistema enviará al usuario un correo con la justificación. No se gestionarán envíos.
>
> El sistema no contempla un modelo de marketplace ni la administración de múltiples vendedores. Todos los productos pertenecen al mismo vendedor y las órdenes se generan sobre productos pertenecientes a ese único catálogo.
>
> Para resolver el problema se plantea una arquitectura dividida entre frontend, backend y base de datos, utilizando React, Node.js/Express y MongoDB, con comunicación mediante una API REST.

Este análisis se mantiene deliberadamente en el **planteamiento y delimitación del problema**. No incluye todavía diseño de clases, diagramas UML, diseño de base de datos, casos de uso detallados, arquitectura interna ni implementación, ya que esos corresponden a etapas posteriores del análisis y diseño del sistema.
