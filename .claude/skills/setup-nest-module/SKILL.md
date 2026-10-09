---
name: setup-nest-module
description: Crea un módulo NestJS nuevo en gestion-proyecto-back siguiendo las convenciones del proyecto (entidad, DTOs, servicio, controlador, auditoría, pruebas y registro).
---

# Nuevo módulo NestJS (gestion-proyecto-back)

Toma como referencia `src/modules/materiales/` (CRUD anidado en proyecto con auditoría) y `src/modules/parametros/`.

1. **Entidad** `src/modules/<modulo>/<nombre>.entity.ts`: tabla en plural snake_case (`@Entity('nombres')`), `type` explícito en cada columna, `numeric` con `transformadorNumerico`, fechas `date` como `string`, FK con `@Column() xId` + `@ManyToOne` + `@JoinColumn({ name: 'x_id' })` y `Relation<>`. Regístrala en `src/database/entidades.ts` y genera la migración (skill `generate-migration`).
2. **DTOs** `<modulo>.dto.ts`: class-validator, `@EsFecha()` para fechas, `@Transform(recortar | recortarONulo)` de `common/validacion/transformaciones.js`, `@Type(() => Number)` en query, `PartialType` de `@nestjs/swagger` para PATCH.
3. **Reglas puras** `reglas-<modulo>.ts` + `reglas-<modulo>.spec.ts` (Vitest) para validaciones y cálculos.
4. **Servicio**: escrituras en `dataSource.transaction`, `auditoria.registrar({ entidad, entidadId, accion, antes, despues, usuario }, m)`, errores 400/404/409 en español; con TypeORM 1.x arma los `where` solo con filtros definidos.
5. **Controlador**: rutas según `docs/API.md` (actualízalo primero), `@Roles(...)` en escrituras, `@UsuarioActual()`, `ParseIntPipe` en ids.
6. **Módulo** con `TypeOrmModule.forFeature([...])`, impórtalo en `src/app.module.ts`.
7. **Frontend**: tipos en `src/types/api.ts` y hooks en `src/features/<dominio>/api.ts`.
8. Verifica: `npm run typecheck`, `npm run lint`, `npm test`, `npm run test:e2e`. Imports relativos siempre con `.js` (ESM).
