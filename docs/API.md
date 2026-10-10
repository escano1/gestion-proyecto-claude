# Contrato de API — Gestión de Proyectos y Nómina

Fuente de verdad compartida entre `gestion-proyecto-back` (NestJS) y `gestion-proyecto-frontend` (Next.js).
Cualquier cambio de forma en una respuesta debe reflejarse aquí.

## Convenciones

- Base: `{API_URL}/api` (local `http://localhost:3001/api`). Swagger: `/api/docs`.
- Autenticación: `Authorization: Bearer <accessToken>`. Todo requiere token salvo `POST /auth/login`, `POST /auth/refresh` y `GET /health`.
- Fechas de calendario: `"YYYY-MM-DD"`. Marcas de tiempo: ISO 8601 (`creadoEn`, `actualizadoEn`).
- Dinero: `number` en pesos colombianos (máx. 2 decimales). Porcentajes legales: fracción (`0.35` = 35 %).
- Errores (formato Nest): `{ statusCode, message: string | string[], error }`.
  - 400 validación · 401 sin token/expirado · 403 rol insuficiente · 404 no existe · 409 conflicto (duplicado, periodo cerrado, relación existente).
- Listas: arreglo plano, salvo las paginadas: `{ datos: T[], total, pagina, limite }` (query `pagina` ≥1, `limite` 1–200, por defecto 50).
- Roles: `ADMIN`, `GESTOR_PROYECTOS`, `NOMINA`, `CONSULTA`. `ADMIN` puede todo. "Lectura: todos" = cualquier usuario autenticado.

## Enumeraciones

| Tipo | Valores |
|---|---|
| Rol | `ADMIN`, `GESTOR_PROYECTOS`, `NOMINA`, `CONSULTA` |
| TipoDocumento | `CC`, `CE`, `PA`, `PPT`, `TI` |
| TipoSalario | `MENSUAL`, `POR_HORA` |
| TipoContrato | `INDEFINIDO`, `FIJO`, `OBRA_LABOR`, `APRENDIZAJE` |
| TipoJornada | `COMPLETA`, `PARCIAL` |
| EstadoTrabajador | `ACTIVO`, `INACTIVO` |
| EstadoProyecto | `ACTIVO`, `SUSPENDIDO`, `FINALIZADO` |
| CodigoTipoHora | `ORD`, `NOC`, `HED`, `HEN`, `DOM`, `DOMN`, `HEDD`, `HEDN` |
| EstadoLiquidacion | `BORRADOR`, `CERRADA` |
| TipoConcepto | `DEVENGADO`, `DEDUCCION` |
| AccionAuditoria | `CREAR`, `ACTUALIZAR`, `ELIMINAR`, `CALCULAR`, `CERRAR`, `REABRIR` |
| CodigoParametro | `SMMLV`, `AUXILIO_TRANSPORTE`, `UVT`, `JORNADA_MAXIMA_SEMANAL`, `RECARGO_NOCTURNO`, `RECARGO_EXTRA_DIURNA`, `RECARGO_EXTRA_NOCTURNA`, `RECARGO_DOMINICAL_FESTIVO`, `APORTE_SALUD_EMPLEADO`, `APORTE_PENSION_EMPLEADO`, `LIMITE_HORAS_EXTRA_DIA`, `LIMITE_HORAS_EXTRA_SEMANA` |

## Tipos compartidos

```ts
type Usuario = { id: number; nombre: string; email: string; rol: Rol; activo: boolean; creadoEn: string };

type Trabajador = {
  id: number; nombre: string; tipoDocumento: TipoDocumento; numeroDocumento: string; cargo: string;
  tipoSalario: TipoSalario; salarioBase: number;            // mensual o valor hora según tipoSalario
  tipoContrato: TipoContrato; tipoJornada: TipoJornada; horasSemanales: number | null;
  aplicaAuxilioTransporte: boolean;
  estado: EstadoTrabajador; email: string | null; telefono: string | null; eps: string | null; fondoPension: string | null;
  creadoEn: string; actualizadoEn: string;
};

type Proyecto = {
  id: number; codigo: string; nombre: string; cliente: string; ubicacion: string; descripcion: string | null;
  fechaInicio: string; fechaFinPlaneada: string | null; fechaFinReal: string | null;
  estado: EstadoProyecto; presupuesto: number | null; creadoEn: string; actualizadoEn: string;
};

type TrabajadorRef = { id: number; nombre: string; numeroDocumento: string; cargo: string };
type ProyectoRef = { id: number; codigo: string; nombre: string };
type UsuarioRef = { id: number; nombre: string };

type Asignacion = {
  id: number; trabajadorId: number; proyectoId: number; rolEnProyecto: string | null;
  trabajador: TrabajadorRef; proyecto: ProyectoRef; creadoEn: string;
};

type TipoHora = {
  id: number; codigo: CodigoTipoHora; nombre: string; descripcion: string | null;
  esExtra: boolean; esNocturna: boolean; esDominicalFestiva: boolean; orden: number; activo: boolean;
  recargo: number;                                            // fracción vigente en la fecha consultada
};

type RegistroHoras = {
  id: number; fecha: string; observacion: string | null;
  trabajador: { id: number; nombre: string; numeroDocumento: string }; proyecto: ProyectoRef;
  detalles: { tipoHoraId: number; codigo: CodigoTipoHora; nombre: string; horas: number }[];
  totalHoras: number; creadoPor: UsuarioRef; actualizadoPor: UsuarioRef | null; creadoEn: string; actualizadoEn: string;
};

type LiquidacionResumen = {
  id: number; anio: number;                                   // año del lunes de la semana liquidada
  fechaInicio: string; fechaFin: string;                      // lunes y domingo de la semana
  estado: EstadoLiquidacion; requiereRecalculo: boolean;
  totalDevengado: number; totalDeducciones: number; totalNeto: number; numeroTrabajadores: number;
  calculadaEn: string | null; cerradaEn: string | null; creadoEn: string;
};

type NominaTrabajadorResumen = {
  id: number; trabajadorId: number; nombreTrabajador: string; numeroDocumento: string; cargo: string;
  tipoSalario: TipoSalario; diasLaborados: number; ibc: number;
  totalDevengado: number; totalDeducciones: number; netoPagar: number; alertas: string[];
};

type Desprendible = NominaTrabajadorResumen & {
  tipoDocumento: TipoDocumento; salarioBase: number; valorHora: number;
  parametros: Record<string, number>;                         // valores legales aplicados
  liquidacion: { id: number; fechaInicio: string; fechaFin: string; estado: EstadoLiquidacion };
  conceptos: { tipo: TipoConcepto; codigo: string; descripcion: string; cantidad: number | null; valorUnitario: number | null; valor: number }[];
};

```

## Salud y autenticación

| Método | Ruta | Roles | Cuerpo / Query | Respuesta |
|---|---|---|---|---|
| GET | `/health` | público | — | `{ estado: 'ok', baseDatos: 'ok' }` |
| POST | `/auth/login` | público (máx. 10/min) | `{ email, password }` | `{ accessToken, refreshToken, usuario: Usuario }` |
| POST | `/auth/refresh` | público | `{ refreshToken }` | igual que login |
| GET | `/auth/me` | todos | — | `Usuario` |
| PATCH | `/auth/password` | todos | `{ passwordActual, passwordNueva }` (nueva ≥ 8, con letra y número) | 204 |

Access token: 15 min. Refresh: 7 días. Credenciales inválidas o usuario inactivo → 401 `"Credenciales inválidas"`.

## Usuarios (ADMIN)

| Método | Ruta | Cuerpo | Respuesta |
|---|---|---|---|
| GET | `/usuarios` | — | `Usuario[]` |
| POST | `/usuarios` | `{ nombre, email, password, rol }` | `Usuario` (409 email duplicado) |
| PATCH | `/usuarios/:id` | `{ nombre?, rol?, activo?, password? }` | `Usuario` (un admin no puede desactivarse ni quitarse el rol a sí mismo → 400) |

## Trabajadores (lectura: todos · escritura: ADMIN, NOMINA)

| Método | Ruta | Cuerpo / Query | Respuesta |
|---|---|---|---|
| GET | `/trabajadores` | `?estado=ACTIVO\|INACTIVO&q=texto` (nombre o documento) | `Trabajador[]` ordenados por nombre |
| GET | `/trabajadores/:id` | — | `Trabajador` |
| POST | `/trabajadores` | `CrearTrabajador` | `Trabajador` (409 documento duplicado) |
| PATCH | `/trabajadores/:id` | `Partial<CrearTrabajador>` | `Trabajador` |
| DELETE | `/trabajadores/:id` | — | 204 · elimina también sus horas, asignaciones y desprendibles de nómina; las liquidaciones afectadas recalculan sus totales |
| GET | `/trabajadores/:id/asignaciones` | — | `Asignacion[]` (proyectos en los que está asignado) |

`CrearTrabajador`: obligatorios `nombre, tipoDocumento, numeroDocumento, cargo, tipoSalario, salarioBase (>0), tipoContrato`; opcionales `tipoJornada` (def. `COMPLETA`), `horasSemanales` (obligatorio y 1–48 si `PARCIAL`; se ignora/limpia si `COMPLETA`), `estado` (def. `ACTIVO`; solo los `ACTIVO` entran en la nómina semanal, salvo que tengan horas esa semana), `email, telefono, eps, fondoPension`. `aplicaAuxilioTransporte` no se envía: el backend lo calcula y sobrescribe en cada creación/edición según el salario mensual equivalente (≤ 2 SMMLV vigente).
Cambios de salario/jornada/fechas marcan las liquidaciones `BORRADOR` con `requiereRecalculo = true`.

## Proyectos (lectura: todos · escritura: ADMIN, GESTOR_PROYECTOS)

| Método | Ruta | Cuerpo / Query | Respuesta |
|---|---|---|---|
| GET | `/proyectos` | `?estado=` | `Proyecto[]` ordenados por código |
| GET | `/proyectos/:id` | — | `Proyecto` |
| POST | `/proyectos` | obligatorios `codigo, nombre, cliente, ubicacion, fechaInicio`; opcionales `descripcion, fechaFinPlaneada, fechaFinReal, estado, presupuesto` | `Proyecto` (409 código duplicado) |
| PATCH | `/proyectos/:id` | parcial | `Proyecto` |
| DELETE | `/proyectos/:id` | — | 204 · elimina también sus horas registradas, asignaciones y materiales; las liquidaciones en borrador quedan con `requiereRecalculo` |

Fechas de fin ≥ fechaInicio (400).

## Asignaciones (lectura: todos · escritura: ADMIN, GESTOR_PROYECTOS)

| Método | Ruta | Cuerpo / Query | Respuesta |
|---|---|---|---|
| GET | `/asignaciones` | `?proyectoId&trabajadorId` | `Asignacion[]` |
| POST | `/asignaciones` | `{ trabajadorId, proyectoId, rolEnProyecto? }` | `Asignacion` · 409 si ya está asignado |
| PATCH | `/asignaciones/:id` | `{ rolEnProyecto? }` | `Asignacion` |
| DELETE | `/asignaciones/:id` | — | 204 · 409 si hay horas registradas del trabajador en el proyecto |

Una asignación por trabajador y proyecto (409 si se repite), sin fechas. Un trabajador puede estar en varios proyectos a la vez.

## Tipos de hora y parámetros legales

| Método | Ruta | Roles | Cuerpo / Query | Respuesta |
|---|---|---|---|---|
| GET | `/tipos-hora` | todos | `?fecha` (def. hoy) | `TipoHora[]` ordenados |
| GET | `/parametros` | todos | — | `{ id, codigo, nombre, unidad: 'PESOS'\|'FRACCION'\|'HORAS', valor, vigenteDesde, norma, creadoEn }[]` (por código, vigencia desc) |
| GET | `/parametros/catalogo` | todos | — | `{ codigo, nombre, unidad, descripcion }[]` |
| GET | `/parametros/vigentes` | todos | `?fecha` | `{ fecha, valores: { smmlv, auxilioTransporte, uvt, jornadaMaximaSemanal, recargoNocturno, recargoExtraDiurna, recargoExtraNocturna, recargoDominicalFestivo, aporteSaludEmpleado, aportePensionEmpleado, limiteHorasExtraDia, limiteHorasExtraSemana } }` |
| POST | `/parametros` | ADMIN, NOMINA | `{ codigo, valor, vigenteDesde, norma? }` | fila creada (409 si ya existe esa vigencia) |
| DELETE | `/parametros/:id` | ADMIN, NOMINA | — | 204 · 409 si hay nóminas CERRADAS con fechaFin ≥ vigenteDesde |

## Registro de horas (lectura: todos · escritura: ADMIN, GESTOR_PROYECTOS, NOMINA)

| Método | Ruta | Cuerpo / Query | Respuesta |
|---|---|---|---|
| GET | `/registro-horas` | `?desde&hasta&trabajadorId&proyectoId&pagina&limite` | `Pagina<RegistroHoras>` (fecha desc) |
| GET | `/registro-horas/:id` | — | `RegistroHoras` |
| POST | `/registro-horas` | `{ trabajadorId, proyectoId, fecha, observacion?, detalles: { tipoHoraId, horas }[] }` | `{ registro: RegistroHoras, alertas: string[] }` · 409 si ya existe trabajador+proyecto+fecha |
| PATCH | `/registro-horas/:id` | `{ observacion?, detalles? }` (detalles reemplaza el conjunto completo) | `{ registro, alertas }` |
| DELETE | `/registro-horas/:id` | — | 204 |
| PUT | `/registro-horas/lote` | `{ registros: { trabajadorId, proyectoId, fecha, observacion?, detalles }[] }` (1–500) | `{ creados, actualizados, eliminados, alertas: string[] }` |

Las horas se registran **por día**: cada registro es la jornada de un trabajador en un proyecto y una fecha, con las horas por tipo de hora. La nómina agrupa los días de cada semana (lunes a domingo).

`PUT /lote` es un upsert transaccional por (trabajador, proyecto, fecha): crea, reemplaza detalles o, si todas las horas del ítem son 0 o no hay detalles, elimina el registro existente. Si un ítem falla, no se guarda nada y el 400/409 indica el ítem: `"Registro 3 (trabajador 5, semana 2026-10-05): ..."`.

Validaciones (bloquean con 400 salvo indicación):
- `horas` > 0 y ≤ 168 con máx. 2 decimales (en lote se permiten 0 para borrar); sin tipos repetidos; tipos existentes y activos.
- `fecha` dentro de la semana en curso o anterior (America/Bogota): se pueden registrar sábado y domingo de la semana actual; no semanas futuras.
- El trabajador debe estar asignado al proyecto.
- Suma de horas del trabajador en el día (todos los proyectos) ≤ 24.
- 409 si la fecha pertenece a una liquidación `CERRADA` ("El periodo ya fue liquidado y cerrado").
- Toda creación, modificación o eliminación se audita y marca `requiereRecalculo` en la liquidación `BORRADOR` que cubra la fecha.

Alertas (no bloquean; se devuelven en `alertas`), evaluadas con todas las horas del trabajador:
- Horas extra del día > `LIMITE_HORAS_EXTRA_DIA` (2).
- Horas extra de la semana (lunes–domingo) > `LIMITE_HORAS_EXTRA_SEMANA` (12).
- Horas no extra de la semana > `JORNADA_MAXIMA_SEMANAL` (posibles extras no clasificadas).

## Nómina (ADMIN, NOMINA; `reabrir` solo ADMIN)

| Método | Ruta | Cuerpo / Query | Respuesta |
|---|---|---|---|
| GET | `/nomina/liquidaciones` | `?anio` | `LiquidacionResumen[]` (fechaInicio desc) |
| POST | `/nomina/liquidaciones` | `{ fecha }` (cualquier día de la semana a liquidar; se liquida de lunes a domingo) | `LiquidacionResumen & { nominas: NominaTrabajadorResumen[] }` · 409 si esa semana ya está liquidada |
| GET | `/nomina/liquidaciones/:id` | — | igual que POST |
| POST | `/nomina/liquidaciones/:id/recalcular` | — | igual · 409 si CERRADA |
| POST | `/nomina/liquidaciones/:id/cerrar` | — | igual · 409 si ya cerrada o `requiereRecalculo` |
| POST | `/nomina/liquidaciones/:id/reabrir` | — (ADMIN) | igual · 409 si no está cerrada |
| DELETE | `/nomina/liquidaciones/:id` | — | 204 · 409 si CERRADA |
| GET | `/nomina/liquidaciones/:id/excel` | — | archivo `.xlsx` (consolidado + detalle) |
| GET | `/nomina/liquidaciones/:id/pdf` | — | archivo `.pdf` (consolidado) |
| GET | `/nomina/desprendibles/:id` | — | `Desprendible` |
| GET | `/nomina/desprendibles/:id/pdf` | — | archivo `.pdf` (comprobante de pago) |

La nómina es **semanal** (lunes a domingo, 7 días). Se liquida a los trabajadores `ACTIVO` y a cualquier trabajador con horas esa semana; los de salario `POR_HORA` solo si tienen horas. El salario mensual se paga por días (7/30 por semana) y el auxilio de transporte también (7/30). A quien cobra por hora se le pagan los días distintos con horas registradas esa semana (días laborados × auxilio ÷ 30).
Las descargas usan `Content-Disposition: attachment; filename="..."` (header expuesto por CORS).

## Materiales (lectura: todos · escritura: ADMIN, GESTOR_PROYECTOS)

| Método | Ruta | Cuerpo / Query | Respuesta |
|---|---|---|---|
| GET | `/proyectos/:proyectoId/materiales` | — | `Material[]` (más reciente primero) |
| POST | `/proyectos/:proyectoId/materiales` | `{ nombre, unidadMedida, cantidadSolicitada (>0), precioUnitario (≥0) }` | `Material` |
| PATCH | `/materiales/:id` | parcial | `Material` |
| DELETE | `/materiales/:id` | — | 204 |


```ts
type Material = { id; proyectoId; nombre; unidadMedida; cantidadSolicitada; precioUnitario;
  costo: number /* precioUnitario × cantidadSolicitada, pesos enteros */; creadoEn; actualizadoEn };
```

## Reportes y dashboard (todos; los montos de nómina solo para ADMIN y NOMINA)

El costo de mano de obra por proyecto es un indicador de gestión (horas × valor hora × recargo) y es visible para todos los roles; los totales de liquidaciones de nómina solo para ADMIN y NOMINA.

| Método | Ruta | Query | Respuesta |
|---|---|---|---|
| GET | `/reportes/horas` | `desde, hasta` (obligatorios) `&trabajadorId&proyectoId&agrupacion=trabajador\|proyecto\|tipo\|fecha` (def. trabajador) | `{ desde, hasta, agrupacion, filas: { clave: string, etiqueta: string, horas, horasExtra, costo, porTipo: Partial<Record<CodigoTipoHora, number>> }[], totales: { horas, horasExtra, costo } }` |
| GET | `/reportes/costos-proyectos` | `desde, hasta` | `{ filas: { proyectoId, codigo, nombre, estado, presupuesto, horas, costo }[], total }` |
| GET | `/reportes/alertas-horas-extra` | `desde, hasta` | `{ alertas: { trabajadorId, nombre, tipo: 'EXTRA_DIA'\|'EXTRA_SEMANA'\|'JORNADA_SEMANA', periodo: string, horas, limite, mensaje }[] }` (`periodo`: la fecha o el lunes de la semana) |
| GET | `/reportes/nomina-periodos` | `anio` | `{ filas: LiquidacionResumen[], totales: { devengado, deducciones, neto } }` (ADMIN, NOMINA) |
| GET | `/reportes/proyectos/:id/resumen` | `?desde&hasta` (def. toda la vida del proyecto) | `{ proyecto, asignaciones: Asignacion[], horas: { total, horasExtra, porTipo: { codigo, nombre, horas }[] }, costoManoObra, costoPorTrabajador: { trabajadorId, nombre, horas, costo }[], costoMateriales, costoPorMaterial: { materialId, nombre, cantidad, costo }[], gastoTotal }` (gastoTotal = mano de obra + materiales) |
| GET | `/reportes/dashboard` | — | ver abajo |

```ts
type Dashboard = {
  fecha: string; proyectosActivos: number; trabajadoresActivos: number;
  mes: { desde: string; hasta: string; horas: number; horasExtra: number; costo: number;
         porTipo: { codigo: CodigoTipoHora; nombre: string; horas: number }[] };
  costoPorProyecto: { proyectoId: number; codigo: string; nombre: string; horas: number; costo: number }[]; // mes, top 8
  alertasHorasExtra: number;                    // mes en curso
  ultimaLiquidacion: LiquidacionResumen | null;  // null si el rol no es ADMIN/NOMINA
};
```

`clave` del reporte de horas: id del trabajador o del proyecto, código del tipo de hora (`HED`) o la fecha, según la agrupación.

**Costo de mano de obra** (directo, sin carga prestacional): `horas × valorHora × (1 + recargo)` por fecha, con el salario actual del trabajador y los parámetros vigentes en la fecha trabajada. Es lo que se refleja en los costos de cada proyecto.

## Auditoría (ADMIN)

| Método | Ruta | Query | Respuesta |
|---|---|---|---|
| GET | `/auditoria` | `?entidad&entidadId&usuarioId&accion&desde&hasta&pagina&limite` | `Pagina<{ id, entidad, entidadId, accion, datosAnteriores, datosNuevos, descripcion, usuarioId, usuarioEmail, fecha }>` |

Entidades auditadas: `Usuario`, `Trabajador`, `Proyecto`, `AsignacionProyecto`, `ParametroLegal`, `RegistroHoras`, `LiquidacionNomina`, `MaterialProyecto`.
