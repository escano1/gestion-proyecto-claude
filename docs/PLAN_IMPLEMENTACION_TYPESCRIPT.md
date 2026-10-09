> **Documento histórico de planeación.** El stack implementado (NestJS 12 ESM + Vitest, Next.js 16) y los parámetros legales vigentes (jornada 42 h, dominical 90 %, retención por tabla UVT del art. 383 E.T.) difieren de lo escrito aquí. Fuente de verdad: `.claude/CLAUDE.md`, `docs/API.md` y los README de cada repositorio.

# PLAN DE IMPLEMENTACIÓN - TypeScript Fullstack

**Stack**: NestJS + React + Next.js + PostgreSQL  
**Duración**: 12-14 semanas  
**Lenguaje**: TypeScript (Frontend + Backend)  
**Inicio**: 2026-10-08  

---

## FASE 0: SETUP INICIAL (Semana 1)

### 0.1 Preparar Ambiente
- [ ] Instalar Node.js 18+ + npm/yarn
- [ ] Instalar PostgreSQL 15+ (local o acceso a servidor)
- [ ] Instalar pgAdmin o DBeaver
- [ ] Instalar VSCode + extensiones (ES7+, Prettier, TypeScript)
- [ ] Crear usuario PostgreSQL: `gestion_usuarios`

### 0.2 Crear Repositorio Git
```bash
mkdir gestion-nomina-app
cd gestion-nomina-app
git init
git config user.name "Jhonnier"
git config user.email "jhonnier.tangarife@sofka.com.co"

# .gitignore
cat > .gitignore << EOF
node_modules/
.env.local
.env.*.local
dist/
build/
.next/
out/
*.log
.DS_Store
.vscode/settings.json
EOF

git add .gitignore
git commit -m "Initial commit: .gitignore"
```

### 0.3 Crear Backend (NestJS)
```bash
# Instalar NestJS CLI
npm i -g @nestjs/cli

# Crear proyecto
nest new backend --package-manager npm

cd backend

# Instalar dependencias adicionales
npm install \
  @nestjs/typeorm \
  @nestjs/jwt \
  @nestjs/passport \
  @nestjs/config \
  @nestjs/swagger \
  typeorm \
  pg \
  passport \
  passport-jwt \
  class-validator \
  class-transformer \
  bcrypt \
  pdfkit \
  exceljs \
  winston

npm install --save-dev \
  @types/bcrypt \
  @types/node \
  ts-jest \
  @nestjs/testing
```

### 0.4 Crear Frontend (Next.js)
```bash
cd ..

# Crear Next.js con TypeScript
npx create-next-app@latest frontend \
  --typescript \
  --tailwind \
  --app

cd frontend

# Instalar dependencias adicionales
npm install \
  axios \
  zustand \
  react-hook-form \
  zod \
  @hookform/resolvers \
  react-table \
  recharts \
  @headlessui/react \
  lucide-react \
  jspdf \
  xlsx \
  date-fns

npm install --save-dev \
  @testing-library/react \
  @testing-library/jest-dom \
  jest \
  @types/react \
  @types/node
```

### 0.5 Configurar Docker Compose (PostgreSQL)
```bash
cd ..

cat > docker-compose.yml << 'EOF'
version: '3.8'

services:
  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_USER: gestion_usuarios
      POSTGRES_PASSWORD: tu_contraseña_segura
      POSTGRES_DB: gestion_proyectos_db
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U gestion_usuarios"]
      interval: 10s
      timeout: 5s
      retries: 5

volumes:
  postgres_data:
EOF

# Iniciar servicios
docker-compose up -d
```

### 0.6 Estructura del Proyecto

```
gestion-nomina-app/
├── backend/                 ← NestJS
├── frontend/                ← Next.js
├── docker-compose.yml
├── .gitignore
├── README.md
└── NOTAS_DESARROLLO.md
```

### 0.7 Crear archivos README
```bash
cat > README.md << 'EOF'
# Sistema de Gestión de Proyectos y Nómina

## Stack
- Backend: NestJS + TypeScript
- Frontend: Next.js + React + TailwindCSS
- BD: PostgreSQL

## Instalación Rápida
```bash
docker-compose up -d          # Inicia PostgreSQL
cd backend && npm install     # Backend
cd ../frontend && npm install # Frontend
```

## Desarrollo
```bash
# Terminal 1: Backend
cd backend
npm run start:dev

# Terminal 2: Frontend
cd frontend
npm run dev

# Abre http://localhost:3000
```

## Documentación
- [Backend API](backend/README.md)
- [Frontend](frontend/README.md)
EOF

git add .
git commit -m "feat: Setup inicial NestJS + Next.js"
```

**Fin Fase 0**: Proyecto listo para desarrollar.

---

## FASE 1: BACKEND - MODELOS Y BD (Semanas 2-3)

### 1.1 Configurar TypeORM + PostgreSQL

**backend/src/database/database.module.ts**:
```typescript
import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';
import { ConfigService } from '@nestjs/config';

@Module({
  imports: [
    TypeOrmModule.forRootAsync({
      useFactory: (configService: ConfigService) => ({
        type: 'postgres',
        host: configService.get('DB_HOST', 'localhost'),
        port: configService.get('DB_PORT', 5432),
        username: configService.get('DB_USERNAME', 'gestion_usuarios'),
        password: configService.get('DB_PASSWORD'),
        database: configService.get('DB_NAME', 'gestion_proyectos_db'),
        entities: [__dirname + '/../**/*.entity{.ts,.js}'],
        synchronize: false, // Usar migrations
        migrations: [__dirname + '/migrations/*{.ts,.js}'],
        migrationsRun: true,
        logging: process.env.NODE_ENV === 'development',
      }),
      inject: [ConfigService],
    }),
  ],
})
export class DatabaseModule {}
```

**backend/.env**:
```
NODE_ENV=development
DB_HOST=localhost
DB_PORT=5432
DB_USERNAME=gestion_usuarios
DB_PASSWORD=contraseña_segura
DB_NAME=gestion_proyectos_db
JWT_SECRET=tu_jwt_secret_muy_largo_y_seguro
JWT_EXPIRATION=900 # 15 minutos
```

### 1.2 Crear Entidades TypeORM

**backend/src/modules/trabajador/entities/trabajador.entity.ts**:
```typescript
import {
  Entity,
  PrimaryGeneratedColumn,
  Column,
  OneToMany,
  CreateDateColumn,
  UpdateDateColumn,
} from 'typeorm';
import { AsignacionProyecto } from '../../asignacion-proyecto/entities/asignacion-proyecto.entity';
import { RegistroHoras } from '../../registro-horas/entities/registro-horas.entity';
import { Nomina } from '../../nomina/entities/nomina.entity';

@Entity('trabajador')
export class Trabajador {
  @PrimaryGeneratedColumn()
  id: number;

  @Column({ type: 'varchar', length: 150 })
  nombre: string;

  @Column({ type: 'varchar', length: 50, unique: true })
  documentoIdentidad: string;

  @Column({ type: 'varchar', length: 20 })
  tipoDocumento: string; // CC, CE, PA

  @Column({ type: 'varchar', length: 100 })
  cargo: string;

  @Column({ type: 'decimal', precision: 12, scale: 2 })
  salarioBase: number;

  @Column({ type: 'varchar', length: 20 })
  tipoSalario: string; // mensual, hora

  @Column({ type: 'date' })
  fechaIngreso: Date;

  @Column({ type: 'varchar', length: 20, default: 'activo' })
  estado: string; // activo, inactivo, suspendido

  @Column({ type: 'varchar', length: 50 })
  tipoContrato: string; // indefinido, fijo, obra

  @Column({ type: 'varchar', length: 50 })
  tipoJornada: string; // diurna, nocturna, mixta

  @Column({ type: 'varchar', length: 100, nullable: true })
  eps: string;

  @Column({ type: 'varchar', length: 100, nullable: true })
  afiliacionPension: string;

  @Column({ type: 'varchar', length: 50, nullable: true })
  numeroAfiliacion: string;

  @CreateDateColumn()
  fechaCreacion: Date;

  @UpdateDateColumn()
  fechaUltimaModificacion: Date;

  // Relaciones
  @OneToMany(() => AsignacionProyecto, ap => ap.trabajador)
  asignacionesProyectos: AsignacionProyecto[];

  @OneToMany(() => RegistroHoras, rh => rh.trabajador)
  registrosHoras: RegistroHoras[];

  @OneToMany(() => Nomina, n => n.trabajador)
  nominas: Nomina[];
}
```

[Crear análogamente: Proyecto, AsignacionProyecto, TipoHora, RegistroHoras, Nomina, etc.]

### 1.3 Crear TypeORM Migrations

```bash
cd backend

# Generar migration
npx typeorm migration:generate src/database/migrations/InitialSchema

# Ejecutar (automático en startup)
```

### 1.4 Crear Repositories (TypeORM)

**backend/src/modules/trabajador/repositories/trabajador.repository.ts**:
```typescript
import { Injectable } from '@nestjs/common';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository } from 'typeorm';
import { Trabajador } from '../entities/trabajador.entity';

@Injectable()
export class TrabajadorRepository {
  constructor(
    @InjectRepository(Trabajador)
    private repository: Repository<Trabajador>,
  ) {}

  async findAll(): Promise<Trabajador[]> {
    return this.repository.find();
  }

  async findById(id: number): Promise<Trabajador> {
    return this.repository.findOne({ where: { id } });
  }

  async findByDocumento(documento: string): Promise<Trabajador> {
    return this.repository.findOne({
      where: { documentoIdentidad: documento },
    });
  }

  async findActivos(): Promise<Trabajador[]> {
    return this.repository.find({
      where: { estado: 'activo' },
    });
  }

  async create(trabajador: Trabajador): Promise<Trabajador> {
    return this.repository.save(trabajador);
  }

  async update(id: number, trabajador: Partial<Trabajador>): Promise<Trabajador> {
    await this.repository.update(id, trabajador);
    return this.findById(id);
  }

  async delete(id: number): Promise<void> {
    await this.repository.delete(id);
  }
}
```

**Fin Fase 1**: BD lista, entidades y repositories funcionales.

---

## FASE 2: BACKEND - MÓDULOS BÁSICOS (Semanas 4-5)

### 2.1 Crear Módulo de Autenticación

**backend/src/modules/auth/auth.module.ts**:
```typescript
import { Module } from '@nestjs/common';
import { JwtModule } from '@nestjs/jwt';
import { PassportModule } from '@nestjs/passport';
import { ConfigService } from '@nestjs/config';
import { AuthService } from './auth.service';
import { AuthController } from './auth.controller';
import { JwtStrategy } from './strategies/jwt.strategy';
import { TypeOrmModule } from '@nestjs/typeorm';
import { Usuario } from './entities/usuario.entity';

@Module({
  imports: [
    TypeOrmModule.forFeature([Usuario]),
    PassportModule,
    JwtModule.registerAsync({
      useFactory: (configService: ConfigService) => ({
        secret: configService.get<string>('JWT_SECRET'),
        signOptions: {
          expiresIn: configService.get<string>('JWT_EXPIRATION'),
        },
      }),
      inject: [ConfigService],
    }),
  ],
  providers: [AuthService, JwtStrategy],
  controllers: [AuthController],
  exports: [AuthService],
})
export class AuthModule {}
```

**backend/src/modules/auth/auth.service.ts**:
```typescript
import { Injectable, UnauthorizedException } from '@nestjs/common';
import { JwtService } from '@nestjs/jwt';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository } from 'typeorm';
import * as bcrypt from 'bcrypt';
import { Usuario } from './entities/usuario.entity';
import { LoginDto } from './dto/login.dto';

@Injectable()
export class AuthService {
  constructor(
    private jwtService: JwtService,
    @InjectRepository(Usuario)
    private usuarioRepository: Repository<Usuario>,
  ) {}

  async login(loginDto: LoginDto) {
    const usuario = await this.usuarioRepository.findOne({
      where: { email: loginDto.email },
    });

    if (!usuario) {
      throw new UnauthorizedException('Credenciales inválidas');
    }

    const isPasswordValid = await bcrypt.compare(
      loginDto.password,
      usuario.passwordHash,
    );

    if (!isPasswordValid) {
      throw new UnauthorizedException('Credenciales inválidas');
    }

    const payload = {
      sub: usuario.id,
      email: usuario.email,
      rol: usuario.rol,
    };

    return {
      access_token: this.jwtService.sign(payload),
      usuario: {
        id: usuario.id,
        nombre: usuario.nombre,
        email: usuario.email,
        rol: usuario.rol,
      },
    };
  }

  async register(email: string, password: string, nombre: string) {
    const hashedPassword = await bcrypt.hash(password, 10);
    const usuario = this.usuarioRepository.create({
      email,
      nombre,
      passwordHash: hashedPassword,
      rol: 'consulta', // rol default
    });
    return this.usuarioRepository.save(usuario);
  }

  validateToken(token: string) {
    try {
      return this.jwtService.verify(token);
    } catch {
      throw new UnauthorizedException('Token inválido');
    }
  }
}
```

**backend/src/modules/auth/auth.controller.ts**:
```typescript
import { Controller, Post, Body } from '@nestjs/common';
import { AuthService } from './auth.service';
import { LoginDto } from './dto/login.dto';

@Controller('api/auth')
export class AuthController {
  constructor(private authService: AuthService) {}

  @Post('login')
  async login(@Body() loginDto: LoginDto) {
    return this.authService.login(loginDto);
  }

  @Post('register')
  async register(@Body() body: { email: string; password: string; nombre: string }) {
    return this.authService.register(body.email, body.password, body.nombre);
  }
}
```

### 2.2 Crear Guards y Decoradores

**backend/src/common/guards/jwt-auth.guard.ts**:
```typescript
import { Injectable } from '@nestjs/common';
import { AuthGuard } from '@nestjs/passport';

@Injectable()
export class JwtAuthGuard extends AuthGuard('jwt') {}
```

**backend/src/common/decorators/roles.decorator.ts**:
```typescript
import { SetMetadata } from '@nestjs/common';

export const Roles = (...roles: string[]) => SetMetadata('roles', roles);
```

**backend/src/common/guards/roles.guard.ts**:
```typescript
import { Injectable, CanActivate, ExecutionContext } from '@nestjs/common';
import { Reflector } from '@nestjs/core';

@Injectable()
export class RolesGuard implements CanActivate {
  constructor(private reflector: Reflector) {}

  canActivate(context: ExecutionContext): boolean {
    const requiredRoles = this.reflector.get<string[]>(
      'roles',
      context.getHandler(),
    );

    if (!requiredRoles) return true;

    const request = context.switchToHttp().getRequest();
    const usuario = request.user;

    return requiredRoles.includes(usuario.rol);
  }
}
```

### 2.3 Crear Módulos CRUD Básicos

**backend/src/modules/trabajador/trabajador.module.ts**:
```typescript
import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';
import { Trabajador } from './entities/trabajador.entity';
import { TrabajadorService } from './trabajador.service';
import { TrabajadorController } from './trabajador.controller';
import { TrabajadorRepository } from './repositories/trabajador.repository';

@Module({
  imports: [TypeOrmModule.forFeature([Trabajador])],
  providers: [TrabajadorService, TrabajadorRepository],
  controllers: [TrabajadorController],
  exports: [TrabajadorService],
})
export class TrabajadorModule {}
```

**backend/src/modules/trabajador/trabajador.service.ts**:
```typescript
import { Injectable, BadRequestException } from '@nestjs/common';
import { TrabajadorRepository } from './repositories/trabajador.repository';
import { Trabajador } from './entities/trabajador.entity';
import { CreateTrabajadorDto } from './dto/create-trabajador.dto';
import { UpdateTrabajadorDto } from './dto/update-trabajador.dto';

@Injectable()
export class TrabajadorService {
  constructor(private repository: TrabajadorRepository) {}

  async findAll(): Promise<Trabajador[]> {
    return this.repository.findAll();
  }

  async findById(id: number): Promise<Trabajador> {
    const trabajador = await this.repository.findById(id);
    if (!trabajador) {
      throw new BadRequestException(`Trabajador ${id} no encontrado`);
    }
    return trabajador;
  }

  async create(dto: CreateTrabajadorDto): Promise<Trabajador> {
    if (dto.salarioBase <= 0) {
      throw new BadRequestException('El salario debe ser mayor a 0');
    }

    const existente = await this.repository.findByDocumento(
      dto.documentoIdentidad,
    );
    if (existente) {
      throw new BadRequestException('El documento ya está registrado');
    }

    return this.repository.create(dto as Trabajador);
  }

  async update(id: number, dto: UpdateTrabajadorDto): Promise<Trabajador> {
    await this.findById(id); // Validar existencia
    return this.repository.update(id, dto);
  }

  async delete(id: number): Promise<void> {
    await this.findById(id);
    await this.repository.delete(id);
  }

  async desactivar(id: number): Promise<Trabajador> {
    return this.repository.update(id, { estado: 'inactivo' as any });
  }
}
```

**backend/src/modules/trabajador/trabajador.controller.ts**:
```typescript
import {
  Controller,
  Get,
  Post,
  Put,
  Delete,
  Param,
  Body,
  UseGuards,
} from '@nestjs/common';
import { JwtAuthGuard } from '../../common/guards/jwt-auth.guard';
import { Roles } from '../../common/decorators/roles.decorator';
import { RolesGuard } from '../../common/guards/roles.guard';
import { TrabajadorService } from './trabajador.service';
import { CreateTrabajadorDto } from './dto/create-trabajador.dto';
import { UpdateTrabajadorDto } from './dto/update-trabajador.dto';

@Controller('api/trabajadores')
@UseGuards(JwtAuthGuard)
export class TrabajadorController {
  constructor(private service: TrabajadorService) {}

  @Get()
  findAll() {
    return this.service.findAll();
  }

  @Get(':id')
  findById(@Param('id') id: number) {
    return this.service.findById(id);
  }

  @Post()
  @UseGuards(RolesGuard)
  @Roles('admin', 'gestor_proyectos')
  create(@Body() dto: CreateTrabajadorDto) {
    return this.service.create(dto);
  }

  @Put(':id')
  @UseGuards(RolesGuard)
  @Roles('admin', 'gestor_proyectos')
  update(@Param('id') id: number, @Body() dto: UpdateTrabajadorDto) {
    return this.service.update(id, dto);
  }

  @Delete(':id')
  @UseGuards(RolesGuard)
  @Roles('admin')
  delete(@Param('id') id: number) {
    return this.service.delete(id);
  }
}
```

**Fin Fase 2**: Autenticación + CRUD básico funcionando.

---

## FASE 3: BACKEND - REGISTRO DE HORAS (Semanas 6-7) ← CRÍTICA

### 3.1 Crear Validador de Registro de Horas

**backend/src/modules/registro-horas/dto/create-registro-horas.dto.ts**:
```typescript
import { IsInt, IsNotEmpty, IsPositive, Max, Min, IsISO8601 } from 'class-validator';

export class CreateRegistroHorasDto {
  @IsInt()
  @IsNotEmpty()
  trabajadorId: number;

  @IsInt()
  @IsNotEmpty()
  proyectoId: number;

  @IsISO8601()
  fecha: string;

  @IsInt()
  @IsNotEmpty()
  tipoHoraId: number;

  @IsPositive()
  @Max(12, { message: 'Máximo 12 horas por día' })
  cantidadHoras: number;
}
```

**backend/src/modules/registro-horas/validators/registro-horas.validator.ts**:
```typescript
import { Injectable, BadRequestException } from '@nestjs/common';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository } from 'typeorm';
import { RegistroHoras } from '../entities/registro-horas.entity';
import { CreateRegistroHorasDto } from '../dto/create-registro-horas.dto';

@Injectable()
export class RegistroHorasValidator {
  constructor(
    @InjectRepository(RegistroHoras)
    private repository: Repository<RegistroHoras>,
  ) {}

  async validarDuplicado(
    dto: CreateRegistroHorasDto,
  ): Promise<void> {
    const existe = await this.repository.findOne({
      where: {
        trabajadorId: dto.trabajadorId,
        proyectoId: dto.proyectoId,
        fecha: new Date(dto.fecha),
        tipoHoraId: dto.tipoHoraId,
      },
    });

    if (existe) {
      throw new BadRequestException(
        'Ya existe un registro para esta combinación',
      );
    }
  }

  async validarLimiteDiario(
    trabajadorId: number,
    fecha: Date,
    horasNuevas: number,
  ): Promise<void> {
    const registros = await this.repository.find({
      where: {
        trabajadorId,
        fecha: fecha,
      },
    });

    const totalActual = registros.reduce((sum, r) => sum + r.cantidadHoras, 0);

    if (totalActual + horasNuevas > 12) {
      throw new BadRequestException(
        `Exceso de horas diarias. Máximo 12h. Actual: ${totalActual}h + ${horasNuevas}h`,
      );
    }
  }

  async validarLimiteSemanal(
    trabajadorId: number,
    fecha: Date,
    horasExtrasNuevas: number,
  ): Promise<void> {
    const inicioSemana = new Date(fecha);
    inicioSemana.setDate(fecha.getDate() - fecha.getDay());

    const finSemana = new Date(inicioSemana);
    finSemana.setDate(inicioSemana.getDate() + 7);

    const registrosExtras = await this.repository
      .createQueryBuilder('r')
      .leftJoinAndSelect('r.tipoHora', 'th')
      .where('r.trabajadorId = :trabajadorId', { trabajadorId })
      .andWhere('r.fecha >= :inicioSemana', { inicioSemana })
      .andWhere('r.fecha < :finSemana', { finSemana })
      .andWhere('th.esExtra = :esExtra', { esExtra: true })
      .getMany();

    const totalExtrasActuales = registrosExtras.reduce(
      (sum, r) => sum + r.cantidadHoras,
      0,
    );

    if (totalExtrasActuales + horasExtrasNuevas > 80) {
      console.warn(
        `ALERTA: Trabajador ${trabajadorId} ` +
        `supera límite de extras semanales (80h)`,
      );
    }
  }
}
```

**backend/src/modules/registro-horas/registro-horas.service.ts**:
```typescript
import { Injectable, BadRequestException } from '@nestjs/common';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository } from 'typeorm';
import { RegistroHoras } from './entities/registro-horas.entity';
import { CreateRegistroHorasDto } from './dto/create-registro-horas.dto';
import { RegistroHorasValidator } from './validators/registro-horas.validator';
import { AuditoriaService } from '../auditoria/auditoria.service';

@Injectable()
export class RegistroHorasService {
  constructor(
    @InjectRepository(RegistroHoras)
    private repository: Repository<RegistroHoras>,
    private validator: RegistroHorasValidator,
    private auditoriaService: AuditoriaService,
  ) {}

  async crear(
    dto: CreateRegistroHorasDto,
    usuarioId: number,
  ): Promise<RegistroHoras> {
    // Validaciones
    await this.validator.validarDuplicado(dto);

    const fecha = new Date(dto.fecha);
    await this.validator.validarLimiteDiario(
      dto.trabajadorId,
      fecha,
      dto.cantidadHoras,
    );

    // Validar límite semanal (solo alerta)
    await this.validator.validarLimiteSemanal(
      dto.trabajadorId,
      fecha,
      dto.cantidadHoras,
    );

    const registro = this.repository.create({
      ...dto,
      fecha,
      creadoPorId: usuarioId,
    });

    const resultado = await this.repository.save(registro);

    // Auditoría
    await this.auditoriaService.crear({
      tablaNombre: 'registro_horas',
      registroId: resultado.id,
      accion: 'INSERT',
      datosNuevos: resultado,
      usuarioId,
    });

    return resultado;
  }

  async obtenerPorPeriodo(
    trabajadorId: number,
    mes: number,
    año: number,
  ): Promise<RegistroHoras[]> {
    return this.repository
      .createQueryBuilder('r')
      .where('r.trabajadorId = :trabajadorId', { trabajadorId })
      .andWhere('EXTRACT(MONTH FROM r.fecha) = :mes', { mes })
      .andWhere('EXTRACT(YEAR FROM r.fecha) = :año', { año })
      .leftJoinAndSelect('r.tipoHora', 'th')
      .orderBy('r.fecha', 'ASC')
      .getMany();
  }

  async actualizar(
    id: number,
    dto: Partial<CreateRegistroHorasDto>,
    usuarioId: number,
  ): Promise<RegistroHoras> {
    const anterior = await this.repository.findOne({ where: { id } });

    if (!anterior) {
      throw new BadRequestException('Registro no encontrado');
    }

    await this.repository.update(id, {
      ...dto,
      modificadoPorId: usuarioId,
      fechaUltimaModificacion: new Date(),
    });

    const actualizado = await this.repository.findOne({ where: { id } });

    await this.auditoriaService.crear({
      tablaNombre: 'registro_horas',
      registroId: id,
      accion: 'UPDATE',
      datosAnteriores: anterior,
      datosNuevos: actualizado,
      usuarioId,
    });

    return actualizado;
  }

  async eliminar(id: number, usuarioId: number): Promise<void> {
    const registro = await this.repository.findOne({ where: { id } });

    if (!registro) {
      throw new BadRequestException('Registro no encontrado');
    }

    await this.auditoriaService.crear({
      tablaNombre: 'registro_horas',
      registroId: id,
      accion: 'DELETE',
      datosAnteriores: registro,
      usuarioId,
    });

    await this.repository.delete(id);
  }
}
```

**backend/src/modules/registro-horas/registro-horas.controller.ts**:
```typescript
import {
  Controller,
  Post,
  Get,
  Put,
  Delete,
  Body,
  Param,
  Query,
  UseGuards,
  Request,
} from '@nestjs/common';
import { JwtAuthGuard } from '../../common/guards/jwt-auth.guard';
import { RegistroHorasService } from './registro-horas.service';
import { CreateRegistroHorasDto } from './dto/create-registro-horas.dto';

@Controller('api/registro-horas')
@UseGuards(JwtAuthGuard)
export class RegistroHorasController {
  constructor(private service: RegistroHorasService) {}

  @Post()
  crear(@Body() dto: CreateRegistroHorasDto, @Request() req) {
    return this.service.crear(dto, req.user.id);
  }

  @Get('periodo')
  obtenerPorPeriodo(
    @Query('trabajadorId') trabajadorId: number,
    @Query('mes') mes: number,
    @Query('año') año: number,
  ) {
    return this.service.obtenerPorPeriodo(trabajadorId, mes, año);
  }

  @Put(':id')
  actualizar(
    @Param('id') id: number,
    @Body() dto: Partial<CreateRegistroHorasDto>,
    @Request() req,
  ) {
    return this.service.actualizar(id, dto, req.user.id);
  }

  @Delete(':id')
  eliminar(@Param('id') id: number, @Request() req) {
    return this.service.eliminar(id, req.user.id);
  }
}
```

### 3.2 Tests para Registro de Horas

**backend/src/modules/registro-horas/registro-horas.service.spec.ts**:
```typescript
import { Test, TestingModule } from '@nestjs/testing';
import { getRepositoryToken } from '@nestjs/typeorm';
import { RegistroHorasService } from './registro-horas.service';
import { RegistroHoras } from './entities/registro-horas.entity';
import { RegistroHorasValidator } from './validators/registro-horas.validator';
import { AuditoriaService } from '../auditoria/auditoria.service';

describe('RegistroHorasService', () => {
  let service: RegistroHorasService;

  beforeEach(async () => {
    const module: TestingModule = await Test.createTestingModule({
      providers: [
        RegistroHorasService,
        {
          provide: getRepositoryToken(RegistroHoras),
          useValue: {
            create: jest.fn(),
            save: jest.fn(),
            find: jest.fn(),
            findOne: jest.fn(),
            update: jest.fn(),
            delete: jest.fn(),
            createQueryBuilder: jest.fn(),
          },
        },
        {
          provide: RegistroHorasValidator,
          useValue: {
            validarDuplicado: jest.fn(),
            validarLimiteDiario: jest.fn(),
            validarLimiteSemanal: jest.fn(),
          },
        },
        {
          provide: AuditoriaService,
          useValue: {
            crear: jest.fn(),
          },
        },
      ],
    }).compile();

    service = module.get<RegistroHorasService>(RegistroHorasService);
  });

  it('debe crear un registro válido', async () => {
    const dto = {
      trabajadorId: 1,
      proyectoId: 1,
      fecha: '2026-10-15',
      tipoHoraId: 1,
      cantidadHoras: 8,
    };

    const resultado = await service.crear(dto, 1);
    expect(resultado).toBeDefined();
  });

  it('debe rechazar registro con > 12 horas/día', async () => {
    // Implementar test
  });

  it('debe alertar sobre exceso de extras semanales', async () => {
    // Implementar test
  });
});
```

**Fin Fase 3**: Registro de horas robusto, auditoría funcionando.

---

## FASE 4: BACKEND - NÓMINA (Semanas 8-10) ← CRÍTICA

### 4.1 Crear Calculadores de Nómina

**backend/src/modules/nomina/calculators/hora.calculator.ts**:
```typescript
import { Injectable } from '@nestjs/common';
import { RegistroHoras } from '../../registro-horas/entities/registro-horas.entity';

interface CalculoHoras {
  totalHorasOrdinarias: number;
  totalHorasNocturnas: number;
  totalHorasExtraDiurnas: number;
  totalHorasExtraNocturnas: number;
  totalHorasDominicales: number;
  valorOrdinarias: number;
  valorNocturnas: number;
  valorExtras: number;
  valorDominicales: number;
  subtotalDevengado: number;
}

@Injectable()
export class HoraCalculator {
  private readonly HORAS_POR_MES = 240; // 30 días × 8 horas

  calcular(
    registros: RegistroHoras[],
    salarioBase: number,
  ): CalculoHoras {
    const valorHora = salarioBase / this.HORAS_POR_MES;

    // Agrupar por tipo
    const agrupacion = this.agrupar(registros);

    // Calcular valores
    const totalHorasOrdinarias = agrupacion['Ordinaria'] || 0;
    const valorOrdinarias = totalHorasOrdinarias * valorHora;

    const totalHorasNocturnas = agrupacion['Nocturna'] || 0;
    const valorNocturnas = totalHorasNocturnas * valorHora * 1.35; // +35%

    const totalHorasExtraDiurnas = agrupacion['Extra Diurna'] || 0;
    const valorExtras = totalHorasExtraDiurnas * valorHora * 1.25; // +25%

    const totalHorasExtraNocturnas = agrupacion['Extra Nocturna'] || 0;
    const valorExtraNocturna =
      totalHorasExtraNocturnas * valorHora * 1.75; // +75% (35%+25%+15%)

    const totalHorasDominicales = agrupacion['Dominical'] || 0;
    const valorDominicales = totalHorasDominicales * valorHora * 1.75; // +75%

    const subtotalDevengado =
      valorOrdinarias +
      valorNocturnas +
      valorExtras +
      valorExtraNocturna +
      valorDominicales;

    return {
      totalHorasOrdinarias,
      totalHorasNocturnas,
      totalHorasExtraDiurnas,
      totalHorasExtraNocturnas,
      totalHorasDominicales,
      valorOrdinarias,
      valorNocturnas,
      valorExtras: valorExtras + valorExtraNocturna,
      valorDominicales,
      subtotalDevengado,
    };
  }

  private agrupar(registros: RegistroHoras[]): Record<string, number> {
    return registros.reduce((acc, r) => {
      const tipoNombre = r.tipoHora.nombre;
      acc[tipoNombre] = (acc[tipoNombre] || 0) + r.cantidadHoras;
      return acc;
    }, {} as Record<string, number>);
  }
}
```

**backend/src/modules/nomina/calculators/deduccion.calculator.ts**:
```typescript
import { Injectable } from '@nestjs/common';

interface Deducciones {
  deduccionSalud: number;
  deduccionPension: number;
  deduccionRetencion: number;
  totalDeducciones: number;
}

@Injectable()
export class DeduccionCalculator {
  calcular(subtotalDevengado: number): Deducciones {
    // Deducciones Colombia 2026
    const deduccionSalud = subtotalDevengado * 0.04; // 4%
    const deduccionPension = subtotalDevengado * 0.04; // 4%
    const deduccionRetencion = this.calcularRetencionEnLaFuente(
      subtotalDevengado,
    );

    return {
      deduccionSalud,
      deduccionPension,
      deduccionRetencion,
      totalDeducciones:
        deduccionSalud + deduccionPension + deduccionRetencion,
    };
  }

  private calcularRetencionEnLaFuente(ingresoBase: number): number {
    // Tabla simplificada Colombia 2026
    if (ingresoBase <= 919900) return 0;
    if (ingresoBase <= 1910000)
      return (ingresoBase - 919900) * 0.05; // 5%
    if (ingresoBase <= 2865000)
      return 495050 + (ingresoBase - 1910000) * 0.1; // 10%
    if (ingresoBase <= 3814625)
      return 591550 + (ingresoBase - 2865000) * 0.15; // 15%

    return 733643.75 + (ingresoBase - 3814625) * 0.2; // 20%
  }
}
```

### 4.2 Crear Servicio de Nómina

**backend/src/modules/nomina/nomina.service.ts**:
```typescript
import { Injectable, BadRequestException } from '@nestjs/common';
import { InjectRepository } from '@nestjs/typeorm';
import { Repository } from 'typeorm';
import { Nomina } from './entities/nomina.entity';
import { DetalleNomina } from './entities/detalle-nomina.entity';
import { RegistroHorasService } from '../registro-horas/registro-horas.service';
import { TrabajadorService } from '../trabajador/trabajador.service';
import { HoraCalculator } from './calculators/hora.calculator';
import { DeduccionCalculator } from './calculators/deduccion.calculator';
import { PDFGeneratorService } from './services/pdf-generator.service';
import { ExcelGeneratorService } from './services/excel-generator.service';
import { AuditoriaService } from '../auditoria/auditoria.service';

@Injectable()
export class NominaService {
  constructor(
    @InjectRepository(Nomina)
    private nominaRepository: Repository<Nomina>,
    @InjectRepository(DetalleNomina)
    private detalleNominaRepository: Repository<DetalleNomina>,
    private registroHorasService: RegistroHorasService,
    private trabajadorService: TrabajadorService,
    private horaCalculator: HoraCalculator,
    private deduccionCalculator: DeduccionCalculator,
    private pdfGenerator: PDFGeneratorService,
    private excelGenerator: ExcelGeneratorService,
    private auditoriaService: AuditoriaService,
  ) {}

  async calcularNomina(
    trabajadorId: number,
    mes: number,
    año: number,
    usuarioId: number,
  ): Promise<Nomina> {
    // VALIDACIÓN 1: No existe nómina anterior
    const existente = await this.nominaRepository.findOne({
      where: {
        trabajadorId,
        periodoMes: mes,
        periodoAnio: año,
      },
    });

    if (existente) {
      throw new BadRequestException('Nómina para este período ya existe');
    }

    // VALIDACIÓN 2: Trabajador existe y está activo
    const trabajador = await this.trabajadorService.findById(trabajadorId);

    if (trabajador.estado !== 'activo') {
      throw new BadRequestException('El trabajador debe estar activo');
    }

    // PASO 3: Obtener registros de horas
    const registros = await this.registroHorasService.obtenerPorPeriodo(
      trabajadorId,
      mes,
      año,
    );

    if (!registros.length) {
      throw new BadRequestException(
        'No hay registros de horas para este período',
      );
    }

    // PASO 4-7: Calcular nómina
    const calculoHoras = this.horaCalculator.calcular(
      registros,
      trabajador.salarioBase,
    );

    const deducciones = this.deduccionCalculator.calcular(
      calculoHoras.subtotalDevengado,
    );

    // PASO 8: Crear objeto Nómina
    const nomina = this.nominaRepository.create({
      trabajadorId,
      periodoMes: mes,
      periodoAnio: año,
      fechaInicio: new Date(año, mes - 1, 1),
      fechaFin: new Date(año, mes, 0),
      salarioBase: trabajador.salarioBase,
      ...calculoHoras,
      ...deducciones,
      totalNeto:
        calculoHoras.subtotalDevengado - deducciones.totalDeducciones,
      estado: 'procesada',
      fechaProcesamiento: new Date(),
      procesadoPorId: usuarioId,
    });

    const nominaGuardada = await this.nominaRepository.save(nomina);

    // PASO 9: Crear detalles
    const detalles = this.generarDetalles(nominaGuardada);
    for (const detalle of detalles) {
      await this.detalleNominaRepository.save(detalle);
    }

    // PASO 10: Auditoría
    await this.auditoriaService.crear({
      tablaNombre: 'nomina',
      registroId: nominaGuardada.id,
      accion: 'INSERT',
      datosNuevos: nominaGuardada,
      usuarioId,
    });

    return nominaGuardada;
  }

  async obtenerNomina(id: number): Promise<Nomina> {
    const nomina = await this.nominaRepository.findOne({
      where: { id },
      relations: ['detalles', 'trabajador'],
    });

    if (!nomina) {
      throw new BadRequestException('Nómina no encontrada');
    }

    return nomina;
  }

  async generarPDF(id: number): Promise<Buffer> {
    const nomina = await this.obtenerNomina(id);
    return this.pdfGenerator.generar(nomina);
  }

  async generarExcel(id: number): Promise<Buffer> {
    const nomina = await this.obtenerNomina(id);
    return this.excelGenerator.generar(nomina);
  }

  private generarDetalles(nomina: Nomina): DetalleNomina[] {
    const detalles: DetalleNomina[] = [];

    // Ingresos
    if (nomina.totalHorasOrdinarias > 0) {
      detalles.push(
        this.detalleNominaRepository.create({
          nominaId: nomina.id,
          tipoConcepto: 'ingreso',
          descripcion: 'Horas Ordinarias',
          cantidad: nomina.totalHorasOrdinarias,
          valor: nomina.valorOrdinarias / nomina.totalHorasOrdinarias,
          subtotal: nomina.valorOrdinarias,
        }),
      );
    }

    if (nomina.totalHorasNocturnas > 0) {
      detalles.push(
        this.detalleNominaRepository.create({
          nominaId: nomina.id,
          tipoConcepto: 'ingreso',
          descripcion: 'Horas Nocturnas (35%)',
          cantidad: nomina.totalHorasNocturnas,
          valor: nomina.valorNocturnas / nomina.totalHorasNocturnas,
          subtotal: nomina.valorNocturnas,
        }),
      );
    }

    // Deducciones
    detalles.push(
      this.detalleNominaRepository.create({
        nominaId: nomina.id,
        tipoConcepto: 'deduccion',
        descripcion: 'Aporte Salud (4%)',
        cantidad: 1,
        valor: nomina.deduccionSalud,
        subtotal: nomina.deduccionSalud,
      }),
    );

    detalles.push(
      this.detalleNominaRepository.create({
        nominaId: nomina.id,
        tipoConcepto: 'deduccion',
        descripcion: 'Aporte Pensión (4%)',
        cantidad: 1,
        valor: nomina.deduccionPension,
        subtotal: nomina.deduccionPension,
      }),
    );

    return detalles;
  }
}
```

### 4.3 Tests de Nómina (50+ tests)

**backend/src/modules/nomina/nomina.service.spec.ts**:
```typescript
import { Test, TestingModule } from '@nestjs/testing';
import { NominaService } from './nomina.service';

describe('NominaService', () => {
  let service: NominaService;

  beforeEach(async () => {
    // Setup...
  });

  // Tests de validaciones
  describe('Validaciones', () => {
    it('debe rechazar nómina duplicada', async () => {
      // Test
    });

    it('debe rechazar si trabajador está inactivo', async () => {
      // Test
    });

    it('debe rechazar si no hay registros de horas', async () => {
      // Test
    });
  });

  // Tests de cálculos
  describe('Cálculos de Horas', () => {
    it('debe calcular horas ordinarias correctamente', async () => {
      // 240 horas/mes = 10,000 por hora
      // 160 horas ordinarias = $1,600,000
    });

    it('debe aplicar recargo nocturno 35%', async () => {
      // 10,000 × 1.35 = 13,500
    });

    it('debe aplicar recargo extra 25%', async () => {
      // 10,000 × 1.25 = 12,500
    });

    it('debe aplicar recargo dominical 75%', async () => {
      // 10,000 × 1.75 = 17,500
    });

    it('debe aplicar recargo extra nocturno 75%', async () => {
      // (35% + 25% + 15% acumulativo) = 75%
    });
  });

  // Tests de deducciones
  describe('Deducciones', () => {
    it('debe deducir salud 4%', async () => {
      // Subtotal × 0.04
    });

    it('debe deducir pensión 4%', async () => {
      // Subtotal × 0.04
    });

    describe('Retención en la Fuente', () => {
      it('ingresos <= 919,900: retención = 0', async () => {
        // Test
      });

      it('ingresos 919,900 - 1,910,000: retención 5%', async () => {
        // Test
      });

      it('ingresos 1,910,000 - 2,865,000: retención 10%', async () => {
        // Test
      });

      it('ingresos 2,865,000 - 3,814,625: retención 15%', async () => {
        // Test
      });

      it('ingresos > 3,814,625: retención 20%', async () => {
        // Test
      });
    });
  });

  // Tests de casos reales
  describe('Casos Reales Colombianos', () => {
    it('debe calcular correctamente salario mínimo legal (SMMLV)', async () => {
      // SMMLV 2026 ≈ $1,387,526
      // 240 horas = $5,781.35 por hora
    });

    it('debe calcular correctamente para profesional senior', async () => {
      // Salario: $5,000,000
      // Cálculos completos con todos los recargos
    });

    it('debe generar nómina con múltiples tipos de hora', async () => {
      // Mezcla ordinarias, nocturnas, extras, dominicales
    });
  });

  // Tests de salida
  describe('Generación de Desprendibles', () => {
    it('debe generar PDF válido', async () => {
      // Verificar buffer no es null
    });

    it('debe generar Excel válido', async () => {
      // Verificar buffer no es null
    });
  });
});
```

**Fin Fase 4**: Motor de nómina robusto, 50+ tests pasando.

---

## FASE 5: FRONTEND - UI CON NEXT.JS + REACT (Semanas 11-12)

### 5.1 Estructura del Frontend

```bash
frontend/
├── src/
│   ├── app/
│   │   ├── layout.tsx
│   │   ├── page.tsx              # Home
│   │   ├── (auth)/
│   │   │   └── login/page.tsx
│   │   └── (dashboard)/
│   │       ├── layout.tsx         # Sidebar + navbar
│   │       ├── dashboard/page.tsx
│   │       ├── trabajadores/page.tsx
│   │       ├── registro-horas/page.tsx
│   │       └── nomina/page.tsx
│   │
│   ├── components/
│   │   ├── forms/RegistroHorasForm.tsx
│   │   ├── tables/TrabajadoresTable.tsx
│   │   └── ...
│   │
│   ├── hooks/
│   │   ├── useAuth.ts
│   │   ├── useApi.ts
│   │   └── ...
│   │
│   ├── lib/
│   │   ├── api.ts
│   │   └── formatter.ts
│   │
│   └── store/
│       └── useAuthStore.ts
│
└── package.json
```

### 5.2 API Cliente

**frontend/src/lib/api.ts**:
```typescript
import axios from 'axios';

const API_URL = process.env.NEXT_PUBLIC_API_URL || 'http://localhost:3000/api';

const api = axios.create({
  baseURL: API_URL,
  timeout: 10000,
});

// Interceptor: Añadir JWT
api.interceptors.request.use((config) => {
  const token = localStorage.getItem('access_token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

// Interceptor: Manejo de errores
api.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      // Redirigir a login
      window.location.href = '/login';
    }
    return Promise.reject(error);
  },
);

export default api;
```

### 5.3 Página de Registro de Horas

**frontend/src/app/(dashboard)/registro-horas/page.tsx**:
```typescript
'use client';

import { useState } from 'react';
import { RegistroHorasForm } from '@/components/forms/RegistroHorasForm';
import { RegistroHorasTable } from '@/components/tables/RegistroHorasTable';

export default function RegistroHorasPage() {
  const [refresh, setRefresh] = useState(0);

  const handleRegistroAgregado = () => {
    setRefresh(prev => prev + 1);
  };

  return (
    <div className="space-y-6">
      <h1 className="text-3xl font-bold">Registrar Horas</h1>

      <div className="grid grid-cols-2 gap-6">
        <div>
          <RegistroHorasForm onSuccess={handleRegistroAgregado} />
        </div>
        <div>
          {/* Info, alertas, etc */}
        </div>
      </div>

      <RegistroHorasTable key={refresh} />
    </div>
  );
}
```

**frontend/src/components/forms/RegistroHorasForm.tsx**:
```typescript
'use client';

import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';
import api from '@/lib/api';
import { toast } from 'react-hot-toast';
import { useState, useEffect } from 'react';

const schema = z.object({
  trabajadorId: z.number().min(1),
  proyectoId: z.number().min(1),
  fecha: z.string().refine(date => new Date(date) <= new Date()),
  tipoHoraId: z.number().min(1),
  cantidadHoras: z.number().min(0.5).max(12),
});

type RegistroHorasFormData = z.infer<typeof schema>;

interface RegistroHorasFormProps {
  onSuccess?: () => void;
}

export function RegistroHorasForm({ onSuccess }: RegistroHorasFormProps) {
  const [trabajadores, setTrabajadores] = useState([]);
  const [proyectos, setProyectos] = useState([]);
  const [tiposHora, setTiposHora] = useState([]);
  const [loading, setLoading] = useState(false);

  const {
    register,
    formState: { errors },
    handleSubmit,
    reset,
  } = useForm<RegistroHorasFormData>({
    resolver: zodResolver(schema),
  });

  useEffect(() => {
    cargarDatos();
  }, []);

  const cargarDatos = async () => {
    try {
      const [trabajResult, proyResult, tiposResult] = await Promise.all([
        api.get('/trabajadores'),
        api.get('/proyectos'),
        api.get('/tipo-hora'),
      ]);

      setTrabajadores(trabajResult.data);
      setProyectos(proyResult.data);
      setTiposHora(tiposResult.data);
    } catch (error) {
      toast.error('Error al cargar datos');
    }
  };

  const onSubmit = async (data: RegistroHorasFormData) => {
    setLoading(true);
    try {
      await api.post('/registro-horas', data);
      toast.success('Registro agregado exitosamente');
      reset();
      onSuccess?.();
    } catch (error: any) {
      toast.error(
        error.response?.data?.message || 'Error al registrar horas',
      );
    } finally {
      setLoading(false);
    }
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)} className="space-y-4 p-6 bg-white rounded-lg shadow">
      <h2 className="text-2xl font-bold">Registrar Horas</h2>

      <div>
        <label className="block font-medium mb-2">Trabajador</label>
        <select
          {...register('trabajadorId', {
            valueAsNumber: true,
          })}
          className="w-full border rounded px-3 py-2"
        >
          <option value="">Seleccionar</option>
          {trabajadores.map((t: any) => (
            <option key={t.id} value={t.id}>
              {t.nombre}
            </option>
          ))}
        </select>
        {errors.trabajadorId && (
          <span className="text-red-500 text-sm">
            {errors.trabajadorId.message}
          </span>
        )}
      </div>

      <div>
        <label className="block font-medium mb-2">Proyecto</label>
        <select
          {...register('proyectoId', {
            valueAsNumber: true,
          })}
          className="w-full border rounded px-3 py-2"
        >
          <option value="">Seleccionar</option>
          {proyectos.map((p: any) => (
            <option key={p.id} value={p.id}>
              {p.nombre}
            </option>
          ))}
        </select>
        {errors.proyectoId && (
          <span className="text-red-500 text-sm">
            {errors.proyectoId.message}
          </span>
        )}
      </div>

      <div>
        <label className="block font-medium mb-2">Fecha</label>
        <input
          type="date"
          {...register('fecha')}
          className="w-full border rounded px-3 py-2"
        />
        {errors.fecha && (
          <span className="text-red-500 text-sm">{errors.fecha.message}</span>
        )}
      </div>

      <div>
        <label className="block font-medium mb-2">Tipo de Hora</label>
        <select
          {...register('tipoHoraId', {
            valueAsNumber: true,
          })}
          className="w-full border rounded px-3 py-2"
        >
          <option value="">Seleccionar</option>
          {tiposHora.map((t: any) => (
            <option key={t.id} value={t.id}>
              {t.nombre}
            </option>
          ))}
        </select>
        {errors.tipoHoraId && (
          <span className="text-red-500 text-sm">
            {errors.tipoHoraId.message}
          </span>
        )}
      </div>

      <div>
        <label className="block font-medium mb-2">Horas</label>
        <input
          type="number"
          step="0.5"
          {...register('cantidadHoras', {
            valueAsNumber: true,
          })}
          className="w-full border rounded px-3 py-2"
        />
        {errors.cantidadHoras && (
          <span className="text-red-500 text-sm">
            {errors.cantidadHoras.message}
          </span>
        )}
      </div>

      <button
        type="submit"
        disabled={loading}
        className="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded disabled:opacity-50"
      >
        {loading ? 'Guardando...' : 'Registrar'}
      </button>
    </form>
  );
}
```

### 5.4 Página de Nómina

**frontend/src/app/(dashboard)/nomina/page.tsx**:
```typescript
'use client';

import { useEffect, useState } from 'react';
import api from '@/lib/api';
import Link from 'next/link';
import { toast } from 'react-hot-toast';

export default function NominaPage() {
  const [nominas, setNominas] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    cargarNominas();
  }, []);

  const cargarNominas = async () => {
    try {
      const response = await api.get('/nomina');
      setNominas(response.data);
    } catch (error) {
      toast.error('Error al cargar nóminas');
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="space-y-6">
      <div className="flex justify-between items-center">
        <h1 className="text-3xl font-bold">Nóminas</h1>
        <Link
          href="/nomina/generar"
          className="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded"
        >
          Generar Nómina
        </Link>
      </div>

      {loading ? (
        <p>Cargando...</p>
      ) : (
        <table className="w-full border-collapse">
          <thead>
            <tr className="bg-gray-200">
              <th className="border p-2">Trabajador</th>
              <th className="border p-2">Período</th>
              <th className="border p-2">Devengado</th>
              <th className="border p-2">Neto</th>
              <th className="border p-2">Acciones</th>
            </tr>
          </thead>
          <tbody>
            {nominas.map((nomina: any) => (
              <tr key={nomina.id} className="hover:bg-gray-100">
                <td className="border p-2">{nomina.trabajador.nombre}</td>
                <td className="border p-2">
                  {nomina.periodoMes}/{nomina.periodoAnio}
                </td>
                <td className="border p-2">
                  ${nomina.subtotalDevengado.toLocaleString()}
                </td>
                <td className="border p-2">
                  ${nomina.totalNeto.toLocaleString()}
                </td>
                <td className="border p-2 space-x-2">
                  <Link
                    href={`/nomina/${nomina.id}`}
                    className="text-blue-600"
                  >
                    Ver
                  </Link>
                  <button
                    onClick={() => descargarPDF(nomina.id)}
                    className="text-red-600"
                  >
                    PDF
                  </button>
                </td>
              </tr>
            ))}
          </tbody>
        </table>
      )}
    </div>
  );

  async function descargarPDF(nominaId: number) {
    try {
      const response = await api.get(`/nomina/${nominaId}/pdf`, {
        responseType: 'blob',
      });
      const url = window.URL.createObjectURL(new Blob([response.data]));
      const link = document.createElement('a');
      link.href = url;
      link.setAttribute('download', `nomina-${nominaId}.pdf`);
      document.body.appendChild(link);
      link.click();
      link.parentNode?.removeChild(link);
    } catch (error) {
      toast.error('Error al descargar PDF');
    }
  }
}
```

**Fin Fase 5**: UI completa y funcional, integrada con backend.

---

## FASE 6: QA Y DEPLOYMENT (Semana 13-14)

### 6.1 Tests Finales
- [ ] 100+ tests unitarios en NestJS (pasando)
- [ ] 50+ tests en React
- [ ] Tests e2e con Cypress

### 6.2 Performance
- [ ] Optimizar queries BD (índices)
- [ ] Cachear datos estáticos
- [ ] Lazy loading en tablas

### 6.3 Seguridad
- [ ] HTTPS en producción
- [ ] Rate limiting en API
- [ ] CORS configurado
- [ ] SQL injection prevenido

### 6.4 Deployment

**Backend (NestJS)**:
```bash
# Build
npm run build

# Dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY dist ./dist
EXPOSE 3000
CMD ["node", "dist/main"]

# Docker run
docker run -e DATABASE_URL=... -p 3000:3000 nomina-backend
```

**Frontend (Next.js)**:
```bash
# Build
npm run build

# Vercel (recomendado)
vercel deploy

# O Docker
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build
EXPOSE 3000
CMD ["npm", "start"]
```

---

## RESUMEN POR FASE

| Fase | Semana | Objetivo | Entrega |
|------|--------|----------|---------|
| 0 | 1 | Setup inicial | Proyecto listo para desarrollar |
| 1 | 2-3 | BD + Modelos | Entidades TypeORM, migrations |
| 2 | 4-5 | Auth + CRUD | Autenticación JWT funcionando |
| 3 | 6-7 | Registro de Horas | Validaciones, auditoría |
| 4 | 8-10 | Motor de Nómina | 50+ tests, cálculos correctos |
| 5 | 11-12 | UI Completa | Frontend Next.js integrado |
| 6 | 13-14 | QA + Deployment | Producción lista |

---

## Stack Final

```
Frontend:    Next.js 14 + React 18 + TailwindCSS
Backend:     NestJS + TypeORM + PostgreSQL
Testing:     Jest + Supertest + React Testing Library
DevOps:      Docker + GitHub Actions
Deployment:  Vercel (frontend) + VPS (backend)
```

---
