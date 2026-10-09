# Sistema de Gestión de Proyectos y Nómina - Lineamientos de Desarrollo

**Stack**: NestJS + React + Next.js + PostgreSQL + TypeScript  
**País**: Colombia  
**Equipo**: 1-2 desarrolladores  
**Duración**: 14 semanas  

---

## 🎯 Objetivo Principal

Desarrollar un ERP web moderno para gestionar proyectos de ingeniería, registro de horas y cálculo automático de nómina colombiana con precisión legal.

---

## 📋 Estructura del Proyecto

```
gestion-nomina-app/
├── backend/                    # NestJS API
├── frontend/                   # Next.js React
├── docker-compose.yml
├── PLAN_IMPLEMENTACION_TYPESCRIPT.md
├── STACK_TYPESCRIPT_FULLSTACK.md
└── .claude/                    # Este directorio
    ├── claude.md              # Este archivo
    ├── agents/                # Agentes especializados
    ├── skills/                # Skills personalizadas
    └── settings.json          # Configuración
```

---

## 🏗️ Stack Tecnológico

### Backend
- **NestJS 10+** — Decoradores, módulos, inyección de dependencias
- **TypeORM 0.3+** — ORM para PostgreSQL
- **PostgreSQL 15+** — BD relacional
- **Passport + JWT** — Autenticación
- **Class Validator** — Validación de DTOs
- **Jest + Supertest** — Testing

### Frontend
- **Next.js 14+** — React con SSR y App Router
- **React 18+** — Componentes funcionales
- **TailwindCSS** — Estilos
- **React Hook Form + Zod** — Validación
- **Axios** — Cliente HTTP
- **Zustand** — State management

### DevOps
- **Docker** — Containerización
- **PostgreSQL en Docker** — Base de datos
- **GitHub Actions** — CI/CD (opcional)

---

## 🔑 Principios de Desarrollo

### 1. **TypeScript Strict Mode**
```json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true
  }
}
```

### 2. **Módulos Independientes (NestJS)**
Cada feature es un módulo: `trabajador.module`, `nomina.module`, `registro-horas.module`

### 3. **DTOs para Validación Automática**
```typescript
// Backend
@IsInt()
@IsPositive()
@Max(12)
cantidadHoras: number;

// Frontend
z.number().min(0.5).max(12)
```

### 4. **Auditoría en Operaciones Críticas**
- Registro de horas: quién, cuándo, qué cambió
- Cálculo de nómina: trazabilidad completa
- Cambios en parámetros legales: historial

### 5. **Testing Exhaustivo**
- Backend: Jest + Supertest (mínimo 80% coverage en servicios críticos)
- Frontend: React Testing Library (mínimo 70% en componentes)
- Nómina: 50+ tests de cálculos

### 6. **Código Limpio**
- Nombres descriptivos en español para lógica de negocio
- Funciones pequeñas y enfocadas
- Sin duplicación (DRY)
- Errores explícitos con mensajes claros

---

## 📁 Estructura de Carpetas Backend

```
backend/src/
├── main.ts                          # Entrada
├── app.module.ts                    # Módulo raíz
│
├── modules/
│   ├── auth/                        # Autenticación JWT
│   ├── usuario/                     # Gestión de usuarios
│   ├── trabajador/                  # CRUD de trabajadores
│   ├── proyecto/                    # CRUD de proyectos
│   ├── asignacion-proyecto/         # Relación M-M
│   ├── tipo-hora/                   # Configuración de tipos
│   ├── registro-horas/              # Captura de horas (validado)
│   ├── nomina/                      # Motor de nómina (crítico)
│   │   ├── calculators/
│   │   │   ├── hora.calculator.ts
│   │   │   ├── deduccion.calculator.ts
│   │   │   └── retencion.calculator.ts
│   │   ├── services/
│   │   │   ├── nomina.service.ts
│   │   │   ├── pdf-generator.service.ts
│   │   │   └── excel-generator.service.ts
│   │   └── tests/
│   │       └── nomina.service.spec.ts  (50+ tests)
│   ├── reporte/                     # Reportes y dashboards
│   ├── auditoria/                   # Logs de cambios
│   └── material-herramienta/        # Materiales y herramientas
│
├── database/
│   ├── entities/                    # Entidades TypeORM
│   ├── migrations/                  # Versionado de esquema
│   └── seeders/                     # Data inicial
│
├── common/
│   ├── decorators/                  # @Roles, @IsOwner, etc.
│   ├── guards/                      # JwtAuthGuard, RolesGuard
│   ├── filters/                     # Error handling
│   ├── interceptors/                # Logging, transformación
│   ├── pipes/                       # Validación personalizada
│   └── exceptions/                  # CustomException
│
└── config/
    ├── database.config.ts
    ├── jwt.config.ts
    ├── nomina-parametros.ts         # Parámetros legales Colombia
    └── app.config.ts
```

---

## 📁 Estructura de Carpetas Frontend

```
frontend/src/
├── app/
│   ├── layout.tsx                   # Root layout
│   ├── page.tsx                     # Home
│   ├── (auth)/
│   │   └── login/page.tsx
│   └── (dashboard)/
│       ├── layout.tsx               # Sidebar + navbar
│       ├── dashboard/page.tsx
│       ├── trabajadores/page.tsx
│       ├── registro-horas/page.tsx
│       ├── nomina/page.tsx
│       ├── nomina/generar/page.tsx
│       └── reportes/page.tsx
│
├── components/
│   ├── layout/
│   │   ├── Sidebar.tsx
│   │   ├── Navbar.tsx
│   │   └── MainLayout.tsx
│   ├── forms/
│   │   ├── TrabajadorForm.tsx
│   │   ├── RegistroHorasForm.tsx    # Captura rápida
│   │   └── NominaGenerarForm.tsx
│   ├── tables/
│   │   ├── TrabajadoresTable.tsx
│   │   ├── RegistroHorasTable.tsx
│   │   └── NominasTable.tsx
│   ├── modals/
│   │   ├── ConfirmDialog.tsx
│   │   └── ErrorModal.tsx
│   └── ui/                          # Componentes base
│
├── hooks/
│   ├── useAuth.ts                   # Autenticación
│   ├── useApi.ts                    # Llamadas HTTP
│   ├── useForm.ts                   # Form management
│   └── usePermissions.ts            # Control de acceso
│
├── lib/
│   ├── api.ts                       # Cliente HTTP
│   ├── auth.ts                      # JWT local storage
│   ├── formatter.ts                 # Números, fechas, moneda
│   └── validadores.ts
│
├── types/
│   └── index.ts                     # Tipos compartidos
│
└── store/
    └── useAuthStore.ts              # Zustand
```

---

## 🛡️ Seguridad

### Backend
- JWT: Access token (15 min) + Refresh token (7 días)
- Todas las contraseñas con bcrypt (10 salts)
- Rate limiting en endpoints (`@Throttle`)
- CORS configurado por dominio
- Validación de input en DTOs
- SQL injection prevenido (TypeORM)

### Frontend
- Tokens en localStorage con expiración
- Interceptor HTTP: añade JWT automáticamente
- Redirige a login si 401
- Validación client-side + server-side

### BD
- Contraseña strong para usuario PostgreSQL
- Backups automáticos
- Auditoría en tabla log_auditoria

---

## 📊 Parámetros Legales Colombia 2026

```typescript
// backend/src/config/nomina-parametros.ts

export const RECARGOS = {
  ORDINARIA: 0,           // 0%
  NOCTURNA: 0.35,        // 35%
  EXTRA_DIURNA: 0.25,    // 25%
  EXTRA_NOCTURNA: 0.75,  // 75% (acumulativo)
  DOMINICAL: 0.75,       // 75%
  EXTRA_DOMINICAL_DIURNA: 1.0,   // 100%
  EXTRA_DOMINICAL_NOCTURNA: 1.5, // 150%
};

export const DEDUCCIONES = {
  SALUD: 0.04,    // 4% del salario
  PENSION: 0.04,  // 4% del salario
  RETENCION: {    // Progresiva
    '0-919900': 0,
    '919901-1910000': 0.05,
    '1910001-2865000': 0.10,
    '2865001-3814625': 0.15,
    '3814626+': 0.20,
  },
};

export const HORAS_POR_MES = 240;  // 30 días × 8 horas
```

---

## ✅ Checklist por Fase

### Fase 0-1 (Semanas 1-3): Setup + BD
- [ ] Repositorio Git con .gitignore
- [ ] NestJS project generado
- [ ] Next.js project generado
- [ ] PostgreSQL en Docker
- [ ] 14 entidades TypeORM creadas
- [ ] Migrations ejecutadas
- [ ] Repositories implementados

### Fase 2 (Semanas 4-5): Auth + CRUD
- [ ] JWT strategy funcionando
- [ ] Login/logout endpoint
- [ ] CRUD trabajadores
- [ ] CRUD proyectos
- [ ] Tests básicos pasando

### Fase 3 (Semanas 6-7): Registro de Horas
- [ ] Validaciones de horas (12h/día, 80h extra/semana)
- [ ] Prevención de duplicados
- [ ] Auditoría de cambios
- [ ] UI para captura rápida
- [ ] Tests de validación

### Fase 4 (Semanas 8-10): NÓMINA ← CRÍTICA
- [ ] HoraCalculator funcionando
- [ ] DeduccionCalculator funcionando
- [ ] RetensionCalculator funcionando
- [ ] 50+ tests pasando
- [ ] PDF generator
- [ ] Excel generator

### Fase 5 (Semanas 11-12): UI Completa
- [ ] Dashboard con gráficos
- [ ] Todas las páginas CRUD
- [ ] Responsivo en mobile
- [ ] Reportes funcionales

### Fase 6 (Semanas 13-14): QA + Deployment
- [ ] 80%+ coverage tests
- [ ] Performance optimizado
- [ ] Documentación completa
- [ ] Docker image creada
- [ ] Deployment a producción

---

## 🚀 Comandos Rápidos

```bash
# Backend
cd backend
npm install
npm run start:dev              # Desarrollo con hot reload
npm run build                  # Build producción
npm test                       # Tests
npm run test:cov              # Coverage

# Frontend
cd frontend
npm install
npm run dev                    # Desarrollo
npm run build                  # Build
npm run start                  # Producción
npm test                       # Tests

# Docker
docker-compose up -d          # Inicia PostgreSQL
docker-compose down           # Detiene servicios
```

---

## 📞 Contacto y Escalaciones

- **Bloqueos BD**: Revisar migrations en `backend/src/database/migrations/`
- **Errores de nómina**: Validar calculators y tests en `backend/src/modules/nomina/`
- **Problemas de auth**: Revisar `backend/src/modules/auth/`
- **Bugs en UI**: Checkear validación de schema en `frontend/src/lib/validadores.ts`

---

## 📚 Referencias Principales

1. **PLAN_IMPLEMENTACION_TYPESCRIPT.md** — Roadmap detallado por semana
2. **STACK_TYPESCRIPT_FULLSTACK.md** — Arquitectura y decisiones técnicas
3. **Documentación oficial**:
   - NestJS: https://docs.nestjs.com
   - Next.js: https://nextjs.org/docs
   - TypeORM: https://typeorm.io
   - PostgreSQL: https://www.postgresql.org/docs

---

**Última actualización**: 2026-10-08  
**Versión**: 1.0  
**Estado**: Análisis completado, listo para Fase 0
