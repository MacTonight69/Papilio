# Análisis del sistema — Papilio

> Documento dirigido a todo el equipo: desarrolladores (back-end y front-end), diseñador GUI y project manager.
> Objetivo: que todos entiendan **qué** se construye, con **qué datos** y con **qué rutas**. El *cómo* interno (clases, esquemas, componentes) corresponde a la etapa de diseño.

---

## 1. Qué es Papilio

Una **tienda en línea de un único vendedor**. Hay un solo catálogo y una sola persona administradora. No es un marketplace: nadie más puede publicar productos.

Los clientes **no tienen cuenta ni inicio de sesión**: eligen productos, completan sus datos al comprar y reciben por email un **código de pedido** con el detalle de la compra. El vendedor recibe el mismo mensaje.

Es un proyecto escolar con fin pedagógico, por lo que **se prioriza la simplicidad**. Ante la duda, se elige la opción más simple.

**Problema que resuelve:** un vendedor necesita mostrar sus productos por internet, recibir pedidos de sus clientes con todos los datos necesarios para entregarlos y poder mantener su catálogo sin tocar el código.

---

## 2. Actores

| Actor | Qué hace |
| --- | --- |
| **Cliente** | Navega el catálogo, busca, arma el carrito y confirma el pedido completando sus datos. Recibe un email con el código y el detalle. |
| **Administrador / vendedor** | Ingresa al panel con una clave y hace altas, bajas y modificaciones de productos. Recibe un email por cada pedido. Hay **una sola persona** con este rol. |

---

## 3. Requisitos funcionales

| Código | Requisito |
| --- | --- |
| RF-01 | El catálogo muestra los productos (imagen, nombre y precio) obtenidos de la API de forma asincrónica. |
| RF-02 | Se puede buscar por **nombre** y filtrar por **categoría**. |
| RF-03 | Se puede agregar un producto al carrito. Si ya estaba, aumenta su cantidad. |
| RF-04 | En el carrito se puede cambiar la cantidad de un producto o quitarlo. |
| RF-05 | El carrito se guarda en `LocalStorage` (sobrevive a recargar la página) y el total se calcula automáticamente. |
| RF-06 | Para confirmar la compra el cliente completa: nombre, email, teléfono y dirección. No necesita cuenta. Los datos y el carrito se envían con `POST /api/pedidos`. |
| RF-07 | Al recibir un pedido, el back-end genera un **código único**, guarda el pedido y lo asocia a ese código. |
| RF-08 | El back-end envía un email con el código y el detalle de la compra **al cliente y al vendedor**. |
| RF-09 | Tras registrarse el pedido, se vacía el carrito y se muestra una confirmación con el código. |
| RF-10 | El administrador ingresa una **clave** para acceder al panel. |
| RF-11 | El panel está en una **ruta protegida** (`/admin`) y permite crear, modificar y eliminar productos. La imagen se sube como archivo. |
| RF-12 | El back-end rechaza crear, modificar o eliminar productos si la petición no trae un token válido de administrador. |

---

## 4. Reglas de negocio

1. **El precio manda el servidor.** El carrito guarda precios solo para mostrarlos. Al recibir un pedido, el back-end busca cada producto en la base de datos y **recalcula el total** con los precios reales.
2. **El pedido guarda una copia** de los datos al momento de la compra: nombre y precio de cada producto. Así, editar o eliminar un producto después no altera los pedidos anteriores.
3. Un pedido debe tener al menos un producto (cantidad entera mayor a 0) y los cuatro datos del cliente completos. El email debe tener formato válido.
4. Cada pedido tiene un **código único**, que identifica la compra para el cliente y el vendedor.
5. **Un fallo en el envío de emails no cancela el pedido.** El pedido queda guardado, el error se registra en el servidor y el cliente igualmente ve su código en pantalla.
6. Las imágenes se guardan en una carpeta del servidor. El producto guarda solo la ruta del archivo. Al reemplazar la imagen o eliminar el producto, se borra el archivo anterior.
7. Todos los productos se consideran siempre disponibles (no hay stock).
8. La clave del administrador y los datos del correo se configuran en el servidor (archivo `.env`); nunca se escriben en el código ni se suben al repositorio.

---

## 5. Datos que maneja el sistema

### Producto
| Campo | Detalle |
| --- | --- |
| `id` | Lo genera la base de datos |
| `nombre` | Texto, obligatorio |
| `descripcion` | Texto, opcional |
| `precio` | Número mayor a 0, obligatorio |
| `categoria` | Texto, obligatorio (se usa para filtrar) |
| `imagen` | Ruta del archivo en el servidor (ej. `/uploads/remera.jpg`), obligatoria |

### Pedido
| Campo | Detalle |
| --- | --- |
| `id` | Lo genera la base de datos |
| `codigo` | Código único de pedido (ej. `PAP-7K3M9Q`) |
| `cliente` | `nombre`, `email`, `telefono`, `direccion` (todos obligatorios) |
| `items` | Lista de: `productoId`, `nombre`, `precio`, `cantidad` |
| `total` | Calculado por el back-end |
| `fecha` | Fecha y hora de creación |

### Datos del navegador
- **Carrito** (`LocalStorage`): lista de `productoId`, `nombre`, `precio`, `imagen`, `cantidad`. No se guarda en la base de datos.
- **Token de acceso del administrador** (JWT): lo devuelve el acceso con clave y el front-end lo envía en las peticiones protegidas.

---

## 6. API REST

Las rutas `GET /api/productos` y `POST /api/pedidos` vienen exigidas por la consigna; las demás se derivan de los requisitos.

| Método y ruta | Acceso | Función |
| --- | --- | --- |
| `GET /api/productos?buscar=&categoria=` | Público | Lista productos; los filtros son opcionales. |
| `POST /api/pedidos` | Público | Recibe datos del cliente y productos; guarda el pedido, genera el código, envía los emails y devuelve el código. |
| `POST /api/admin/acceso` | Público | Recibe la clave; si es correcta, devuelve el token de administrador. |
| `POST /api/productos` | Administrador | Crea un producto (`multipart/form-data`, con imagen). |
| `PUT /api/productos/:id` | Administrador | Modifica un producto (`multipart/form-data`; si no se envía imagen, se conserva la actual). |
| `DELETE /api/productos/:id` | Administrador | Elimina un producto y su imagen. |

Las rutas de administrador exigen el token en la cabecera `Authorization`. Las imágenes se sirven como archivos estáticos desde `/uploads`.

---

## 7. Pantallas (guía para el diseñador)

| Pantalla | Contenido mínimo |
| --- | --- |
| **Catálogo** (inicio) | Buscador, selector de categoría, grilla de productos con botón "Agregar", ícono de carrito con contador. |
| **Carrito** | Productos con cantidad editable y botón "Quitar", total y botón "Confirmar compra". |
| **Datos del cliente** | Formulario con nombre, email, teléfono y dirección, junto al resumen del pedido (productos y total) y botón "Enviar pedido". |
| **Confirmación** | Mensaje de éxito, código del pedido y aviso de que se envió un email con el detalle. |
| **Acceso de administrador** | Campo de clave y mensaje de error. |
| **Panel de administración** | Tabla de productos con botones "Editar" y "Eliminar", botón "Nuevo producto" y botón "Salir". |
| **Formulario de producto** | Nombre, descripción, precio, categoría e imagen (selector de archivo con vista previa). Se reutiliza para crear y editar. |

Estados que deben diseñarse: **cargando**, **sin resultados** (búsqueda vacía), **carrito vacío**, **acceso vencido** (panel) y **error** (falla de la API).

### Email del pedido (lo diseña el diseñador y lo implementa el back-end)

Se envía un mensaje al cliente y otro al vendedor, con el mismo contenido:

- Código del pedido y fecha.
- Datos del cliente: nombre, email, teléfono y dirección.
- Tabla de productos: nombre, cantidad, precio unitario y subtotal.
- Total.

---

## 8. Restricciones tecnológicas

- Lenguaje: **JavaScript o TypeScript**.
- Front-end: **React**, comunicándose con la API mediante `fetch` o `axios`.
- Back-end: **Node.js con Express**, API REST.
- Base de datos: **MongoDB**.
- Autenticación del administrador: **JWT**.
- Imágenes: guardadas en el servidor.
- Emails: enviados desde el back-end (se propone **Nodemailer** con un servidor SMTP).
- Licencia del proyecto: **GNU GPL v3**.

---

## 9. Decisiones tomadas y propuestas

| Tema | Decisión |
| --- | --- |
| Base de datos | MongoDB |
| Token | JWT |
| Imágenes | Se guardan en el servidor |
| Clientes | Sin cuenta ni inicio de sesión; sus datos viajan dentro del pedido |
| Datos del cliente | Nombre, email, teléfono y dirección |
| Comprobante | Código de pedido enviado al cliente y al vendedor con el detalle |

**Propuestas por confirmar:**

- Envío por **email** (el cliente ya ingresa su email en el formulario).
- Acceso del administrador con una **clave única** guardada en `.env` (la consigna exige una ruta protegida, por lo que algún acceso es necesario).
- Formato del código: `PAP-` seguido de 6 caracteres alfanuméricos.
- Imágenes limitadas a JPG, PNG o WEBP de hasta 2 MB.
- Token de administrador con vencimiento (por ejemplo, 24 horas).

---

## 10. Flujos

El diagrama general está en [`Diagrama.mmd`](./Diagrama.mmd) y cada proceso tiene su propio diagrama en la carpeta `diagramas/`:

| Archivo | Proceso |
| --- | --- |
| `diagramas/tienda.mmd` | Catálogo y carrito |
| `diagramas/pedido.mmd` | Confirmar pedido y enviar el código |
| `diagramas/admin.mmd` | Acceso y administración de productos |

**Símbolos usados en los diagramas**

| Símbolo | Significado |
| --- | --- |
| Óvalo | Inicio o fin |
| Rectángulo | Proceso o menú de opciones (puede tener varias salidas) |
| Rombo | Decisión con solo dos salidas: Sí / No |
| Paralelogramo | Pantalla o formulario (lo que ve o completa el usuario) |
| Cilindro | Base de datos (MongoDB) |
| Rectángulo de doble borde | Subproceso con su propio diagrama |
