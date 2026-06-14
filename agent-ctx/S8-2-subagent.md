# Task S8-2: Create Plugin↔Agent Adapters (Writer→DOC, Research→RECHERCHE)

## Summary
Created type conversion adapters between DocAgentWorker↔WriterWorker and RechercheAgentWorker↔ResearchWorker, enabling plugin delegation with graceful fallback.

## Files Created
1. **`packages/api/src/core/agents/adapters/WriterDocAdapter.ts`** — Converts between DocAgentWorker types and WriterWorker types
   - `adaptDocInputToResearchInput()`: prompt + context → ResearchInput
   - `adaptWriterSessionToDocOutput()`: WriterSession → DocOutput
   - `IWriterCapability`: typed capability interface

2. **`packages/api/src/core/agents/adapters/ResearchRechercheAdapter.ts`** — Converts between RechercheAgentWorker types and ResearchWorker types
   - `adaptRechercheConfigToResearchConfig()`: context → Partial<ResearchConfig>
   - `adaptResearchSessionToRechercheOutput()`: ResearchSession → RechercheOutput
   - `IResearchCapability`: typed capability interface

## Files Modified
3. **`packages/api/src/core/agents/DocAgentWorker.ts`** — Added plugin delegation to generate()
4. **`packages/api/src/core/agents/RechercheAgentWorker.ts`** — Added plugin delegation to generate()
5. **`packages/api/src/core/agents/index.ts`** — Exported new adapters

## Key Design Decisions
- **Lazy PluginLoader access**: Used `getPluginLoader()` with try/catch require() instead of static import to avoid test-time failures when plugin packages aren't installed
- **Non-blocking delegation**: All plugin calls wrapped in try/catch; errors logged as warnings and fall back to internal pipeline
- **Public API preserved**: generate() signatures unchanged, all delegation logic is internal
- **Branded ID handling**: ResearchWorker uses branded types (ResearchSessionId, SourceId, FindingId); adapters cast to string for RechercheOutput
- **Enum mapping**: Source types, credibility levels, finding types, temporal focus, and sentiment values mapped between the two type systems

## Test Results
- 25/25 agent-worker tests pass
- 0 new TypeScript compilation errors
