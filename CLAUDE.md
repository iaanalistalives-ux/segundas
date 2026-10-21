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

## Base de datos (Supabase lives-datos)
Solo dos funciones:
- `ctex_leer` (`p_accion`): `alias`, `catalogos`, `indices`, `corte_res`, `corte_rech`, `read_deftex`, `tint_res`, `rep_datos`, `rep_listas`, `buscar_oc`, `buscar_partida`, `por_estado`, `status`, `origenes`. Lee `ctex_catalogos`, `ctex_telas`, `ctex_corte_aud`, `ctex_corte_ins`, `ctex_corte_liq`, `ctex_def_tex`, `ctex_def_tex_det`, `ctex_tint` y las tablas de segundas `ctex_seg_det`, `ctex_seg_tot`.
- `ctex_guardar` (`d.tabla`): `AUD_FORM`, `DEFTEX_FORM`, `TINT_FORM`, `CORREGIR`, `CAMBIO_ESTADO`, `CORTE_LIQ`, `CORTE_INS`, `CORTE_AUD`. Escribe en esas mismas tablas `ctex_*`.
- No usa Storage ni lee tablas directo. Sin Apps Script desde el 24/09/2026.

## Quién la usa
Según las entradas por el portal de los últimos 30 días al 21/10/2026: 4 personas (Gerencia Textil, digitadora de Calidad Textil, supervisora de calidad).

## Quién puede registrar
- Lo decide la base: `ctex_guardar` exige sesión y la función de permiso `ctex_puede_escribir`. El nombre de quien registra sale del usuario del portal.
- A Sistemas: `ctex_puede_escribir` es la pieza que reemplazan con su seguridad.

## Pendientes vistos al escribir esto (21/10/2026)
- En los formularios, la barra lateral enlaza a `segundas.html` (retirada) y a `dashboard.html` (no existe): enlaces rotos, hoy ocultos en modo de una sola vista.
- Queda código de segundas que ya no se usa (acción `read`, carga SEG en `carga-corte.html`).
