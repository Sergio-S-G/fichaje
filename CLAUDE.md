# Control de fichajes y costes

App móvil y web (PWA) para que un responsable registre las horas de cada trabajador de una pequeña empresa de construcción (unos 4 trabajadores) y el administrador conozca el coste real de personal: por hora, por trabajador, por proyecto y por mes.

## Cómo trabajar conmigo

- Responde en español, directo y conciso.
- Avanza paso a paso: un paso por respuesta y espera mi confirmación.
- Señala los desacuerdos. No repitas avisos ya dados.
- Antes de escribir código en una tarea nueva, propón un plan breve y espera mi visto bueno.
- No amplíes el alcance: lo que no esté en "Alcance" no se construye.
- Commits pequeños, con mensaje en español, uno por cambio coherente.

## Reparto del trabajo

- **Todo el código lo escribes tú** (backend, frontend, despliegue y configuración). Quiero aprender a trabajar con agentes.
- **Yo tengo que entender cómo funciona la app** para que no se me vaya de las manos. Para eso:
  - Cambios pequeños: una sola cosa por paso, que pueda revisar de una vez.
  - Al terminar cada paso, resume qué archivos has tocado, qué hace cada cambio y por qué, y cómo probarlo yo mismo.
  - Explica las decisiones de diseño antes de aplicarlas y dame alternativas cuando las haya.
  - Mantén `docs/arquitectura.md` al día: qué piezas tiene la app, cómo se comunican, el modelo de datos y el recorrido de un marcaje de principio a fin.
  - No añadas dependencias, servicios ni carpetas nuevas sin explicármelo y esperar mi visto bueno.
  - Si te pregunto cómo funciona algo, explícalo sobre el código real, indicando archivo y función.

## Tecnología

- **Frontend:** una sola PWA con React (móvil y web).
- **Backend:** Python con FastAPI.
- **Base de datos:** PostgreSQL.
- **Sin conexión:** IndexedDB en el móvil (Android).
- **Alojamiento:** de pago, servidores en la UE, copias de seguridad automáticas.

## Estructura del repositorio

```
backend/    API FastAPI
frontend/   PWA React
docs/       documentación del proyecto
```

## Comandos

Pendiente: añadir aquí los comandos de arranque, pruebas y migraciones cuando existan.

## Alcance de la primera versión

### Roles

- **Responsable:** marca las horas desde el móvil y cambia a los trabajadores de proyecto. No ve sueldos ni costes.
- **Administrador:** gestiona los datos y ve los costes desde la web. Da de alta y de baja a usuarios y trabajadores, y también puede cambiar a los trabajadores de proyecto.
- **Altas y bajas:** dar de baja desactiva, no borra: el usuario o trabajador deja de aparecer para marcar, pero sus horas, costes e historial se conservan.

### Marcado

- **Dos formas de marcar, las dos disponibles siempre y al mismo nivel:**
  - **Por grupo:** un toque marca la entrada o la salida de todo el equipo.
  - **Individual:** se marca la entrada o la salida de un trabajador concreto.
- **Tramos:** cada tramo tiene trabajador, proyecto, entrada y salida. Un cambio de proyecto cierra un tramo y abre otro. La jornada es la suma de los tramos del día. Cada trabajador puede estar en un proyecto distinto.
- **Registro flexible:** se pueden añadir o corregir horas a posteriori, sin bloqueos. Se guarda la hora de trabajo, cuándo se apuntó y quién lo hizo. La app avisa si falta una salida.
- Sin geolocalización ni segundo responsable.

### Sin conexión

- El marcado funciona sin cobertura y se sincroniza al recuperarla.
- La sincronización se hace con la app abierta, sin depender del segundo plano.
- La app muestra cuántos marcajes quedan pendientes de enviar.
- Cada marcaje lleva un identificador generado en el móvil, para que reenviarlo no lo duplique.
- Se guarda la hora del marcaje en el móvil y la de llegada al servidor.

### Fuera de alcance (no construir)

- Gastos generales de la empresa y gastos de proyecto.
- Ingresos, beneficios y rentabilidad.
- Varias empresas con registro y login por empresa.
- Uso como registro oficial de jornada.

## Cálculo de costes

- Coste del mes = (horas trabajadas × tarifa del trabajador) + otros gastos del trabajador en ese mes.
- Coste por hora = coste del mes ÷ horas trabajadas en el mes.
- Coste de un trabajador en un proyecto = horas en ese proyecto × su coste por hora de ese mes.

Ejemplo de comprobación (tarifa 12 €/h, 800 € de otros gastos):

| Mes | Horas | Salario | Otros gastos | Total | Coste por hora |
|---|---|---|---|---|---|
| Enero | 160 | 1.920 € | 800 € | 2.720 € | 17,00 € |
| Febrero | 150 | 1.800 € | 800 € | 2.600 € | 17,33 € |

### Reglas

- Cada trabajador tiene una única tarifa: sin precios distintos para horas extra, festivos o fines de semana.
- La tarifa funciona por periodos: un cambio no altera los meses anteriores.
- **Gastos recurrentes:** se aplican desde un mes de inicio hasta que se les pone fin.
- **Gastos puntuales:** se aplican solo a un mes concreto.
- **Quitar un gasto recurrente:** se finaliza; los meses anteriores no cambian. El borrado real solo sirve para corregir errores.
- **Cambiar un importe:** se finaliza el anterior y se crea uno nuevo.
- **Sin retroactividad:** los gastos de un mes no afectan a otros meses.
- Vacaciones, bajas, festivos, días de lluvia y pagas extra se añaden como gasto del mes, no como horas trabajadas.
- Sin prorrateo por altas o bajas a mitad de mes.
- La cifra es mensual; no hay media de 12 meses.

## Historial de cambios

- Los meses no se cierran: si se corrigen datos de un mes pasado, sus cifras se recalculan.
- Se guardan todos los cambios desde el primer día (horas, tarifas y gastos): qué cambió, valor anterior y nuevo, quién y cuándo.
- Cada mes terminado muestra los cambios hechos después de su fin.

## Reglas técnicas obligatorias

- Todas las tablas llevan `empresa_id` desde el principio, aunque solo haya una empresa.
- Todos los datos se guardan en la base de datos: sin valores fijos en el código.
- El historial de cambios existe desde la fase 0.
- Importes con tipo decimal, nunca coma flotante.
- Horas guardadas en UTC y mostradas en hora de España.
- No usar datos reales de personas hasta que se revisen las obligaciones del RGPD.
- Secretos y contraseñas fuera del repositorio (variables de entorno).

## Fases

Se trabaja una fase cada vez. No se empieza la siguiente hasta cumplir el criterio de la actual.

| Fase | Qué se hace | Terminada cuando |
|---|---|---|
| 0. Base | Repositorio, backend, base de datos, PWA, despliegue, login con roles, `empresa_id` e historial de cambios | Ambos roles entran en la app publicada |
| 1. Prueba sin conexión | Marcar y sincronizar con datos mínimos | Funciona en el Android real, sin duplicados, tras un tiempo sin cobertura |
| 2. Datos base | Usuarios, trabajadores (altas y bajas), proyectos y tarifas por periodos | El administrador los gestiona desde la web |
| 3. Marcado completo | Marcado por grupo e individual, cambios de proyecto, correcciones y avisos | El responsable registra un día completo, incluso con el equipo dividido |
| 4. Costes | Gastos recurrentes y puntuales, y cálculo mensual | El resultado coincide con el ejemplo de comprobación |
| 5. Informes | Coste por hora, trabajador, proyecto y mes, e historial de meses terminados | El administrador obtiene los datos sin ayuda |
| 6. Piloto | Un mes de uso real, comparando con las nóminas | Las diferencias están localizadas y explicadas |

**Fase actual: 0 (sin empezar).** Actualiza esta línea al cambiar de fase.
