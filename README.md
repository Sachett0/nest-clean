# Nest Clean

API de um fórum de perguntas e respostas feita com **NestJS**, desenvolvida durante a formação Node.js da Rocketseat. O objetivo é aplicar Clean Architecture e DDD em um projeto NestJS real.

> Projeto em andamento. Por enquanto a API tem cadastro de usuários, autenticação JWT e criação/listagem de perguntas.

## Tecnologias

- [NestJS 11](https://nestjs.com/)
- [Prisma](https://www.prisma.io/) + PostgreSQL (via Docker)
- [Zod](https://zod.dev/) para validar o ambiente e os dados das requisições
- Passport + JWT com algoritmo **RS256** (par de chaves pública/privada)
- [Vitest](https://vitest.dev/) + SWC para testes unitários e E2E
- ESLint com a config da Rocketseat

## Funcionalidades

| Método | Rota         | Autenticação | Descrição                               |
| ------ | ------------ | ------------ | --------------------------------------- |
| POST   | `/accounts`  | Não          | Cria uma conta de usuário               |
| POST   | `/sessions`  | Não          | Autentica e retorna um `access_token`   |
| POST   | `/questions` | Bearer JWT   | Cria uma pergunta                       |
| GET    | `/questions` | Bearer JWT   | Lista as perguntas recentes (`?page=1`) |

O arquivo [client.http](client.http) tem exemplos de requisições para a extensão REST Client do VS Code.

## Estrutura

```
src/
├── auth/          # Módulo de autenticação (JWT strategy, guard, decorator)
├── controllers/   # Controllers HTTP e testes E2E
├── pipes/         # ZodValidationPipe
├── prisma/        # PrismaService
├── env.ts         # Validação das variáveis de ambiente
└── main.ts
prisma/
├── schema.prisma
└── migrations/
```

## Como rodar

### Pré-requisitos

- Node.js 20+
- Docker e Docker Compose
- OpenSSL (para gerar as chaves do JWT)

### Passo a passo

```bash
# 1. Instale as dependências
npm install

# 2. Suba o PostgreSQL
docker compose up -d

# 3. Crie o arquivo de ambiente
cp .env.example .env
```

Gere o par de chaves RSA e coloque o conteúdo em base64 no `.env`:

```bash
openssl genpkey -algorithm RSA -out private.key -pkeyopt rsa_keygen_bits:2048
openssl rsa -pubout -in private.key -out public.key

# Converta para base64 (Linux/macOS/Git Bash)
base64 -w 0 private.key   # -> JWT_PRIVATE_KEY
base64 -w 0 public.key    # -> JWT_PUBLIC_KEY
```

> Os arquivos `.key` e o `.env` estão no `.gitignore` e não devem ser commitados.

```bash
# 4. Rode as migrations
npx prisma migrate dev

# 5. Inicie em modo de desenvolvimento
npm run start:dev
```

A API sobe em `http://localhost:3333` (configurável pela variável `PORT`).

## Variáveis de ambiente

| Variável          | Descrição                                  |
| ----------------- | ------------------------------------------ |
| `DATABASE_URL`    | URL de conexão do PostgreSQL               |
| `JWT_PRIVATE_KEY` | Chave privada RSA em base64                |
| `JWT_PUBLIC_KEY`  | Chave pública RSA em base64                |
| `PORT`            | Porta da aplicação (padrão: `3333`)        |

## Scripts

| Comando              | Descrição                         |
| -------------------- | --------------------------------- |
| `npm run start:dev`  | Inicia em modo watch              |
| `npm run build`      | Gera o build em `dist/`           |
| `npm run start:prod` | Roda o build de produção          |
| `npm test`           | Testes unitários                  |
| `npm run test:e2e`   | Testes end-to-end                 |
| `npm run test:cov`   | Testes com cobertura              |
| `npm run lint`       | Lint com correção automática      |
