# STACK TYPESCRIPT FULLSTACK - NÓMINA Y PROYECTOS

## Stack Actualizado

```
┌──────────────────────────────────────────────────────────────┐
│                   Frontend (Browser)                         │
│         React + Next.js + TypeScript + TailwindCSS           │
│  (App moderna, SSR, API integrada, responsive)               │
└────────────────────────┬─────────────────────────────────────┘
                         │ HTTP/REST/GraphQL
                         ▼
┌──────────────────────────────────────────────────────────────┐
│                 Backend (Node.js Server)                     │
│  NestJS + TypeScript + TypeORM + PostgreSQL                  │
│  (Arquitectura modular, validación fuerte, tests)            │
└──────────────────────────────────────────────────────────────┘
                         │
                         ▼
┌──────────────────────────────────────────────────────────────┐
│           PostgreSQL (Red/Servidor)                          │
│    Base de datos relacional + auditoría                      │
└──────────────────────────────────────────────────────────────┘
```

---

## VENTAJAS DE ESTE STACK

| Aspecto | TypeScript Fullstack | .NET WPF |
|---------|----------------------|----------|
| **Desarrollo** | Más rápido, HMR | Más lento |
| **Escalabilidad** | Excelente (web) | Limitada (desktop) |
| **Equipo** | Mismo lenguaje (TS) | Divide frontend/backend |
| **Deployment** | Cualquier VPS | Windows-specific |
| **UI/UX** | React ecosystem | WPF limitado |
| **Actualizaciones** | Sin reinstalar | Requiere nuevo installer |
| **Móvil futuro** | Reutilizar backend | No reutilizable |

---

## ESTRUCTURA DEL PROYECTO

```
gestion-nomina-app/
│
├── backend/                              ← NestJS
│   ├── src/
│   │   ├── main.ts
│   │   ├── app.module.ts
│   │   │
│   │   ├── modules/
│   │   │   ├── auth/                    ← Autenticación JWT
│   │   │   │   ├── auth.module.ts
│   │   │   │   ├── auth.service.ts
│   │   │   │   ├── auth.controller.ts
│   │   │   │   └── jwt.strategy.ts
│   │   │   │
│   │   │   ├── trabajador/              ← CRUD Trabajadores
│   │   │   │   ├── trabajador.module.ts
│   │   │   │   ├── trabajador.service.ts
│   │   │   │   ├── trabajador.controller.ts
│   │   │   │   ├── entities/
│   │   │   │   │   └── trabajador.entity.ts
│   │   │   │   └── dto/
│   │   │   │       ├── create-trabajador.dto.ts
│   │   │   │       └── update-trabajador.dto.ts
│   │   │   │
│   │   │   ├── proyecto/                ← CRUD Proyectos
│   │   │   ├── asignacion-proyecto/     ← Relación M-M
│   │   │   ├── tipo-hora/               ← Configuración
│   │   │   │
│   │   │   ├── registro-horas/          ← CRÍTICO
│   │   │   │   ├── registro-horas.module.ts
│   │   │   │   ├── registro-horas.service.ts  ← Validaciones
│   │   │   │   ├── registro-horas.controller.ts
│   │   │   │   ├── entities/
│   │   │   │   ├── dto/
│   │   │   │   ├── validators/
│   │   │   │   │   └── registro-horas.validator.ts
│   │   │   │   └── tests/
│   │   │   │       └── registro-horas.service.spec.ts
│   │   │   │
│   │   │   ├── nomina/                  ← CRÍTICA
│   │   │   │   ├── nomina.module.ts
│   │   │   │   ├── nomina.service.ts    ← Motor cálculo
│   │   │   │   ├── nomina.controller.ts
│   │   │   │   ├── entities/
│   │   │   │   ├── dto/
│   │   │   │   ├── calculators/
│   │   │   │   │   ├── hora.calculator.ts
│   │   │   │   │   ├── deduccion.calculator.ts
│   │   │   │   │   └── retencion.calculator.ts
│   │   │   │   ├── services/
│   │   │   │   │   ├── nomina.service.ts
│   │   │   │   │   ├── pdf-generator.service.ts
│   │   │   │   │   └── excel-generator.service.ts
│   │   │   │   └── tests/
│   │   │   │       ├── nomina.service.spec.ts ← 50+ tests
│   │   │   │       └── calculators.spec.ts
│   │   │   │
│   │   │   ├── reporte/                 ← Reportes
│   │   │   └── auditoria/               ← Logs críticos
│   │   │
│   │   ├── database/
│   │   │   ├── migrations/              ← TypeORM migrations
│   │   │   └── seeders/                 ← Data inicial
│   │   │
│   │   ├── common/
│   │   │   ├── decorators/              ← @IsNotNegative, etc.
│   │   │   ├── filters/                 ← Error handling
│   │   │   ├── interceptors/            ← Logging, transformación
│   │   │   ├── guards/                  ← JWT, Roles
│   │   │   ├── pipes/                   ← Validación
│   │   │   └── exceptions/
│   │   │       ├── nomina.exception.ts
│   │   │       └── registro-horas.exception.ts
│   │   │
│   │   └── config/
│   │       ├── database.config.ts       ← PostgreSQL
│   │       ├── jwt.config.ts
│   │       ├── nomina-parametros.ts     ← CONFIGURABLES
│   │       └── app.config.ts
│   │
│   ├── test/
│   │   └── ... tests e2e
│   │
│   ├── .env.example
│   ├── nest-cli.json
│   ├── package.json
│   └── tsconfig.json
│
├── frontend/                            ← Next.js (React)
│   ├── src/
│   │   ├── app/
│   │   │   ├── layout.tsx               ← Root layout
│   │   │   ├── page.tsx                 ← Home/Dashboard
│   │   │   │
│   │   │   ├── (auth)/
│   │   │   │   ├── login/page.tsx
│   │   │   │   └── logout/page.tsx
│   │   │   │
│   │   │   ├── (dashboard)/
│   │   │   │   ├── layout.tsx           ← Sidebar, navbar
│   │   │   │   │
│   │   │   │   ├── dashboard/page.tsx   ← Dashboard principal
│   │   │   │   │
│   │   │   │   ├── trabajadores/
│   │   │   │   │   ├── page.tsx         ← Lista
│   │   │   │   │   ├── [id]/page.tsx    ← Detalle
│   │   │   │   │   └── nuevo/page.tsx   ← Crear
│   │   │   │   │
│   │   │   │   ├── proyectos/
│   │   │   │   │   ├── page.tsx
│   │   │   │   │   ├── [id]/page.tsx
│   │   │   │   │   └── nuevo/page.tsx
│   │   │   │   │
│   │   │   │   ├── registro-horas/
│   │   │   │   │   ├── page.tsx         ← Captura (crítica)
│   │   │   │   │   └── historial/page.tsx
│   │   │   │   │
│   │   │   │   ├── nomina/
│   │   │   │   │   ├── page.tsx         ← Listado de nóminas
│   │   │   │   │   ├── [id]/page.tsx    ← Detalle + PDF/Excel
│   │   │   │   │   └── generar/page.tsx ← Calcular nómina
│   │   │   │   │
│   │   │   │   ├── reportes/
│   │   │   │   │   ├── horas/page.tsx
│   │   │   │   │   ├── proyectos/page.tsx
│   │   │   │   │   └── alertas/page.tsx
│   │   │   │   │
│   │   │   │   └── configuracion/
│   │   │   │       └── parametros-nomina/page.tsx
│   │   │   │
│   │   │   └── api/                     ← API Routes (opcional, usar backend)
│   │   │
│   │   ├── components/
│   │   │   ├── layout/
│   │   │   │   ├── Sidebar.tsx
│   │   │   │   ├── Navbar.tsx
│   │   │   │   └── MainLayout.tsx
│   │   │   │
│   │   │   ├── forms/
│   │   │   │   ├── TrabajadorForm.tsx
│   │   │   │   ├── ProyectoForm.tsx
│   │   │   │   ├── RegistroHorasForm.tsx ← Captura rápida
│   │   │   │   └── RegistroHorasBulk.tsx ← Por lotes
│   │   │   │
│   │   │   ├── tables/
│   │   │   │   ├── TrabajadoresTable.tsx
│   │   │   │   └── ... otras tablas
│   │   │   │
│   │   │   ├── modals/
│   │   │   │   ├── ConfirmDialog.tsx
│   │   │   │   └── ErrorModal.tsx
│   │   │   │
│   │   │   └── ui/
│   │   │       ├── Button.tsx
│   │   │       ├── Input.tsx
│   │   │       ├── Card.tsx
│   │   │       └── ... componentes base
│   │   │
│   │   ├── hooks/
│   │   │   ├── useApi.ts                ← Llamadas HTTP
│   │   │   ├── useAuth.ts               ← JWT, usuario
│   │   │   ├── useForm.ts               ← Form management
│   │   │   └── useTrabajadores.ts       ← Data específico
│   │   │
│   │   ├── lib/
│   │   │   ├── api.ts                   ← Cliente API
│   │   │   ├── auth.ts                  ← JWT local storage
│   │   │   ├── formatter.ts             ← Números, fechas
│   │   │   └── validadores.ts
│   │   │
│   │   ├── types/
│   │   │   ├── index.ts                 ← Tipos compartidos
│   │   │   └── api.ts
│   │   │
│   │   ├── store/                       ← Zustand o Context
│   │   │   ├── useAuthStore.ts
│   │   │   └── useAppStore.ts
│   │   │
│   │   └── styles/
│   │       └── globals.css              ← TailwindCSS
│   │
│   ├── public/
│   │   └── ... assets estáticos
│   │
│   ├── .env.local
│   ├── next.config.js
│   ├── package.json
│   ├── tailwind.config.js
│   └── tsconfig.json
│
├── docker-compose.yml                  ← PostgreSQL containerizado
├── .gitignore
├── README.md
└── PLAN_IMPLEMENTACION.md               ← Este documento
```

---

## STACK TÉCNICO DETALLADO

### Backend (NestJS)

**Dependencias principales**:
```json
{
  "dependencies": {
    "@nestjs/common": "^10.0.0",
    "@nestjs/core": "^10.0.0",
    "@nestjs/jwt": "^12.0.0",
    "@nestjs/passport": "^10.0.0",
    "@nestjs/typeorm": "^10.0.0",
    "@nestjs/config": "^3.0.0",
    "@nestjs/swagger": "^7.0.0",
    "@nestjs/schedule": "^4.0.0",
    
    "typeorm": "^0.3.0",
    "pg": "^8.0.0",
    "passport": "^0.7.0",
    "passport-jwt": "^4.0.0",
    "class-validator": "^0.14.0",
    "class-transformer": "^0.5.0",
    
    "pdfkit": "^0.13.0",
    "exceljs": "^4.4.0",
    
    "winston": "^3.0.0"
  },
  "devDependencies": {
    "@nestjs/testing": "^10.0.0",
    "@types/jest": "^29.0.0",
    "jest": "^29.0.0",
    "ts-jest": "^29.0.0"
  }
}
```

**Arquitectura NestJS**:
- Modular (cada módulo = Feature completo)
- Inyección de dependencias
- Decoradores TypeScript
- TypeORM para ORM
- Passport para autenticación JWT
- Swagger para API docs

---

### Frontend (Next.js + React)

**Dependencias principales**:
```json
{
  "dependencies": {
    "next": "^14.0.0",
    "react": "^18.0.0",
    "typescript": "^5.0.0",
    
    "axios": "^1.6.0",
    "zustand": "^4.0.0",
    
    "react-hook-form": "^7.0.0",
    "zod": "^3.0.0",
    "@hookform/resolvers": "^3.0.0",
    
    "react-table": "^8.0.0",
    "recharts": "^2.0.0",
    
    "tailwindcss": "^3.0.0",
    "@headlessui/react": "^1.0.0",
    "lucide-react": "^0.0.0",
    
    "jspdf": "^2.5.0",
    "xlsx": "^0.18.0"
  },
  "devDependencies": {
    "@types/node": "^20.0.0",
    "@types/react": "^18.0.0",
    "@testing-library/react": "^14.0.0",
    "@testing-library/jest-dom": "^6.0.0",
    "jest": "^29.0.0"
  }
}
```

**Características**:
- App Router (última versión Next.js)
- SSR para mejor SEO
- API Routes opcional (usaremos backend)
- TailwindCSS para estilos
- React Hook Form para formularios
- Zustand para state management

---

## DISEÑO DE BD (Igual, optimizado para PostgreSQL)

```sql
-- Las mismas 14 tablas del análisis anterior
-- Optimizadas para TypeORM + PostgreSQL
```

---

## FLUJO DE AUTENTICACIÓN

```
1. Usuario inicia sesión (LoginForm)
   └─> POST /api/auth/login { email, password }
   
2. Backend valida y retorna JWT
   └─> { access_token, refresh_token, usuario }
   
3. Frontend guarda tokens en localStorage (o httpOnly cookies)
   └─> Zustand useAuthStore
   
4. Todas las peticiones llevan token JWT
   └─> Authorization: Bearer <token>
   
5. Backend valida JWT con Guard @UseGuards(JwtAuthGuard)
   └─> Procesa request o retorna 401

6. Token expira → refresh token genera nuevo access_token
   └─> Transparente para usuario
```

---

## VALIDACIONES Y SEGURIDAD

### Backend (NestJS)
```typescript
// DTOs con validaciones automáticas
export class CreateRegistroHorasDto {
  @IsNotEmpty()
  @IsPositive()
  @Max(12)
  cantidadHoras: number;
  
  @IsISO8601()
  fecha: string;
  
  @IsNotEmpty()
  @IsInt()
  proyectoId: number;
}

// Pipes de validación
@UsePipes(new ValidationPipe({ whitelist: true }))
@Post()
crear(@Body() dto: CreateRegistroHorasDto) { ... }

// Guards de autorización
@UseGuards(JwtAuthGuard, RolesGuard)
@Roles('admin', 'nómina')
@Post(':id/procesar')
procesarNomina(@Param('id') id: number) { ... }
```

### Frontend (React)
```typescript
// Validación con Zod + React Hook Form
const registroHorasSchema = z.object({
  cantidadHoras: z.number().min(0.5).max(12),
  fecha: z.string().refine(date => new Date(date) <= new Date()),
  tipoHoraId: z.number().int().positive(),
});

function RegistroHorasForm() {
  const { register, formState: { errors } } = useForm({
    resolver: zodResolver(registroHorasSchema)
  });
  
  return (
    <>
      <input {...register('cantidadHoras')} />
      {errors.cantidadHoras && <span>{errors.cantidadHoras.message}</span>}
    </>
  );
}
```

---

## FLUJO CRÍTICO: CÁLCULO DE NÓMINA (NestJS)

```typescript
// backend/src/modules/nomina/services/nomina.service.ts

@Injectable()
export class NominaService {
  constructor(
    private registroHorasService: RegistroHorasService,
    private trabajadorService: TrabajadorService,
    private pdfGenerator: PDFGeneratorService,
    private excelGenerator: ExcelGeneratorService,
    @InjectRepository(Nomina)
    private nominaRepository: Repository<Nomina>,
  ) {}
  
  async calcularNomina(trabajadorId: number, mes: number, año: number): Promise<Nomina> {
    // 1. Validar
    const existente = await this.nominaRepository.findOne({
      where: { trabajadorId, periodoMes: mes, periodoAnio: año }
    });
    
    if (existente)
      throw new NominaException('Nómina ya calculada');
    
    // 2. Obtener datos
    const trabajador = await this.trabajadorService.findOne(trabajadorId);
    const registros = await this.registroHorasService.obtenerPorPeriodo(
      trabajadorId, mes, año
    );
    
    // 3. Calcular usando calculators
    const calculoHoras = this.horaCalculator.agruparYCalcular(registros);
    const calculoDeducciones = this.deduccionCalculator.calcular(
      calculoHoras.subtotalDevengado
    );
    
    // 4. Generar nómina
    const nomina = this.nominaRepository.create({
      trabajadorId,
      periodoMes: mes,
      periodoAnio: año,
      ...calculoHoras,
      ...calculoDeducciones,
      estado: 'procesada',
      fechaProcesamiento: new Date(),
    });
    
    return this.nominaRepository.save(nomina);
  }
  
  async generarPDF(nominaId: number): Promise<Buffer> {
    const nomina = await this.nominaRepository.findOne(nominaId);
    return this.pdfGenerator.generarDesprendible(nomina);
  }
}
```

---

## COMPONENTE FRONTEND: CAPTURA DE HORAS

```typescript
// frontend/src/components/forms/RegistroHorasForm.tsx

export function RegistroHorasForm() {
  const [registros, setRegistros] = useState<RegistroHoras[]>([]);
  const { register, watch, formState: { errors }, reset } = useForm({
    resolver: zodResolver(registroHorasSchema),
  });
  
  const handleAgregarRegistro = async (data) => {
    try {
      const response = await api.post('/registro-horas', data);
      setRegistros([...registros, response.data]);
      reset();
      toast.success('Registro agregado');
    } catch (error) {
      toast.error(error.response.data.message);
    }
  };
  
  return (
    <div className="space-y-4">
      <h2 className="text-2xl font-bold">Registrar Horas</h2>
      
      <form onSubmit={handleSubmit(handleAgregarRegistro)} className="space-y-3">
        <select {...register('trabajadorId')}>
          <option>Seleccionar trabajador</option>
          {/* opciones */}
        </select>
        
        <select {...register('proyectoId')}>
          <option>Seleccionar proyecto</option>
        </select>
        
        <input 
          type="date" 
          {...register('fecha')}
        />
        
        <select {...register('tipoHoraId')}>
          <option>Tipo de hora</option>
          <option value="1">Ordinaria</option>
          <option value="2">Nocturna (35%)</option>
          <option value="3">Extra Diurna (25%)</option>
        </select>
        
        <input 
          type="number" 
          step="0.5"
          placeholder="Cantidad de horas"
          {...register('cantidadHoras')}
        />
        
        {errors.cantidadHoras && (
          <span className="text-red-500">{errors.cantidadHoras.message}</span>
        )}
        
        <button type="submit" className="btn btn-primary">
          Agregar
        </button>
      </form>
      
      {/* Tabla de registros */}
      <table className="w-full">
        <thead>
          <tr>
            <th>Trabajador</th>
            <th>Proyecto</th>
            <th>Fecha</th>
            <th>Tipo Hora</th>
            <th>Cantidad</th>
            <th>Acciones</th>
          </tr>
        </thead>
        <tbody>
          {registros.map(registro => (
            <tr key={registro.id}>
              <td>{registro.trabajador.nombre}</td>
              <td>{registro.proyecto.nombre}</td>
              <td>{formatDate(registro.fecha)}</td>
              <td>{registro.tipoHora.nombre}</td>
              <td>{registro.cantidadHoras}h</td>
              <td>
                <button onClick={() => handleEliminar(registro.id)}>
                  Eliminar
                </button>
              </td>
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  );
}
```

---

## TESTING STRATEGY

### Backend (Jest + Supertest)
```typescript
describe('NominaService', () => {
  let service: NominaService;
  let nominaRepository: Repository<Nomina>;
  
  beforeEach(async () => {
    const module = await Test.createTestingModule({
      providers: [
        NominaService,
        { provide: getRepositoryToken(Nomina), useValue: mockRepository },
      ],
    }).compile();
    
    service = module.get<NominaService>(NominaService);
  });
  
  it('debe calcular nómina correctamente', async () => {
    const resultado = await service.calcularNomina(1, 10, 2026);
    expect(resultado.totalNeto).toBeGreaterThan(0);
  });
  
  it('debe validar que no hay duplicados', async () => {
    await expect(service.calcularNomina(1, 10, 2026))
      .rejects.toThrow('Nómina ya calculada');
  });
});

describe('NominaController (e2e)', () => {
  it('POST /nomina debe retornar 201', async () => {
    const response = await request(app.getHttpServer())
      .post('/nomina')
      .set('Authorization', `Bearer ${token}`)
      .send({ trabajadorId: 1, mes: 10, año: 2026 });
    
    expect(response.status).toBe(201);
  });
});
```

### Frontend (React Testing Library)
```typescript
describe('RegistroHorasForm', () => {
  it('debe validar que horas sea <= 12', async () => {
    render(<RegistroHorasForm />);
    
    const input = screen.getByPlaceholderText(/Cantidad de horas/);
    await userEvent.type(input, '15');
    
    expect(screen.getByText(/máximo 12/i)).toBeInTheDocument();
  });
});
```

---

## DEPLOYMENT

### Backend (NestJS)
```bash
# Build
npm run build

# Run
node dist/main.js

# Docker
docker build -t nomina-backend .
docker run -p 3000:3000 nomina-backend
```

### Frontend (Next.js)
```bash
# Build
npm run build

# Run (SSR)
npm run start

# Export estático
npm run build && npm run export

# Vercel
vercel deploy
```

---

## SEGURIDAD

1. **JWT**: Access token (15 min) + Refresh token (7 días)
2. **HTTPS**: Solo en producción
3. **CORS**: Backend valida origen
4. **Rate Limiting**: Para API endpoints
5. **SQL Injection**: Prevenido por TypeORM
6. **CSRF**: Token CSRF en POST/PUT/DELETE
7. **Auditoría**: Quién cambió qué y cuándo
8. **Encriptación**: Contraseñas con bcrypt

---

## VENTAJAS DE ESTE STACK

✅ **Lenguaje único**: TypeScript en frontend + backend  
✅ **Equipo integrado**: Pueden trabajar en cualquier parte  
✅ **Escalabilidad**: Microservicios futuros (separar módulos)  
✅ **Reutilización**: Tipos compartidos (types/)  
✅ **Testing**: Mismo ecosystem (Jest)  
✅ **Deployment**: Cualquier VPS + Docker  
✅ **DevOps**: CI/CD simple (GitHub Actions, etc)  
✅ **Actualización**: Sin reinstalar (usuarios siempre ven última versión)  
✅ **Móvil futuro**: API REST lista, solo crear app React Native  
✅ **Performance**: Node.js + React = rápido  

---
