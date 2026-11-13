# SPECS.md — Especificación Técnica: Panel de Administración de AgentHub

## 1. Descripción del Producto y Usuario Objetivo
- **Producto:** **AgentHub** es una plataforma SaaS B2B donde las empresas alquilan agentes de Inteligencia Artificial preconfigurados, equipándolos bajo demanda con capacidades modulares llamadas *skills* (navegación web, lectura de PDFs, gestión de calendarios, consultas SQL y notificaciones) para automatizar procesos de negocio.
- **Usuario Administrador:** Equipo interno de Operaciones, Soporte Técnico y Finanzas de AgentHub. Utiliza este panel de administración para monitorear ingresos mensuales, auditar descuentos, gestionar cuentas de clientes, supervisar la salud operativa y los *prompts* de sistema de los agentes desplegados, administrar el catálogo de *skills*, revisar contratos de alquiler y diagnosticar trazas de error en tiempo real.

---

## 2. Stack Tecnológico y Restricciones de Arquitectura
- **Estructura:** Aplicación de página única (SPA simulada por vistas) en un único archivo `index.html` utilizando etiquetas HTML5 estrictamente semánticas (`<aside>`, `<nav>`, `<header>`, `<main>`, `<section>`, `<article>`, `<table>`, `<thead>`, `<tbody>`).
- **Estilos:** **Tailwind CSS vía Play CDN** (`https://cdn.tailwindcss.com`) configurado con `darkMode: 'class'`.
- **Prohibiciones estrictas de estilo:** Cero archivos `.css` externos o personalizados y cero atributos `style="..."` en línea (ni en el marcado estático ni inyectados mediante JavaScript).
- **Interactividad:** **JavaScript Vanilla (ES6+)** puro embebido al final del documento. Prohibido el uso de frameworks (React, Vue, Angular), librerías (jQuery) o herramientas de *build*.
- **Datos:** 100% *hardcodeados* en estructuras JSON en memoria con total consistencia referencial entre las seis secciones (los mismos clientes, agentes, conteos de *skills* e importes coinciden exactamente en todo el panel). Sin llamadas a APIs ni backend.
- **Diseño Responsivo:** Layout adaptable y completamente usable en viewports de escritorio (`>= 1024px`) y tablet (`>= 768px`), con soporte colapsable en móvil.

---

## 3. Matriz de Consistencia de Datos (Referencia Cruzada)
- **Usuarios / Clientes (5):** Elena Mendoza (FinTech Latam - Enterprise), Carlos Silva (Logística Alpha - Pro), Sofía Ramírez (MediCloud Salud - Enterprise), Diego Torres (NovaRetail - Starter), Valeria Castro (LegalBotics - Pro).
- **Agentes (5):**
  1. `Atlas-FinOps` (Propietario: Elena Mendoza | Estado: Activo | 3 skills)
  2. `Hermes-Support` (Propietario: Carlos Silva | Estado: Activo | 3 skills)
  3. `Aura-SalesBot` (Propietario: Elena Mendoza | Estado: Activo | 3 skills)
  4. `Clara-DocAudit` (Propietario: Sofía Ramírez | Estado: Fallando | 2 skills)
  5. `Nexus-Scheduler` (Propietario: Valeria Castro | Estado: Inactivo | 2 skills)
- **Skills (5):** `Navegación Web` (3 agentes), `Lectura de Documentos PDF` (3 agentes), `Gestión de Calendarios` (3 agentes), `Consulta SQL Segura` (2 agentes), `Envío de Notificaciones Slack` (2 agentes).
- **Métricas del Mes Actual (Noviembre 2026):** Ingresos totales `$2,770.00` (suma de los 4 contratos activos del mes), pérdida por descuentos `-$230.00`, agentes activos `3`, agentes fallando `1`.

---

## 4. Especificaciones Funcionales y Visuales por Sección

### Sección 1: Dashboard (`#view-dashboard`)
1. **Cuadrícula de Tarjetas de Métricas (KPI Grid):** Contenedor `grid` responsivo (`grid-cols-1 sm:grid-cols-2 xl:grid-cols-4 gap-5`) con cuatro tarjetas: *Ingresos Totales (Este Mes)* (`$2,770.00`), *Pérdida por Descuentos y Cupones* (`-$230.00`), *Agentes Activos* (`3`) y *Agentes Fallando* (`1`).
2. **Acentos Visuales por Tipo de KPI:** Cada tarjeta incluye un icono SVG temático dentro de un contenedor redondeado, etiqueta descriptiva, cifra principal en tipografía destacada y borde/indicador de color semántico (esmeralda para ingresos, ámbar para descuentos, índigo para agentes activos y rojo para agentes en fallo) con sombra sutil (`shadow-sm`).
3. **Marcador de Posición de Gráfico de Actividad Semanal:** Debajo de las tarjetas, un contenedor de ancho completo (`w-full`) con borde discontinuo (`border-2 border-dashed`), cabecera descriptiva y representación visual mediante barras de altura Tailwind (`h-20` a `h-44`) para los días Lunes a Domingo mostrando ejecuciones de agentes vs. incidencias.
4. **Resumen Rápido de Estado Operativo:** Pie informativo dentro del área de actividad semanal que indica la tasa de éxito global (`98.4%`) y sincronización con el estado actual de los agentes en fallo.

### Sección 2: Gestión de Usuarios (`#view-users`)
1. **Tabla Semántica de Usuarios:** Componente `<table>` dentro de un contenedor estilizado que renderiza 5 filas de usuarios *hardcodeados* con columnas para: Usuario (avatar de iniciales + nombre), Email, Empresa / Plan (`Enterprise`, `Pro`, `Starter`) y Estado mediante *Badge* codificado por color (`Activo`, `Suspendido`, `Pendiente`).
2. **Menú Dropdown de Acciones por Fila:** En la última columna de cada fila, un botón disparador `⋮` abre un menú flotante posicionado con clases relativas/absolutas que ofrece las opciones `"Ver detalle"` y `"Eliminar"`.
3. **Modal de Registro Completo de Usuario:** Al hacer clic en `"Ver detalle"`, se abre el modal global mostrando el expediente completo del usuario: ID interno, nombre completo, correo electrónico, organización, nivel de plan, fecha de alta, facturación mensual acumulada y lista de agentes asociados.
4. **Acción de Eliminación en Memoria:** Al seleccionar `"Eliminar"` en el dropdown, la fila correspondiente se elimina dinámicamente del estado de la vista y se muestra una notificación visual breve en pantalla.

### Sección 3: Gestión de Agentes (`#view-agents`)
1. **Listado de Tarjetas/Filas de Agentes:** Vista estructurada con 5 agentes registrados mostrando nombre técnico del agente, icono identificador, cliente propietario, modelo base y un *Badge* semántico de estado (`Activo` en verde, `Inactivo` en gris, `Fallando` en rojo).
2. **Acordeón de Skills Colapsable con Transición Suave:** Cada agente incluye un botón control expandible (`"Ver skills asociadas (N)"` con icono chevron rotatorio). Por defecto la lista de *skills* permanece oculta (`max-h-0 opacity-0 overflow-hidden`) y al hacer clic alterna suavemente (`transition-all duration-300 ease-in-out`) a `max-h-96 opacity-100`, revelando las etiquetas de *skills* equipadas; un segundo clic vuelve a colapsarlas.
3. **Dropdown de Acciones del Agente:** Cada tarjeta/registro de agente incorpora un botón `⋮` con las opciones `"Configurar"` y `"Eliminar"`.
4. **Modal de Configuración de System Prompt:** Al seleccionar `"Configurar"`, se abre un modal interactivo con los metadatos del agente y un campo `<textarea>` editable que contiene su *prompt* de sistema actual, permitiendo modificarlo y guardar los cambios en memoria durante la sesión.

### Sección 4: Catálogo de Skills (`#view-skills`)
1. **Banner Explicativo Contextual:** En la parte superior de la sección se presenta un bloque informativo destacado explicando qué es una *"skill"* dentro de AgentHub (módulos de capacidad preconstruidos y auditados que otorgan herramientas externas a los agentes IA durante su alquiler).
2. **Cuadrícula de Catálogo de Skills:** Grid responsivo (`grid-cols-1 md:grid-cols-2 xl:grid-cols-3 gap-5`) con 5 *skills* disponibles, mostrando título, categoría técnica, descripción breve del alcance funcional y tarifa mensual unitaria de referencia.
3. **Indicador de Adopción en Agentes:** Cada tarjeta de *skill* muestra un contador visual exacto del número de agentes que la tienen habilitada actualmente en la plataforma, consistente con el listado de la sección Gestión de Agentes.
4. **Dropdown de Acciones y Modal Técnico:** Cada *skill* cuenta con un botón `⋮` con las opciones `"Ver detalle"` (abre un modal mostrando versión, permisos OAuth/API requeridos, precio individual y nombres exactos de los agentes que la usan) y `"Eliminar"`.

### Sección 5: Contrataciones de Agentes (`#view-contracts`)
1. **Tabla de Contratos Activos e Históricos:** Tabla estructurada con 5 contratos de alquiler (4 vigentes y 1 finalizado) mostrando código de contrato, cliente, nombre del agente alquilado, resumen de *skills* contratadas, rango de fechas (inicio y fin), estado contractual e importe total pagado.
2. **Indicadores Visuales de Facturación y Vigencia:** Las fechas se presentan en formato legible junto a un *Badge* de vigencia (`Vigente` en esmeralda o `Finalizado` en pizarra) y el importe total alineado a la derecha con formato monetario USD.
3. **Dropdown de Acciones de Contrato:** Cada fila dispone de un menú `⋮` con las opciones `"Ver detalle"` y `"Eliminar"`.
4. **Modal de Desglose Financiero Detallado:** Al pulsar `"Ver detalle"`, el modal presenta la factura desglosada del contrato: datos del cliente y agente, vigencia, tarifa base mensual del agente, lista individualizada de cada *skill* contratada con su precio unitario exacto, descuento/cupón aplicado y total neto pagado.

### Sección 6: Log de Errores (`#view-logs`)
1. **Tabla de Registro de Ejecución:** Listado tabular con 6 eventos de error *hardcodeados* que muestran *timestamp* exacto (`YYYY-MM-DD HH:MM:SS`), nombre del agente afectado (consistente con Gestión de Agentes), *Badge* de tipo/gravedad y descripción breve del incidente.
2. **Categorización Visual por Gravedad:** Uso de *Badges* con código de color diferenciado según severidad: `Crítico` (rojo), `Error BD / Red` (naranja), `Advertencia` (ámbar), `Info` (azul) y `Resuelto` (verde esmeralda).
3. **Dropdown Operativo de Incidencias:** Cada fila incorpora un botón `⋮` con dos acciones específicas: `"Ver detalle"` y `"Marcar como resuelto"`.
4. **Modal de Traza Completa y Resolución en Vivo:** `"Ver detalle"` abre un modal con metadatos del fallo y un bloque `<pre>` monoespaciado que contiene el *stack trace* completo de ejecución; `"Marcar como resuelto"` actualiza inmediatamente el *badge* de la fila al estado `Resuelto` con estilo atenuado.

---

## 5. Inventario de Componentes de UI Reutilizables
1. **Barra Lateral Persistente (`SidebarNav`):** Navegación vertical fija en escritorio/tablet (`aside` + `nav`) con logotipo de AgentHub, enlaces a las 6 secciones e indicador visual activo (fondo de acento índigo, texto resaltado y borde/icono activo).
2. **Barra Superior con Toggle de Tema (`TopHeader` + `ThemeToggle`):** Cabecera `<header>` persistente con el título dinámico de la sección actual y botón conmutador Sol/Luna que alterna la clase `dark` en `<html>` usando utilidades `dark:` de Tailwind y persiste la preferencia al cambiar de vista.
3. **Tarjeta de Métrica (`MetricCard`):** Contenedor reutilizable con borde adaptable claro/oscuro, icono SVG coloreado, etiqueta secundaria y valor numérico principal.
4. **Etiqueta de Estado/Gravedad (`StatusBadge`):** Píldora compacta (`inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium`) con variantes cromáticas semánticas adaptadas a modo claro y oscuro.
5. **Menú Desplegable de Acciones (`ActionDropdown`):** Botón `⋮` accesible (`aria-haspopup="true"`) acoplado a un menú flotante contextual con cierre automático al seleccionar una opción o al hacer clic en cualquier punto externo del documento.
6. **Lista Colapsable de Skills (`CollapsibleSkills`):** Componente acordeón controlado por clases utilitarias de Tailwind (`max-h-0` / `max-h-96`, `opacity-0` / `opacity-100`, `transition-all duration-300`) sin estilos en línea.
7. **Modal Superpuesto (`ModalOverlay`):** Ventana emergente centrada sobre un *backdrop* semitransparente (`bg-slate-950/70 backdrop-blur-sm`) con cabecera dinámica, cuerpo inyectable, botón de cierre superior (`✕`), botón inferior y cierre al hacer clic directamente en el *backdrop*.

---

## 6. Criterios de Aceptación Verificables
1. El archivo `SPECS.md` figura en el historial de Git en un commit previo e independiente a cualquier creación o modificación del prototipo HTML.
2. Las 6 secciones (`Dashboard`, `Gestión de usuarios`, `Gestión de agentes`, `Skills`, `Contrataciones de agentes`, `Log de errores`) se renderizan y son navegables desde la barra lateral persistente actualizando el indicador de sección activa.
3. Todo el diseño utiliza exclusivamente clases utilitarias de Tailwind CSS vía CDN; no existe ningún archivo `.css` externo ni ningún atributo `style=""` en el HTML ni en el DOM manipulado por JS.
4. El botón *toggle* de la barra superior conmuta toda la interfaz entre modo claro y modo oscuro mediante variantes `dark:`, conservando el esquema elegido al navegar por las 6 secciones.
5. Todos los registros en Usuarios (5), Agentes (5), Skills (5), Contrataciones (5) y Log de errores (6) cuentan con un botón `⋮` funcional que despliega su menú de acciones correspondiente.
6. Al hacer clic fuera de cualquier dropdown `⋮` abierto o al elegir una de sus opciones, el menú se cierra inmediatamente; abrir un dropdown cierra cualquier otro que estuviera abierto.
7. Seleccionar `"Ver detalle"` abre un modal con información completa y contextualizada en cuatro secciones distintas: Usuarios, Skills, Contrataciones (con precios individuales por *skill*) y Log de errores (con traza completa).
8. Seleccionar `"Configurar"` en Gestión de agentes abre el modal con el *prompt* de sistema cargado dentro de un elemento `<textarea>` editable.
9. Todos los modales se cierran tanto al pulsar el botón de cierre como al hacer clic sobre el fondo oscuro (*backdrop*).
10. En Gestión de agentes, las listas de *skills* inician colapsadas por defecto y se expanden/colapsan al hacer clic en su control con una transición suave y visible de `300ms`.
11. Los nombres de agentes, propietarios/clientes, conteos de *skills* e importes financieros son 100% consistentes entre todas las secciones del panel.
12. El documento emplea etiquetas HTML5 semánticas (`header`, `aside`, `nav`, `main`, `section`, `table`) y mantiene un layout limpio y usable tanto en escritorio como en tablet.