---
name: web-dev-simple
description: 'Implementar o modificar funcionalidades web en Papilio con código claro, simple y ágil. Usar al crear páginas, componentes, flujos de catálogo, carrito, pedidos o administración; aplicar variables lowerCamelCase y verificar el cambio con las herramientas existentes.'
---

# Desarrollo web simple para Papilio

## Objetivo

Implementar cambios web funcionales, comprensibles y fáciles de mantener, siguiendo la arquitectura que ya use el repositorio. Favorecer la solución más sencilla que cumpla los requisitos sin sacrificar accesibilidad, seguridad ni comportamiento correcto.

## Procedimiento

1. **Aclarar el resultado.** Identificar qué debe poder hacer el usuario, qué datos entran y salen, y los estados relevantes. Consultar `README.md`, `ANALISIS.md` y los requisitos cercanos al cambio. Para Papilio, respetar el alcance documentado: tienda de un solo vendedor, catálogo consultable y filtrable, carrito persistido en `LocalStorage`, generación de pedidos y administración CRUD en una ruta protegida. No añadir capacidades fuera de alcance sin necesidad.
2. **Ubicar el cambio.** Inspeccionar los archivos, dependencias y convenciones que controlan directamente la funcionalidad. Reutilizar componentes, estilos, utilidades, rutas y patrones existentes. Si aún no hay implementación o no se ha elegido stack, no asumir uno ni añadir dependencias antes de justificarlo.
3. **Elegir una solución pequeña.** Definir el mínimo cambio que satisfaga el comportamiento solicitado. Mantener juntas las responsabilidades relacionadas, evitar abstracciones prematuras y no duplicar lógica que ya tenga una utilidad local. Preservar contratos existentes, incluidos nombres de campos, rutas y formato de almacenamiento.
4. **Implementar de forma consistente.** Usar HTML semántico y controles apropiados; mantener la interfaz adaptable a móvil y escritorio. En JavaScript o TypeScript, escribir los nombres de variables en `lowerCamelCase`. Seguir las convenciones existentes para funciones, clases, tipos, constantes y nombres de API, sin cambiar contratos externos para uniformar nombres internos. Gestionar explícitamente carga, errores, resultados vacíos y entradas inválidas cuando correspondan.
5. **Cuidar los límites de confianza y los datos.** Validar entradas en el punto adecuado, no exponer secretos ni confiar en validaciones solo del navegador. Para el carrito, conservar el formato acordado en `LocalStorage`, manejar datos ausentes o inválidos y calcular totales de forma coherente. Para pedidos y operaciones administrativas, usar las rutas y controles definidos por el proyecto; la protección de la interfaz no sustituye la autorización del servidor.
6. **Comprobar el cambio.** Ejecutar primero la prueba o comando más específico disponible para la funcionalidad; después, los chequeos pertinentes del proyecto. Si no existen pruebas automatizadas, comprobar manualmente el flujo afectado y los estados importantes. Informar con claridad qué se verificó y qué quedó sin verificar.

## Criterios de calidad

- El cambio cumple los requisitos observables y encaja con la implementación actual.
- El código usa variables `lowerCamelCase` y evita complejidad o dependencias innecesarias.
- La interfaz es semántica, accesible en sus interacciones principales y usable en tamaños de pantalla habituales.
- Errores, estados vacíos y datos persistidos inválidos no rompen el flujo.
- No se alteran contratos existentes ni se agregan funcionalidades ajenas a la solicitud.
- La verificación se limita a comandos disponibles y relevantes; sus resultados se comunican sin afirmar comprobaciones que no se ejecutaron.
