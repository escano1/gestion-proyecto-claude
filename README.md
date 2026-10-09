# 🏢 Sistema de Gestión de Proyectos y Nómina (Colombia)

Aplicación web moderna para gestionar proyectos de ingeniería, registro de horas y cálculo automático de nómina colombiana con precisión legal.

**Stack**: TypeScript Fullstack | NestJS + Next.js + PostgreSQL  
**País**: Colombia | **Equipo**: 1-2 desarrolladores | **Duración**: 14 semanas

---

## 📁 Estructura del Proyecto

```
gestion-nomina-app/
│
├── 📋 DOCUMENTACIÓN (Raíz)
│   ├── README.md                          (este archivo)
│   ├── .claude/                           (agentes, skills, configuración)
│   ├── docs/                              (documentación técnica)
│   └── .gitignore
│
├── 💻 REPOSITORIO BACKEND
│   └── gestion-proyecto-back/             (NestJS + PostgreSQL)
│       ├── README.md                      (instrucciones backend)
│       ├── .gitignore
│       ├── package.json
│       └── src/
│
└── 🎨 REPOSITORIO FRONTEND
    └── gestion-proyecto-frontend/         (Next.js + React)
        ├── README.md                      (instrucciones frontend)
        ├── .gitignore
        ├── package.json
        └── src/
```

---

## 🎯 Propósito

Centralizar la gestión de:
- **Proyectos** de ingeniería en múltiples ubicaciones
- **Trabajadores** asignados a varios proyectos
- **Horas trabajadas** con 7+ tipos (ordinaria, nocturna, extras, dominical)
- **Nómina** con parámetros legales colombianos precisos
- **Materiales y herramientas** por proyecto
- **Bitácora de avance** con trazabilidad completa

---

## 🚀 Quick Start

### Prerequisitos
- **Node.js** 18+
- **PostgreSQL** 15+
- **Docker** (opcional, recomendado)
- **Git**

### 1. Clonar y Estructurar

```bash
cd gestion-nomina-app

# Backend
cd gestion-proyecto-back
npm install

# Frontend (en otra terminal)
cd gestion-proyecto-frontend
npm install
```

### 2. Base de Datos

```bash
# Opción A: Docker (recomendado)
docker-compose up -d

# Opción B: PostgreSQL local
# Crear BD: gestion_proyectos_db
# Usuario: gestion_usuarios
# Contraseña: (ver .env.local)
```

### 3. Desarrollo

```bash
# Terminal 1: Backend
cd gestion-proyecto-back
npm run start:dev
# → http://localhost:3000

# Terminal 2: Frontend
cd gestion-proyecto-frontend
npm run dev
# → http://localhost:3000 (Next.js)
```

### 4. Tests

```bash
# Backend
cd gestion-proyecto-back
npm test              # Todos los tests
npm test -- --coverage  # Con cobertura

# Frontend
cd gestion-proyecto-frontend
npm test
```

---

## 📚 Documentación Completa

### Para Empezar
1. **[.claude/claude.md](.claude/claude.md)** — Lineamientos del proyecto
2. **[.claude/README.md](.claude/README.md)** — Cómo usar agentes y skills
3. **[PLAN_IMPLEMENTACION](docs/PLAN_IMPLEMENTACION_TYPESCRIPT.md)** — Roadmap 14 semanas

### Referencia Técnica
- **[Stack Tecnológico](docs/STACK_TYPESCRIPT_FULLSTACK.md)** — Arquitectura detallada
- **[Backend README](gestion-proyecto-back/README.md)** — Instrucciones NestJS
- **[Frontend README](gestion-proyecto-frontend/README.md)** — Instrucciones Next.js

### Agentes Especializados (en .claude/agents/)
- **[backend-dev.md](.claude/agents/backend-dev.md)** — Desarrollo backend
- **[frontend-dev.md](.claude/agents/frontend-dev.md)** — Desarrollo frontend
- **[nomina-specialist.md](.claude/agents/nomina-specialist.md)** — Nómina colombiana ⭐
- **[database-admin.md](.claude/agents/database-admin.md)** — PostgreSQL
- **[qa-tester.md](.claude/agents/qa-tester.md)** — Testing y QA

---

## 🎓 Principios de Desarrollo

### SOLID
```
Single Responsibility    → Cada servicio una responsabilidad
Open/Closed             → Extensible sin modificar código existente
Liskov Substitution     → Interfaces bien definidas
Interface Segregation   → DTOs específicos, no genéricos
Dependency Inversion    → Inyección de dependencias
```

### DRY (Don't Repeat Yourself)
```
- Reutilizar calculators, validators, hooks
- Centralizar schemas de validación
- Utilidades compartidas en /common
```

### KISS (Keep It Simple, Stupid)
```
- Funciones pequeñas y enfocadas
- Nombres descriptivos en español
- Errores explícitos y claros
```

---

## 🔒 Seguridad & Auditoría

### Autenticación
- JWT: Access token (15 min) + Refresh token (7 días)
- Contraseñas: bcrypt (10 salts mínimo)
- CORS: Configurado por dominio

### Auditoría (Tabla log_auditoria)
Registra: **QUÉ** cambió, **QUIÉN** lo cambió, **CUÁNDO**

Crítico para:
- Registro de horas (cambios de horas)
- Nómina (cálculos, parámetros legales)
- Trabajadores (cambios de salario)

---

## 📊 Parámetros Legales Colombia 2026

### Recargos sobre Valor Hora
```
Ordinaria:              0%      (base)
Nocturna:              35%     (21:00 - 06:00)
Extra Diurna:          25%     
Extra Nocturna:        75%     (35% + 25% + 15%)
Dominical:             75%     
Dominical Extra:      100-150% 
```

### Deducciones Obligatorias
```
Salud:                 4%      (aporte trabajador)
Pensión:               4%      (aporte trabajador)
Retención en la Fuente: Progresiva (5 tramos)
```

Más detalles en: [nomina-specialist.md](.claude/agents/nomina-specialist.md)

---

## ✅ Fases de Desarrollo

| Fase | Semanas | Objetivo | Estado |
|------|---------|----------|--------|
| 0 | 1 | Setup inicial | 📋 Documentación |
| 1 | 2-3 | BD + Modelos | ⏳ Pendiente |
| 2 | 4-5 | Auth + CRUD | ⏳ Pendiente |
| 3 | 6-7 | Registro Horas | ⏳ Pendiente |
| 4 | 8-10 | Motor Nómina ⭐ | ⏳ Pendiente |
| 5 | 11-12 | UI Completa | ⏳ Pendiente |
| 6 | 13-14 | QA + Deployment | ⏳ Pendiente |

---

## 🛠️ Herramientas Disponibles

### Agentes Claude
Especialistas para cada área de desarrollo:

```bash
# Backend
@backend-dev "Crea el servicio TrabajadorService"

# Frontend  
@frontend-dev "Crea la página de registro de horas"

# Nómina (CRÍTICO)
@nomina-specialist "Implementa HoraCalculator"

# Base de datos
@database-admin "Crea entidad RegistroHoras"

# Tests
@qa-tester "Escribe 50+ tests para nómina"
```

### Skills Reutilizables
```bash
# Generar módulo NestJS completo
"Usa skill: setup-nest-module para módulo de Materiales"

# Generar migration TypeORM
"Usa skill: generate-migration para agregar columna"
```

---

## 📋 Checklist Antes de Empezar

### Setup Inicial (Fase 0)
- [ ] Leer [.claude/claude.md](.claude/claude.md)
- [ ] Leer [PLAN_IMPLEMENTACION](docs/PLAN_IMPLEMENTACION_TYPESCRIPT.md)
- [ ] Clonar repositorio
- [ ] Instalar Node.js + PostgreSQL + Docker
- [ ] `npm install` en ambos repos
- [ ] Configurar `.env.local` (credenciales BD)
- [ ] Ejecutar migrations iniciales
- [ ] Verificar backend + frontend funcionan

### Configuración de IDE
- [ ] VSCode + extensiones (ES7+, TypeScript, Prettier)
- [ ] Prettier configurado (auto format)
- [ ] TypeScript strict mode (verificar)
- [ ] Pre-commit hooks (linting, tests)

---

## 🔍 Comandos Útiles

### Backend (NestJS)
```bash
cd gestion-proyecto-back

npm install              # Instalar dependencias
npm run start:dev        # Desarrollo (hot reload)
npm run build            # Build producción
npm test                 # Todos los tests
npm test -- --coverage   # Con cobertura
npm run lint             # Linter
```

### Frontend (Next.js)
```bash
cd gestion-proyecto-frontend

npm install              # Instalar dependencias
npm run dev              # Desarrollo
npm run build            # Build producción
npm run start            # Ejecutar build
npm test                 # Tests
npm run lint             # Linter
```

### Base de Datos
```bash
# Docker
docker-compose up -d     # Inicia PostgreSQL
docker-compose down      # Detiene servicios
docker-compose logs db   # Ver logs

# TypeORM (Backend)
npm run typeorm migration:show      # Ver migraciones
npm run typeorm migration:run       # Ejecutar
npm run typeorm migration:revert    # Revertir última
```

---

## 📞 Troubleshooting

### "Cannot find module 'typeorm'"
```bash
cd gestion-proyecto-back
npm install @nestjs/typeorm typeorm pg
```

### "PostgreSQL connection error"
- Verificar que Docker está corriendo: `docker ps`
- Verificar credenciales en `.env.local`
- Verificar que BD `gestion_proyectos_db` existe

### "Port 3000 already in use"
```bash
# Backend usa 3000, Frontend usa 3001 (Next.js auto-redirige)
# O cambiar puerto en .env
```

---

## 📈 Métricas de Éxito

✅ **Backend**:
- TypeScript strict mode: 0 errores
- Coverage: 80%+ en servicios críticos (85% en nómina)
- Tests pasando: 100%

✅ **Frontend**:
- TypeScript strict mode: 0 errores
- Coverage: 70%+ en componentes
- Responsive: mobile + tablet + desktop
- Accesibilidad: WCAG AA

✅ **Nómina**:
- 50+ tests exhaustivos
- Validados con especialista laboral
- Auditoría completa en cada cálculo

---

## 🔗 Enlaces Útiles

### Documentación Oficial
- [NestJS Docs](https://docs.nestjs.com)
- [Next.js Docs](https://nextjs.org/docs)
- [TypeORM Docs](https://typeorm.io)
- [PostgreSQL Docs](https://www.postgresql.org/docs)

### Proyecto
- [Stack Detallado](docs/STACK_TYPESCRIPT_FULLSTACK.md)
- [Plan 14 Semanas](docs/PLAN_IMPLEMENTACION_TYPESCRIPT.md)
- [Agentes Claude](.claude/)

---

## 👥 Contribuyendo

1. Crear rama: `git checkout -b feature/descripcion`
2. Seguir principios SOLID, DRY, KISS
3. TypeScript strict mode (sin `any`)
4. Tests para código crítico (mínimo 80% coverage)
5. Pull request con descripción clara
6. Revisar con agente correspondiente

---

## 📄 Licencia

Proyecto privado. Uso interno.

---

## 📞 Contacto

**Estado**: 📋 Documentación y organización completada  
**Siguiente**: Fase 0 - Setup Inicial  
**Actualizado**: 2026-10-08

Para preguntas técnicas, consultar agentes especializados en `.claude/agents/`

---

**Recuerda**: 
- Lee primero la documentación
- Usa agentes según tu tarea
- Sigue SOLID, DRY, KISS
- En nómina: precisión = responsabilidad legal
