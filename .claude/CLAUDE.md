# Sistema de Gestión de Proyectos y Nómina — Lineamientos

ERP web para una empresa de ingeniería eléctrica en Colombia: proyectos, trabajadores, registro de horas, nómina colombiana, materiales/herramientas, avance y bitácora. Uso interno (pocos usuarios), escalable.

## Repositorios

| Carpeta | Repo | Despliegue |
|---|---|---|
| `./` (este) | lineamientos: `.claude/`, `docs/` | — |
| `gestion-proyecto-back/` | API NestJS (repo git independiente) | Railway (Dockerfile + `railway.json`) |
| `gestion-proyecto-frontend/` | Next.js (repo git independiente) | Vercel |

El contrato de API está en `docs/API.md`: es la fuente de verdad entre backend y frontend; si cambia una forma de respuesta, actualícelo.

## Stack real

- **Backend:** NestJS 12 (**ESM**: imports relativos con `.js`), TypeORM 1.x, PostgreSQL 16, TypeScript 6 strict, **Vitest** (no Jest), class-validator, decimal.js, pdfkit, exceljs, bcryptjs.
- **Frontend:** Next.js 16 (App Router; lea `node_modules/next/dist/docs/` ante dudas), React 19, Tailwind 4, TanStack Query 5, React Hook Form + Zod 4, Zustand (sesión), Recharts, Vitest + Testing Library.

## Principios

- SOLID, DRY, KISS. Nombres de negocio en español. Sin `any`. Comentarios solo para explicar el porqué.
- Backend: un módulo por funcionalidad; DTOs con class-validator (`@EsFecha()`, `recortar`/`recortarONulo` de `common/validacion`); lógica pura (cálculos, reglas) en archivos separados con pruebas; escrituras en transacción con `AuditoriaService.registrar(…, manager)`.
- TypeORM 1.x: un `where` con `undefined` lanza error; `date` = string `YYYY-MM-DD`; `numeric` se expone como `number` vía transformador.
- Entidades y migraciones se registran explícitamente en `src/database/entidades.ts` y `src/database/migraciones.ts`. Nunca `synchronize`.
- Frontend: todo acceso al API pasa por `src/features/<dominio>/api.ts` (hooks TanStack Query); pantallas en `src/app/(app)/`; componentes base en `src/components/ui`; permisos visuales con `src/lib/permisos.ts` (el backend siempre valida).
- Dinero con `Decimal`, redondeo al peso; retención al múltiplo de mil.

## Parámetros legales (Colombia)

**No se codifican**: están en la tabla `parametros_legales` con fecha de vigencia y se editan desde la app. El valor aplicable es el de mayor `vigenteDesde` ≤ fecha de la hora trabajada. Valores sembrados (verificar cada año con la norma):

| Parámetro | Valor vigente (oct-2026) | Norma |
|---|---|---|
| SMMLV / auxilio de transporte | $1.750.905 / $249.095 | Decretos 1469 y 1470 de 2025 |
| UVT | $52.374 | Res. DIAN 000238 de 2025 |
| Jornada máxima semanal | 42 h desde 15-jul-2026 (44 h antes) | Ley 2101 de 2021 |
| Recargo dominical/festivo | 90 % desde 1-jul-2026 (80 % antes; 100 % desde 1-jul-2027) | Ley 2466 de 2025 |
| Nocturno · extra diurna · extra nocturna | 35 % · 25 % · 75 % (jornada nocturna 7 p.m.–6 a.m.) | C.S.T. art. 168; Ley 2466 |
| Salud · pensión (trabajador) | 4 % · 4 % del IBC | Ley 1122/2007, Ley 797/2003 |
| Alertas | > 2 h extra/día, > 12 h extra/semana | art. 22 Ley 50/1990 |

Reglas clave (implementadas en `gestion-proyecto-back/src/modules/nomina/calculo/`):
- Valor hora = salario ÷ (jornada semanal × 5) → con 42 h, salario ÷ 210.
- Salario mensual: se paga por días (30 días/mes); horas ordinarias ya incluidas → solo se pagan recargos. Extras y salario por hora: valor hora × (1 + recargo).
- Extra nocturna (75 %) no acumula el nocturno; el dominical sí se suma a nocturno y extras.
- Auxilio de transporte si salario ≤ 2 SMMLV (fuera del IBC). FSP 1–2 % desde 4 SMMLV. Retención: procedimiento 1, art. 383 E.T. (tabla en UVT), renta exenta 25 % con tope 790 UVT/año.
- Cualquier cambio de horas, salarios o parámetros marca las liquidaciones en borrador para recálculo; una liquidación cerrada bloquea las horas de su periodo.

## Comandos

```bash
# Backend (gestion-proyecto-back)
npm run db:up && npm run migration:run && npm run start:dev   # http://localhost:3001/api  (Swagger: /api/docs)
npm test && npm run test:e2e && npm run lint && npm run typecheck

# Frontend (gestion-proyecto-frontend)
npm run dev                                                    # http://localhost:3000
npm test && npm run lint && npm run typecheck && npm run build
```
