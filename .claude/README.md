# 🎯 Configuración Claude Code para Nómina App

Estructura de agentes y skills para desarrollo optimizado del sistema de nómina y proyectos.

---

## 📁 Archivos

```
.claude/
├── claude.md                    # Documentación principal del proyecto
├── settings.json                # Configuración de agentes y skills
├── agents/                      # Agentes especializados
│   ├── backend-dev.md          # Desarrollo backend NestJS
│   ├── frontend-dev.md         # Desarrollo frontend Next.js
│   ├── nomina-specialist.md    # Motor de nómina (CRÍTICO)
│   ├── database-admin.md       # Administración BD PostgreSQL
│   └── qa-tester.md            # Testing y QA
└── skills/                      # Skills reutilizables
    ├── setup-nest-module.md    # Generar módulo NestJS completo
    └── generate-migration.md   # Generar migrations TypeORM
```

---

## 🤖 Agentes Disponibles

### 1. **backend-dev** (Recomendado para backend)
Especialista en NestJS, TypeORM, DTOs, validación.

**Cuándo usarlo:**
- Crear nuevos servicios
- Implementar controladores
- Diseñar repositorios
- Revisar código backend

**Principios:** SOLID, DRY, KISS

```bash
# Ejemplo: "Crea un servicio para gestionar asignaciones de proyectos"
```

### 2. **frontend-dev** (Recomendado para frontend)
Especialista en Next.js, React hooks, validación Zod, TailwindCSS.

**Cuándo usarlo:**
- Crear páginas y componentes
- Implementar formularios
- Manejo de estado
- Optimización de UI

**Principios:** UX-first, responsive, SOLID

```bash
# Ejemplo: "Crea la página de registro de horas con formulario validado"
```

### 3. **nomina-specialist** (Para módulo nómina)
Especialista en parámetros legales Colombia, cálculos, tests exhaustivos.

**Cuándo usarlo:**
- Implementar calculators
- Diseñar logic nómina
- Crear tests de nómina
- Validar parámetros legales

**Crítico:** Errores en nómina = problemas legales

```bash
# Ejemplo: "Implementa el HoraCalculator con todos los tipos de recargo colombianos"
```

### 4. **database-admin** (Para cambios BD)
Especialista en PostgreSQL, TypeORM, migrations, índices.

**Cuándo usarlo:**
- Crear entidades
- Generar migrations
- Diseñar relaciones
- Optimizar queries

```bash
# Ejemplo: "Crea entidad de RegistroHoras con relaciones correctas"
```

### 5. **qa-tester** (Para testing)
Especialista en Jest, Supertest, React Testing Library.

**Cuándo usarlo:**
- Escribir tests unitarios
- Crear tests e2e
- Medir cobertura
- Tests casos límite

**Target:** 80%+ coverage en críticos

```bash
# Ejemplo: "Crea 50+ tests para el motor de nómina cubriendo todos los casos"
```

---

## 🛠️ Skills Disponibles

### 1. **setup-nest-module**
Genera módulo NestJS completo (entity, DTO, service, controller, tests).

**Uso:**
```bash
# Parámetros:
moduleName: trabajador
fields:
  - name: nombre
    type: string
    required: true
  - name: salarioBase
    type: decimal
    required: true
    validations: [isPositive]
hasRepository: true
```

### 2. **generate-migration**
Genera TypeORM migration automática.

**Uso:**
```bash
# Parámetros:
changeType: add-column
tableName: trabajador
changes:
  - column: numeroAfiliacion
    newType: varchar(50)
    nullable: true
```

---

## 📋 Workflow Típico

### 1️⃣ Iniciar Función Nueva

```
Usuario → "Quiero crear módulo para gestionar materiales"
         ↓
        skill: setup-nest-module
         ↓
        Agent: backend-dev
         ↓
        Genera: Material module completo (SOLID)
```

### 2️⃣ Modificar Base de Datos

```
Usuario → "Necesito agregar campo 'numeroAfiliacion' a trabajador"
         ↓
        skill: generate-migration
         ↓
        Agent: database-admin
         ↓
        Genera: Migration + validación
```

### 3️⃣ Implementar Nómina

```
Usuario → "Implementa el cálculo de horas extras"
         ↓
        Agent: nomina-specialist
         ↓
        Implementa: HoraCalculator + 10+ tests
         ↓
        Resultado: Cálculos precisos, Colombia 2026
```

### 4️⃣ Crear UI

```
Usuario → "Crea formulario de registro de horas"
         ↓
        Agent: frontend-dev
         ↓
        Genera: Página + componente + validación Zod
```

### 5️⃣ Testing

```
Usuario → "Escribe tests para el servicio de nómina"
         ↓
        Agent: qa-tester
         ↓
        Genera: 50+ tests con edge cases
```

---

## ✅ Checklist por Fase

### Fase 0 (Semana 1): Setup
- [ ] Git inicializado
- [ ] Backend NestJS creado
- [ ] Frontend Next.js creado
- [ ] PostgreSQL en Docker
- [ ] Equipos (agentes) listos

### Fase 1 (Semanas 2-3): BD + Modelos
- [ ] 14 entidades TypeORM
- [ ] Migrations ejecutadas
- [ ] Repositories implementados
- [ ] Tests BD básicos

**Agentes a usar:** `database-admin`, `qa-tester`

### Fase 2 (Semanas 4-5): Auth + CRUD
- [ ] Autenticación JWT
- [ ] CRUD trabajadores/proyectos
- [ ] Tests unitarios

**Agentes a usar:** `backend-dev`, `qa-tester`

### Fase 3 (Semanas 6-7): Registro Horas
- [ ] Validaciones (12h/día, 80h extra/semana)
- [ ] UI captura rápida
- [ ] Auditoría completa
- [ ] Tests validaciones

**Agentes a usar:** `backend-dev`, `frontend-dev`, `qa-tester`

### Fase 4 (Semanas 8-10): NÓMINA ← CRÍTICA
- [ ] HoraCalculator
- [ ] DeduccionCalculator
- [ ] 50+ tests
- [ ] PDF/Excel generator

**Agentes a usar:** `nomina-specialist`, `qa-tester`

### Fase 5 (Semanas 11-12): UI Completa
- [ ] Dashboard
- [ ] Todas las páginas CRUD
- [ ] Responsive design
- [ ] Reportes

**Agentes a usar:** `frontend-dev`, `qa-tester`

### Fase 6 (Semanas 13-14): QA + Deployment
- [ ] 80%+ coverage
- [ ] Performance optimizado
- [ ] Docker build
- [ ] Documentación

**Agentes a usar:** `qa-tester`, `database-admin`

---

## 🎓 Principios Enforced

### ✅ Siempre
- **SOLID**: Cada clase una responsabilidad
- **DRY**: No repetir código (usar utilidades compartidas)
- **KISS**: Simple, sin complejidad innecesaria
- **TypeScript Strict**: Sin `any`, tipos explícitos
- **Auditoría**: Quién, qué, cuándo en cambios críticos

### ❌ Nunca
- Skipear tests
- Cambiar parámetros nómina sin validar
- Hardcoding de valores
- Usar `any` en TypeScript
- Modificar BD sin migration
- Nómina sin auditoría

---

## 🔍 Cómo Usar

### Opción 1: Especificar agente directamente

```
Usuario: "@nomina-specialist Implementa el DeduccionCalculator"
         ↓
         Claude invoca agente nomina-specialist
```

### Opción 2: Let Claude choose

```
Usuario: "Crea un formulario para registrar horas"
         ↓
         Claude identifica que es UI → usa frontend-dev
```

### Opción 3: Usar skill

```
Usuario: "Usa la skill setup-nest-module para crear módulo de materiales"
         ↓
         Claude invoca skill → genera código completo
```

---

## 📊 Configuración del Settings

El archivo `settings.json` define:

- **Agentes disponibles**: 5 especializados
- **Skills**: 2 generadores de código
- **Principios**: SOLID, DRY, KISS
- **Cobertura de tests**: 80% mínimo, 85% en críticos
- **TypeScript**: Strict mode obligatorio
- **Módulos críticos**: nomina, registro-horas, auth

---

## 🚀 Quick Start

1. Leer `claude.md` (visión general del proyecto)
2. Leer `PLAN_IMPLEMENTACION_TYPESCRIPT.md` (roadmap de 14 semanas)
3. Elegir agente según tarea:
   - Backend → `backend-dev`
   - Frontend → `frontend-dev`
   - Nómina → `nomina-specialist`
   - BD → `database-admin`
   - Tests → `qa-tester`
4. Usar skills para tareas repetitivas

---

## 📞 Troubleshooting

**P: ¿Por qué me sugiere usar `@UseGuards` en backend?**  
R: Por SOLID + seguridad. El Backend-dev enforza autenticación en endpoints críticos.

**P: ¿Por qué no puedo usar `any` en TypeScript?**  
R: Porque en nómina, errores de tipo = dinero incorrecto. TypeScript strict previene bugs.

**P: ¿Y si cambio parámetros de nómina?**  
R: Consulta con nomina-specialist. Está validado para Colombia 2026.

**P: ¿Cuántos tests para nómina?**  
R: 50+ mínimo. Cubre todos los tipos de hora + tramos retención + edge cases.

---

## 📈 Siguiente Paso

1. Ejecutar **Fase 0** (Setup inicial)
2. Invocar agente `backend-dev` o `frontend-dev` según prefiera
3. Seguir plan en `PLAN_IMPLEMENTACION_TYPESCRIPT.md`

---

**Actualizado**: 2026-10-08  
**Stack**: NestJS + Next.js + PostgreSQL  
**Principios**: SOLID, DRY, KISS  
**Crítico**: Nómina = precisión legal
