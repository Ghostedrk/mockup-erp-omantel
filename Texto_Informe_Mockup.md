# Ítem 5 y 6 – Requerimientos, sistemas críticos, normativa, OWASP y prototipo (mockup)

> Texto listo para pegar en el informe (Arial 12, interlineado 1,5, justificado; cada ítem en hoja nueva).
> Las imágenes están en la carpeta `capturas/`. Para el ítem 5 y 6 se piden al menos 2: se recomiendan **03_dashboard.png** y **05_modal.png**. En la sección "Mockup" puedes usar el resto.

---

## 5.1 Contexto del proyecto

Omantel, único operador de telefonía fija de Omán, implementó el ERP de Oracle para prepararse para la liberalización del mercado de telecomunicaciones y reemplazar sistemas heredados dispares por una sola base de datos integrada. El caso muestra que el proyecto se atrasó cerca de seis meses por falta de personal con experiencia en Oracle, mala comunicación con los consultores, pruebas insuficientes, capacitación tardía (casi uno de cada tres empleados no estaba capacitado al momento de la puesta en marcha y un 15 % no recibió ninguna) y poca información a las partes interesadas.

El prototipo propuesto, **"Portal ERP Omantel"**, es una aplicación web que se apoya en Oracle E-Business Suite y busca resolver esos problemas: centraliza el seguimiento de la implementación, controla la migración de clientes con plan de reversión, formaliza el control de cambios, mide la capacitación y protege la información con controles de seguridad.

## 5.2 Requerimientos funcionales (RF)

| ID | Requerimiento funcional | Problema del caso que resuelve |
|----|-------------------------|--------------------------------|
| RF-01 | El sistema debe autenticar a los usuarios con usuario, contraseña robusta y segundo factor (MFA), bloqueando la cuenta tras 5 intentos fallidos. | Protección de información comercial sensible compartida con interesados. |
| RF-02 | El sistema debe mostrar un panel con el avance del proyecto, los hitos (Tabla II del caso) y el estado de los sistemas críticos. | Falta de visibilidad: pocos empleados sabían del proyecto. |
| RF-03 | El sistema debe permitir programar lotes de migración de clientes y bloquear fechas dentro de los períodos de congelamiento por facturación. | Los clientes pidieron no migrar durante los ciclos de facturación. |
| RF-04 | El sistema debe notificar a los clientes con anticipación (72 h) y generar un respaldo y plan de reversión (back-out) para cada lote. | Plan de reversión al sistema heredado sin pérdida de servicio. |
| RF-05 | El sistema debe registrar solicitudes de cambio, evaluar su impacto y aprobarlas mediante un flujo (comité de cambios → firma Oracle → publicación del plan). | Proceso formal de control de cambios; ninguna personalización sin firma de Oracle. |
| RF-06 | El sistema debe registrar la capacitación por área y usuario, programar cursos y dar acceso a un entorno de práctica (sandbox). | Capacitación tardía, 15 % sin capacitar y conocimiento olvidado por falta de práctica. |
| RF-07 | El sistema debe permitir consultar cuentas y facturas de clientes, mostrando datos enmascarados según el rol. | Integración de la facturación con Oracle Receivables. |
| RF-08 | El sistema debe gestionar roles y permisos (RBAC), incluyendo accesos temporales para consultores externos. | Dependencia de consultores de Oracle; mínimo privilegio. |
| RF-09 | El sistema debe registrar en un log inalterable todas las acciones relevantes (quién, qué, cuándo, desde dónde). | Trazabilidad y apoyo ante incidentes o delitos informáticos. |

**Requerimientos no funcionales (RNF) destacados:** disponibilidad ≥ 99,9 % en sistemas críticos; tiempo de respuesta < 3 s; interfaz bilingüe (árabe/inglés, según la arabización de contenidos que menciona el caso); cifrado de datos en tránsito (TLS 1.3) y en reposo (Oracle TDE); cierre de sesión por inactividad de 15 minutos.

## 5.3 Sistemas críticos del proceso

Un sistema crítico es aquel cuya falla detiene el negocio, provoca pérdidas económicas o incumple obligaciones legales. En Omantel son:

1. **Facturación (Billing):** genera los ingresos de la empresa. Por eso se definieron períodos de congelamiento durante los ciclos de facturación.
2. **Oracle Financials (Contabilidad, Cuentas por pagar y por cobrar):** obligaciones contables y regulatorias ante la TRA y los accionistas (el gobierno de Omán).
3. **CRM / Atención a clientes:** cualquier interrupción afecta directamente la experiencia del cliente, justo antes de que entre competencia al mercado.
4. **Compras e Inventario:** abastecimiento de equipos de red y de cliente.
5. **Integración con sistemas heredados:** en especial la facturación móvil y prepago (Al-Ufuq), que el caso identifica como un punto delicado.
6. **Base de datos Oracle única:** concentra la información de todas las áreas; si falla, fallan todos los módulos.

El panel del prototipo (RF-02) muestra la disponibilidad y el estado de cada uno de estos sistemas, con alertas cuando alguno se degrada.

## 5.4 Normativa de delitos informáticos

**Chile – Ley 21.459 (2022)**, que adapta la legislación al Convenio de Budapest y tipifica, entre otros:

- **Ataque a la integridad de un sistema informático (art. 1):** obstaculizar o impedir el funcionamiento del sistema.
- **Acceso ilícito (art. 2):** entrar sin autorización o superando las barreras técnicas.
- **Interceptación ilícita (art. 3):** captar datos en transmisión.
- **Ataque a la integridad de los datos (art. 4):** alterar, dañar o suprimir datos.
- **Falsificación informática (art. 5)** y **fraude informático (art. 7).**
- **Abuso de dispositivos (art. 8):** herramientas o contraseñas para cometer estos delitos.

También se considera la **Ley 19.628** sobre protección de la vida privada y su reemplazo, la **Ley 21.719** de protección de datos personales. Como la empresa opera en Omán, se menciona también la **Ley de Ciberdelitos de Omán (Real Decreto 12/2011)**.

**Cómo se aplica en el prototipo:**

- Aviso legal en la pantalla de inicio de sesión, que advierte que el acceso no autorizado es delito (disuasión y respaldo legal).
- Registro de auditoría (RF-09) que entrega evidencia de accesos ilícitos o de alteración de datos (por ejemplo, el intento de modificar la tabla de tarifas).
- Bloqueo por intentos fallidos y alerta al equipo de ciberseguridad (CSIRT) para evaluar una denuncia.
- Enmascaramiento de datos personales y de facturación según el rol (protección de datos personales).

## 5.5 Buenas prácticas de desarrollo OWASP (Top 10 2021)

| Riesgo OWASP | Control en el prototipo | Pantalla |
|--------------|-------------------------|----------|
| A01 – Pérdida de control de acceso | Matriz de roles (RBAC), mínimo privilegio, accesos temporales para consultores. | Seguridad |
| A02 – Fallas criptográficas | TLS 1.3 en tránsito y cifrado TDE en reposo; respaldos cifrados. | Seguridad |
| A03 – Inyección | Consultas parametrizadas y validación de entradas (por ejemplo, la fecha del lote). | Migración / Seguridad |
| A04 – Diseño inseguro | Reglas de negocio en el servidor: no se puede migrar en período de congelamiento. | Modal de migración |
| A05 – Configuración de seguridad incorrecta | Entornos separados (pruebas/producción) indicados en el menú lateral. | Menú lateral |
| A07 – Fallas de identificación y autenticación | MFA, contraseña de 12 o más caracteres, bloqueo tras 5 intentos, expiración de sesión. | Login |
| A08 – Fallas de integridad de software y datos | Cambios solo con firma de Oracle y flujo de aprobación. | Control de cambios |
| A09 – Fallas de registro y monitoreo | Log inalterable, exportación firmada y alertas. | Seguridad |

---

# Mockup – Explicación del prototipo según los RF

El prototipo se construyó como una maqueta web navegable (archivo `index.html`) con un menú lateral que separa las funciones del **Proyecto** (panel, migración, cambios, capacitación) de las de **Operación** (facturación, seguridad). Cada pantalla muestra con una etiqueta naranja el RF que cumple y con una etiqueta morada el control OWASP que aplica. Las etiquetas se pueden ocultar con el selector "Mostrar RF".

**Pantalla 1 – Inicio de sesión (RF-01).** *(imágenes 01 y 02)*
Se ingresa con el usuario corporativo y la contraseña. Después se pide un código MFA de 6 dígitos que vence a los 45 segundos. El texto de ayuda informa la política de contraseñas y el bloqueo por intentos (OWASP A07). Abajo aparece el aviso legal que cita la Ley 21.459. El panel izquierdo resume el propósito del ERP: una sola base de datos integrada.

**Pantalla 2 – Panel del proyecto (RF-02).** *(imagen 03)*
Muestra cuatro indicadores clave: avance global, clientes migrados, personal capacitado y cambios pendientes. La línea de tiempo reproduce los hitos de la Tabla II del caso, con su estado (completado, en curso o pendiente). A la derecha se ve el estado de los **sistemas críticos** con su disponibilidad. Esto responde al problema de que solo un puñado de empleados conocía el proyecto: ahora todos los interesados ven el mismo estado actualizado.

**Pantalla 3 – Migración de clientes (RF-03 y RF-04).** *(imágenes 04 y 05)*
Lista los lotes de migración por segmento (Gobierno, Corporativo, Residencial, Oman Mobile) y su estado, incluido un lote **revertido**, que demuestra que el plan back-out funciona. El calendario marca en rojo el período de congelamiento por facturación y en verde las migraciones programadas. Al programar un lote (modal), el sistema **rechaza** una fecha dentro del congelamiento y deja marcadas por defecto la notificación de 72 horas a los clientes y la generación del respaldo y del plan de reversión. Esto aplica la lección del caso: migrar con el menor impacto posible en el cliente.

**Pantalla 4 – Control de cambios (RF-05).** *(imagen 06)*
Registra las solicitudes de cambio con solicitante (incluidos el regulador TRA y Oman Mobile), prioridad y estado. El detalle muestra el impacto evaluado y el flujo de aprobación: registro → análisis de impacto → comité → **firma de Oracle** → publicación del plan. Así se respeta el acuerdo del caso de no personalizar el ERP sin la firma de Oracle (OWASP A08).

**Pantalla 5 – Capacitación (RF-06).** *(imagen 07)*
Un gráfico de cobertura y una tabla por área muestran quién está capacitado y si tiene acceso al entorno de práctica (sandbox). Los cursos se programan antes del go-live e incluyen práctica inmediata, para evitar lo ocurrido en Omantel, donde el personal aprendió SQL y no lo usó hasta siete meses después. También se incluye un curso de seguridad de la información y Ley 21.459.

**Pantalla 6 – Facturación (RF-07).** *(imagen 08)*
Muestra los indicadores del ciclo de facturación y la consulta de facturas por cuenta. El número de cuenta aparece **enmascarado** según el rol (protección de datos personales). El indicador de errores compara el ERP con el sistema heredado.

**Pantalla 7 – Seguridad y auditoría (RF-08 y RF-09).** *(imagen 09)*
El registro de auditoría guarda fecha, usuario, acción, IP y resultado. Ahí se ven un bloqueo por intentos fallidos desde una IP externa y un intento denegado de modificar tarifas, con una alerta al CSIRT (Ley 21.459). La matriz de roles aplica el mínimo privilegio (OWASP A01), y la lista de controles activos resume MFA, cifrado, consultas parametrizadas y cierre de sesión por inactividad.

## Relación del mockup con IaaS, SaaS y Cloud (para conectar con los ítems 2–4)

- **IaaS:** la base de datos y los servidores del ERP se alojan en Oracle Cloud Infrastructure (indicado en el pie del menú), con alta disponibilidad (Oracle RAC) y respaldos cifrados.
- **SaaS:** módulos como capacitación (e-learning) o, en una evolución, Oracle Fusion Cloud ERP pueden consumirse como servicio, sin infraestructura propia.
- **Cloud Computing:** el portal web es accesible desde cualquier lugar, escala según la demanda (por ejemplo, durante los ciclos de facturación) y usa el modelo de responsabilidad compartida en seguridad.

## Conclusión del prototipo

El mockup traduce las lecciones del caso Omantel en funciones concretas: visibilidad para las partes interesadas, migración controlada y reversible, control formal de cambios, capacitación medible y seguridad alineada con OWASP y con la Ley 21.459. Con esto se reducen los riesgos que provocaron el atraso de seis meses del proyecto original.
