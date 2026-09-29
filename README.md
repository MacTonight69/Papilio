<div align="center">

  # 🚀 Papilio

  **Tienda en línea educativa para un único vendedor.**

  [![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
  [![Status](https://img.shields.io/badge/Status-En%20Desarrollo-blue.svg)]()

  [Ver Demo](#) · [Reportar Error](https://github.com/MacTonight69/Papilio/issues)

</div>

---

## Descripción

Papilio es un proyecto escolar para simular la construcción de una tienda de comercio electrónico. El sistema administra un único catálogo y vendedor; no es un marketplace. El código se desarrolla con fines educativos y se distribuye bajo la licencia GNU GPL v3.

## Alcance

- Registro de clientes con nombre, apellido, correo electrónico y contraseña. Para confirmar el alta, se envía un token al correo ingresado; la cuenta se guarda definitivamente en MongoDB después de validar el token. Las contraseñas se protegen con `bcryptjs` y JWT autentica las sesiones.
- Catálogo consultable, ordenable por fecha de incorporación al stock (más reciente o más antiguo) y precio (mayor o menor), y filtrable por nombre y categoría.
- Carrito persistido en `LocalStorage`, con selección de cantidades y cálculo del precio final.
- No se aplican descuentos ni se procesan pagos. Al confirmar una compra, el sistema guarda el pedido y envía su información por correo electrónico.
- Administración protegida de productos y usuarios por el administrador root, denominado usuario 0. Su tipo y permisos son exclusivos y no pueden replicarse en otras cuentas.
- El usuario puede eliminar su cuenta desde configuración. El administrador también puede eliminar cuentas; en ese caso, se envía un correo con la justificación.
- Mailtrap Sandbox se utiliza para probar los correos de validación, compra y eliminación. Para enviar correos reales a usuarios será necesario configurar un servicio de producción.
- No se gestionan envíos.

## Productos

Cada producto contiene nombre, categoría, cantidad en stock, precio por unidad, unidad de medida, imagen o foto y descripción. Todos los campos son obligatorios para crear un producto. La fecha de incorporación al stock se conserva para ordenar los productos por antigüedad. Las imágenes se guardan en el subdirectorio `imgs` y MongoDB conserva su referencia. El stock se almacena en MongoDB y debe validarse al confirmar un pedido.

## Tecnologías

- Frontend: React.
- Backend: Node.js y Express, mediante una API REST.
- Base de datos: MongoDB.
- Lenguaje: JavaScript o TypeScript.
- Autenticación: `bcryptjs` y JWT.
- Correo en pruebas: Mailtrap Sandbox.

## Estado del proyecto

El repositorio contiene actualmente documentación de análisis y diseño; todavía no incluye el código de la aplicación ni manifiestos de dependencias. Por eso, aún no hay instrucciones ejecutables de instalación o inicio. El alcance y los flujos del sistema están detallados en [ANALISIS.md](ANALISIS.md).

---

## 👥 Equipo de Desarrollo

| Avatar | Nombre | Rol | GitHub |
| :---: | :--- | :--- | :---: |
| <img src="https://github.com/MacTonight69.png" width="50px" style="border-radius:50%"> | **Bautista Alejandro Cafferata** | Project Manager | [<img src="https://img.shields.io/badge/GitHub-100000?style=for-the-badge&logo=github&logoColor=white"/>](https://github.com/MacTonight69) |
| <img src="https://github.com/Lautaro-Salas.png" width="50px" style="border-radius:50%"> | **Lautaro Benjamín Salas** | Desarrollador (back-end) | [<img src="https://img.shields.io/badge/GitHub-100000?style=for-the-badge&logo=github&logoColor=white"/>](https://github.com/Lautaro-Salas) |
| <img src="https://github.com/totofrkhs.png" width="50px" style="border-radius:50%"> | **Enzo Ferreyra** | Desarrollador (front-end) | [<img src="https://img.shields.io/badge/GitHub-100000?style=for-the-badge&logo=github&logoColor=white"/>](https://github.com/totofrkhs/) |
| <img src="https://github.com/usuario4.png" width="50px" style="border-radius:50%"> | **Miqueas Alexander Herrera** | Analista de sistema | [<img src="https://img.shields.io/badge/GitHub-100000?style=for-the-badge&logo=github&logoColor=white"/>](https://github.com/usuario4) |
| <img src="https://github.com/PeladoAtomico50.png" width="50px" style="border-radius:50%"> | **Nino Iván Martínez** | Diseñador GUI | [<img src="https://img.shields.io/badge/GitHub-100000?style=for-the-badge&logo=github&logoColor=white"/>](https://github.com/PeladoAtomico50) |

---

## 🚀 Instalación y Uso

La instalación y ejecución se documentarán cuando se incorpore el esqueleto de frontend y backend.
