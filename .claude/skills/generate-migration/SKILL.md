---
name: generate-migration
description: Genera y registra una migración TypeORM en gestion-proyecto-back después de cambiar entidades, o una migración de datos (p. ej. nueva vigencia de un parámetro legal).
---

# Generar una migración (gestion-proyecto-back)

1. Modifica o crea la entidad (`src/modules/<modulo>/<nombre>.entity.ts`). Si es nueva, agrégala a `src/database/entidades.ts`.
2. Con la BD local arriba (`npm run db:up`) y migrada, genera el diff:
   ```bash
   npm run migration:generate -- src/database/migrations/<NombreEnPascalCase>
   ```
3. Revisa el SQL generado (nombres snake_case, `numeric` para dinero, FKs, índices) y formatea con `npx prettier --write src/database/migrations`.
4. Registra la clase en `src/database/migraciones.ts` (en orden). Sin esto no se ejecuta: no hay globs.
5. Aplica y verifica: `npm run migration:run`, luego `npm run test:e2e` (recrea el esquema desde cero con todas las migraciones).

Migraciones de datos (vigencias legales, catálogos): créalas a mano con `npx typeorm migration:create src/database/migrations/<Nombre>`, usa `queryRunner.query` con parámetros (`$1`) e incluye `down`.

Nunca uses `synchronize` ni modifiques una migración ya desplegada: crea una nueva. En Railway se ejecutan solas en el pre-deploy (`npm run migration:run:prod`).
