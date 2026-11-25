# Fase 1: Análisis de Requisitos y Alcance — BuildLab

## Descripción del Proyecto
**BuildLab** es una aplicación web interactiva orientada a principiantes para el aprendizaje y simulación del armado de computadoras paso a paso. El sistema cuenta con validación de compatibilidad en tiempo real, cálculo de consumo energético, diagnóstico de posibles cuellos de botella, gestión de perfiles de usuario y un panel de administración para el mantenimiento del catálogo de hardware.

---

## 1. Requisitos Funcionales (RF)

### Módulo de Autenticación y Usuarios
* **RF-01 (Registro de usuarios):** Permitir el registro de cuentas con nombre completo, correo electrónico único y contraseña encriptada de mínimo 8 caracteres.
* **RF-02 (Registro de administradores):** Habilitar el registro con rol `ADMIN` únicamente si se ingresa un código de autorización válido verificado en el servidor.
* **RF-03 (Inicio de sesión):** Autenticar usuarios mediante correo electrónico y contraseña, emitiendo un token JWT para gestionar la sesión.
* **RF-04 (Gestión de perfil y modo invitado):** Mostrar la información del usuario autenticado en el perfil, permitiendo además la navegación en modo invitado para explorar el catálogo y el simulador.
* **RF-05 (Recuperación de contraseña):** Permitir solicitar un enlace de restablecimiento ingresando el correo electrónico registrado.

### Módulo de Administración (Solo `ADMIN`)
* **RF-06 (Gestión de Marcas):** Permitir registrar nuevas marcas (nombre y sitio web opcional), editar sus datos y alternar su estado entre `Activa` e `Inactiva` (*Soft Delete*).
* **RF-07 (Gestión de Componentes):** Permitir registrar nuevos componentes asociados a una marca y categoría, editar sus especificaciones técnicas (consumo en Watts, plataforma/socket, gama y perfil recomendado) y alternar su estado entre `Activo` e `Inactivo`.

### Módulo de Catálogo y Simulador
* **RF-08 (Consulta y filtrado del catálogo):** Listar únicamente componentes activos, permitiendo filtrar por categoría, plataforma/socket (opcional para el modo de paso independiente), perfil de uso recomendado y búsqueda por texto (nombre, marca o socket).
* **RF-09 (Soporte para flujo guiado e independiente):** Proveer los componentes correspondientes a cada paso del armado (CPU, Disipador, RAM, Almacenamiento, PSU, GPU y futuros pasos adicionales) junto con su consumo en Watts para el cálculo en vivo.

### Módulo de Diagnóstico y Builds Guardadas
* **RF-10 (Motor de diagnóstico):** Recibir una lista de componentes instalados y devolver un diagnóstico real que valide compatibilidad de socket, consumo total de Watts frente a la capacidad de la fuente (PSU) y posibles advertencias de cuello de botella.
* **RF-11 (Guardado de PCs):** Permitir al usuario autenticado guardar una PC armada con un nombre personalizado, su perfil de uso (*Gaming*, *Diseño Gráfico* u *Oficina*) y la lista dinámica de componentes que la conforman.
* **RF-12 (Consulta de PCs guardadas):** Listar las PCs guardadas del usuario autenticado en su perfil, con filtro por perfil de uso y visualización detallada de los componentes de cada armado.

---

## 2. Reglas de Negocio (RN)

* **RN-01 (Seguridad de roles):** Ningún usuario puede obtener permisos de `ADMIN` sin validar el código de autorización configurado en el servidor. Los endpoints de gestión de marcas y componentes deben rechazar cualquier petición que no provenga de un `ADMIN`.
* **RN-02 (Borrado lógico e integridad):** Las marcas y componentes no se eliminan físicamente de la base de datos; se desactivan mediante su estado. Los registros inactivos no aparecen en el catálogo público ni en nuevos armados, pero se conservan intactos en las PCs que los usuarios ya tenían guardadas.
* **RN-03 (Validación de encendido POST):** Si existe incompatibilidad de socket o el consumo total supera la capacidad de la fuente de poder, el diagnóstico debe devolver `compatible = false` especificando el componente en conflicto para bloquear el encendido.
* **RN-04 (Privacidad de builds):** Un usuario solo puede consultar y gestionar sus propias PCs guardadas.
* **RN-05 (Escalabilidad del armado):** La estructura de una build guardada debe admitir una cantidad dinámica de componentes para soportar la incorporación de nuevos pasos de armado a futuro sin modificar el esquema de la base de datos.

---

## 3. Alcance del Proyecto: MVP (v1.0) vs. Mejoras Futuras (v2.0)

| Entra en MVP (v1.0) | Queda para v2.0 (Post-MVP) |
| :--- | :--- |
| Autenticación JWT con roles `USER` y `ADMIN` (código de autorización) | Envío real de correos SMTP para recuperación de contraseña (en v1.0 se simula por consola) |
| Panel Admin: creación, edición y cambio de estado (*Soft Delete*) de Marcas y Componentes | Gestión dinámica desde base de datos de enlaces de YouTube y Guías Externas |
| Catálogo público con búsqueda y filtros dinámicos (categoría, plataforma, perfil recomendado) | Edición de PCs ya guardadas y opción para compartir builds por enlace público |
| Motor de diagnóstico (compatibilidad, Watts y cuello de botella) + Guardado y listado de Builds | Recomendador automático de builds completas según presupuesto |

### Fuera de alcance
* Secciones de **Contacto** y **Preguntas y Respuestas (P&R)** descartadas del proyecto.