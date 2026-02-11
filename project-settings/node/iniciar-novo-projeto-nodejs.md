# Guia — Iniciar projeto Node.js + TypeScript do zero

## 1. Inicializar o projeto

```bash
mkdir meu-projeto && cd meu-projeto
npm init -y
```

> [Documentação npm init](https://docs.npmjs.com/cli/v10/commands/npm-init)

---

## 2. Estrutura de pastas

```
src/
├── @types/          # Tipagens customizadas
├── config/          # Configurações (env, database, etc.)
├── error/           # Classes de erro (ApiError, etc.)
├── modules/
│   └── user/
│       ├── controller/
│       ├── middleware/
│       ├── model/
│       ├── routes/
│       └── service/
├── shared/
│   └── middleware/   # Middlewares globais (errorHandler, auth, etc.)
├── app.ts
└── server.ts
prisma/
└── schema.prisma
tests/
└── user.test.ts
```

---

## 3. TypeScript

### Instalação

```bash
npm install -D typescript ts-node tsx @types/node
npx tsc --init
```

> [Documentação TypeScript](https://www.typescriptlang.org/docs/)
> [Documentação tsx](https://tsx.is/)

### `tsconfig.json`

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "commonjs",
    "lib": ["ES2022"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "include": ["src"],
  "exclude": ["node_modules", "dist"]
}
```

---

## 4. Framework HTTP

Escolha **um** dos dois:

### Opção A — Express

```bash
npm install express
npm install -D @types/express
```

> [Documentação Express](https://expressjs.com/)

#### `src/app.ts`

```ts
import express from "express";

const app = express();

app.use(express.json());

export { app };
```

#### `src/server.ts`

```ts
import { app } from "./app";

const PORT = process.env.PORT || 3000;

app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

### Opção B — Fastify

```bash
npm install fastify @fastify/cors @fastify/helmet
```

> [Documentação Fastify](https://fastify.dev/docs/latest/)

#### `src/app.ts`

```ts
import Fastify from "fastify";

const app = Fastify({ logger: true });

export { app };
```

#### `src/server.ts`

```ts
import { app } from "./app";

const PORT = Number(process.env.PORT) || 3000;

app.listen({ port: PORT, host: "0.0.0.0" }).then(() => {
  console.log(`Server running on port ${PORT}`);
});
```

---

## 5. ORM / Banco de Dados

Escolha **um** dos dois:

### Opção A — Prisma

```bash
npm install @prisma/client
npm install -D prisma
npx prisma init
```

> [Documentação Prisma](https://www.prisma.io/docs)

#### `prisma/schema.prisma`

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
}

model User {
  id        String   @id @default(uuid())
  name      String
  email     String   @unique
  password  String
  createdAt DateTime @default(now()) @map("created_at")
  updatedAt DateTime @updatedAt @map("updated_at")

  @@map("users")
}
```

#### Comandos úteis do Prisma

```bash
npx prisma migrate dev --name init   # Criar migration
npx prisma generate                  # Gerar client
npx prisma studio                    # Interface visual do banco
npx prisma db seed                   # Executar seed
```

### Opção B — TypeORM

```bash
npm install typeorm reflect-metadata pg
npm install -D @types/node
```

> [Documentação TypeORM](https://typeorm.io/)

Adicione ao `tsconfig.json`:

```json
{
  "compilerOptions": {
    "emitDecoratorMetadata": true,
    "experimentalDecorators": true
  }
}
```

#### `src/config/data-source.ts`

```ts
import "reflect-metadata";
import { DataSource } from "typeorm";

export const AppDataSource = new DataSource({
  type: "postgres",
  host: process.env.DB_HOST || "localhost",
  port: Number(process.env.DB_PORT) || 5432,
  username: process.env.DB_USER || "postgres",
  password: process.env.DB_PASS || "postgres",
  database: process.env.DB_NAME || "meu_banco",
  synchronize: false,
  logging: false,
  entities: ["src/modules/**/model/*.ts"],
  migrations: ["src/migrations/*.ts"],
});
```

---

## 6. Variáveis de ambiente

```bash
npm install dotenv
```

> [Documentação dotenv](https://www.npmjs.com/package/dotenv)

#### `.env`

```env
PORT=3000
DATABASE_URL="postgresql://postgres:postgres@localhost:5432/meu_banco"
JWT_SECRET="sua-chave-secreta"
```

#### `.env.example`

```env
PORT=
DATABASE_URL=
JWT_SECRET=
```

> Adicione `.env` ao `.gitignore`. Nunca versione segredos.

---

## 7. Segurança e Middlewares

### Helmet (cabeçalhos HTTP de segurança)

```bash
npm install helmet
npm install -D @types/helmet
```

> [Documentação Helmet](https://helmetjs.github.io/)

### CORS

```bash
npm install cors
npm install -D @types/cors
```

> [Documentação CORS](https://www.npmjs.com/package/cors)

### Exemplo no `app.ts` (Express)

```ts
import express from "express";
import helmet from "helmet";
import cors from "cors";

const app = express();

app.use(helmet());
app.use(cors({ origin: process.env.CORS_ORIGIN || "*" }));
app.use(express.json());

export { app };
```

---

## 8. Validação — Zod

```bash
npm install zod
```

> [Documentação Zod](https://zod.dev/)

#### Exemplo de schema

```ts
import { z } from "zod";

export const createUserSchema = z.object({
  name: z.string().min(2, "Nome deve ter no mínimo 2 caracteres"),
  email: z.string().email("E-mail inválido"),
  password: z.string().min(6, "Senha deve ter no mínimo 6 caracteres"),
});

export type CreateUserDTO = z.infer<typeof createUserSchema>;
```

---

## 9. Autenticação

### bcryptjs (hash de senha)

```bash
npm install bcryptjs
npm install -D @types/bcryptjs
```

> [Documentação bcryptjs](https://www.npmjs.com/package/bcryptjs)

### jsonwebtoken (JWT)

```bash
npm install jsonwebtoken
npm install -D @types/jsonwebtoken
```

> [Documentação jsonwebtoken](https://www.npmjs.com/package/jsonwebtoken)

#### Exemplo de uso

```ts
import bcrypt from "bcryptjs";
import jwt from "jsonwebtoken";

// Hash
const hash = await bcrypt.hash("senha123", 10);

// Comparar
const match = await bcrypt.compare("senha123", hash);

// Gerar token
const token = jwt.sign({ id: user.id }, process.env.JWT_SECRET!, {
  expiresIn: "1d",
});

// Verificar token
const decoded = jwt.verify(token, process.env.JWT_SECRET!);
```

---

## 10. ESLint + Prettier

### Instalação

```bash
npm install -D eslint @eslint/js typescript-eslint eslint-config-prettier eslint-plugin-prettier prettier
```

> [Documentação ESLint](https://eslint.org/docs/latest/)
> [Documentação Prettier](https://prettier.io/docs/en/)
> [Documentação typescript-eslint](https://typescript-eslint.io/)

### `eslint.config.mjs`

```js
import eslint from "@eslint/js";
import tseslint from "typescript-eslint";
import prettier from "eslint-config-prettier";
import prettierPlugin from "eslint-plugin-prettier";

export default tseslint.config(
  eslint.configs.recommended,
  ...tseslint.configs.recommended,
  prettier,
  {
    plugins: {
      prettier: prettierPlugin,
    },
    rules: {
      "prettier/prettier": "error",
      "@typescript-eslint/no-unused-vars": ["error", { argsIgnorePattern: "^_" }],
      "@typescript-eslint/explicit-function-return-type": "off",
    },
  },
  {
    ignores: ["node_modules", "dist"],
  },
);
```

### `.prettierrc`

```json
{
  "semi": true,
  "singleQuote": false,
  "trailingComma": "all",
  "printWidth": 100,
  "tabWidth": 2,
  "arrowParens": "always"
}
```

---

## 11. Testes — Jest + Supertest

### Instalação

```bash
npm install -D jest ts-jest @types/jest supertest @types/supertest
npx ts-jest config:init
```

> [Documentação Jest](https://jestjs.io/docs/getting-started)
> [Documentação ts-jest](https://kulshekhar.github.io/ts-jest/)
> [Documentação Supertest](https://www.npmjs.com/package/supertest)

### `jest.config.ts`

```ts
import type { Config } from "jest";

const config: Config = {
  preset: "ts-jest",
  testEnvironment: "node",
  roots: ["<rootDir>/tests"],
  testMatch: ["**/*.test.ts"],
  clearMocks: true,
  collectCoverage: true,
  coverageDirectory: "coverage",
  coverageProvider: "v8",
};

export default config;
```

### Exemplo de teste — `tests/user.test.ts`

```ts
import request from "supertest";
import { app } from "../src/app";

describe("POST /users", () => {
  it("deve criar um novo usuário", async () => {
    const response = await request(app).post("/users").send({
      name: "João",
      email: "joao@email.com",
      password: "123456",
    });

    expect(response.status).toBe(201);
    expect(response.body).toHaveProperty("message");
  });

  it("deve retornar 422 com dados inválidos", async () => {
    const response = await request(app).post("/users").send({
      name: "",
      email: "email-invalido",
      password: "123",
    });

    expect(response.status).toBe(422);
  });
});
```

---

## 12. `.gitignore`

```
node_modules/
dist/
coverage/
.env
*.log
```

---

## 13. Scripts — `package.json`

```json
{
  "scripts": {
    "dev": "tsx watch src/server.ts",
    "build": "tsc",
    "start": "node dist/server.js",
    "lint": "eslint src/",
    "lint:fix": "eslint src/ --fix",
    "format": "prettier --write \"src/**/*.ts\"",
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage"
  }
}
```

Se usar **Prisma**, adicione:

```json
{
  "scripts": {
    "prisma:migrate": "prisma migrate dev",
    "prisma:generate": "prisma generate",
    "prisma:studio": "prisma studio",
    "prisma:seed": "prisma db seed"
  }
}
```

Se usar **TypeORM**, adicione:

```json
{
  "scripts": {
    "typeorm": "ts-node -r tsconfig-paths/register ./node_modules/typeorm/cli.js",
    "migration:generate": "npm run typeorm -- migration:generate src/migrations/Migration",
    "migration:run": "npm run typeorm -- migration:run -d src/config/data-source.ts"
  }
}
```

---

## 14. Resumo — Todos os comandos de instalação

### Dependências de produção

```bash
npm install express helmet cors zod bcryptjs jsonwebtoken dotenv
```

Com **Prisma**:

```bash
npm install @prisma/client
```

Com **TypeORM**:

```bash
npm install typeorm reflect-metadata pg
```

### Dependências de desenvolvimento

```bash
npm install -D typescript ts-node tsx @types/node @types/express @types/helmet @types/cors @types/bcryptjs @types/jsonwebtoken eslint @eslint/js typescript-eslint eslint-config-prettier eslint-plugin-prettier prettier jest ts-jest @types/jest supertest @types/supertest
```

Com **Prisma**:

```bash
npm install -D prisma
```

---

## 15. Referências

| Biblioteca         | Documentação                                         |
| ------------------ | ---------------------------------------------------- |
| Node.js            | <https://nodejs.org/docs/latest/api/>                  |
| TypeScript         | <https://www.typescriptlang.org/docs/>                 |
| Express            | <https://expressjs.com/>                               |
| Fastify            | <https://fastify.dev/docs/latest/>                     |
| Prisma             | <https://www.prisma.io/docs>                           |
| TypeORM            | <https://typeorm.io/>                                  |
| Zod                | <https://zod.dev/>                                     |
| ESLint             | <https://eslint.org/docs/latest/>                      |
| Prettier           | <https://prettier.io/docs/en/>                         |
| typescript-eslint  | <https://typescript-eslint.io/>                        |
| Jest               | <https://jestjs.io/docs/getting-started>               |
| ts-jest            | <https://kulshekhar.github.io/ts-jest/>                |
| Supertest          | <https://www.npmjs.com/package/supertest>              |
| Helmet             | <https://helmetjs.github.io/>                          |
| CORS               | <https://www.npmjs.com/package/cors>                   |
| bcryptjs           | <https://www.npmjs.com/package/bcryptjs>               |
| jsonwebtoken       | <https://www.npmjs.com/package/jsonwebtoken>            |
| dotenv             | <https://www.npmjs.com/package/dotenv>                 |
| tsx                | <https://tsx.is/>                                      |
