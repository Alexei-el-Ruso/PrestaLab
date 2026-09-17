# Resumen del Proyecto — Sistema de Control de Materiales

## 1. Problema y Objetivo

### Problema Actual

Actualmente, el control de materiales de laboratorio se realiza mediante registros manuales, lo que genera problemas de seguimiento, pérdida de equipos y posibles robos debido a la falta de trazabilidad.

### Objetivo

Desarrollar un sistema digital que automatice la gestión de préstamos de materiales, elimine la dependencia de registros en papel y permita mantener un control preciso del inventario en todo momento.

---

## 2. Alcance y Usuarios

### Usuarios Objetivo

- Alumnos
- Encargados de laboratorio
- Personal administrativo

### Alcance Inicial

La primera versión del sistema estará orientada a la gestión de préstamos en un único laboratorio.

### Escalabilidad

El sistema deberá diseñarse de forma modular para facilitar su futura expansión a otros laboratorios, departamentos o facultades de la institución.

### Concurrencia

La cantidad máxima de usuarios simultáneos aún no ha sido definida y deberá determinarse durante las siguientes etapas del proyecto.

---

## 3. Modalidad de Uso e Implementación

### Plataforma

El sistema estará disponible como una aplicación web responsiva, accesible desde:

- Computadoras
- Teléfonos móviles
- Tabletas

### Conectividad

El sistema funcionará en línea y permitirá la actualización de información en tiempo real para todos los usuarios.

### Accesibilidad

Los requisitos de accesibilidad serán definidos posteriormente conforme a las necesidades institucionales y a los estándares aplicables.

---

## 4. Funcionalidades Principales

### Registro de Información

#### Captura de Evidencia

Al momento del préstamo, el sistema permitirá:

- Capturar una imagen del estado físico del material.
- Consultar posteriormente la evidencia almacenada.

#### Gestión Digital

El sistema automatizará el registro de:

- Usuarios
- Préstamos
- Devoluciones

### Reportes y Salidas

#### Comprobantes de Préstamo

El sistema generará comprobantes digitales que servirán como evidencia de la transacción realizada.

#### Reportes de Inventario

Se podrán consultar reportes relacionados con:

- Estado de los materiales
- Historial de movimientos
- Trazabilidad de inventario
- Disponibilidad de recursos

---

## 5. Aspectos Pendientes por Definir

### Información de los Materiales

Se deberá establecer el conjunto definitivo de atributos para cada material, incluyendo:

- Código QR o código de barras
- Número de serie
- Categoría
- Estado físico
- Disponibilidad
- Evidencias fotográficas
- Otros atributos relevantes

### Capacidad del Sistema

Se deberá determinar la cantidad esperada de usuarios concurrentes para dimensionar adecuadamente la infraestructura tecnológica.

### Accesibilidad

Será necesario evaluar el cumplimiento de estándares de accesibilidad, como WCAG, así como otros requerimientos institucionales.

---

## 6. Restricciones y Consideraciones

- El sistema estará enfocado exclusivamente en la gestión de préstamos de materiales.
- Los procesos administrativos externos al préstamo quedan fuera del alcance de esta versión.
- La solución deberá mantener la integridad y trazabilidad de toda la información registrada.

---

# Resumen Ejecutivo

PrestaLab es un sistema orientado a la gestión y control de préstamos de materiales de laboratorio. Su objetivo es sustituir los registros manuales por un proceso digital que permita conocer en tiempo real la disponibilidad de los recursos, registrar préstamos y devoluciones, almacenar evidencia fotográfica y generar reportes administrativos.

La solución estará disponible mediante una aplicación web responsiva, accesible desde distintos dispositivos, y deberá diseñarse bajo principios de escalabilidad para permitir futuras ampliaciones dentro de la institución.