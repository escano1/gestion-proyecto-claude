---
name: setup-nest-module
type: skill
description: Crear un módulo NestJS completo (entity, DTO, service, controller, tests)
keywords: nestjs, module, crud, scaffold
---

# Skill: Setup NestJS Module Completo

## Propósito
Generar estructura completa de un módulo NestJS siguiendo SOLID, DRY, KISS.

## Entrada (Parámetros)

```
- moduleName: string        # Nombre del módulo (ej: "trabajador")
- fields: Array<{           # Campos de la entidad
    name: string,
    type: 'string' | 'number' | 'date' | 'decimal',
    required: boolean,
    validations?: string[]  # Ej: ['isPositive', 'min:0', 'max:12']
  }>
- hasRepository: boolean    # Si es complejo, crear repository
- relations?: Array<{       # Si tiene relaciones
    field: string,
    type: 'ManyToOne' | 'OneToMany' | 'ManyToMany',
    target: string          # Entidad relacionada
  }>
```

## Salida (Archivos Generados)

```
backend/src/modules/[moduleName]/
├── entities/
│   └── [moduleName].entity.ts
├── dto/
│   ├── create-[moduleName].dto.ts
│   └── update-[moduleName].dto.ts
├── repositories/
│   └── [moduleName].repository.ts (si hasRepository=true)
├── [moduleName].service.ts
├── [moduleName].controller.ts
├── [moduleName].module.ts
└── tests/
    └── [moduleName].service.spec.ts
```

## Checklist Generado

- [ ] Entity TypeORM con decoradores
- [ ] DTOs con validación `class-validator`
- [ ] Service con inyección de dependencias
- [ ] Controller con guards y pipes
- [ ] Módulo exportando providers
- [ ] Tests: 3 casos (éxito, error, edge)
- [ ] TypeScript strict: sin `any`

## Ejemplo de Uso

```bash
# Skill: setup-nest-module
moduleName: trabajador
fields:
  - name: nombre
    type: string
    required: true
    validations: [isNotEmpty, minLength:3]
  - name: documentoIdentidad
    type: string
    required: true
    validations: [isUnique]
  - name: salarioBase
    type: decimal
    required: true
    validations: [isPositive]
  - name: fechaIngreso
    type: date
    required: true
hasRepository: true
relations:
  - field: proyectos
    type: ManyToMany
    target: Proyecto
```

## Resultado

Módulo `TrabajadorModule` con:
- Entity con UNIQUE constraints
- DTOs validados
- Service con CRUD + métodos específicos
- Controller con @UseGuards(JwtAuthGuard)
- Tests básicos
- Repository si es complejo

---

Generado por: NestJS Scaffold Tool  
Sigue: SOLID, DRY, KISS
