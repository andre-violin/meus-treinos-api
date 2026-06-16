---
description: 'Cria um endpoint completo na API: use case, schema Zod e handler de rota, seguindo todas as convenções do projeto.'
argument-hint: 'Descreva o endpoint: método HTTP, path e o que ele faz. Ex: POST /workout-plans/:id/duplicate - duplica um plano de treino'
agent: 'agent'
---

Crie um endpoint completo na API `meus-treinos-api` com base na descrição: $args

Siga as instruções em [api-conventions.instructions.md](../.github/instructions/api-conventions.instructions.md).

## O que criar

### 1. Use Case — `src/usecases/<NomeUseCase>.ts`

- Exportar a classe `<NomeUseCase>`
- Definir `InputDto` e `OutputDto` como interfaces locais
- Implementar `async execute(dto: InputDto): Promise<OutputDto>`
- Importar `prisma` de `'../lib/db.js'` (extensão `.js` obrigatória)
- Importar tipos do Prisma de `'../generated/prisma/...'` (nunca de `@prisma/client`)

### 2. Schema — `src/schemas/index.ts`

- Adicionar os schemas Zod necessários para request e response
- Usar Zod 4: `z.uuid()`, `z.iso.date()`, `z.iso.datetime()`, `z.url()` (sem `.string()` antes)

### 3. Rota — arquivo existente em `src/routes/` ou novo arquivo

- Usar `app.withTypeProvider<ZodTypeProvider>().route(...)`
- Incluir `schema` com `tags`, `summary`, e `response` para **todos** os status codes
- Verificar sessão com Better Auth antes de executar a lógica:
  ```ts
  const session = await auth.api.getSession({ headers: fromNodeHeaders(request.headers) })
  if (!session) return reply.status(401).send({ error: 'Unauthorized', code: 'UNAUTHORIZED' })
  ```
- Tratar erros de domínio com os códigos corretos (ver tabela nas instruções)
- Se for um arquivo novo, lembrar de registrá-lo em `src/index.ts`

## Checklist antes de finalizar

- [ ] Todos os imports locais terminam com `.js`
- [ ] Prisma Client importado de `../generated/prisma/`
- [ ] Schemas Zod usam API do Zod 4
- [ ] `ErrorSchema` usado em todos os status de erro
- [ ] Rota registrada em `src/index.ts` (se arquivo novo)
