# Arquitectura

Documento vivo: se actualiza en cada paso. Lo marcado como *(previsto)* aún no existe en el código.

## Piezas

| Pieza | Tecnología | Dónde está | Estado |
|---|---|---|---|
| Base de datos | PostgreSQL 18 | `docker-compose.yml` (servicio `db`) | Funciona en local |
| API | Python + FastAPI | `backend/` | *(previsto, paso 2)* |
| PWA | React + TypeScript | `frontend/` | *(previsto, paso 7)* |
| Servidor publicado | Por decidir (UE) | — | *(previsto, paso 8)* |

## Cómo se comunican

```
Móvil / navegador ──HTTPS──▶ API FastAPI ──SQL──▶ PostgreSQL
   (PWA React)                (backend/)
   IndexedDB sin conexión
```

- La PWA solo habla con la API; nunca con la base de datos.
- La API es la única que accede a PostgreSQL.
- *(previsto)* La sesión viaja en una cookie `httpOnly`.

## Modelo de datos

*(previsto, paso 4)* Reglas que cumplirán todas las tablas:

- Llevan `empresa_id`.
- Horas en UTC (`timestamptz`); se muestran en hora de España.
- Importes en `numeric`, nunca coma flotante.
- Las bajas desactivan (`activo = false`), no borran.
- Cada cambio queda en `historial_cambios` mediante un trigger de PostgreSQL.

## Recorrido de un marcaje

*(previsto, fase 1)*

## Configuración y secretos

- Los valores de configuración están en `.env` (no se sube al repositorio).
- `.env.example` es la plantilla con todas las variables necesarias y valores de ejemplo.
