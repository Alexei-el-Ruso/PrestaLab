# Modelo de Datos y Reglas de Negocio — PrestaLab

## Descripción General

El sistema administra el préstamo de equipos y materiales a estudiantes. Existen dos tipos de usuarios con responsabilidades diferenciadas:

- **Estudiante:** solicita préstamos y consulta únicamente sus propios registros.
- **Administrador:** valida solicitudes, ajusta condiciones del préstamo y tiene acceso a todos los préstamos del sistema.

---

# Entidades

## Usuario

Entidad base para las personas que utilizan el sistema.

| Campo | Tipo | Descripción |
|---------|---------|---------|
| id | serial | Identificador único |
| nombre | varchar | Nombre(s) |
| apellido_paterno | varchar | Primer apellido |
| apellido_materno | varchar | Segundo apellido |
| correo | varchar | Correo institucional |
| telefono | varchar | Número de contacto |

### Especializaciones

#### Estudiante

| Campo | Tipo | Descripción |
|---------|---------|---------|
| id | FK | Referencia a Usuario |
| matricula | varchar | Matrícula institucional |

#### Administrador

| Campo | Tipo | Descripción |
|---------|---------|---------|
| id | FK | Referencia a Usuario |
| nivel_acceso | int | Nivel o perfil administrativo |

---

## CategoríaMaterial

Clasifica los diferentes tipos de materiales o equipos.

| Campo | Tipo |
|---------|---------|
| id | serial |
| nombre | varchar |
| descripcion | varchar |

---

## Material

Representa cada equipo o recurso disponible para préstamo.

| Campo | Tipo |
|---------|---------|
| id | serial |
| nombre | varchar |
| activo | boolean |
| categoria_id | FK |

### Relaciones

- Un material pertenece a una categoría.
- Una categoría puede contener múltiples materiales.

---

## Prestamo

Representa la solicitud y autorización de equipos.

| Campo | Tipo |
|---------|---------|
| id | serial |
| fecha_solicitud | datetime |
| fecha_inicio | datetime |
| fecha_devolucion_programada | datetime |
| dias_solicitados | int |
| dias_autorizados | int |
| usuario_id | FK |
| administrador_id | FK |
| estado | varchar |

### Descripción de Fechas

- **dias_solicitados:** duración propuesta por el estudiante.
- **dias_autorizados:** duración final aprobada por el administrador.
- Ambas duraciones deben conservarse para fines de auditoría y seguimiento.

---

## DetallePrestamo

Entidad intermedia que permite asociar varios materiales a un mismo préstamo.

| Campo | Tipo |
|---------|---------|
| id | serial |
| prestamo_id | FK |
| material_id | FK |

### Propósito

Permite implementar la regla de negocio:

> Un préstamo puede incluir varios equipos o materiales.

---

## DanoMaterial

Registra daños detectados durante la devolución.

| Campo | Tipo |
|---------|---------|
| id | serial |
| material_id | FK |
| prestamo_id | FK |
| descripcion | text |
| fecha_registro | datetime |

### Importante

Los daños se registran sobre cada material específico involucrado, no sobre el préstamo completo.

---

# Cardinalidades

## Usuario - Préstamo

| Relación | Cardinalidad |
|------------|------------|
| Usuario realiza Préstamo | 1 : N |
| Préstamo pertenece a Usuario | N : 1 |

### Interpretación

- Un usuario puede realizar muchos préstamos.
- Cada préstamo pertenece a un solo usuario.

---

## Administrador - Préstamo

| Relación | Cardinalidad |
|------------|------------|
| Administrador gestiona Préstamo | 1 : N |
| Préstamo es aprobado por Administrador | N : 1 |

### Interpretación

- Un administrador puede gestionar múltiples préstamos.
- Cada préstamo queda asociado a un administrador responsable.

---

## CategoríaMaterial - Material

| Relación | Cardinalidad |
|------------|------------|
| Categoría contiene Material | 1 : N |
| Material pertenece a Categoría | N : 1 |

---

## Préstamo - Material

| Relación | Cardinalidad |
|------------|------------|
| Préstamo contiene Material | N : M |
| Material participa en Préstamos | N : M |

### Implementación

La relación se implementa mediante:

- **DetallePrestamo**

---

## Material - DanoMaterial

| Relación | Cardinalidad |
|------------|------------|
| Material registra daños | 1 : N |
| Daño pertenece a Material | N : 1 |

---

## Préstamo - DanoMaterial

| Relación | Cardinalidad |
|------------|------------|
| Préstamo puede generar daños | 1 : N |
| Daño está asociado a un préstamo | N : 1 |

---

# Reglas de Negocio

## Gestión de Préstamos

1. Un préstamo puede incluir uno o varios materiales.
2. La devolución se realiza de forma integral.
3. No se permiten devoluciones parciales de los artículos de un préstamo.
4. El estudiante propone una duración inicial del préstamo.
5. El administrador puede modificar la duración solicitada antes de aprobarla.
6. Deben almacenarse tanto los días solicitados como los días finalmente autorizados.

---

## Gestión de Daños

7. Los daños se registran individualmente por material.
8. Un mismo préstamo puede generar múltiples registros de daño si varios materiales presentan incidencias.

---

## Control de Acceso

### Estudiante

**Puede:**

- Solicitar préstamos.
- Consultar sus préstamos.
- Consultar el estado de sus solicitudes.

**No puede:**

- Ver préstamos de otros usuarios.
- Autorizar préstamos.
- Modificar inventario.

### Administrador

**Puede:**

- Consultar todos los préstamos.
- Aprobar o rechazar solicitudes.
- Ajustar la duración autorizada.
- Registrar devoluciones.
- Registrar daños.
- Gestionar materiales y categorías.

---

## Criterio de Autorización

9. No existe un proceso formal ni reglas automáticas de aprobación.
10. La autorización de cada préstamo queda a discreción del administrador responsable y se evalúa caso por caso.

---

## Alcance del Sistema

11. Los materiales no se relacionan con materias, asignaturas o cursos académicos.
12. Esta asociación fue considerada durante el levantamiento de requisitos, pero fue descartada por el cliente debido a que no aporta valor al proceso de préstamo.

---

# Observaciones de Diseño

### Relación N:M

La relación entre **Préstamo** y **Material** es de muchos a muchos (N:M), por lo que se implementa mediante la entidad **DetallePrestamo**.

### Control de Daños

Los daños se registran por artículo específico y no por préstamo, permitiendo identificar con precisión qué material presentó incidencias.

### Auditoría

El sistema conserva tanto los días solicitados como los días autorizados para mantener trazabilidad sobre las decisiones tomadas por los administradores.

### Responsabilidad Administrativa

Cada préstamo queda asociado a un administrador responsable mediante `administrador_id`, permitiendo conocer quién autorizó la operación.