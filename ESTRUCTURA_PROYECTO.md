> **Documento histórico de planeación inicial.** Puede no coincidir con el sistema implementado. Vigente: `README.md`, `.claude/CLAUDE.md`, `docs/API.md` y los README de cada repositorio.

# 📊 Estructura Completa del Proyecto

Organización visual del monorepo de gestión de nómina y proyectos.

---

## 🏠 Carpeta Raíz: Lineamientos y Documentación

```
GestionProyecto/
│
├── 📋 README.md                         ← INICIO AQUÍ
│   └── Guía general del proyecto
│
├── 📌 ESTRUCTURA_PROYECTO.md            ← Este archivo
│   └── Visualización de la organización
│
├── 🔧 .gitignore                        ← Ignorar archivos en Git
│
├── 📚 .claude/                          ← LINEAMIENTOS DE DESARROLLO
│   ├── claude.md                        (Documentación principal)
│   ├── README.md                        (Cómo usar agentes)
│   ├── ESTRUCTURA.md                    (Estructura visual)
│   ├── RESUMEN_SETUP.md                 (Setup completado)
│   ├── settings.json                    (Config agentes + skills)
│   │
│   ├── agents/                          (Agentes especializados)
│   │   ├── backend-dev.md              → NestJS
│   │   ├── frontend-dev.md             → Next.js
│   │   ├── nomina-specialist.md        → CRÍTICO ⭐
│   │   ├── database-admin.md           → PostgreSQL
│   │   └── qa-tester.md                → Testing
│   │
│   └── skills/                          (Skills reutilizables)
│       ├── setup-nest-module.md        → Generar módulo completo
│       └── generate-migration.md       → Generar migration
│
├── 📖 docs/                             ← DOCUMENTACIÓN TÉCNICA
│   ├── PLAN_IMPLEMENTACION_TYPESCRIPT.md    (14 semanas detalladas)
│   ├── STACK_TYPESCRIPT_FULLSTACK.md       (Arquitectura)
│   └── contexto.md                         (Contexto original)
│
├── 💻 gestion-proyecto-back/            ← REPOSITORIO BACKEND
│   ├── README.md                        (Setup backend)
│   ├── .gitignore                       (Git ignore backend)
│   ├── .git/                            (Git repo independiente)
│   ├── package.json                     (Dependencias Node)
│   ├── tsconfig.json                    (TypeScript config)
│   ├── nest-cli.json                    (NestJS config)
│   │
│   ├── src/
│   │   ├── main.ts                      (Punto de entrada)
│   │   ├── app.module.ts                (Módulo raíz)
│   │   │
│   │   ├── modules/                     (Módulos por feature)
│   │   │   ├── auth/                   (Autenticación JWT)
│   │   │   ├── usuario/                (CRUD usuarios)
│   │   │   ├── trabajador/             (CRUD trabajadores)
│   │   │   ├── proyecto/               (CRUD proyectos)
│   │   │   ├── asignacion-proyecto/    (Relación M-M)
│   │   │   ├── tipo-hora/              (Configuración)
│   │   │   ├── registro-horas/         (Captura horas - CRÍTICO)
│   │   │   ├── nomina/                 (Motor nómina - CRÍTICO ⭐)
│   │   │   │   ├── calculators/
│   │   │   │   │   ├── hora.calculator.ts
│   │   │   │   │   ├── deduccion.calculator.ts
│   │   │   │   │   └── retencion.calculator.ts
│   │   │   │   ├── services/
│   │   │   │   │   ├── nomina.service.ts
│   │   │   │   │   ├── pdf-generator.service.ts
│   │   │   │   │   └── excel-generator.service.ts
│   │   │   │   └── tests/
│   │   │   │       └── nomina.service.spec.ts (50+ tests)
│   │   │   ├── reporte/                (Reportes)
│   │   │   ├── auditoria/              (Logs de cambios)
│   │   │   └── material-herramienta/   (Materiales)
│   │   │
│   │   ├── database/
│   │   │   ├── entities/               (Entidades TypeORM)
│   │   │   ├── migrations/             (Versionado BD)
│   │   │   └── seeders/                (Data inicial)
│   │   │
│   │   ├── common/
│   │   │   ├── decorators/             (@Roles, etc.)
│   │   │   ├── guards/                 (JwtAuthGuard)
│   │   │   ├── filters/                (Error handling)
│   │   │   ├── interceptors/           (Logging)
│   │   │   ├── pipes/                  (Validación)
│   │   │   └── exceptions/             (Custom exceptions)
│   │   │
│   │   └── config/
│   │       ├── database.config.ts      (PostgreSQL)
│   │       ├── jwt.config.ts           (JWT)
│   │       ├── nomina-parametros.ts    (Colombia 2026)
│   │       └── app.config.ts           (App config)
│   │
│   ├── test/
│   │   └── ... (e2e tests)
│   │
│   └── docker-compose.yml              (PostgreSQL container)
│
└── 🎨 gestion-proyecto-frontend/       ← REPOSITORIO FRONTEND
    ├── README.md                        (Setup frontend)
    ├── .gitignore                       (Git ignore frontend)
    ├── .git/                            (Git repo independiente)
    ├── package.json                     (Dependencias Node)
    ├── tsconfig.json                    (TypeScript config)
    ├── next.config.js                   (Next.js config)
    ├── tailwind.config.js               (TailwindCSS config)
    │
    ├── src/
    │   ├── app/
    │   │   ├── layout.tsx               (Root layout)
    │   │   ├── page.tsx                 (Home)
    │   │   │
    │   │   ├── (auth)/
    │   │   │   ├── login/page.tsx
    │   │   │   └── logout/page.tsx
    │   │   │
    │   │   └── (dashboard)/
    │   │       ├── layout.tsx           (Sidebar + navbar)
    │   │       ├── dashboard/page.tsx   (Dashboard)
    │   │       ├── trabajadores/page.tsx (CRUD)
    │   │       ├── proyectos/page.tsx   (CRUD)
    │   │       ├── registro-horas/page.tsx (Captura - CRÍTICO)
    │   │       ├── nomina/page.tsx      (Listado + detalle)
    │   │       ├── reportes/page.tsx    (Reportes)
    │   │       └── configuracion/page.tsx (Parámetros)
    │   │
    │   ├── components/
    │   │   ├── layout/                  (Layout components)
    │   │   ├── forms/                   (Formularios)
    │   │   │   ├── RegistroHorasForm.tsx
    │   │   │   ├── TrabajadorForm.tsx
    │   │   │   └── ProyectoForm.tsx
    │   │   ├── tables/                  (Tablas)
    │   │   ├── modals/                  (Diálogos)
    │   │   └── ui/                      (Componentes base)
    │   │
    │   ├── hooks/
    │   │   ├── useAuth.ts               (Autenticación)
    │   │   ├── useApi.ts                (HTTP calls)
    │   │   ├── useForm.ts               (Form management)
    │   │   └── usePermissions.ts        (Control acceso)
    │   │
    │   ├── lib/
    │   │   ├── api.ts                   (Cliente HTTP)
    │   │   ├── auth.ts                  (JWT management)
    │   │   ├── formatter.ts             (Formato moneda/fechas)
    │   │   └── validadores.ts           (Schemas Zod)
    │   │
    │   ├── types/
    │   │   └── index.ts                 (Tipos compartidos)
    │   │
    │   ├── store/
    │   │   └── useAuthStore.ts          (Zustand state)
    │   │
    │   └── styles/
    │       └── globals.css              (TailwindCSS)
    │
    ├── public/                          (Assets estáticos)
    │
    └── __tests__/                       (Tests)
```

---

## 🎯 Propósito de Cada Sección

### 📋 Raíz: Documentación
- **Guías generales** del proyecto
- **Configuración global** (.gitignore, .claude)
- **Documentación técnica** (docs/)

### 💻 Backend (gestion-proyecto-back)
- **API NestJS** en puerto 3000
- **Base de datos**: PostgreSQL vía TypeORM
- **Módulos**: Auth, CRUD, Nómina, Auditoría
- **Crítico**: Motor de nómina (50+ tests)

### 🎨 Frontend (gestion-proyecto-frontend)
- **UI Next.js** en puerto 3000 (o 3001)
- **Interfaz moderna** con React + TailwindCSS
- **Validación**: Zod + React Hook Form
- **Estado**: Zustand para auth

---

## 📊 Capas de Documentación

```
Nivel 1: INICIO
├── README.md (raíz)                ← Comienza aquí
└── Guía general

Nivel 2: LINEAMIENTOS
├── .claude/CLAUDE.md               ← Visión y principios
├── PLAN_IMPLEMENTACION...          ← Roadmap 14 semanas
└── STACK_TYPESCRIPT_FULLSTACK.md   ← Arquitectura

Nivel 3: AGENTES ESPECIALIZADOS
├── .claude/agents/backend-dev.md
├── .claude/agents/frontend-dev.md
├── .claude/agents/nomina-specialist.md
├── .claude/agents/database-admin.md
└── .claude/agents/qa-tester.md

Nivel 4: REPOSITORIOS
├── gestion-proyecto-back/README.md
└── gestion-proyecto-frontend/README.md

Nivel 5: CÓDIGO FUENTE
├── backend: src/modules/*/
└── frontend: src/app/, src/components/
```

---

## 🔄 Workflow de Desarrollo

### Para Empezar Desarrollo

1. **Leer raíz**
   ```
   README.md (este proyecto)
     ↓
   .claude/CLAUDE.md (lineamientos)
   ```

2. **Elegir agente según tarea**
   ```
   Backend        → @backend-dev
   Frontend       → @frontend-dev
   Nómina         → @nomina-specialist
   BD             → @database-admin
   Tests          → @qa-tester
   ```

3. **Consultar README específico**
   ```
   gestion-proyecto-back/README.md    → Si es backend
   gestion-proyecto-frontend/README.md → Si es frontend
   ```

4. **Seguir documentación del agente**
   ```
   .claude/agents/[agente].md
   ```

---

## ✅ Checklist: Antes de Codear

- [ ] Leer README principal (raíz)
- [ ] Leer .claude/CLAUDE.md
- [ ] Leer PLAN_IMPLEMENTACION_TYPESCRIPT.md
- [ ] Leer agente correspondiente
- [ ] Instalar dependencias: `npm install`
- [ ] Verificar BD: `docker-compose up -d`
- [ ] Iniciar desarrollo: `npm run start:dev`
- [ ] Verificar tests: `npm test`

---

## 🎓 Principios Aplicados

### Estructura
- **Monorepo**: Frontend + Backend + Documentación
- **Separación de concerns**: Cada repo independiente
- **Documentación en contexto**: README en cada nivel

### Código
- **SOLID**: Single responsibility, Open/Closed, etc.
- **DRY**: No repetir código
- **KISS**: Simple sin complejidad innecesaria

### Seguridad
- **Auditoría**: Log completo en tabla log_auditoria
- **Validación**: Client-side + Server-side
- **Autenticación**: JWT con refresh tokens

### Nómina
- **Precisión legal**: Parámetros Colombia 2026
- **50+ tests**: Exhaustivos y validados
- **Trazabilidad**: Quién, qué, cuándo

---

## 📈 Fases de Desarrollo

```
Fase 0 (Sem 1):   Setup inicial (este doc)
Fase 1 (Sem 2-3): BD + Modelos
Fase 2 (Sem 4-5): Auth + CRUD
Fase 3 (Sem 6-7): Registro de Horas
Fase 4 (Sem 8-10): Motor Nómina ⭐
Fase 5 (Sem 11-12): UI Completa
Fase 6 (Sem 13-14): QA + Deployment
```

---

## 🔗 Navegación Rápida

| Necesito... | Ir a... |
|---|---|
| Entender proyecto | README.md (raíz) |
| Lineamientos | .claude/CLAUDE.md |
| Roadmap | docs/PLAN_IMPLEMENTACION_TYPESCRIPT.md |
| Arquitectura | docs/STACK_TYPESCRIPT_FULLSTACK.md |
| Agentes | .claude/agents/*.md |
| Setup Backend | gestion-proyecto-back/README.md |
| Setup Frontend | gestion-proyecto-frontend/README.md |
| Código Base | src/ en cada repo |

---

## 💾 .gitignore Strategy

### Nivel 1: Raíz
Archivos comunes: IDE, OS, environment

### Nivel 2: Backend
Node modules, dist, .env, logs

### Nivel 3: Frontend
Node modules, .next, .env, coverage

---

## 🚀 Comandos Principales

### Desarrollo Completo
```bash
# Terminal 1: Backend
cd gestion-proyecto-back && npm run start:dev

# Terminal 2: Frontend
cd gestion-proyecto-frontend && npm run dev

# Terminal 3: BD
docker-compose up -d
```

### Testing
```bash
cd gestion-proyecto-back && npm test -- --coverage
cd gestion-proyecto-frontend && npm test
```

### Build Producción
```bash
cd gestion-proyecto-back && npm run build
cd gestion-proyecto-frontend && npm run build
```

---

## 📋 Archivos Clave

| Archivo | Propósito | Crítico |
|---------|-----------|---------|
| README.md | Guía inicio | ⭐⭐⭐ |
| .claude/CLAUDE.md | Lineamientos | ⭐⭐⭐ |
| PLAN_IMPLEMENTACION... | Roadmap | ⭐⭐ |
| .claude/agents/nomina-specialist.md | Nómina | ⭐⭐⭐ |
| gestion-proyecto-back/README.md | Backend | ⭐⭐ |
| gestion-proyecto-frontend/README.md | Frontend | ⭐⭐ |

---

**Estado**: ✅ Organización completada  
**Siguiente**: Fase 0 - Setup inicial  
**Actualizado**: 2026-10-08
