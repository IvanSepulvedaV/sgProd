# SG-Prod · Definición de Alcance del Proyecto

**Documento:** Alcance y visión de producto
**Audiencia:** los 7 integrantes del equipo
**Vigencia:** Sprint 1 a Sprint 5 (10 semanas)
**Autoridad:** cualquier cambio de alcance requiere acuerdo del equipo y actualización de este documento
**Documentos relacionados:** `Lluvia de Ideas.md` (§MVP), `docs/00_guia_ambiente_desarrollo.md`

---

## 1. Propósito de este documento

Fijar qué se construye, qué no se construye y cómo se sabrá que está terminado. Sin esto, cada integrante asume un producto distinto, y la desmotivación aparece cuando el trabajo hecho no coincide con lo que se imaginaba.

**Regla de lectura:** si algo no está en la sección 5 (dentro del alcance) o en la sección 6 (fuera del alcance), **no existe** para los próximos 10 semanas. Cualquier idea nueva se anota en el backlog futuro, no se implementa.

---

## 2. Problema y contexto

En la industria maquiladora, tres fallas se repiten turno tras turno:

1. **Asignación de personal a ciegas.** El jefe de línea decide quién va a qué estación sin evidencia de quién domina qué. Un operador principiante en una estación crítica produce rechazo y retrabajo.
2. **Calidad sin visibilidad en línea.** Los defectos se descubren al final del turno o al día siguiente. Cuando se detectan, la pieza ya se perdió y la causa ya no es reconstruible.
3. **Esfuerzo no reconocido.** Los operadores con cero defectos no se identifican, así que no se premian. La rotación crece porque el buen desempeño es invisible.

El resultado: Scrap alto, retrabajo costoso, entregas tardías y pérdida de talento.

---

## 3. Visión del producto

**SG-Prod es un tablero de mando gerencial que convierte los datos del piso de producción en tres decisiones automáticas:** a quién asignar, dónde capacitar y a quién bonificar.

**Declaración de alcance del MVP (una frase):**
un Jefe de Línea puede asignar personal según una matriz de habilidades, registrar la calidad hora por hora, y un Gerente puede ver en un solo tablero el FPY, las piezas/hora y los operadores elegibles para bono del periodo.

---

## 4. Objetivo general y objetivos específicos

### 4.1 Objetivo general

Entregar, al término de 10 semanas, un sistema web funcional en Django + PostgreSQL que permita **dirigir la operación de una línea de producción con datos**, demostrable en vivo con datos reales del caso de estudio.

### 4.2 Objetivos específicos (medibles al final del Sprint 5)

| # | Objetivo | Métrica de logro |
| :---- | :---- | :---- |
| O1 | Asignación basada en competencias | El motor sugiere la distribución completa de una línea y emite alerta visual por cada operador Principiante asignado |
| O2 | Captura horaria sin fricción | El Jefe de Línea registra una hora completa de una línea de 8 estaciones en **menos de 3 minutos** (RNF-2: respuesta < 1.5 s por registro) |
| O3 | Visibilidad gerencial inmediata | Cualquier KPI del tablero se alcanza en **máximo 2 clics** (RNF-4) |
| O4 | Calidad accionable | FPY, Piezas/Hora vs Meta y Tasa de Defectos por estación y por empleado, calculados desde los registros, sin captura manual adicional |
| O5 | Incentivos y capacitación automáticos | Lista de operadores con cero defectos en el periodo evaluado y reporte de estaciones con mayor índice de rechazo, ambos generados por el sistema |
| O6 | Autenticación de planta | Login por escaneo de QR (N° de empleado + PIN de 6 dígitos) y por entrada manual (RNF-1) |

---

## 5. Dentro del alcance (MVP)

Trazabilidad directa con los requerimientos del documento de ideas. **Nada más entra.**

### Módulo 1 — Matriz de Habilidades y Asignación

| ID | Alcance | Estado esperado |
| :---- | :---- | :---- |
| RF-1.1 | CRUD de empleados (número, nombre, estado) y de estaciones por línea | Admin de Django operativo + pantallas de consulta |
| RF-1.2 | Matriz de habilidades con 4 niveles: Principiante, Autónomo, Experto, Instructor | Captura y edición por empleado/estación |
| RF-1.3 | Motor de sugerencia de asignación por línea + alerta visual por Principiante | Pantalla de asignación funcional con datos reales |
| RF-1.4 | Asistencia por defecto (todo el personal activo) con *hooks* preparados para un módulo futuro | Interfaz y puntos de extensión documentados en código |

### Módulo 2 — Control de Calidad y Registro Operativo

| ID | Alcance | Estado esperado |
| :---- | :---- | :---- |
| RF-2.1 | Captura hora por hora por estación: Aceptadas, Rechazadas, Retrabajo | Interfaz optimizada para tableta, mínimo 56 px por control |
| RF-2.2 | Catálogo estandarizado de defectos, selección obligatoria | Catálogo precargado y validación que impide guardar sin motivo |

### Módulo 3 — Dashboard, KPIs e Incentivos

| ID | Alcance | Estado esperado |
| :---- | :---- | :---- |
| RF-3.1 | Tablero *At-a-Glance* con semáforos Verde/Amarillo/Rojo y tendencia hora por hora | Vista gerencial con datos reales |
| RF-3.2 | FPY por estación/línea/turno; Piezas/Hora vs Meta; Tasa de Defectos por estación y empleado | Cálculo verificable contra un juego de datos conocido |
| RF-3.3 | Regla de bonos: operadores con cero defectos en el periodo evaluado | Lista generada por el sistema, con regla configurable |
| RF-3.4 | Alertas de capacitación dirigida por estación con mayor rechazo | Reporte automático por periodo |

### Transversal

| ID | Alcance | Estado esperado |
| :---- | :---- | :---- |
| RNF-1 | Login por QR + PIN de 6 dígitos y entrada manual | Funcional en la demo |
| RNF-2 | Interfaz ligera; respuesta < 1.5 s en la captura horaria | Medido sobre Wi-Fi de planta o emulación de red |
| RNF-3 | Modelos normalizados y capas desacopladas para futura ingesta por API (ERP/MES) | Arquitectura documentada; servicios separados de las vistas |
| RNF-4 | Máximo 2 clics para cualquier métrica; sin tablas extensas sin procesar | Verificación por recorrido en la demo |
| — | Autenticación y permisos por rol (Operador/Jefe de Línea/Gerente) | Tres roles con vistas diferenciadas |
| — | Datos de prueba reproducibles (`seeding`) | Un comando reproduce el mismo escenario en cualquier máquina |

---

## 6. Fuera del alcance (explícito)

Estos puntos **no se implementan** en las 10 semanas. Se documentan para evitar discusiones y para que nadie se sienta evaluado por no construirlos.

| Fuera de alcance | Motivo |
| :---- | :---- |
| Control de asistencia real (checador, biométrico, RFID) | Solo se dejan *hooks* de código (RF-1.4) |
| Integración efectiva con ERP/MES (SAP, Oracle, sistemas propietarios) | Se construye la arquitectura, no el conector |
| Aplicación móvil nativa (Android/iOS) | La interfaz web responsive cubre la tableta de planta |
| Captura automática desde PLC, sensores o IoT | El MVP captura manual, como lo indica el documento de ideas |
| Notificaciones por WhatsApp, SMS o correo | Fuera del flujo de valor del MVP |
| Firma electrónica, auditoría legal o cumplimiento normativo formal | No aplica a una demo académica |
| Multiplanta, multiempresa o multi-idioma | Una planta, un idioma (español de México) |
| Facturación, nómina o pago real de bonos | El sistema identifica elegibles; el pago se hace fuera |
| Pronóstico con Machine Learning o analítica predictiva | El MVP es descriptivo y basado en reglas |
| App de escritorio, instalación en servidor de producción, alta disponibilidad | La demo corre localmente / en un servidor de desarrollo |
| Reportes exportables a PDF/Excel | No prioritario; se evalúa solo si sobra tiempo en el Sprint 5 |
| Rediseño de la identidad visual | Martha define la línea gráfica una vez; no se itera sobre ella |

---

## 7. Usuarios y roles

| Rol | Quién es | Qué necesita de SG-Prod | Módulos que usa |
| :---- | :---- | :---- | :---- |
| **Operador** | Personal de piso | Saber su estación asignada; que su cero defectos se reconozca | Consulta de asignación |
| **Jefe de Línea** | Responsable del turno | Asignar bien, capturar rápido, ver alertas de su línea | Módulo 1 y Módulo 2 |
| **Gerente de Producción** | Dirección | FPY, tendencias, quién bonifica, dónde capacitar | Módulo 3 |

**Decisión de alcance:** el MVP construye las **tres** vistas porque sin la tercera no hay producto gerencial, y sin la segunda no hay datos.

---

## 8. Flujo de valor de extremo a extremo

Este es el recorrido que se demostrará en la presentación final. Si cualquier paso falla, el MVP no está terminado.

```
1. El Gerente entra y ve el tablero del turno anterior (semáforos).
2. El Jefe de Línea inicia sesión (QR + PIN o manual).
3. Asigna personal a las estaciones de su línea; el sistema sugiere y alerta
   cuando coloca un Principiante.
4. Durante el turno, cada 60 minutos registra piezas por estación
   (Aceptadas / Rechazadas / Retrabajo) y el motivo del defecto.
5. El dashboard se actualiza: FPY, Piezas/Hora vs Meta, Tasa de Defectos.
6. Al cierre del periodo, el Gerente ve los operadores con cero defectos
   (elegibles a bono) y las estaciones que requieren capacitación.
```

---

## 9. Criterios de éxito

### 9.1 Éxito del producto (Sprint 5)

El MVP se considera terminado cuando, **en una sola sesión en vivo**, se ejecutan completos los pasos del flujo de la sección 8 usando los datos del caso de estudio, sin intervención manual en la base de datos ni en el código.

### 9.2 Éxito del proceso (cómo sabremos que vamos bien)

| Señal | Meta |
| :---- | :---- |
| Cada integrante tiene ambiente funcional | Fin de la Semana 1 |
| Entregable terminado por sprint | 5 de 5 |
| Historias de usuario completadas | ≥ 85 % del compromiso de cada sprint |
| Pruebas automatizadas del cálculo de KPIs | Cobertura del 100 % de las fórmulas (FPY, defectos, bonos) |
| Demo funcional en cada cierre de sprint | 5 de 5 |

> El cálculo de KPIs es la única parte con cobertura de pruebas exigida al 100 %: un FPY mal calculado invalida las decisiones de bonos y capacitación, y es el error más caro del proyecto.

---

## 10. Supuestos

1. Hay conectividad Wi-Fi disponible en el área de demostración.
2. Los datos del caso de estudio (empleados, estaciones, catálogo de defectos, metas de producción) los entrega el equipo de gestión de información a más tardar en la Semana 3.
3. Cada integrante cuenta con una computadora capaz de ejecutar Docker.
4. El periodo de evaluación de bonos y el umbral de los semáforos de calidad son **reglas de negocio configurables**, no constantes en el código.
5. El sistema se demuestra con datos reales o realistas, no con datos de relleno.

---

## 11. Riesgos y respuesta

| Riesgo | Impacto | Probabilidad | Respuesta |
| :---- | :---- | :---- | :---- |
| Reglas de bonos y semáforos no definidas a tiempo | Alto: el Módulo 3 no se puede calcular | Media | Definirlas en el Sprint 1 y registrarlas en `docs/`; tratarlas como configuración |
| Un integrante sin ambiente en la Semana 1 | Alto: pierde 10 % del calendario | Media | Parejas de instalación; *padrinos* en Windows y Linux |
| Modelo de datos inestable después del Sprint 2 | Alto: retrabajo en los tres módulos | Media | Congelar el MER al final del Sprint 1; dueña única de migraciones |
| El alcance crece con ideas nuevas a mitad del sprint | Alto: ningún sprint cierra | Alta | Este documento: lo nuevo va al backlog futuro |
| Interfaz de captura lenta en tableta | Medio: el Jefe de Línea abandona la captura | Media | Medir < 1.5 s desde el Sprint 3; mínimo 56 px por control |
| Datos insuficientes para la demo | Medio: el dashboard se ve vacío | Media | *Seeding* reproducible desde el Sprint 2 (Karen) |
| Una sola persona concentra conocimiento crítico | Alto: se bloquea el sprint | Media | Revisiones cruzadas obligatorias en cada PR |

---

## 12. Restricciones

| Tipo | Restricción |
| :---- | :---- |
| Tiempo | 10 semanas, 5 sprints de 2 semanas. No hay prórroga |
| Equipo | 7 personas, perfiles junior. Nadie trabaja de tiempo completo |
| Tecnología | Python 3.12, Django 5.2 LTS, PostgreSQL 16 en Docker, virtualenv + pip. Sin frameworks de frontend |
| Infraestructura | PostgreSQL local por persona. Sin servidor compartido, sin nube |
| Proceso | Una historia de usuario = una rama = un dueño. PR con revisión obligatoria |

---

## 13. Asignación de módulos

| Módulo / Frente | Responsable | Apoyo |
| :---- | :---- | :---- |
| Gestión de información, acuerdos y reglas de negocio | Rocío | — |
| Diseño UI/UX e identidad visual | Martha | Eduardo |
| Base de datos, MER y migraciones | Ángela | Karen |
| Módulo 1: Matriz de habilidades y asignación | Mariana | Martha (funcional), Eduardo |
| Módulo 2: Control de calidad y captura horaria | Iván | Eduardo (funcional) |
| Módulo 3: Dashboard, KPIs y bonos | Distribuido entre los tres frentes | Todos |
| Autenticación QR/PIN (RNF-1) | Iván | Ángela |

> El Módulo 3 no tiene un dueño único porque depende de datos que producen los Módulos 1 y 2. Su cierre es responsabilidad colectiva y se revisa en cada cierre de sprint desde el Sprint 4.

---

## 14. Mapa de sprints y alcance por sprint

| Sprint | Semanas | Alcance que se cierra | Criterio de salida |
| :---- | :---- | :---- | :---- |
| **Sprint 1** | 1–2 | Entorno, repositorio, MER, modelos base, login QR/PIN, Admin configurado | Los 7 con ambiente; `migrate` limpio desde `db-data/` vacía; login funcional |
| **Sprint 2** | 3–4 | RF-1.1 a RF-1.4 completos | Pantalla de asignación funcionando con motor de sugerencia y alerta por Principiante |
| **Sprint 3** | 5–6 | RF-2.1, RF-2.2 y RNF-2 | Un Jefe de Línea registra una hora completa en < 3 min, con motivo de defecto obligatorio |
| **Sprint 4** | 7–8 | RF-3.1, RF-3.2, RF-3.3, RF-3.4 | KPIs calculados con cobertura de pruebas al 100 %; tablero y reportes operativos |
| **Sprint 5** | 9–10 | Hardening, datos reales, pruebas E2E, presentación | Flujo completo de la sección 8 demostrado en vivo sin ayuda manual |

**Compromiso de alcance:** lo que no cierre en su sprint **no se arrastra indefinidamente**. Se decide en el cierre: se recorta, se simplifica o se mueve al backlog futuro. Nunca se extiende el alcance de un sprint en curso.

---

## 15. Criterios de aceptación por módulo

| Módulo | Aceptación |
| :---- | :---- |
| **Módulo 1** | Dado un empleado con nivel Principiante en una estación, cuando el Jefe de Línea lo asigna, entonces el sistema muestra alerta visual y sugiere un Instructor de apoyo |
| **Módulo 2** | Dado un registro de piezas con Rechazadas > 0, cuando el Jefe de Línea intenta guardar sin seleccionar motivo, entonces el sistema lo impide con mensaje claro |
| **Módulo 3** | Dado un juego de registros conocido, cuando se consulta el FPY, entonces el valor coincide exactamente con el cálculo manual |
| **Módulo 3 (bonos)** | Dado un operador sin defectos en el periodo, cuando el Gerente consulta elegibles, entonces aparece en la lista; si tiene un solo defecto, no aparece |
| **RNF-1** | Dado un empleado activo, cuando escanea su QR e ingresa PIN correcto, entonces accede; con PIN incorrecto, no accede |
| **RNF-4** | Dado el tablero gerencial, cuando el Gerente busca cualquier KPI, entonces lo alcanza en máximo 2 clics |

---

## 16. Glosario

| Término | Significado |
| :---- | :---- |
| **FPY** (First Pass Yield) | Porcentaje de piezas que pasan correctamente en el primer intento: `Aceptadas / (Aceptadas + Rechazadas + Retrabajo)` |
| **Retrabajo** | Pieza que no pasó en el primer intento pero puede corregirse |
| **Scrap** | Pieza perdida de forma irrecuperable |
| **Skill Matrix** | Matriz que relaciona cada empleado con su nivel de competencia en cada estación |
| **At-a-Glance** | Tablero de lectura inmediata con semáforos, sin tablas crudas |
| **Seeding** | Datos de prueba reproducibles que se cargan con un comando |
| **MER** | Modelo Entidad-Relación |
| **Hook** | Punto de extensión en el código, preparado para una funcionalidad futura sin implementarla hoy |

---

## 17. Autorización y control de cambios

1. Este documento se considera **congelado** al cierre del Sprint 1.
2. Un cambio de alcance requiere: justificación escrita, impacto en el cronograma, y aprobación del equipo.
3. Todo cambio aprobado se refleja en este archivo con fecha y responsable. Sin registro aquí, el cambio no existe.
4. El alcance nunca se amplía para "aprovechar" que alguien terminó temprano: ese tiempo se usa en pruebas y en pulir lo ya comprometido.
