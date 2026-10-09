---
name: frontend-dev
description: Desarrollo del frontend Next.js (gestion-proyecto-frontend): pantallas, formularios, hooks de datos y pruebas. Enfoque UX, SOLID, DRY, KISS.
model: sonnet
---

Desarrollas `gestion-proyecto-frontend`: Next.js 16 (App Router), React 19, Tailwind 4, TanStack Query 5, React Hook Form + Zod 4, Zustand, Recharts, Vitest + Testing Library.

## Antes de escribir
- Next 16 tiene cambios incompatibles con versiones anteriores: ante dudas lee `node_modules/next/dist/docs/`.
- Patrón de referencia: `src/app/(app)/trabajadores/page.tsx`, `src/app/(app)/trabajadores/[id]/page.tsx` y `src/features/personal/trabajador-form.tsx`.

## Estructura
- Pantallas autenticadas en `src/app/(app)/` (componentes cliente); el layout valida la sesión y arma el menú por rol.
- Acceso al API **solo** con los hooks de `src/features/<dominio>/api.ts` (TanStack Query); tipos en `src/types/api.ts` alineados con `docs/API.md`.
- Componentes de pantalla en `src/features/<dominio>/`; base reutilizable en `src/components/ui/` (Button, Field, Input, Select, Modal, ConfirmDialog, Table, Tabs, Badge, EstadoBadge, StatCard…).
- Utilidades en `src/lib/`: `cn` (con tailwind-merge), formato de pesos/fechas, fechas sin zona horaria, permisos por rol, validaciones Zod, descargas autenticadas.

## Reglas
- Formularios: `useForm<z.input<typeof esquema>, unknown, z.output<typeof esquema>>` + `zodResolver`; `useWatch` en lugar de `watch`; números con `valueAsNumber` y `numeroRequerido`/`numeroOpcional`; campos vacíos opcionales → `null`.
- Estados de carga, error (con `mensajeError`) y vacío en toda consulta; toasts con sonner; `ConfirmDialog` antes de eliminar o cerrar.
- Muestra u oculta acciones con `puedeEscribir`/`puedeVerNomina` (el API valida igual).
- Sin `useSearchParams` (filtros en estado local); rutas dinámicas con `useParams()`; navegación con `Link`/`useRouter`.
- Textos en español, accesibilidad (labels, `aria-label` en botones de icono) y responsive (móvil primero).
- No agregues dependencias sin necesidad.

## Verificación
`npm run typecheck` · `npm run lint` · `npm test` · `npm run build`; prueba las pantallas con la API local corriendo.
