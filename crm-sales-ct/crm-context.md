# Colombia Tech · Sales CRM — Contexto del proyecto

## Brief

El Sales CRM centraliza todo el ciclo comercial del equipo de ventas de Colombia Tech Week: desde la prospección inicial de empresas patrocinantes hasta el cierre de deals, pasando por la gestión de reuniones de discovery, tareas de seguimiento por SDR y reportes de cumplimiento de metas por AE. Reemplaza hojas de cálculo y flujos manuales dispersos por un pipeline en tiempo real con trazabilidad completa de actividades.

---

## Qué hace

| Módulo | Descripción |
|--------|-------------|
| **Pipeline SDR** | Vista de tabla con todas las empresas prospectadas, filtros por SDR / ICP fit / estado / industria / país, panel lateral de detalle, y acciones de cambio de estado con log automático de actividad |
| **Reuniones** | Agenda de reuniones de discovery semana por semana por AE, con metas configurables, registro de outcome (qualified / unqualified / no-show) e integración con Google Calendar |
| **Deals** | Kanban de pipeline de ventas por AE con etapas configurables, valor de deal, pronóstico ponderado, tareas internas, exportación a Excel y búsqueda por empresa |
| **Dashboard AE** | Métricas de cierre por AE y por quarter: won+commit, pipeline abierto, forecast, % vs meta trimestral, deals cerrados con nombres y valores, y promedio de días en cerrar |
| **Tareas SDR** | Lista de tareas de seguimiento por empresa, con fecha límite y estado de completado |
| **Tareas AE** | Tareas asociadas a deals activos, con asignación y vencimiento |
| **Configuración de equipo** | CRUD de miembros del equipo (SDRs, AEs, Sub-AEs) y edición de metas de pipeline / reuniones / ingresos trimestrales |
| **Gestión de usuarios** | Alta de nuevos usuarios con rol (admin / user) — solo visible para admins |
| **Duplicados** | Detección automática de empresas duplicadas con fusión en un solo click |

---

## Ventajas clave

- **Pipeline en tiempo real**: cualquier cambio de estado, nuevo contacto o reunión agendada se refleja al instante en todas las sesiones activas mediante Supabase Realtime.
- **Trazabilidad completa de actividades**: cada cambio de estado, contacto añadido y outcome de reunión queda registrado en un log inmutable con timestamp y SDR responsable.
- **Dashboard de metas cuantitativo**: cada AE ve en tiempo real cuánto ha cerrado vs. su meta trimestral, cuánto pipeline necesita generar y cuánto necesita cerrar por semana para llegar.
- **Google Calendar integrado**: al agendar una reunión se crea automáticamente el evento en el calendario del AE con Meet link y invitación a los contactos de la empresa.
- **Equipo dinámico**: los SDRs y AEs se gestionan desde la UI sin tocar código — no hay listas hardcodeadas.
- **Exportación Excel**: el pipeline de deals se puede exportar con filtros por AE, SDR, stage, evento y fecha de creación.

---

## Stack tecnológico

| Capa | Tecnología |
|------|-----------|
| Frontend | React 18 + TypeScript 5 + Vite 5 |
| UI | TailwindCSS 3 + shadcn/ui + Radix UI |
| Estado CRM | React Context + useState + Supabase Realtime |
| Estado Deals / Equipo | TanStack React Query 5 |
| Base de datos | Supabase (PostgreSQL 16 + RLS) |
| Autenticación | Supabase Auth (email + password) |
| Funciones serverless | Supabase Edge Functions (Deno) |
| Drag & drop | DnD Kit 6 |
| Gráficas | Recharts 2 |
| Exportación | xlsx |
| Notificaciones | Sonner |
| Correos / reportes | Slack (vía Edge Functions) |
| Automatizaciones | n8n (colombiatechweek.co) |
| Outreach LinkedIn | Aimfox (vía n8n relay) |
| Calendario | Google Calendar API (vía Lovable connector gateway) |
| Repositorio | GitHub |

---

## Accesos a servicios

| Servicio | Usuario | Contraseña / Autenticación |
|----------|---------|---------------------------|
| **GitHub** (repo `crm-sales-ct`) | santiago.tellez@colombiatechweek.co | Magic link / Google |
| **Supabase** (proyecto `esuyjulkvrylglncenhe`) | santiago.tellez@colombiatechweek.co | Magic link / Google |
| **Deploy** (plataforma de hosting) | santiago.tellez@colombiatechweek.co | Magic link / Google |

**Supabase — URLs:**
- Dashboard: https://supabase.com/dashboard/project/esuyjulkvrylglncenhe
- API URL: https://esuyjulkvrylglncenhe.supabase.co

---

## Accesos a la aplicación

**URL:** https://crm.colombiatechweek.co

El primer usuario registrado en Supabase Auth queda automáticamente con rol `admin`. Los usuarios adicionales se crean desde `/admin/users` dentro de la app.

| Usuario | Rol | Contraseña |
|---------|-----|-----------|
| setr7706@gmail.com | admin | La tiene el usuario |

---

## Variables de entorno (producción)

| Variable | Valor |
|----------|-------|
| `VITE_SUPABASE_PROJECT_ID` | `esuyjulkvrylglncenhe` |
| `VITE_SUPABASE_URL` | `https://esuyjulkvrylglncenhe.supabase.co` |
| `VITE_SUPABASE_PUBLISHABLE_KEY` | Clave anon del proyecto (ver Supabase Dashboard → API) |

**Secrets de Edge Functions** (en Supabase Dashboard → Edge Functions → Secrets):

| Secret | Para qué sirve |
|--------|---------------|
| `LOVABLE_API_KEY` | Google Calendar vía gateway de Lovable |
| `GOOGLE_CALENDAR_API_KEY` | Google Calendar API |
| `SLACK_BOT_TOKEN` | Reportes y notificaciones en Slack |
| `SLACK_REPORT_CHANNEL` | Canal de Slack destino (ej. `ct-sales`) |
| `SDR_EMAILS_JSON` | Emails de SDRs para @-menciones en Slack |

---

## Flujo de deploy

1. Desarrollo y prueba en local: `npm run dev` → `localhost:8080`.
2. Verificar que no hay errores de TypeScript: `npx tsc --noEmit`.
3. `git push origin master` al repositorio en GitHub.
4. La plataforma de hosting detecta el push y despliega automáticamente.
5. Si hay nuevas migraciones en `supabase/migrations/`, aplicarlas manualmente en Supabase Dashboard → SQL Editor **antes** del deploy.
6. Si hay cambios en Edge Functions: `supabase functions deploy <nombre>`.

---

## Qué hacer ante errores o caída

1. Revisar logs del hosting (build logs y runtime logs).
2. Revisar logs de Edge Functions en Supabase Dashboard → Edge Functions → [nombre] → Logs.
3. Abrir el repo en el IDE, consultar el error con asistencia de IA para identificar causa raíz.
4. Si el error es de DB: revisar en Supabase → Logs Explorer si hay errores de RLS o queries fallidas.

---

## Migraciones pendientes de aplicar

Las siguientes migraciones están en el repositorio pero **no han sido aplicadas aún** en el proyecto remoto de Supabase:

| Archivo | Qué crea |
|---------|---------|
| `supabase/migrations/20260703000000_team_members.sql` | Tabla `team_members` con seed de SDRs, AEs y Sub-AEs |
| `supabase/migrations/20260703000001_team_goals.sql` | Columnas `pipe_goal` / `meeting_goal` en `team_members` + tabla `ae_targets` con targets trimestrales |

Aplicar ambas desde Supabase Dashboard → SQL Editor para activar el módulo de configuración de equipo y metas.

---

## Integraciones externas activas

| Integración | Qué hace |
|-------------|---------|
| **Google Calendar** | Crea, actualiza y elimina eventos de discovery al agendar / reasignar / cancelar reuniones |
| **Slack** | Reportes diarios de pipeline SDR, reportes matutinos para AEs, notificaciones de commit handoff |
| **n8n** (`n8n-7391.colombiatechweek.co`) | Automatizaciones: recibe contactos y los procesa en Aimfox |
| **Aimfox** | Herramienta de outreach LinkedIn; los contactos añadidos al CRM se sincronizan automáticamente |
