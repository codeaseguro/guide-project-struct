# Clean Architecture - Aplicada en Backend

## Descripción

Clean Architecture (conocida como Arquitectura Cebolla) es un patrón arquitectónico propuesto por Robert C. Martin (Uncle Bob) que promueve la separación de responsabilidades mediante capas concéntricas, donde las dependencias apuntan hacia el centro en donde se podra encontrar lo mas importante de tu codigo que es la logica de negocio.

### Principios fundamentales

- **Independencia de frameworks**: La lógica de negocio no depende de bibliotecas externas.
- **Testeable**: Las reglas de negocio se pueden probar sin UI, base de datos o servicios externos.
- **Independencia de la UI**: La interfaz puede cambiar sin afectar el core.
- **Independencia de la base de datos**: Podes cambiar de SQL a NoSQL sin afectar las reglas de negocio.
- **Independencia de agentes externos**: Las reglas de negocio no conocen el mundo exterior.

## Estructura del Proyecto

```
project-example/
├── src/
│   ├── domain/                    # Capa más interna
│   │   ├── entities/              # Entidades de negocio
│   │   │   ├── User.ts
│   │   │   └── Product.ts
│   │   ├── repositories/          # Interfaces de repositorios
│   │   │   ├── IUserRepository.ts
│   │   │   └── IProductRepository.ts
│   │   └── value-objects/         # Objetos de valor
│   │       ├── Email.ts
│   │       └── Money.ts
│   │
│   ├── application/               # Casos de uso
│   │   ├── use-cases/
│   │   │   ├── user/
│   │   │   │   ├── CreateUser.ts
│   │   │   │   ├── GetUserById.ts
│   │   │   │   └── UpdateUser.ts
│   │   │   └── product/
│   │   │       ├── CreateProduct.ts
│   │   │       └── ListProducts.ts
│   │   ├── dtos/                  # Data Transfer Objects
│   │   │   ├── CreateUserDTO.ts
│   │   │   └── ProductResponseDTO.ts
│   │   └── interfaces/            # Interfaces de servicios
│   │       ├── IEmailService.ts
│   │       └── IPaymentService.ts
│   │
│   ├── infrastructure/            # Implementaciones concretas como conexiones a ddbb
│   │   ├── database/
│   │   │   ├── repositories/
│   │   │   │   ├── UserRepository.ts
│   │   │   │   └── ProductRepository.ts
│   │   │   ├── models/            # Modelos ORM
│   │   │   │   ├── UserModel.ts
│   │   │   │   └── ProductModel.ts
│   │   │   └── connection.ts
│   │   ├── services/
│   │   │   ├── EmailService.ts
│   │   │   └── PaymentService.ts
│   │   └── config/
│   │       └── database.config.ts
│   │
│   └── presentation/              # Capa de presentación
│       ├── controllers/
│       │   ├── UserController.ts
│       │   └── ProductController.ts
│       ├── middlewares/
│       │   ├── auth.middleware.ts
│       │   └── error.middleware.ts
│       ├── routes/
│       │   ├── user.routes.ts
│       │   └── product.routes.ts
│       └── validators/
│           ├── userValidator.ts
│           └── productValidator.ts
│
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/
│
└── package.json
```

## Ejemplo de Código

### 1. Entidad (Domain)

```typescript
// src/domain/entities/User.ts
import { Email } from '../value-objects/Email';

export class User {
  constructor(
    public readonly id: string,
    public name: string,
    public email: Email,
    public readonly createdAt: Date
  ) {}

  changeName(newName: string): void {
    if (newName.length < 2) {
      throw new Error('Name must be at least 2 characters');
    }
    this.name = newName;
  }

  changeEmail(newEmail: Email): void {
    this.email = newEmail;
  }
}
```

### 2. Interfaz de Repositorio (Domain)

```typescript
// src/domain/repositories/IUserRepository.ts
import { User } from '../entities/User';

export interface IUserRepository {
  save(user: User): Promise<User>;
  findById(id: string): Promise<User | null>;
  findByEmail(email: string): Promise<User | null>;
  update(user: User): Promise<User>;
  delete(id: string): Promise<void>;
}
```

### 3. Caso de Uso (Application)

```typescript
// src/application/use-cases/user/CreateUser.ts
import { IUserRepository } from '../../../domain/repositories/IUserRepository';
import { User } from '../../../domain/entities/User';
import { Email } from '../../../domain/value-objects/Email';
import { CreateUserDTO } from '../../dtos/CreateUserDTO';

export class CreateUserUseCase {
  constructor(private userRepository: IUserRepository) {}

  async execute(dto: CreateUserDTO): Promise<User> {
    const email = new Email(dto.email);
    
    const existingUser = await this.userRepository.findByEmail(email.value);
    if (existingUser) {
      throw new Error('User already exists');
    }

    const user = new User(
      this.generateId(),
      dto.name,
      email,
      new Date()
    );

    return await this.userRepository.save(user);
  }

  private generateId(): string {
    return Date.now().toString(36) + Math.random().toString(36).substr(2);
  }
}
```

### 4. Repositorio Concreto (Infrastructure)

```typescript
// src/infrastructure/database/repositories/UserRepository.ts
import { IUserRepository } from '../../../domain/repositories/IUserRepository';
import { User } from '../../../domain/entities/User';
import { Email } from '../../../domain/value-objects/Email';
import { UserModel } from '../models/UserModel';

export class UserRepository implements IUserRepository {
  async save(user: User): Promise<User> {
    const userModel = await UserModel.create({
      id: user.id,
      name: user.name,
      email: user.email.value,
      createdAt: user.createdAt
    });

    return this.toDomain(userModel);
  }

  async findById(id: string): Promise<User | null> {
    const userModel = await UserModel.findByPk(id);
    return userModel ? this.toDomain(userModel) : null;
  }

  async findByEmail(email: string): Promise<User | null> {
    const userModel = await UserModel.findOne({ where: { email } });
    return userModel ? this.toDomain(userModel) : null;
  }

  async update(user: User): Promise<User> {
    await UserModel.update(
      { name: user.name, email: user.email.value },
      { where: { id: user.id } }
    );
    return user;
  }

  async delete(id: string): Promise<void> {
    await UserModel.destroy({ where: { id } });
  }

  private toDomain(model: any): User {
    return new User(
      model.id,
      model.name,
      new Email(model.email),
      model.createdAt
    );
  }
}
```

### 5. Controlador (Presentation)

```typescript
// src/presentation/controllers/UserController.ts
import { Request, Response } from 'express';
import { CreateUserUseCase } from '../../application/use-cases/user/CreateUser';

export class UserController {
  constructor(private createUserUseCase: CreateUserUseCase) {}

  async create(req: Request, res: Response): Promise<void> {
    try {
      const user = await this.createUserUseCase.execute(req.body);
      res.status(201).json({
        success: true,
        data: {
          id: user.id,
          name: user.name,
          email: user.email.value,
          createdAt: user.createdAt
        }
      });
    } catch (error) {
      res.status(400).json({
        success: false,
        error: error.message
      });
    }
  }
}
```

## Ventajas de esta arquitectura

- **Alta testeabilidad**: Las capas internas se pueden probar sin dependencias externas.
- **Mantenibilidad**: Cambios en una capa no afectan a las demás.
- **Escalabilidad**: Fácil agregar nuevas funcionalidades.
- **Flexibilidad**: Podes cambiar tecnologías sin afectar la lógica de negocio.
- **Claridad**: Separación clara de responsabilidades.

## Desventajas

- **Complejidad inicial**: Requiere más estructura desde el principio.
- **Curva de aprendizaje**: Puede ser difícil para equipos nuevos o para adaptarla a un proyecto existente.
- **Boilerplate**: Más código y archivos para mantener.
- **Over-engineering**: Para proyectos pequeños puede ser excesivo.

## Casos de Uso Recomendados

- Aplicaciones empresariales complejas.
- Proyectos con ciclo de vida largo.
- Equipos grandes que necesitan estructura clara.
- Aplicaciones que requieren alta testeabilidad.
- Proyectos donde las reglas de negocio son complejas y centrales.

## Recursos Adicionales

- [The Clean Architecture - Uncle Bob](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
- [Clean Architecture Book](https://www.amazon.com/Clean-Architecture-Craftsmans-Software-Structure/dp/0134494164)
- [Clean Architecture Example](https://github.com/eduardomoroni/clean-architecture-example)

## Flujo de Dependencias

```
Presentation → Application → Domain
Infrastructure → Application → Domain
```

Las dependencias siempre apuntan hacia adentro, hacia las reglas de negocio.