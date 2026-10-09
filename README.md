# Gestión de Proyectos y Nómina — Lineamientos

Sistema web para una empresa de ingeniería eléctrica en Colombia: proyectos, trabajadores, registro de horas, nómina colombiana, materiales, herramientas, avance y bitácora.

Este repositorio contiene los **lineamientos de desarrollo** (configuración de Claude Code en `.claude/`) y la documentación compartida. El código está en dos repositorios independientes:

| Repositorio | Contenido | Despliegue |
|---|---|---|
| [gestion-proyecto-backend](https://github.com/escano1/gestion-proyecto-backend) | API NestJS 12 + PostgreSQL | Railway |
| [gestion-proyecto-frontend](https://github.com/escano1/gestion-proyecto-frontend) | Next.js 16 | Vercel |
| gestion-proyecto-claude (este) | `.claude/`, `docs/API.md` | — |

## Ambiente local

Requisitos: **Node.js 24** (24.15 o posterior; también 22.22.3+) y **Docker**. En Windows funcionan PowerShell, Git Bash o WSL.

```bash
mkdir GestionProyecto
cd GestionProyecto
git clone https://github.com/escano1/gestion-proyecto-claude.git .
git clone https://github.com/escano1/gestion-proyecto-backend.git gestion-proyecto-back
git clone https://github.com/escano1/gestion-proyecto-frontend.git gestion-proyecto-frontend
```

**API** (terminal 1):

```bash
cd gestion-proyecto-back
cp .env.example .env          # cambie JWT_SECRET y JWT_REFRESH_SECRET por valores aleatorios
npm ci
npm run db:up                 # PostgreSQL 16 en Docker (puerto 5432)
npm run migration:run         # tablas, tipos de hora y parámetros legales
npm run start:dev             # http://localhost:3001/api  ·  Swagger en /api/docs
```

**Datos de prueba** (terminal 2, con la API arriba):

```bash
cd gestion-proyecto-back
npm run seed:demo             # usuarios por rol + proyectos, horas y nómina de ejemplo
```

**Frontend** (terminal 3):

```bash
cd gestion-proyecto-frontend
cp .env.example .env.local    # NEXT_PUBLIC_API_URL=http://localhost:3001/api
npm ci
npm run dev                   # http://localhost:3000
```

### Usuarios de prueba

| Rol | Correo | Contraseña |
|---|---|---|
| Administrador (se crea al arrancar la API) | `admin@empresa.com` | `CambiarEsta123*` |
| Gestor de proyectos (`seed:demo`) | `gestor@empresa.com` | `Gestor12345` |
| Nómina (`seed:demo`) | `nomina@empresa.com` | `Nomina12345` |
| Consulta (`seed:demo`) | `consulta@empresa.com` | `Consulta123` |

Son credenciales de desarrollo. En producción defina `ADMIN_EMAIL` / `ADMIN_PASSWORD` propios y no ejecute el seed.

Detalle completo (scripts, pruebas, despliegue y reglas de nómina): README de cada repositorio.

## Documentación

- [`docs/API.md`](docs/API.md): contrato de la API, fuente de verdad entre backend y frontend.
- [`.claude/CLAUDE.md`](.claude/CLAUDE.md): stack, convenciones y parámetros legales (los carga Claude Code en cada sesión).
- `.claude/agents/`: agentes especializados (backend, frontend, nómina, base de datos, QA). `.claude/skills/`: crear módulos y migraciones.
- [`docs/contexto.md`](docs/contexto.md): requerimientos originales.
- `docs/PLAN_*`, `docs/STACK_*`, `ESTRUCTURA_PROYECTO.md`, `DOCUMENTACION_COMPLETA.txt`: documentos de planeación inicial (históricos; el stack y la normativa vigente están en los archivos anteriores).

## Parámetros legales

Los valores legales no están en el código: se guardan con fecha de vigencia en la base de datos y se editan desde *Configuración › Parámetros legales*. Vigentes a octubre de 2026: SMMLV $1.750.905, auxilio de transporte $249.095, UVT $52.374, jornada máxima 42 h/semana (Ley 2101), recargo nocturno 35 % (7 p.m.–6 a.m.), extra diurna 25 %, extra nocturna 75 %, dominical/festivo 90 % (Ley 2466 de 2025), salud 4 %, pensión 4 % y retención por la tabla del art. 383 E.T. Valide cualquier cambio normativo con el área contable.
