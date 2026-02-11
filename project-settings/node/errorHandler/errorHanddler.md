# Error Handler — Node + TypeScript (sem try-catch)

Middleware global de erro para Express que captura exceções automaticamente em cada rota/service.

## `src/error/index.ts`

```ts
export class ApiError extends Error {
  public readonly statusCode: number;

  constructor(message: string, statusCode = 400) {
    super(message);
    this.statusCode = statusCode;
  }
}
```

## `src/shared/middleware/errors/index.ts`

```ts
import { Request, Response, NextFunction } from "express";
import { ApiError } from "../../../error";

export function handleError(
  error: Error,
  _request: Request,
  response: Response,
  _next: NextFunction, // Express exige 4 parâmetros para identificar como error handler
) {
  if (error instanceof ApiError) {
    return response.status(error.statusCode).json({ message: error.message });
  }

  return response.status(500).json({ message: "Erro interno do servidor" });
}
```

## `app.ts`

```ts
import { handleError } from "./shared/middleware/errors";

app.use(handleError);

export { app };
```

## Exemplos de uso

### Em middleware (validação com Zod)

```ts
export class CreateUserMiddleware {
  public CreateUser(request: Request, _response: Response, next: NextFunction) {
    const createUsersSchema = new CreateUsersSchema();
    const typeCreateUsersSchema = createUsersSchema.Create();

    type CreateUsersType = z.infer<typeof typeCreateUsersSchema>;
    const { name, email, birthday, cpf }: CreateUsersType = request.body;

    const result = typeCreateUsersSchema.safeParse({
      name,
      email,
      birthday,
      cpf,
    });

    if (!result.success) {
      throw new ApiError(result.error.issues[0].message, 422);
    }

    next();
  }
}
```

### Em service

```ts
export class CreateUserService {
  public async CreateUser(newUser: ICreateUser) {
    const { name, email } = newUser;

    const getUsersModel = new GetUsersModel();
    const userAlreadyExist = await getUsersModel.GetUserByEmail(email);

    if (userAlreadyExist) {
      throw new ApiError("O e-mail informado já é cadastrado.", 409);
    }

    const createUserModel = new CreateUserModel();
    const result = await createUserModel.CreateUser(newUser);

    if (result) {
      return {
        message: "Novo usuário foi criado com sucesso!",
        status: 200,
      };
    }

    throw new ApiError("Não foi possível criar um novo usuário.", 500);
  }
}
```
