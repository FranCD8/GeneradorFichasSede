# Generador de Fichas de Sede Electrónica (v2)

Aplicación web de un solo archivo que genera **automáticamente las fichas informativas HTML** de los procedimientos administrativos de una sede electrónica a partir del **Excel de trabajo** con la información de cada procedimiento.

No hay que instalar nada ni tener un servidor: se abre [`generador_fichas_sede_v2.html`](generador_fichas_sede_v2.html) en el navegador, se carga el Excel y se descargan las fichas (una a una o todas en un ZIP).

```text
Excel de procedimientos  ──►  Generador de Fichas  ──►  <código>.html  (una ficha por procedimiento)
(.xlsx / .xls / .ods)         + configuración           o ZIP con todas las fichas
                                del cliente
```

---

## Índice

1. [Qué hace](#qué-hace)
2. [Uso rápido](#uso-rápido)
3. [Generador](#generador)
4. [Configurador de clientes](#configurador-de-clientes)
5. [Configuración por defecto (MAU)](#configuración-por-defecto-mau)
6. [HTML generado](#html-generado)
7. [Tecnología](#tecnología)
8. [Limitaciones y notas](#limitaciones-y-notas)

---

## Qué hace

- **Lee el Excel** de procedimientos en el navegador (`.xlsx`, `.xls`, `.ods`).
- **Detecta los procedimientos válidos**: las filas cuyo código cumple el patrón configurado (por defecto, 3 letras).
- **Convierte cada columna en un bloque** del acordeón de la ficha (Descripción, Quién puede solicitar, Plazo, Normativa…).
- Deja **editar el contenido** de cada bloque con un editor de texto enriquecido antes de generar.
- **Genera el HTML** de cada ficha con la plantilla del cliente, codificando tildes y caracteres especiales como entidades HTML.
- **Descarga** una ficha, una selección o todas en un ZIP.
- **Admite varios clientes**: cada uno con sus columnas, bloques y plantilla HTML.

---

## Uso rápido

1. Abre `generador_fichas_sede_v2.html` en Chrome, Edge o Firefox (necesita Internet para cargar las librerías del CDN).
2. **Elige la configuración del cliente**: por defecto está activa *Ayuntamiento (MAU) — Por defecto*. Para otro cliente, ve al **Configurador**.
3. **Carga el Excel**: arrástralo a la zona de carga o haz clic para elegirlo.
4. **Elige un procedimiento** de la lista lateral (hay buscador).
5. **Revisa y edita** los bloques en *✏️ Editar campos* y comprueba el resultado en *👁 Vista previa HTML*.
6. **Descarga**:
   - la ficha abierta (`.html`);
   - los procedimientos marcados (ZIP);
   - todos los procedimientos (ZIP);
   - el **paquete completo**: `fichas/` + `config_<id>.json` + `LEEME.txt`.

---

## Generador

| Función | Detalle |
|---|---|
| Carga del Excel | Se lee la primera hoja. Cada fila cuyo código (columna configurada) cumple el patrón es un procedimiento. |
| Resumen | Número de procedimientos detectados y cuántos tienen datos. |
| Lista y buscador | Selección por procedimiento, con casillas para descargas masivas. *Todos* y *Ninguno* respetan el filtro de búsqueda activo. |
| Editor por bloque | Texto enriquecido (`contenteditable`): párrafos, viñetas en 3 niveles, listas numeradas, sangría, negrita, quitar viñeta y enlaces (con opción de abrir en pestaña nueva). |
| Conversión automática | Las líneas del Excel que empiezan por `- ` o `•` se convierten en `<ul><li>`, y las que empiezan por `1.` o `1)` en `<ol><li>`. |
| Ediciones | Se guardan en memoria por procedimiento: al cambiar de procedimiento y volver siguen ahí. **Cargar otro Excel las borra.** Se marca con un punto qué procedimientos se han editado. |
| Vista previa por bloque | Ventana con dos pestañas: cómo se ve y el código HTML, con botón de copiar y contador de caracteres. |
| Vista previa completa | Código HTML completo de la ficha antes de descargarla. |
| Descargas | Individual · selección (ZIP) · todas (ZIP con barra de progreso) · paquete completo. |
| Interfaz | Adaptable a móvil: por debajo de 900 px el panel lateral pasa a ser un menú deslizante. |

---

## Configurador de clientes

Cada cliente (ayuntamiento u organismo) puede tener su propio Excel y su propia plantilla. El **Configurador** crea configuraciones con un asistente de 4 pasos:

1. **Cliente**: nombre, descripción, **columna del código** del procedimiento y **patrón de validación** del código (expresión regular; p. ej. `^[A-Za-z]{3}$`).
2. **Columnas de referencia**: con un Excel de muestra, marca las columnas que se usan internamente (título, denominación…) pero **no aparecen como bloque**.
3. **Bloques del acordeón**: elige qué columnas del Excel son bloques, pon su etiqueta y ordénalos arrastrando.
4. **Plantilla HTML**: columna del título, cabecera, plantilla de cada bloque y pie. *Cargar plantilla por defecto* recupera la plantilla MAU.

### Marcadores de la plantilla

| Marcador | Dónde | Se sustituye por |
|---|---|---|
| `{{TITLE}}` | Cabecera / pie | Título del procedimiento |
| `{{CODE}}` | Cabecera / pie | Código del procedimiento |
| `{{BLOCKS}}` | Cabecera / pie | Todos los bloques generados |
| `{{BLOCK_ID}}` | Bloque | Número del bloque |
| `{{BLOCK_LABEL}}` | Bloque | Etiqueta del bloque |
| `{{BLOCK_CONTENT}}` | Bloque | Contenido HTML del bloque |
| `{{FIRST_CLASS}}` | Bloque | `''` en el primer bloque, `collapsed` en el resto |
| `{{FIRST_SHOW}}` | Bloque | ` show` en el primer bloque (abierto), `''` en el resto |
| `{{FIRST_ARIA}}` | Bloque | Valor de `aria-expanded` según sea o no el primer bloque |

### Guardar y compartir configuraciones

- Las configuraciones se guardan **solo mientras la página está abierta**: al recargarla vuelve la configuración por defecto.
- Para conservarlas, **exporta** cada configuración a JSON (`config_<id>.json`) e **impórtala** cuando la necesites. El paquete completo también incluye el JSON de la configuración activa.
- Desde la lista se puede activar, editar, duplicar, exportar y borrar cada configuración. La configuración por defecto no se puede borrar.

---

## Configuración por defecto (MAU)

| Parámetro | Valor |
|---|---|
| ID | `mau-default` |
| Columna de código | `Abreviatura` |
| Patrón | `^[A-Za-z]{3}$` |
| Columna de título | `Título para el ciudadano` |
| Columnas de referencia | `Abreviatura`, `Denominación`, `Título para el ciudadano` |
| Plantilla | Acordeón Bootstrap con hojas de estilo del tema de la sede |

**18 bloques, en este orden:**

Descripción → Quién puede realizar la solicitud → Requisitos de iniciación → Fechas en las que puedo presentar → Plazo de resolución → Cómo lo puede presentar → Dónde lo puede presentar → Documentación a aportar → Unidad tramitadora → Órgano de resolución → Obligaciones económicas → Normativa → Efectos de silencio → Fin a la vía administrativa → Notificación → Enlaces de interés → Trámites relacionados → Observaciones

---

## HTML generado

- Sigue exactamente la plantilla del cliente (por defecto, la plantilla base aprobada: `<p>` inicial, `<link>` a las hojas del tema y `<main>` con el acordeón Bootstrap).
- **Siempre salen todos los bloques**; los vacíos llevan `<!-- Contenido pendiente de completar -->` para que se vea qué falta.
- Tildes, `ñ`, `€` y demás caracteres especiales se escriben como entidades HTML (`&aacute;`, `&ntilde;`, `&euro;`…), así que no hay problemas de codificación al publicar.
- Los enlaces insertados con el editor llevan `target="_blank"` y `rel="noopener noreferrer"`.
- No tiene dependencias propias: los recursos apuntan a las rutas relativas `theme/css/` y `theme/js/` que ya existen en la sede.
- Nombre de archivo: `<código en minúsculas>.html`. En los ZIP de selección y de todos, las fichas van en la raíz; en el paquete completo, dentro de `fichas/`.

---

## Tecnología

- Un único archivo HTML con CSS y JavaScript sin *frameworks*.
- [SheetJS `xlsx` 0.18.5](https://sheetjs.com/) (CDN) para leer Excel/ODS en el navegador.
- [JSZip 3.10.1](https://stuk.github.io/jszip/) (CDN) para crear los ZIP.
- Tipografías Inter y JetBrains Mono (Google Fonts).
- Estilo visual con la identidad de Guadaltel (azul marino `#1b2d5e`, verde `#00a878`).

## Estructura del repositorio

```text
.
└── generador_fichas_sede_v2.html   ← Toda la aplicación
```

## Limitaciones y notas

- Hace falta **conexión a Internet** para cargar SheetJS, JSZip y las fuentes desde CDN.
- Las **ediciones** y las **configuraciones** no se guardan entre sesiones: exporta la configuración a JSON y descarga las fichas antes de cerrar la página.
- Solo se lee la **primera hoja** del Excel.
- Los datos del Excel se procesan en local: el archivo no se sube a ningún servidor.

---

**Autor:** Francisco Condado Delgado · Guadaltel.
