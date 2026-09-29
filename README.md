<div align="center">

  # 🦋 Papilio

  **Una tienda en línea simple, hecha para aprender.**

  [![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
  [![Status](https://img.shields.io/badge/Status-En%20Desarrollo-blue.svg)]()

  [Reportar error](https://github.com/MacTonight69/Papilio/issues)

</div>

---

## 📌 Descripción

Papilio es un proyecto escolar: una página web de comercio electrónico para **un único vendedor**. Permite ver un catálogo de productos, armar un carrito de compras y realizar pedidos sin necesidad de crear una cuenta. El vendedor cuenta con un panel de administración para mantener sus productos.

El proyecto es deliberadamente simple. Su objetivo es que cualquier persona que quiera aprender sobre desarrollo web y bases de datos pueda leer el código, entenderlo y modificarlo con fines educativos, algo permitido por su licencia GNU GPL v3.

---

## ✨ Funcionalidades

* 🛍️ **Catálogo** de productos con búsqueda por nombre y filtro por categoría.
* 🛒 **Carrito persistente:** se guarda en el navegador (`LocalStorage`) y calcula el total automáticamente.
* 📦 **Pedidos sin cuenta:** el cliente completa nombre, email, teléfono y dirección, y el pedido se guarda en la base de datos.
* ✉️ **Código de pedido:** cada compra genera un código único que se envía por email, junto con el detalle, al cliente y al vendedor.
* 🔒 **Panel de administración** protegido con clave para crear, modificar y eliminar productos, con subida de imágenes.

El detalle completo está en [`ANALISIS.md`](./ANALISIS.md).

---

## 🧰 Tecnologías

| Capa | Tecnología |
| --- | --- |
| Front-end | React |
| Back-end | Node.js + Express (API REST) |
| Base de datos | MongoDB |
| Acceso de administrador | JWT |
| Imágenes | Almacenadas en el servidor |
| Emails | Nodemailer (propuesta) |

---

## 📚 Documentación

| Archivo | Contenido |
| --- | --- |
| [`ANALISIS.md`](./ANALISIS.md) | Alcance, requisitos, datos, rutas de la API y pantallas. |
| [`Diagrama.mmd`](./Diagrama.mmd) | Diagrama de flujo general (Mermaid). GitHub lo dibuja automáticamente. |
| [`diagramas/`](./diagramas) | Un diagrama por proceso: `tienda.mmd`, `pedido.mmd` y `admin.mmd`. |

---

## 🚀 Instalación y uso

> Requisitos: [Node.js](https://nodejs.org/), [MongoDB](https://www.mongodb.com/) (instalado localmente o en la nube) y una cuenta de correo con acceso SMTP para enviar los emails.
> La estructura de carpetas y los comandos son una propuesta; ajustarlos si el proyecto cambia.

1. **Clonar el repositorio:**
   ```bash
   git clone https://github.com/MacTonight69/Papilio.git
   cd Papilio
   ```

2. **Configurar y ejecutar el back-end** (carpeta `backend/`):
   ```bash
   cd backend
   npm install
   ```
   Crear un archivo `.env` con la conexión a la base de datos, la clave del administrador y los datos del correo:
   ```env
   PORT=3000
   MONGODB_URI=mongodb://localhost:27017/papilio
   JWT_SECRET=<clave-secreta-para-los-tokens>
   ADMIN_CLAVE=<clave-de-acceso-al-panel>
   SMTP_HOST=<servidor-smtp>
   SMTP_PORT=587
   SMTP_USER=<usuario-smtp>
   SMTP_PASS=<contraseña-smtp>
   VENDEDOR_EMAIL=<email-donde-el-vendedor-recibe-los-pedidos>
   ```
   Luego:
   ```bash
   npm run dev
   ```
   Las imágenes subidas se guardan en la carpeta `backend/uploads/`.

3. **Ejecutar el front-end** (carpeta `frontend/`):
   ```bash
   cd ../frontend
   npm install
   npm run dev
   ```

> ⚠️ El archivo `.env` contiene datos privados: **nunca** debe subirse al repositorio.

---

## 👥 Equipo de desarrollo

| Avatar | Nombre | Rol | GitHub |
| :---: | :--- | :--- | :---: |
| <img src="https://github.com/MacTonight69.png" width="50px" style="border-radius:50%"> | **Bautista Alejandro Cafferata** | Project Manager | [<img src="https://img.shields.io/badge/GitHub-100000?style=for-the-badge&logo=github&logoColor=white"/>](https://github.com/MacTonight69) |
| <img src="https://github.com/Lautaro-Salas.png" width="50px" style="border-radius:50%"> | **Lautaro Benjamín Salas** | Desarrollador (back-end) | [<img src="https://img.shields.io/badge/GitHub-100000?style=for-the-badge&logo=github&logoColor=white"/>](https://github.com/Lautaro-Salas) |
| <img src="https://github.com/totofrkhs.png" width="50px" style="border-radius:50%"> | **Enzo Ferreyra** | Desarrollador (front-end) | [<img src="https://img.shields.io/badge/GitHub-100000?style=for-the-badge&logo=github&logoColor=white"/>](https://github.com/totofrkhs) |
| <img src="https://github.com/usuario4.png" width="50px" style="border-radius:50%"> | **Miqueas Alexander Herrera** | Analista de sistemas | [<img src="https://img.shields.io/badge/GitHub-100000?style=for-the-badge&logo=github&logoColor=white"/>](https://github.com/usuario4) |
| <img src="https://github.com/PeladoAtomico50.png" width="50px" style="border-radius:50%"> | **Nino Iván Martínez** | Diseñador GUI | [<img src="https://img.shields.io/badge/GitHub-100000?style=for-the-badge&logo=github&logoColor=white"/>](https://github.com/PeladoAtomico50) |

---

## 📄 Licencia

Distribuido bajo la licencia **GNU GPL v3**. Consultá el texto completo en <https://www.gnu.org/licenses/gpl-3.0>.
