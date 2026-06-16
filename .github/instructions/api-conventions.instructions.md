---
description: "Use when creating or modifying routes, use cases, schemas, errors, or any TypeScript file in the meus-treinos-api project. Covers ESM imports, Zod 4 syntax, Prisma Client path, authentication pattern, route structure, and use case conventions."
applyTo: "src/**/*.ts"
---

# Meus Treinos API — Convenções

## ESM: extensão `.js` obrigatória em todos os imports locais

```ts
import { prisma } from './lib/db.js'                        // ✅
import { WeekDay } from '../generated/prisma/enums.js'      // ✅
import { prisma } from './lib/db'                           // ❌ falha em runtime (moduleResolution: nodenext)
```

## Prisma Client — path não-padrão

O client é gerado em `src/generated/prisma/`. **Nunca** importar de `@prisma/client`.

```ts
import { PrismaClient } from '../generated/prisma/client.js'
import { WeekDay } from '../generated/prisma/enums.js'
import type { WorkoutPlan } from '../generated/prisma/models.js'
```

Após alterar `prisma/schema.prisma`, rodar `pnpm prisma generate` para regenerar.

## Zod 4 — API diferente do Zod 3

```ts
z.uuid()         // ✅  (não z.string().uuid())
z.iso.date()     // ✅  (não z.string().date())
z.iso.datetime() // ✅  (não z.string().datetime())
z.url()          // ✅  (não z.string().url())
```

## Rotas — estrutura padrão

Toda rota usa `app.withTypeProvider<ZodTypeProvider>().route(...)` com bloco `schema` completo incluindo `tags`, `summary` e `response` para todos os status codes retornados.

**Autenticação** (obrigatória em rotas protegidas):

```ts
const session = await auth.api.getSession({ headers: fromNodeHeaders(request.headers) })
if (!session) {
  return reply.status(401).send({ error: 'Unauthorized', code: 'UNAUTHORIZED' })
}
```

**Tratamento de erros** no handler:

| Erro | Status | `code` |
|------|--------|--------|
| `NotFoundError` | 404 | `NOT_FOUND_ERROR` |
| `WorkoutPlanNotActiveError` | 400 | `WORKOUT_PLAN_NOT_ACTIVE` |
| `SessionAlreadyStartedError` | 409 | `SESSION_ALREADY_STARTED` |
| Outros | 500 | `INTERNAL_SERVER_ERROR` |

Sempre usar `ErrorSchema` de `src/schemas/index.js` para o body de todos os erros.

## Use Cases — estrutura padrão

```ts
interface InputDto { /* campos de entrada */ }
interface OutputDto { /* campos de saída */ }

export class NomeDoUseCase {
  async execute(dto: InputDto): Promise<OutputDto> {
    // lógica de negócio usando prisma diretamente
  }
}
```

- Instanciar use cases diretamente no handler: `const uc = new NomeDoUseCase()`
- Usar `prisma` (singleton de `src/lib/db.js`) diretamente dentro do use case
- Transações: `prisma.$transaction(async (tx) => { ... })`

## Adicionando um endpoint novo

1. Criar use case em `src/usecases/NomeUseCase.ts`
2. Adicionar schemas Zod em `src/schemas/index.ts`
3. Adicionar handler em `src/routes/<arquivo>.ts`
4. Se o arquivo de rota for novo, registrar em `src/index.ts`:
   ```ts
   await app.register(novaRota, { prefix: '/prefixo' })
   ```
