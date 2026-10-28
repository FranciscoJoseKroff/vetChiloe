# Bitácora de IA Generativa – Mini Proyecto Vet

**Integrantes:** Francisco Kroff y Vicente Garin
**Declaración de uso de IA:** Claude se utilizó para generar el prompt inicial y GitHub Copilot para implementar cambios de frontend, backend y PostgreSQL. El historial de Git conserva la implementación base de las capas; los cambios descritos en esta bitácora corresponden al trabajo local asistido por Copilot. No se identifican aquí otras herramientas de IA.

## Entrada 1 – Paso 1 y Paso 2, búsqueda en el directorio
- **Paso:** Paso 1, componente `BuscadorPaciente`; Paso 2, filtro y contador en la vista de pacientes.
- **Herramienta:** GitHub Copilot.
- **Prompt:** Claude ayudó a preparar el prompt inicial para implementar los requisitos del Mini Proyecto Vet en React, Node/Express y PostgreSQL. El prompt entregado a Copilot solicitó revisar primero la arquitectura y el esquema existentes, respetar las capas y funcionalidades actuales, y luego implementar el buscador controlado, el filtrado por nombre/especie, el contador, los badges y el estado vacío. Para esta parte se pidió un buscador controlado por el estado del padre, filtrado en tiempo real y sin distinguir mayúsculas. El contexto real del repositorio incluía `App.jsx` y tarjetas de pacientes; no existía un componente `TablaPacientes`.
- **Resultado:** Se añadió `BuscadorPaciente` con hoja CSS y se integró en el directorio de tarjetas existente, manteniendo el flujo de fichas. Se adaptó el requisito de tabla a las tarjetas porque esa tabla no existe en el proyecto. El filtro visual actúa sobre los pacientes cargados en la interfaz; la consulta global a PostgreSQL corresponde al endpoint de búsqueda de la entrada 3.
- **Verificación:** Se ejecutaron `npm run build` y `npm run lint`, ambos sin errores. No se comprobó el buscador en el navegador conectado a una API desplegada, porque todavía no está configurada la conexión entre Vercel, Render y Neon.

## Entrada 2 – Paso 3 y Paso 5, funciones de búsqueda y resumen SQL
- **Paso:** Paso 3, función `buscar_pacientes`; Paso 5, función `resumen_por_especie`.
- **Herramienta:** GitHub Copilot.
- **Prompt:** Se pidió crear las funciones SQL para buscar por nombre o especie con `ILIKE` y agrupar pacientes por especie, junto con las capas necesarias para consumirlas. Se proporcionó el contexto del esquema PostgreSQL del proyecto, que contiene más campos que el ejemplo reducido.
- **Resultado:** Se añadieron `buscar_pacientes()` y `resumen_por_especie()` en `sql/funciones.sql`. El repositorio invoca las funciones con consultas parametrizadas; el resumen convierte `total` de `BIGINT` a `Number`. [COMPLETAR: anotar correcciones manuales adicionales que se hicieron al SQL, si las hubo.]
- **Verificación:** Se revisaron la sintaxis de JavaScript, el diff y la presencia de las funciones. No se ejecutó `sql/funciones.sql` ni sus pruebas en Neon porque la base todavía no está conectada/configurada para el proyecto.

## Entrada 3 – Paso 3, endpoint de búsqueda y arquitectura N-capas
- **Paso:** Paso 3, endpoint `GET /api/pacientes/buscar?q=...`.
- **Herramienta:** GitHub Copilot.
- **Prompt:** Se pidió implementar la búsqueda pasando por ruta, controlador, servicio y repositorio, usando `pool.query` parametrizado y declarando `/buscar` antes de rutas `/:id`.
- **Resultado:** Se añadió la delegación entre las capas existentes y el repositorio consulta `SELECT * FROM buscar_pacientes($1)`. La ruta de búsqueda está declarada antes de la ruta `PUT /:id`. [COMPLETAR: describir cualquier error de ruta o controlador que Copilot propuso y que se corrigió, si ocurrió.]
- **Verificación:** `node --check` pasó para los archivos JavaScript del backend y el endpoint figura en el README. No se hizo una petición real en navegador, Postman o `curl`: la API aún no está desplegada en Render ni conectada a Neon.

## Entrada 4 – Paso 4, registro de paciente y número de atención
- **Paso:** Paso 4, `generar_numero_atencion()` y `registrar_paciente()`.
- **Herramienta:** GitHub Copilot.
- **Prompt:** Se pidió que PostgreSQL genere números de atención con año y secuencia, normalice el nombre/especie y que el repositorio registre llamando a una función parametrizada. El formulario del proyecto incluye raza, peso, dueño y RUT además de los campos del ejemplo.
- **Resultado:** Se añadió una secuencia y la función de registro conserva las columnas del modelo real, incluido historial, RUT, foto y emoji; el servicio dejó de fabricar el número en Node. La secuencia se sincroniza con los números existentes al instalar el script. [COMPLETAR: indicar si se cambió a mano algún parámetro, tipo SQL o regla de número y por qué.]
- **Verificación:** Pasaron `npm run build`, `npm run lint` y `node --check`. No se registró un paciente de prueba ni se comprobó el número generado en PostgreSQL, porque Neon todavía no está configurado para la aplicación.

## Entrada 5 – Desafío extra, historial de diagnósticos
- **Paso:** Desafío extra, función `actualizar_diagnostico()` y endpoint `PUT /api/pacientes/:id/diagnostico`.
- **Herramienta:** GitHub Copilot.
- **Prompt:** Se pidió guardar el diagnóstico anterior antes de actualizar la ficha y exponer esa operación por las capas de ruta, controlador, servicio y repositorio.
- **Resultado:** Se creó `historial_diagnosticos`; la función SQL inserta el diagnóstico previo antes del `UPDATE`. Se agregó la ruta específica antes de `/:id` y la consulta del repositorio es parametrizada. [COMPLETAR: anotar cambios manuales o errores encontrados al integrar el desafío, si los hubo.]
- **Verificación:** La sintaxis de las capas backend pasó con `node --check`. No se probó una actualización real ni se consultó `historial_diagnosticos`, porque las funciones todavía no se han instalado en Neon.

## Reflexión en pareja
La IA nos ayudó a implementar el buscador y a conectar las funciones PostgreSQL con las capas del backend, manteniendo las consultas parametrizadas.
Al revisar el proyecto vimos que el ejemplo hablaba de `TablaPacientes`, pero nuestra interfaz usa tarjetas; adaptamos la integración a la estructura que sí existe.
También tuvimos que conservar los campos reales del formulario, que son más amplios que los del ejemplo de registro.
Las verificaciones automatizadas confirmaron compilación, lint y sintaxis, pero no sustituyen la ejecución de funciones en Neon.
Aprendimos que conviene darle a la IA el esquema real y revisar nombres, tipos y orden de las rutas antes de aceptar la solución.
[COMPLETAR: añadir qué corrección concreta hicieron ustedes y qué aprendieron al probarla en pareja.]