
## Contexto:
Se requiere una aplicación de escritorio para una empresa de ingeniería eléctrica que ejecuta múltiples proyectos simultáneos en diferentes ubicaciones geográficas. Cada proyecto tiene asignado un equipo de trabajadores, y un mismo trabajador puede estar asignado a varios proyectos al mismo tiempo (o en distintos periodos). La aplicación debe permitir el registro manual de horas trabajadas, clasificarlas según su tipo, y usar esa información para calcular la nómina de cada empleado.

## Objetivo principal:
Centralizar la gestión de proyectos, trabajadores, horas trabajadas y liquidación de nómina, garantizando trazabilidad y reportes confiables para pagos.

### 1. Módulo de Proyectos
CRUD de proyectos: nombre, código, cliente, ubicación/sede, fecha de inicio/fin, estado (activo, finalizado, suspendido).
Asignación de uno o varios trabajadores a un proyecto, con fecha de inicio/fin de asignación.
Un trabajador puede pertenecer a múltiples proyectos de forma simultánea.
Vista de resumen por proyecto: trabajadores asignados, horas acumuladas, costo de mano de obra.

### 2. Módulo de Trabajadores
CRUD de empleados: nombre, documento de identidad, cargo, salario base (mensual o por hora), fecha de ingreso, estado (activo/inactivo).
Historial de proyectos asignados por trabajador.
Configuración de tipo de contrato o jornada (para cálculo correcto de recargos).

### 3. Módulo de Registro de Horas (manual)
Registro diario/por turno de horas trabajadas por trabajador y por proyecto.
Clasificación de horas según tipo:
Hora ordinaria (diurna)
Hora nocturna (recargo nocturno)
Hora extra diurna
Hora extra nocturna
Hora dominical/festiva ordinaria
Hora extra dominical/festiva diurna
Hora extra dominical/festiva nocturna
Permitir seleccionar fecha, proyecto, trabajador y cantidad de horas por categoría en cada registro.
Validaciones: no permitir horas negativas, alertar si se exceden límites legales diarios/semanales, evitar duplicidad de registros para el mismo trabajador/fecha/proyecto.
Posibilidad de editar/eliminar registros con auditoría (quién y cuándo modificó).

### 4. Módulo de Nómina
Configuración de parámetros legales (porcentajes de recargo para cada tipo de hora, editable por si cambia la normativa laboral).
Cálculo automático del pago por periodo (quincenal/mensual) considerando:
Salario base (prorrateado a valor hora)
Horas ordinarias, nocturnas, extras y festivas según los porcentajes configurados
Deducciones legales (salud, pensión, retenciones) si aplica
Generación de un desprendible de pago por trabajador y un consolidado general.
Exportación de nómina a Excel/PDF.

### 5. Reportes y Dashboard
Horas trabajadas por trabajador, por proyecto y por tipo de hora (filtrable por rango de fechas).
Costo de mano de obra por proyecto.
Reporte de nómina por periodo.
Alertas de trabajadores con exceso de horas extra.

### 6. Requisitos no funcionales
Aplicación de escritorio (especificar si debe ser multiplataforma: Windows/Linux/Mac).
Base de datos relacional (ej. PostgreSQL, SQL Server o SQLite si es monousuario) con modelo que soporte relación muchos a muchos entre trabajadores y proyectos.
Control de acceso por roles (administrador, gestor de proyectos, nómina, consulta).
Registro de auditoría (logs) para cambios en horas y nómina.
Interfaz intuitiva para captura rápida de horas (idealmente por lotes/semana).

### 7. Modelo de datos sugerido (entidades principales)
Trabajador (id, nombre, documento, cargo, salario, estado)
Proyecto (id, nombre, ubicación, cliente, estado, fechas)
AsignacionProyecto (id, trabajador_id, proyecto_id, fecha_inicio, fecha_fin)
RegistroHoras (id, trabajador_id, proyecto_id, fecha, tipo_hora, cantidad_horas)
TipoHora (id, nombre, porcentaje_recargo)
Nomina (id, trabajador_id, periodo, total_horas_por_tipo, salario_base, total_pagar)

### 8. Stack tecnológico (ajustar según preferencia)
Especificar si se requiere: .NET (WPF/WinForms), Electron + Node.js, Java (JavaFX), Python (PyQt/Tkinter), o aplicación web local.
Base de datos: indicar si debe ser local o en red/servidor.

### 9. Módulo de Requerimientos y Avance de Proyecto

Materiales y Herramientas

Registro de materiales/insumos requeridos por proyecto: nombre, descripción, cantidad solicitada, unidad de medida, estado (pendiente, solicitado, entregado, instalado).
Registro de herramientas/equipos asignados al proyecto: nombre, cantidad, fecha de asignación, fecha de devolución (si aplica), responsable a cargo.
Control de stock/disponibilidad (opcional): si la herramienta/material proviene de un inventario central, descontar automáticamente al asignarlo a un proyecto.
Historial de solicitudes por proyecto, con fecha y quién la generó.
Alertas de materiales pendientes por entregar o herramientas por devolver.
Avance del Proyecto

Registro de hitos o etapas del proyecto (ej. diseño, instalación, pruebas, entrega).
Porcentaje de avance general y por etapa (manual o calculado a partir de hitos completados).
Bitácora/diario de obra: notas de avance con fecha, responsable y observaciones (puede incluir adjuntos como fotos o documentos).
Comparativo de avance planeado vs. real (curva S opcional).
Estado visual del proyecto (ej. en tiempo, retrasado, adelantado) en el dashboard.
Modelo de datos adicional sugerido

MaterialProyecto (id, proyecto_id, nombre, cantidad, unidad, estado, fecha_solicitud, fecha_entrega)
HerramientaProyecto (id, proyecto_id, nombre, cantidad, responsable, fecha_asignacion, fecha_devolucion)
HitoProyecto (id, proyecto_id, nombre, fecha_planeada, fecha_real, porcentaje_peso, estado)
BitacoraProyecto (id, proyecto_id, fecha, usuario_id, descripcion, adjunto_url)
Reportes relacionados

Materiales y herramientas pendientes por proyecto.
Avance consolidado de todos los proyectos activos.
Historial de bitácora por proyecto (línea de tiempo).


Analiza todo el contexto, miremos la viabilidad, los riesgos, y definamos el stack tecnológico para implementarlo, todo desde 0.