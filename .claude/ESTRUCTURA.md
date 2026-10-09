> **Documento histórico de planeación inicial.** Puede no coincidir con el sistema implementado. Vigente: `README.md`, `.claude/CLAUDE.md`, `docs/API.md` y los README de cada repositorio.

# 📊 Estructura de la Carpeta .claude

## Árbol de Archivos

```
.claude/
│
├── 📄 claude.md                      ← DOCUMENTACIÓN PRINCIPAL
│   └── Lineamientos, stack, estructura, parámetros legales Colombia
│
├── ⚙️ settings.json                   ← CONFIGURACIÓN DE AGENTES
│   └── Mapea agentes, skills, principios, paths críticos
│
├── 📖 README.md                       ← GUÍA DE USO (este proyecto)
│   └── Cómo usar agentes y skills, workflow típico
│
├── 📋 ESTRUCTURA.md                   ← este archivo
│   └── Visión rápida de la carpeta .claude
│
├── 🤖 agents/                         ← AGENTES ESPECIALIZADOS
│   │
│   ├── backend-dev.md                ← NestJS, TypeORM, SOLID
│   │   ├── Responsabilidades: servicios, controllers, tests
│   │   ├── Convenciones: nombres, validación, parámetros
│   │   └── Workflow: crear servicio, crear DTO, crear controller, tests
│   │
│   ├── frontend-dev.md               ← Next.js, React, UX
│   │   ├── Responsabilidades: páginas, componentes, validación
│   │   ├── Validación: Zod schemas, React Hook Form
│   │   └── Workflow: crear página, crear forma, integrar API
│   │
│   ├── nomina-specialist.md          ← NÓMINA COLOMBIANA (CRÍTICO)
│   │   ├── Parámetros legales Colombia 2026
│   │   ├── Recargos: ordinaria, nocturna, extras, dominical
│   │   ├── Deducciones: salud 4%, pensión 4%, retención progresiva
│   │   ├── Calculators: HoraCalculator, DeduccionCalculator
│   │   └── Tests: 50+ tests exhaustivos
│   │
│   ├── database-admin.md             ← PostgreSQL, TypeORM, Migrations
│   │   ├── Convenciones: tipos de datos, índices, constraints
│   │   ├── 14 tablas: usuario, trabajador, proyecto, nomina, etc.
│   │   ├── Migrations: generar, ejecutar, revertir
│   │   └── Índices críticos para performance
│   │
│   └── qa-tester.md                  ← Testing, Jest, Coverage
│       ├── Estructura de tests: beforeEach, Arrange-Act-Assert
│       ├── Backend (Jest + Supertest): servicios, controllers, e2e
│       ├── Frontend (React Testing Library): componentes, forms
│       └── Cobertura: 80% mínimo, 85% en módulos críticos
│
└── 💡 skills/                        ← SKILLS REUTILIZABLES
    │
    ├── setup-nest-module.md          ← Generar módulo NestJS completo
    │   ├── Entrada: nombre, fields, validations, relations
    │   ├── Salida: entity, DTO, service, controller, tests
    │   └── Automatiza estructura repetitiva
    │
    └── generate-migration.md         ← Generar TypeORM migration
        ├── Entrada: change type, table name, columns
        ├── Salida: migration file listo para ejecutar
        └── Nunca modificar BD sin migration
```

---

## 🎯 Mapa Rápido por Tarea

### Tarea: Crear nuevo módulo backend
```
Usuario: "Crea módulo de Permisos"
         ↓
         📚 Leer: backend-dev.md (línea 100+, "Workflow para crear servicio")
         ↓
         💡 Usar skill: setup-nest-module
         ↓
         🤖 Agent: backend-dev
         ↓
         ✅ Resultado: Módulo completo con tests
```

### Tarea: Implementar motor de nómina
```
Usuario: "Implementa HoraCalculator"
         ↓
         📚 Leer: nomina-specialist.md (línea 50+, "Calculators")
         ↓
         🤖 Agent: nomina-specialist
         ↓
         ✅ Resultado: Calculator + 50+ tests validados
```

### Tarea: Crear formulario en frontend
```
Usuario: "Crea formulario de login"
         ↓
         📚 Leer: frontend-dev.md (línea 140+, "Workflow para formulario")
         ↓
         🤖 Agent: frontend-dev
         ↓
         ✅ Resultado: Página + componente + validación Zod
```

### Tarea: Agregar campo a BD
```
Usuario: "Agrega campo 'numeroAfiliacion' a trabajador"
         ↓
         💡 Usar skill: generate-migration
         ↓
         🤖 Agent: database-admin
         ↓
         ✅ Resultado: Migration lista para ejecutar
```

### Tarea: Escribir tests
```
Usuario: "Tests para RegistroHoras"
         ↓
         📚 Leer: qa-tester.md (línea 50+, "Casos de prueba críticos")
         ↓
         🤖 Agent: qa-tester
         ↓
         ✅ Resultado: 20+ tests con edge cases
```

---

## 📋 Checklist de Calidad

Cada agente enforza:

### ✅ backend-dev
- [ ] TypeScript strict (sin `any`)
- [ ] DTOs con validación
- [ ] Inyección de dependencias
- [ ] No hardcoding de valores
- [ ] Tests unitarios (3+ casos)
- [ ] Auditoría en cambios críticos

### ✅ frontend-dev
- [ ] TypeScript strict
- [ ] Validación con Zod
- [ ] React Hook Form
- [ ] Responsive design
- [ ] Loading/error states
- [ ] Accesibilidad

### ✅ nomina-specialist
- [ ] Parámetros legales validados
- [ ] Todos los tipos de hora
- [ ] Todos los tramos de retención
- [ ] Edge cases cubiertos
- [ ] 50+ tests
- [ ] Auditoría completa

### ✅ database-admin
- [ ] Tipos correctos (decimal para dinero)
- [ ] Constraints de integridad
- [ ] Índices en queries frecuentes
- [ ] Relaciones sin ciclos
- [ ] Migrations versionadas

### ✅ qa-tester
- [ ] 80%+ coverage (críticos 85%)
- [ ] Casos: éxito, error, edge
- [ ] Mocks correctamente
- [ ] Performance < 100ms
- [ ] Sin tests skippeados

---

## 🔗 Relaciones entre Documentos

```
claude.md (Visión general)
    ↓
    ├─→ PLAN_IMPLEMENTACION_TYPESCRIPT.md (Roadmap 14 semanas)
    │
    ├─→ STACK_TYPESCRIPT_FULLSTACK.md (Arquitectura detallada)
    │
    └─→ Agentes especializados
        ├─→ backend-dev.md
        ├─→ frontend-dev.md
        ├─→ nomina-specialist.md
        ├─→ database-admin.md
        └─→ qa-tester.md
```

---

## 🚀 Cómo Navegar

1. **Primera vez**: Leer `claude.md` (visión general)
2. **Empezar a codear**: Leer el agente correspondiente
3. **Tarea repetitiva**: Usar skill
4. **Dudas sobre calidad**: Consultar checklist del agente
5. **Referencia rápida**: Este archivo (ESTRUCTURA.md)

---

## ⚡ Tips de Productividad

### Tip 1: Especificar agente en prompt
```
"@backend-dev Crea el servicio TrabajadorService"
→ Claude va directo al agente, no pierde contexto
```

### Tip 2: Usar skills para automatizar
```
"Usa skill: setup-nest-module para crear módulo de 'Material'"
→ Genera código scaffolding completo
```

### Tip 3: Referencia línea de agente
```
"¿Cómo estructura DTOs?" → backend-dev.md línea 85
```

### Tip 4: Workflow completo
```
1. Usuario → Define feature
2. Skill generate-migration → crea tablas BD
3. Skill setup-nest-module → crea backend
4. Agent frontend-dev → crea UI
5. Agent qa-tester → escribe tests
```

---

## 🎓 Principios Enforced en .claude

### SOLID (Backend)
```
Single: Cada servicio una responsabilidad
Open/Closed: Extensible sin modificar código
Liskov: Interfaces bien definidas
Interface Segregation: DTOs específicos
Dependency Inversion: Inyección, no hardcoding
```

### DRY (Backend + Frontend)
```
Reutilizar: calculators, validators, hooks
Centralizar: schemas en lib/validadores.ts
No duplicar: lógica de validación
```

### KISS (Todos)
```
Simple: Funciones pequeñas
Claro: Nombres descriptivos
Explícito: Errores claros
```

---

## 📞 Cheat Sheet

| Necesito... | Agente | Skill | Línea |
|---|---|---|---|
| Crear servicio backend | backend-dev | setup-nest-module | 100 |
| Crear página React | frontend-dev | — | 140 |
| Calcular nómina | nomina-specialist | — | 50 |
| Crear tabla BD | database-admin | generate-migration | 60 |
| Escribir tests | qa-tester | — | 80 |
| Validar parámetros | nomina-specialist | — | 30 |

---

## 🔐 Protecciones Activadas

El archivo `settings.json` activa:

```json
{
  "no_any_allowed": true,           // ❌ any bloqueado
  "typescript_strict": true,        // ✅ Tipos explícitos
  "coverage_minimum": 80,           // ✅ 80% tests
  "coverage_critical": 85,          // ✅ 85% en nómina
  "critical_audit": true,           // ✅ Auditoría obligatoria
  "migrations_required": true       // ✅ Nunca BD directa
}
```

---

## 📈 Evolución Típica

```
Semana 1 (Setup)
  → Leer claude.md
  → Usar agent: backend-dev + frontend-dev
  
Semanas 2-7 (CRUD + Horas)
  → Skills: setup-nest-module, generate-migration
  → Agents: backend-dev, frontend-dev, database-admin, qa-tester
  
Semanas 8-10 (NÓMINA)
  → Agent: nomina-specialist (especialización total)
  → Agent: qa-tester (50+ tests)
  
Semanas 11-14 (UI + QA + Deployment)
  → Agent: frontend-dev
  → Agent: qa-tester
  → Agent: database-admin (optimización)
```

---

**Última actualización**: 2026-10-08  
**Stack**: NestJS + Next.js + PostgreSQL  
**Principios**: SOLID, DRY, KISS, TypeScript Strict  
**Crítico**: Nómina = precisión legal
