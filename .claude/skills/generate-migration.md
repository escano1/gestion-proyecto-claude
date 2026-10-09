---
name: generate-migration
type: skill
description: Generar TypeORM migration para cambios en BD
keywords: typeorm, migration, database, postgresql
---

# Skill: Generate TypeORM Migration

## Propósito
Generar migration automática basada en cambios en entidades.

## Entrada

```
- changeType: 'create-table' | 'add-column' | 'drop-column' | 'modify-column'
- tableName: string
- entityPath: string          # Ej: 'src/modules/trabajador/entities/trabajador.entity.ts'
- changes: Array<{
    column: string,
    oldType?: string,         # Para modify-column
    newType: string,
    nullable?: boolean,
    default?: any,
    unique?: boolean
  }>
```

## Salida

```
backend/src/database/migrations/
└── 1728410400000-[TableName].ts
```

## Ejemplo

```bash
# Skill: generate-migration
changeType: add-column
tableName: trabajador
changes:
  - column: numeroAfiliacion
    newType: varchar(50)
    nullable: true
  - column: eps
    newType: varchar(100)
    nullable: true
```

## Resultado

Migration lista para ejecutar:

```typescript
export class Trabajador1728410400000 implements MigrationInterface {
  async up(queryRunner: QueryRunner): Promise<void> {
    await queryRunner.addColumn('trabajador', new TableColumn({
      name: 'numero_afiliacion',
      type: 'varchar(50)',
      isNullable: true,
    }));
    
    await queryRunner.addColumn('trabajador', new TableColumn({
      name: 'eps',
      type: 'varchar(100)',
      isNullable: true,
    }));
  }
  
  async down(queryRunner: QueryRunner): Promise<void> {
    await queryRunner.dropColumn('trabajador', 'numero_afiliacion');
    await queryRunner.dropColumn('trabajador', 'eps');
  }
}
```

---

Luego ejecutar:
```bash
npm run typeorm migration:run
```

Generado por: TypeORM Migration Tool
