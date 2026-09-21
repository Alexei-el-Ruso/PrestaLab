# Decisión de Motor: PostgreSQL

**Equipo 0 · Sesión 8**  
*Compara: modelo relacional vs. modelo de documentos · Se consume en: [[analisis-diseno]]*




> Esta nota documenta la decisión de arquitectura de persistencia para el sistema **PrestaLab**. Se conecta con:
> * **Documento Integrador:** [[analisis-diseno]]
> * **Modelo Relacional:** [[modelo-datos]]
> * **Modelo en Documentos:** [[modelo-documentos]]


## 1. Qué se decide

Qué motor guarda los datos del sistema de préstamos de **PrestaLab**: **PostgreSQL** (relacional, documentado en [[modelo-datos]]) o **Firestore / MongoDB** (documentos, documentado en [[modelo-documentos]]).

---

## 2. Criterios de decisión

| Criterio | Peso | Por qué |
| :--- | :--- | :--- |
| **Garantizar el RNF-4 sin código** | **Alto** | Es la regla que más duele si falla: dos alumnos con el mismo equipo asignado al mismo tiempo. |
| **Consultas por equipo y por persona** | **Medio** | Las dos direcciones de lectura se usan a diario (saber qué tiene un alumno y quién tiene un equipo). |
| **Volumen esperado** | **Bajo** | Cientos de préstamos por semestre; cualquier motor soporta la carga sin sufrir. |
| **Lo que el equipo sabe operar** | **Medio** | Desarrollo acotado al tiempo del semestre académico. |


## 3. Decisión

**PostgreSQL.**



## 4. Razón principal

El índice único parcial:

```sql
CREATE UNIQUE INDEX equipo_en_un_solo_prestamo_abierto
ON prestamo_equipo (equipo_id)
WHERE activo;
