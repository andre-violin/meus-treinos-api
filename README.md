# Meus Treinos API

API REST para gerenciamento de planos de treino, dias de treino e exercícios, construída com **Fastify**, **Prisma** e **TypeScript**.

## Tecnologias

- [Fastify](https://fastify.dev/) — framework HTTP
- [Prisma](https://www.prisma.io/) — ORM para banco de dados PostgreSQL
- [Better Auth](https://www.better-auth.com/) — autenticação com e-mail e senha
- [Zod](https://zod.dev/) — validação de schemas
- [Scalar](https://scalar.com/) — documentação interativa da API (disponível em `/docs`)
- [pnpm](https://pnpm.io/) — gerenciador de pacotes

---

## Pré-requisitos

- [Node.js](https://nodejs.org/) v18 ou superior
- [pnpm](https://pnpm.io/installation) instalado globalmente
- Um banco de dados **PostgreSQL** acessível

---

## Instalação

### 1. Clone o repositório

```bash
git clone <url-do-repositorio>
cd meus-treinos-api
```

### 2. Instale as dependências

```bash
pnpm install
```

---

## Configuração do ambiente (.env)

Crie um arquivo `.env` na raiz do projeto com as seguintes variáveis:

```env
# URL de conexão com o banco de dados PostgreSQL
DATABASE_URL="postgresql://usuario:senha@localhost:5432/meus_treinos"

# Segredo usado pelo Better Auth para assinar tokens (use uma string longa e aleatória)
BETTER_AUTH_SECRET="sua_chave_secreta_aqui"

# URL base da API (usada pelo Better Auth para callbacks)
BETTER_AUTH_URL="http://localhost:8080"
```

> **Nunca** faça commit do arquivo `.env`. Ele já deve estar listado no `.gitignore`.

---

## Configuração do Prisma

O Prisma é o ORM responsável por comunicar a aplicação com o banco de dados PostgreSQL. O schema do banco está definido em `prisma/schema.prisma`.

### Gerar o client do Prisma

Após instalar as dependências e configurar o `.env`, gere o client do Prisma:

```bash
pnpm prisma generate
```

### Criar as tabelas no banco de dados

Execute as migrations para criar as tabelas no banco:

```bash
pnpm prisma migrate dev
```

> Em produção, use `pnpm prisma migrate deploy` para aplicar as migrations sem criar novas.

### Visualizar o banco de dados (opcional)

O Prisma Studio oferece uma interface visual para explorar os dados:

```bash
pnpm prisma studio
```

---

## Executando o projeto

### Modo desenvolvimento (com hot reload)

```bash
pnpm dev
```

A API ficará disponível em: `http://localhost:8080`

A documentação interativa estará em: `http://localhost:8080/docs`

---

## Estrutura do projeto

```
prisma/
  schema.prisma       # Definição dos modelos do banco de dados
src/
  index.ts            # Ponto de entrada da aplicação
  lib/
    auth.ts           # Configuração do Better Auth
    db.ts             # Instância do Prisma Client
  usecases/           # Casos de uso da aplicação
  generated/
    prisma/           # Client gerado automaticamente pelo Prisma
```

---

## Modelos de dados

| Modelo           | Descrição                                      |
| ---------------- | ---------------------------------------------- |
| `User`           | Usuário da aplicação                           |
| `WorkoutPlan`    | Plano de treino de um usuário                  |
| `WorkoutDay`     | Dia de treino dentro de um plano               |
| `WorkoutExercise`| Exercício pertencente a um dia de treino       |
| `Session`        | Sessão de autenticação (gerenciada pelo Better Auth) |
| `Account`        | Conta vinculada ao usuário                     |
| `Verification`   | Token de verificação de e-mail                 |
