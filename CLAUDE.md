# CLAUDE.md — Calidad Textil: Corte, Defectos textiles y Tintorería (segundas)

Las reglas generales de trabajo están en el repo privado `lives-instrucciones` (CLAUDE.md). Este archivo solo describe esta herramienta. (El nombre del repo es histórico: las pantallas de segundas ya se retiraron.)

## Qué hace
Ocho pantallas que salen de **dos plantillas**; cada archivo solo cambia una o dos líneas de configuración.

Tablero "Indicadores de Calidad Textil" (solo consulta, Chart.js; `SOLO_SEC`):
- `corte.html` (s2) — rechazos de Corte: tendencia, responsables, frente a lo producido y detalle.
- `defectos.html` (s3) — seguimiento de defectos textiles: recurrencia, trazabilidad, clientes y telas, evolución.
- `tintoreria.html` (s4) — reporte diario de tintorería: estado, tintorerías, evolución, detalle. Excel de partidas desde el detalle.
- Los tres permiten descargar una copia HTML del tablero con los datos ("Exportar") e imprimir.

Formulario "Registro y carga · Calidad Textil" (`SOLO_VISTA`):
- `form-corte.html` (v_aud) — auditoría de Corte por O/Corte: alta, seguimiento, corrección y "Ver por estado" con cambio de estado en lote. Excel de la lista.
- `form-defectos.html` (v_def) — defectos textiles: cantidad por tipo de defecto y % de rechazo.
- `form-tintoreria.html` (v_tin) — revisión diaria de partidas de tintorería: nueva revisión, corrección, cambio de estado en lote. Excel de la lista.
- `carga-corte.html` (v_carga, COR) — **carga** de los Excel del sistema de Corte: liquidación del mes (hoja `LIQ_CORTE`), archivo INDICADOR (`TOTAL O-CORTE` y `O-CORTE RECHAZADAS`), inspecciones (`REPORTE`) y la carga inicial de auditorías (`DATA LIVES`). Avisa si el mes ya estaba cargado y pide "Reemplazar". Las inspecciones sin origen se clasifican a mano (TEXTIL, CORTE o S/O).
- `partidas-revisadas.html` (v_rep) — consulta de partidas revisadas de tintorería con filtros, suma de peso por taller, "Copiar tabla" para el correo e imprimir.

Auditoría de Tintorería (FO-265 "Reporte control de calidad tela acabada", desde el 26/10/2026; pantallas propias, no salen de las plantillas):
- `auditoria-tintoreria.html` — **entrada** (consulta): filtros, lista con regla 500 (Defectos y Obs. se abren con su ícono, "🔍 N defectos" y "📝 Ver", en una ventanita, para que la tabla no se descuadre), Excel LIVES (`LS_XLS`) e "Imprimir" con el formato de la hoja "Control de auditoría en servicio de tintorería" (una hoja por tintorería). En la barra lleva **Registrar** y **Dashboard**: Dashboard abre `tintoreria.html` (el tablero del reporte diario de tintorería, que ya sabe funcionar embebido) en la misma ventana. El botón **Registrar** abre el formulario en una ventana dentro de la página; cada fila trae su botón **Ver PDF** (y la fila entera también se toca) que abre el reporte en la misma ventana, como el buscador de Auditoría Final.
- `form-auditoria-tintoreria.html` — **formulario** (también con link directo para el celular), con "Corregir" y la Partida siempre arriba (la partida se reduce a una línea cuando ya se eligió y su resumen queda pegado sobre las pestañas) y 4 pestañas debajo para no subir y bajar: Tela (con fecha, lote y muestra), Rollos, Verificación y Cierre (dictamen y firmas). Al guardar con faltantes, salta a la primera pestaña con error y marca con punto rojo las que tienen; si falta la partida, se queda en la parte de arriba. La partida busca primero en `partidas` (escribe "561012" y encuentra "26-561012"; si hay varias telas, se elige); si no está, en lo ya registrado en `ctex_tint`; si tampoco, se llena a mano. Lo que trae la base de partidas (tela, color, grupo, peso y OP) queda bloqueado y no se cambia a mano (decisión de Christian, 28/10/2026); lo que no trae, lo de `ctex_tint` y los STD/proveedores/ruta siguen editables. Un bloque por rollo de la muestra con los 28 defectos en 3 familias (+/−), tabla de verificación, dictamen (Aprobado/Rechazado + Prime/Seg./Terc./Obs.), firmas. Borrador en el equipo, "¿Necesitas corregir…?" y `?id=` para corregir. Con `?modal=1` avisa a la entrada por `postMessage` (`lsAtin`: sucio, guardado, cerrar).
- `ver-auditoria-tintoreria.html?id=` — **reporte** con el formato de la hoja FO-265 (imprime en una A4 horizontal). Desde el 28/10/2026, como el reporte de Artes: una sola tabla de defectos a todo el ancho con solo los defectos registrados (tipo, defecto, cantidad por rollo, total; arriba el ancho de cada rollo), sin firmas (auditor y supervisores van en los datos de arriba) y observaciones a la altura de la verificación. **Compartir PDF** (html2canvas + jsPDF, igual que Auditoría Final; una A4 horizontal; se comparte por el menú del equipo o se descarga), Imprimir, y Corregir y Anular para quien puede. Abierta sin `?id=` dice "Falta el código de la auditoría".

## Entrar fuera del portal
Las pruebas se hacen fuera del portal, sin `?usuario=` en el link. Entonces el buscador muestra el botón **Identificarme** (y el formulario directo lo muestra en su aviso): un cuadro "¿Quién eres?" con las personas del catálogo, o un usuario escrito (ej. `grejano`). Se guarda en este equipo (`localStorage` `lives_atin_quien`) y firma lo que se registre; "Cambiar de persona" lo cambia y "Dejar de usar el nombre" lo quita. Con `?usuario=` (portal) el botón no aparece. El nombre se toma como viene (riesgo aceptado hasta que Sistemas firme el link): lo que decide si puede registrar es la lista de `ctex_atin_puede`. Las ventanas internas (`?modal=1`) no guardan el nombre en la sesión.

## Base de datos (Supabase lives-datos)
Auditoría de Tintorería (registro `CT05 auditoria tintoreria fo265`): una sola puerta `ctex_atin_api(p_accion, p)` con `inicio`, `partida`, `guardar`, `buscar`, `ver`, `anular`. Tablas `ctex_atin` (cabecera), `ctex_atin_rollos`, `ctex_atin_defectos`, `ctex_atin_param` y `ctex_atin_cat` (base de extracción: 4 personas del Excel con código, 6 tintorerías, 28 defectos con su equivalente en el catálogo del resumen, 10 parámetros y el ajuste `ALIMENTA_RESUMEN`). Lee `partidas` y `ctex_tint`.
- Conexión con el resumen diario: si `ctex_atin_cat` AJUSTE `ALIMENTA_RESUMEN` = `SI`, cada auditoría crea/corrige su fila en `ctex_tint` (estado CALIDAD APROBADA / OBSERVADO / RECHAZADO, los 2 defectos más frecuentes) y al anular la borra (queda en `ctex_log`). Hoy está en `NO` (prueba en paralelo, para no duplicar lo que digita Grecia). Desde el 28/10/2026 la base no tiene auditorías: se borraron los 5 ejemplos de prueba (CT09).
- Permiso: `ctex_atin_puede(p_usuario)`. Desde el 28/10/2026 (CT09, decisión de Christian al pasar al portal) el interruptor `ctex_atin_cat` AJUSTE `REGISTRO_LIBRE` está en `SI`: cualquiera registra (salvo Rodrigo con sesión). En `NO` (CT07) registran solo quienes están en `ctex_atin_cat` tipo `REGISTRA` (`ctalepcio`, `grejano`) y las 4 personas del catálogo (que no están en `nombres_sistemas`: con el usuario del portal no se reconocerían). Sin ese interruptor aplica: las personas de `ctex_atin_cat` (firman con su nombre, no se cambia) o quien tiene `ctex_puede_escribir` / Datos e IA (eligen el auditor). Rodrigo nunca. Quien no está en el catálogo (ej. Christian) elige el auditor y corrige o anula cualquiera; cada auditor del catálogo corrige y anula solo lo suyo.

Resto de la herramienta, dos funciones:
- `ctex_leer` (`p_accion`): `alias`, `catalogos`, `indices`, `corte_res`, `corte_rech`, `read_deftex`, `tint_res`, `rep_datos`, `rep_listas`, `buscar_oc`, `buscar_partida`, `por_estado`, `status`, `origenes`. Lee `ctex_catalogos`, `ctex_telas`, `ctex_corte_aud`, `ctex_corte_ins`, `ctex_corte_liq`, `ctex_def_tex`, `ctex_def_tex_det`, `ctex_tint` y las tablas de segundas `ctex_seg_det`, `ctex_seg_tot`.
- `ctex_guardar` (`d.tabla`): `AUD_FORM`, `DEFTEX_FORM`, `TINT_FORM`, `CORREGIR`, `CAMBIO_ESTADO`, `CORTE_LIQ`, `CORTE_INS`, `CORTE_AUD`. Escribe en esas mismas tablas `ctex_*`.
- No usa Storage ni lee tablas directo. Sin Apps Script desde el 24/09/2026.

## Quién la usa
Según las entradas por el portal de los últimos 30 días al 21/10/2026: 4 personas (Gerencia Textil, digitadora de Calidad Textil, supervisora de calidad).

## Quién puede registrar
- Lo decide la base: `ctex_guardar` exige sesión y la función de permiso `ctex_puede_escribir`. El nombre de quien registra sale del usuario del portal.
- A Sistemas: `ctex_puede_escribir` y `ctex_atin_puede` son las piezas que reemplazan con su seguridad.

## Pendientes vistos al escribir esto (21/10/2026)
- En los formularios, la barra lateral enlaza a `segundas.html` (retirada) y a `dashboard.html` (no existe): enlaces rotos, hoy ocultos en modo de una sola vista.
- Queda código de segundas que ya no se usa (acción `read`, carga SEG en `carga-corte.html`).
