# Datos Iniciales del Sistema PrestaLab

## Usuarios

```json
{
  "uuid": "USR001",
  "nombre": "Omar",
  "apellido_paterno": "Lopez",
  "apellido_materno": "Perez",
  "matricula": 2100,
  "correo": "anfa@correo.com",
  "telefono": "2221234567"
}
```

---

## Categorías

```json
{
  "uuid": "CAT001",
  "nombre": "Camaras",
  "descripcion": "Equipo fotografico y audiovisual"
}
```

---

## Materiales

```json
{
  "uuid": "MAT001",
  "nombre": "Camara Nikon",
  "activo": true,
  "categoria_id": "CAT001"
}
```

---

## Préstamos

```json
{
  "uuid": "PRE001",
  "usuario_id": "USR001",
  "material_id": "MAT001",
  "fecha_inicio": "2026-09-12",
  "fecha_devolucion": "2026-09-19",
  "estado_material": "completo"
}
```

---

## Administradores

```json
{
  "uuid": "ADM001",
  "nombre": "Administrador",
  "correo": "admin@prestalab.com",
  "contrasena": "123456"
}
```

---

## Relación entre Entidades

### Usuario

Un usuario puede realizar uno o varios préstamos.

### Categoría

Una categoría puede contener varios materiales.

### Material

Un material pertenece a una categoría y puede estar asociado a varios préstamos a lo largo del tiempo.

### Préstamo

Relaciona un usuario con un material durante un período determinado.

### Administrador

Gestiona usuarios, materiales, categorías y préstamos dentro del sistema.

---

## Resumen del Modelo

- 1 Usuario → N Préstamos
- 1 Categoría → N Materiales
- 1 Material → N Préstamos
- Administrador → Gestión del sistema
