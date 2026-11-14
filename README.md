# Mockup ERP Oracle – Caso Omantel

Prototipo (mockup) de un portal web para la implementación del ERP de Oracle en Omantel. Está hecho **solo con HTML y CSS**, sin JavaScript.

## Cómo verlo

Descarga el repositorio y abre `index.html` con doble clic en un navegador actualizado (Chrome, Edge o Firefox).

La navegación usa enlaces y la pseudoclase CSS `:target`. Cada pantalla tiene su propia dirección:

| Pantalla | Dirección | RF |
|---|---|---|
| Inicio de sesión | `index.html#login` | RF-01 |
| Código de verificación (MFA) | `index.html#mfa` | RF-01 |
| Panel del proyecto | `index.html#dashboard` | RF-02 |
| Migración de clientes | `index.html#migracion` | RF-03, RF-04 |
| Programar migración (modal) | `index.html#modal` | RF-03, RF-04 |
| Control de cambios | `index.html#cambios` | RF-05 |
| Capacitación | `index.html#capacitacion` | RF-06 |
| Facturación | `index.html#facturacion` | RF-07 |
| Seguridad y auditoría | `index.html#seguridad` | RF-08, RF-09 |

La casilla **"Mostrar RF"** de la barra superior muestra u oculta las etiquetas de requerimientos y de OWASP.

## Contenido

- `index.html`: el mockup.
- `capturas/`: capturas de cada pantalla (1440×960) para pegar en el informe.
- `Texto_Informe_Mockup.md`: texto de apoyo para el informe (requerimientos funcionales, sistemas críticos, Ley 21.459, OWASP y explicación de cada pantalla).

> Los datos que aparecen en el mockup (cifras, nombres, fechas) son ilustrativos.
