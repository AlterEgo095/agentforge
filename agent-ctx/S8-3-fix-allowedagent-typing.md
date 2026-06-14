# Task S8-3: Fix allowedAgentTypes typing — string[] → AgentId[]

## Work Log

### Changes Made

1. **`packages/api/src/services/tenant/TenantService.ts`**:
   - Added import: `import type { AgentId } from '@agentforge/shared';`
   - Changed `allowedAgentTypes: string[];` → `allowedAgentTypes: AgentId[];` in `TenantQuotas` interface (line 30)

2. **`packages/api/src/routes/tenants.ts`**:
   - Added import: `import { AgentIdSchema } from '@agentforge/shared';`
   - Changed `allowedAgentTypes: z.array(z.string())` → `allowedAgentTypes: z.array(AgentIdSchema)` in `CreateTenantSchema` (line 40)
   - Changed `allowedAgentTypes: z.array(z.string())` → `allowedAgentTypes: z.array(AgentIdSchema)` in `UpdateTenantSchema` (line 61)

3. **Verified shared package exports**: `AgentId` type and `AgentIdSchema` are already exported from `@agentforge/shared` via `packages/shared/src/types/agent.ts` and `packages/shared/src/schemas/agent.ts` respectively (both re-exported through index files).

4. **Searched for other `allowedAgentTypes: string[]` occurrences**: Found only test files (`tests/chaos/database-failure.test.ts`) using valid `AgentId` literal values (`['DEV', 'SLIDES']`, `['DEV']`), which are compatible with `AgentId[]` — no changes needed.

5. **TypeScript compilation verified**: Only pre-existing errors in `research.ts` and `writer.ts` (missing plugin module declarations). Zero new errors from these changes.

## Summary

- `TenantQuotas.allowedAgentTypes` now has compile-time type safety (`AgentId[]` instead of `string[]`)
- Both `CreateTenantSchema` and `UpdateTenantSchema` now validate at runtime via `AgentIdSchema` (z.enum), rejecting invalid agent IDs like `"FOO"` or `"bar"`
- No changes needed to shared package exports (already correctly exported)
- No changes needed to test files (already using valid AgentId literal values)
