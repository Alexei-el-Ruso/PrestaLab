# 🧪 Sistema de Gestión de Préstamos — Laboratorio

> **Resumen ejecutivo:** Arquitectura ligera, modelo relacional optimizado y trazabilidad estricta para eliminar la libreta del mostrador sin sobreingeniería.

---

## 1. El sistema en una página

| Dimensión | Detalle |
| :--- | :--- |
| **Problema** | Préstamo de equipo (multímetros, osciloscopios, kits, cámaras) en libreta. Nadie sabe en el acto qué está prestado, quién tiene piezas dañadas, ni detecta retrasos a tiempo. |
| **Usuario principal** | **Administrador** (registra, autoriza, recibe). **Prestatarios** (alumnos/profesores en igualdad de rol: consultan y solicitan). |
| **Fuera de alcance** | • Reservas anticipadas (todo es en mostrador con el equipo enfrente)<br>• Sanciones automáticas por retraso (criterio humano opcional)<br>• Inventario de consumibles (cables, resistencias)<br>• Conexión con sistema escolar (carga manual) |

---

## 2. Backlog priorizado

> Backlog completo de referencia: `docs/backlog.md`

| Prioridad | # | Historia | Criterios de aceptación |
| :---: | :---: | :--- | :---: |
| **Must** | #03 | Registrar un préstamo de uno o varios equipos | 3 |
| **Must** | #05 | Consultar qué está prestado ahora mismo | 2 |
| **Must** | #07 | Registrar la devolución completa de un préstamo | 2 |
| **Must** | #08 | Reservar un equipo con anticipación *(Descartado/Revisar)* | 2 |
| **Must** | #12 | Registrar daño de un equipo al devolverlo | 1 |
| **Should** | #14 | Ver el historial de préstamos de una persona | 2 |
| **Should** | #16 | Ajustar la duración que propuso el prestatario | 1 |
| **Could** | #19 | Ver quién devuelve tarde con frecuencia | 1 |
| **Won't** | #21 | Bloquear automáticamente a quien acumule retrasos | — |

> **Nota sobre Won't (#21):** Excluido por instrucción explícita del cliente. No existe regla formal de bloqueo automático; prima el criterio operativo del administrador.

### Requerimientos no funcionales (RNF)
* **RNF-1:** La consulta de *qué está prestado ahora* responde en **< 2 s** con **500 préstamos activos** *(Verificación: base sembrada con 500 registros)*.
* **RNF-2:** Un prestatario solo lee sus propios préstamos; el administrador ve todos *(Verificación: prueba cruce de cuentas)*.
* **RNF-3:** Registrar un préstamo de **3 equipos** toma **≤ 6 interacciones** *(Verificación: conteo de clics/flujo)*.
* **RNF-4:** Un equipo no puede estar en dos préstamos activos simultáneos *(Verificación: rechazo por índice único en BD)*.

---

## 3. Modelo de datos y motor

* **Forma:** Relacional | **Motor:** PostgreSQL

| Entidad | Campos principales |
| :--- | :--- |
| **`persona`** | `id`, `nombre`, `matricula`, `tipo` (alumno / profesor), `correo` |
| **`equipo`** | `id`, `etiqueta`, `nombre`, `categoria_id`, `estado` *(eliminado por redundancia)* |
| **`prestamo`** | `id`, `persona_id`, `admin_id`, `fecha_salida`, `dias_propuestos`, `dias_autorizados`, `fecha_compromiso`, `fecha_cierre` |
| **`prestamo_equipo`** | `prestamo_id`, `equipo_id`, `activo`, `dano`, `nota_dano` |

> ⚠️ **Desnormalización deliberada:** `prestamo_equipo.activo` es booleano redundante de `prestamo.fecha_cierre`, habilitando el índice único parcial `UNIQUE (equipo_id) WHERE activo` para garantizar **RNF-4** a nivel de motor.
> 
> 🔒 **Regla no garantizada por DB:** *El préstamo cierra completo (sin devoluciones parciales)*. Se valida por transacción en capa de aplicación.

---

## 4. Matriz de trazabilidad

| Historia | Criterio de aceptación | Entidad.campo | Operación | RNF | Hueco |
| :---: | :--- | :--- | :---: | :---: | :---: |
| **#03** | Equipo disponible al registrar -> fecha compromiso | `prestamo.fecha_salida/dias_aut/fecha_comp` | Escribe | — | — |
| **#03** | 3 equipos en un mismo registro | `prestamo_equipo.prestamo_id/equipo_id/activo` | Escribe | RNF-4 | — |
| **#03** | Rechazo si equipo ya está prestado | `prestamo_equipo.activo` | Lee | RNF-4 | — |
| **#05** | Inventario muestra no disponible en prestados | `prestamo_equipo.activo`, `equipo.etiqueta` | Lee | RNF-1 | — |
| **#05** | Prestatario acotado a sus propios préstamos | `prestamo.persona_id` | Lee | RNF-2 | — |
| **#07** | Devolución cierra préstamo completo | `prestamo.fecha_cierre`, `prestamo_equipo.activo` | Escribe | — | — |
| **#12** | Daño registrado por artículo específico | `prestamo_equipo.dano/nota_dano` | Escribe | — | — |
| **#14** | Historial por persona | `persona.*`, `prestamo.*` | Lee | — | — |
| **#16** | Registro dual de días propuestos vs. autorizados | `prestamo.dias_propuestos/dias_autorizados` | Escribe | — | — |
| **#19** | Ficha de retrasos frecuentes | `prestamo.fecha_devolucion_real` | Lee | — | — |

> *Cobertura activa:* 5 historias *Must*, 2 *Should*.

---

## 5. Huecos detectados y resolución

| Hueco / Tipo | Descripción | Acción tomada / Estado |
| :--- | :--- | :--- |
| **Hueco 1** <br>*(Tipo 2: Sin dueño)* | `equipo.estado` se leía en **#05**, pero ninguna historia lo escribía. | **Eliminado el campo.** La disponibilidad se deriva de `prestamo_equipo.activo`. *Commit a1f9c02 / Issue #31 cerrado.* |
| **Hueco 2** <br>*(Tipo 4: RNF ineficaz)* | RNF-3 redactado como "fácil de usar" (deseo no verificable). | **Reescrito** a métrica estricta (≤ 6 interacciones/3 equipos). *Issue #28.* |
| **Hueco 3** <br>*(Tipo 2: Sin dueño)* | `prestamo.admin_id` presente en el modelo sin historia que lo alimente. | **Abierto (`pregunta-cliente`).** Verificar si se requiere auditoría de autorización por admin. *Issue #33.* |

---

## 6. Inventario de pantallas

| Pantalla | Historias relacionadas | Estados de borde / Consideraciones |
| :--- | :---: | :--- |
| **Inventario ("qué está prestado")** | #05 | Lista vacía · Estado de equipo (disponible / prestado) |
| **Registro de mostrador** | #03, #16 | Error por equipo ya rentado · Lote multi-equipo |
| **Devolución** | #07, #12 | Con reporte de daño · Sin novedad |
| **Ficha de persona** | #14 | Historial vacío |
| **Control de acceso** | RNF-2 | Vista administrador vs. prestatario |

---

## 7. Decisiones abiertas

| Incógnita | Responsable | Bloqueo asociado |
| :--- | :---: | :--- |
| **Método de autenticación** (correo institucional vs. local) | Cliente | RNF-2 / Arranque MVP |
| **Auditoría de autorizador** (`prestamo.admin_id`) | Cliente | Cierre de Hueco 3 |
| **Administración de categorías** (`categoria_id` huérfano de CRUD) | Equipo + Cliente | Posible hueco tipo 2 |

---

## 8. Declaración de uso de IA generativa

| Herramienta | Solicitud | Resultado aceptado | Resultado rechazado y motivo |
| :--- | :--- | :--- | :--- |
| **Claude (Sonnet)** | Generar matriz de trazabilidad | Estructura de tabla y formato abreviado G/C/E | Inventó el campo ficticio `prestamo.fecha_devolucion_real` para cerrar #19. *Rechazado:* el hueco operativo es el hallazgo real, no un campo fantasma. |
| **Claude (Sonnet)** | Redacción de Sección 1 | Planteamiento de problema y contexto | Frases genéricas como "sistema robusto y escalable". *Rechazado:* carece de verificabilidad y sentido de entrevista. |

> **Nota metodológica:** La IA falló en la lectura retroactiva de dependencias de datos (propuso más historias para justificar campos innecesarios en lugar de recortar el modelo). El criterio humano validó la reducción de complejidad.