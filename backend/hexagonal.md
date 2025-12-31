# Arquitectura Hexagonal (Ports & Adapters) - Backend

## Descripción

La Arquitectura Hexagonal, también conocida como Ports y Adapters, es un patrón arquitectónico creado por Alistair Cockburn que se centra en aislar la lógica de negocio de las dependencias externas mediante puertos (interfaces) y adaptadores (implementaciones).

### Principios fundamentales

- **Separación entre lógica de negocio y tecnología**: El core no conoce detalles técnicos.
- **Inversión de dependencias**: Las dependencias apuntan hacia el dominio.
- **Puertos**: Interfaces que definen cómo interactuar con el core.
- **Adaptadores**: Implementaciones concretas de los puertos.
- **Testeable por diseño**: El core se puede testear sin infraestructura.

### Concepto del Hexágono

El hexágono representa el núcleo de la aplicación (dominio). Los puertos son las conexiones y los adaptadores son las implementaciones externas que se conectan a esos puertos.

## Estructura del Proyecto

```
project-example/
├── src/
│   ├── domain/                           # Núcleo del hexágono
│   │   ├── models/                       # Entidades y objetos de valor
│   │   │   ├── User.ts
│   │   │   ├── Product.ts
│   │   │   └── Order.ts
│   │   ├── services/                     # Servicios de dominio
│   │   │   ├── UserService.ts
│   │   │   ├── ProductService.ts
│   │   │   └── OrderService.ts
│   │   ├── exceptions/                   # Excepciones de dominio
│   │   │   ├── UserNotFoundException.ts
│   │   │   └── InsufficientStockException.ts
│   │   └── value-objects/
│   │       ├── Email.ts
│   │       ├── Money.ts
│   │       └── OrderStatus.ts
│   │
│   ├── application/                      # Casos de uso (orquestación)
│   │   ├── use-cases/
│   │   │   ├── user/
│   │   │   │   ├── RegisterUser.ts
│   │   │   │   ├── LoginUser.ts
│   │   │   │   └── GetUserProfile.ts
│   │   │   ├── product/
│   │   │   │   ├── CreateProduct.ts
│   │   │   │   ├── UpdateProductStock.ts
│   │   │   │   └── SearchProducts.ts
│   │   │   └── order/
│   │   │       ├── CreateOrder.ts
│   │   │       ├── ProcessPayment.ts
│   │   │       └── CancelOrder.ts
│   │   └── dtos/
│   │       ├── RegisterUserDTO.ts
│   │       ├── CreateProductDTO.ts
│   │       └── CreateOrderDTO.ts
│   │
│   ├── ports/                            # Puertos (interfaces)
│   │   ├── input/                        # Puertos de entrada (driving)
│   │   │   ├── IUserService.ts
│   │   │   ├── IProductService.ts
│   │   │   └── IOrderService.ts
│   │   └── output/                       # Puertos de salida (driven)
│   │       ├── repositories/
│   │       │   ├── IUserRepository.ts
│   │       │   ├── IProductRepository.ts
│   │       │   └── IOrderRepository.ts
│   │       ├── services/
│   │       │   ├── IEmailService.ts
│   │       │   ├── IPaymentService.ts
│   │       │   └── INotificationService.ts
│   │       └── cache/
│   │           └── ICacheService.ts
│   │
│   └── adapters/                         # Adaptadores (implementaciones)
│       ├── input/                        # Adaptadores de entrada
│       │   ├── http/                     # API REST
│       │   │   ├── controllers/
│       │   │   │   ├── UserController.ts
│       │   │   │   ├── ProductController.ts
│       │   │   │   └── OrderController.ts
│       │   │   ├── middlewares/
│       │   │   │   ├── authMiddleware.ts
│       │   │   │   ├── errorMiddleware.ts
│       │   │   │   └── validationMiddleware.ts
│       │   │   ├── routes/
│       │   │   │   ├── userRoutes.ts
│       │   │   │   ├── productRoutes.ts
│       │   │   │   └── orderRoutes.ts
│       │   │   └── server.ts
│       │   └── cli/                      # Línea de comandos (opcional)
│       │       └── commands/
│       │           ├── SeedCommand.ts
│       │           └── MigrateCommand.ts
│       │
│       └── output/                       # Adaptadores de salida
│           ├── repositories/
│           │   ├── postgres/
│           │   │   ├── PostgresUserRepository.ts
│           │   │   ├── PostgresProductRepository.ts
│           │   │   ├── PostgresOrderRepository.ts
│           │   │   └── connection.ts
│           │   └── mongodb/              # Alternativa
│           │       ├── MongoUserRepository.ts
│           │       └── connection.ts
│           ├── services/
│           │   ├── email/
│           │   │   ├── SendGridEmailService.ts
│           │   │   └── MailgunEmailService.ts
│           │   ├── payment/
│           │   │   ├── StripePaymentService.ts
│           │   │   └── PayPalPaymentService.ts
│           │   └── notification/
│           │       └── FirebaseNotificationService.ts
│           └── cache/
│               ├── RedisCache.ts
│               └── InMemoryCache.ts
│
├── tests/
│   ├── unit/
│   │   ├── domain/
│   │   └── application/
│   ├── integration/
│   │   └── adapters/
│   └── e2e/
│
├── config/
│   ├── database.config.ts
│   └── app.config.ts
│
└── package.json
```

## Ejemplo de Código

### 1. Modelo de Dominio

```typescript
// src/domain/models/User.ts
import { Email } from '../value-objects/Email';

export class User {
  constructor(
    public readonly id: string,
    public name: string,
    public email: Email,
    public passwordHash: string,
    public readonly createdAt: Date,
    public isActive: boolean = true
  ) {}

  deactivate(): void {
    if (!this.isActive) {
      throw new Error('User is already deactivated');
    }
    this.isActive = false;
  }

  activate(): void {
    if (this.isActive) {
      throw new Error('User is already active');
    }
    this.isActive = true;
  }

  updateEmail(newEmail: Email): void {
    this.email = newEmail;
  }

  changeName(newName: string): void {
    if (newName.trim().length < 2) {
      throw new Error('Name must be at least 2 characters');
    }
    this.name = newName;
  }
}
```

```typescript
// src/domain/value-objects/Email.ts
export class Email {
  private readonly _value: string;

  constructor(email: string) {
    if (!this.isValid(email)) {
      throw new Error('Invalid email format');
    }
    this._value = email.toLowerCase();
  }

  get value(): string {
    return this._value;
  }

  private isValid(email: string): boolean {
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    return emailRegex.test(email);
  }

  equals(other: Email): boolean {
    return this._value === other._value;
  }
}
```

### 2. Puerto de Salida (Output Port)

```typescript
// src/ports/output/repositories/IUserRepository.ts
import { User } from '../../../domain/models/User';

export interface IUserRepository {
  save(user: User): Promise<User>;
  findById(id: string): Promise<User | null>;
  findByEmail(email: string): Promise<User | null>;
  findAll(): Promise<User[]>;
  update(user: User): Promise<User>;
  delete(id: string): Promise<void>;
  existsByEmail(email: string): Promise<boolean>;
}
```

```typescript
// src/ports/output/services/IEmailService.ts
export interface IEmailService {
  sendWelcomeEmail(to: string, userName: string): Promise<void>;
  sendPasswordResetEmail(to: string, resetToken: string): Promise<void>;
  sendOrderConfirmation(to: string, orderDetails: any): Promise<void>;
}
```

### 3. Servicio de Dominio

```typescript
// src/domain/services/UserService.ts
import { User } from '../models/User';
import { Email } from '../value-objects/Email';
import * as bcrypt from 'bcrypt';

export class UserDomainService {
  async hashPassword(password: string): Promise<string> {
    const saltRounds = 10;
    return await bcrypt.hash(password, saltRounds);
  }

  async verifyPassword(password: string, hash: string): Promise<boolean> {
    return await bcrypt.compare(password, hash);
  }

  validatePasswordStrength(password: string): boolean {
    // Mínimo 8 caracteres, una mayúscula, una minúscula, un número
    const passwordRegex = /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d).{8,}$/;
    return passwordRegex.test(password);
  }

  createUser(
    id: string,
    name: string,
    email: string,
    passwordHash: string
  ): User {
    const emailVO = new Email(email);
    return new User(id, name, emailVO, passwordHash, new Date());
  }
}
```

### 4. Caso de Uso (Application)

```typescript
// src/application/use-cases/user/RegisterUser.ts
import { IUserRepository } from '../../../ports/output/repositories/IUserRepository';
import { IEmailService } from '../../../ports/output/services/IEmailService';
import { UserDomainService } from '../../../domain/services/UserService';
import { RegisterUserDTO } from '../../dtos/RegisterUserDTO';
import { User } from '../../../domain/models/User';

export class RegisterUserUseCase {
  constructor(
    private readonly userRepository: IUserRepository,
    private readonly emailService: IEmailService,
    private readonly userDomainService: UserDomainService
  ) {}

  async execute(dto: RegisterUserDTO): Promise<User> {
    // Validar que el email no exista
    const emailExists = await this.userRepository.existsByEmail(dto.email);
    if (emailExists) {
      throw new Error('Email already registered');
    }

    // Validar fortaleza de contraseña
    if (!this.userDomainService.validatePasswordStrength(dto.password)) {
      throw new Error('Password does not meet security requirements');
    }

    // Hash de contraseña
    const passwordHash = await this.userDomainService.hashPassword(dto.password);

    // Crear usuario
    const user = this.userDomainService.createUser(
      this.generateId(),
      dto.name,
      dto.email,
      passwordHash
    );

    // Guardar usuario
    const savedUser = await this.userRepository.save(user);

    // Enviar email de bienvenida (asíncrono)
    this.emailService
      .sendWelcomeEmail(savedUser.email.value, savedUser.name)
      .catch(error => console.error('Failed to send welcome email:', error));

    return savedUser;
  }

  private generateId(): string {
    return `user_${Date.now()}_${Math.random().toString(36).substr(2, 9)}`;
  }
}
```

### 5. Adaptador de Salida - Repositorio

```typescript
// src/adapters/output/repositories/postgres/PostgresUserRepository.ts
import { IUserRepository } from '../../../../ports/output/repositories/IUserRepository';
import { User } from '../../../../domain/models/User';
import { Email } from '../../../../domain/value-objects/Email';
import { Pool } from 'pg';

export class PostgresUserRepository implements IUserRepository {
  constructor(private readonly pool: Pool) {}

  async save(user: User): Promise<User> {
    const query = `
      INSERT INTO users (id, name, email, password_hash, created_at, is_active)
      VALUES ($1, $2, $3, $4, $5, $6)
      RETURNING *
    `;
    
    const values = [
      user.id,
      user.name,
      user.email.value,
      user.passwordHash,
      user.createdAt,
      user.isActive
    ];

    const result = await this.pool.query(query, values);
    return this.mapToDomain(result.rows[0]);
  }

  async findById(id: string): Promise<User | null> {
    const query = 'SELECT * FROM users WHERE id = $1';
    const result = await this.pool.query(query, [id]);
    
    if (result.rows.length === 0) {
      return null;
    }

    return this.mapToDomain(result.rows[0]);
  }

  async findByEmail(email: string): Promise<User | null> {
    const query = 'SELECT * FROM users WHERE email = $1';
    const result = await this.pool.query(query, [email.toLowerCase()]);
    
    if (result.rows.length === 0) {
      return null;
    }

    return this.mapToDomain(result.rows[0]);
  }

  async findAll(): Promise<User[]> {
    const query = 'SELECT * FROM users ORDER BY created_at DESC';
    const result = await this.pool.query(query);
    return result.rows.map(row => this.mapToDomain(row));
  }

  async update(user: User): Promise<User> {
    const query = `
      UPDATE users 
      SET name = $1, email = $2, is_active = $3
      WHERE id = $4
      RETURNING *
    `;
    
    const values = [user.name, user.email.value, user.isActive, user.id];
    const result = await this.pool.query(query, values);
    return this.mapToDomain(result.rows[0]);
  }

  async delete(id: string): Promise<void> {
    const query = 'DELETE FROM users WHERE id = $1';
    await this.pool.query(query, [id]);
  }

  async existsByEmail(email: string): Promise<boolean> {
    const query = 'SELECT COUNT(*) FROM users WHERE email = $1';
    const result = await this.pool.query(query, [email.toLowerCase()]);
    return parseInt(result.rows[0].count) > 0;
  }

  private mapToDomain(row: any): User {
    return new User(
      row.id,
      row.name,
      new Email(row.email),
      row.password_hash,
      row.created_at,
      row.is_active
    );
  }
}
```

### 6. Adaptador de Salida - Servicio Externo

```typescript
// src/adapters/output/services/email/SendGridEmailService.ts
import { IEmailService } from '../../../../ports/output/services/IEmailService';
import * as sgMail from '@sendgrid/mail';

export class SendGridEmailService implements IEmailService {
  constructor(private readonly apiKey: string) {
    sgMail.setApiKey(this.apiKey);
  }

  async sendWelcomeEmail(to: string, userName: string): Promise<void> {
    const msg = {
      to,
      from: 'noreply@yourapp.com',
      subject: '¡Bienvenido a nuestra aplicación!',
      html: `
        <h1>Hola ${userName}!</h1>
        <p>Gracias por registrarte en nuestra aplicación.</p>
      `
    };

    try {
      await sgMail.send(msg);
    } catch (error) {
      console.error('Error sending welcome email:', error);
      throw new Error('Failed to send welcome email');
    }
  }

  async sendPasswordResetEmail(to: string, resetToken: string): Promise<void> {
    const msg = {
      to,
      from: 'noreply@yourapp.com',
      subject: 'Recuperación de contraseña',
      html: `
        <h1>Recuperación de contraseña</h1>
        <p>Haz clic en el siguiente enlace para restablecer tu contraseña:</p>
        <a href="https://yourapp.com/reset-password?token=${resetToken}">
          Restablecer contraseña
        </a>
      `
    };

    await sgMail.send(msg);
  }

  async sendOrderConfirmation(to: string, orderDetails: any): Promise<void> {
    const msg = {
      to,
      from: 'orders@yourapp.com',
      subject: 'Confirmación de pedido',
      html: `
        <h1>Tu pedido ha sido confirmado</h1>
        <p>Número de orden: ${orderDetails.orderNumber}</p>
        <p>Total: $${orderDetails.total}</p>
      `
    };

    await sgMail.send(msg);
  }
}
```

### 7. Adaptador de Entrada - Controlador HTTP

```typescript
// src/adapters/input/http/controllers/UserController.ts
import { Request, Response } from 'express';
import { RegisterUserUseCase } from '../../../../application/use-cases/user/RegisterUser';
import { GetUserProfileUseCase } from '../../../../application/use-cases/user/GetUserProfile';

export class UserController {
  constructor(
    private readonly registerUserUseCase: RegisterUserUseCase,
    private readonly getUserProfileUseCase: GetUserProfileUseCase
  ) {}

  async register(req: Request, res: Response): Promise<void> {
    try {
      const { name, email, password } = req.body;

      const user = await this.registerUserUseCase.execute({
        name,
        email,
        password
      });

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

  async getProfile(req: Request, res: Response): Promise<void> {
    try {
      const userId = req.params.id;
      const user = await this.getUserProfileUseCase.execute(userId);

      if (!user) {
        res.status(404).json({
          success: false,
          error: 'User not found'
        });
        return;
      }

      res.status(200).json({
        success: true,
        data: {
          id: user.id,
          name: user.name,
          email: user.email.value,
          isActive: user.isActive,
          createdAt: user.createdAt
        }
      });
    } catch (error) {
      res.status(500).json({
        success: false,
        error: error.message
      });
    }
  }
}
```

### 8. Inyección de Dependencias

```typescript
// src/adapters/input/http/server.ts
import express from 'express';
import { Pool } from 'pg';
import { PostgresUserRepository } from '../../output/repositories/postgres/PostgresUserRepository';
import { SendGridEmailService } from '../../output/services/email/SendGridEmailService';
import { UserDomainService } from '../../../domain/services/UserService';
import { RegisterUserUseCase } from '../../../application/use-cases/user/RegisterUser';
import { UserController } from './controllers/UserController';

// Configurar dependencias
const pool = new Pool({
  host: process.env.DB_HOST,
  port: parseInt(process.env.DB_PORT || '5432'),
  database: process.env.DB_NAME,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD
});

// Adaptadores de salida
const userRepository = new PostgresUserRepository(pool);
const emailService = new SendGridEmailService(process.env.SENDGRID_API_KEY!);

// Servicios de dominio
const userDomainService = new UserDomainService();

// Casos de uso
const registerUserUseCase = new RegisterUserUseCase(
  userRepository,
  emailService,
  userDomainService
);

// Controladores
const userController = new UserController(registerUserUseCase);

// Configurar Express
const app = express();
app.use(express.json());

// Rutas
app.post('/api/users/register', (req, res) => userController.register(req, res));

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

## Flujo de Datos

```
HTTP Request → Controller (Input Adapter)
    ↓
Use Case (Application Layer)
    ↓
Domain Service + Entities (Domain Layer)
    ↓
Repository Interface (Output Port)
    ↓
Repository Implementation (Output Adapter)
    ↓
Database
```

## Ventajas

- **Testeable**: El dominio se testea sin infraestructura.
- **Independencia tecnológica**: Puedes cambiar bases de datos, frameworks sin tocar el core.
- **Flexibilidad**: Múltiples adaptadores para el mismo puerto.
- **Mantenibilidad**: Responsabilidades claramente separadas.
- **Escalabilidad**: Fácil agregar nuevos adaptadores.
- **Inversión de dependencias**: El dominio no depende de nada.

## Desventajas

- **Complejidad inicial**: Muchas interfaces y capas.
- **Boilerplate**: Más código para escribir y mantener.
- **Curva de aprendizaje**: Requiere entender bien los conceptos.
- **Over-engineering**: Puede ser excesivo para aplicaciones simples.
- **Más archivos**: Mayor número de archivos y carpetas.

## Casos de Uso Recomendados

- Aplicaciones empresariales complejas.
- Proyectos que necesitan múltiples interfaces (API REST, GraphQL, CLI).
- Sistemas que requieren cambiar proveedores externos frecuentemente.
- Aplicaciones con lógica de negocio compleja.
- Proyectos donde la testabilidad es crítica.
- Sistemas que evolucionarán durante años.

## Comparación con Clean Architecture

| Aspecto | Hexagonal | Clean Architecture |
|---------|-----------|-------------------|
| Concepto central | Puertos y Adaptadores | Capas concéntricas |
| Énfasis | Aislamiento de tecnología | Reglas de negocio |
| Flexibilidad | Múltiples adaptadores por puerto | Capas estrictas |
| Complejidad | Media | Media-Alta |

**Similitudes**: Ambas buscan independencia de frameworks, alta testabilidad y separación de responsabilidades.

## 📚 Recursos Adicionales

- [Hexagonal Architecture - Alistair Cockburn](https://alistair.cockburn.us/hexagonal-architecture/)
- [Netflix - Ready for changes with Hexagonal Architecture](https://netflixtechblog.com/ready-for-changes-with-hexagonal-architecture-b315ec967749)
- [Ejemplo práctico en GitHub](https://github.com/Sairyss/domain-driven-hexagon)

## Tips y Mejores Prácticas

1. **Empieza por el dominio**: Defini primero tus entidades y servicios de dominio.
2. **Puertos primero, adaptadores después**: Defini las interfaces antes de implementar.
3. **Un adaptador por tecnología**: Si usas PostgreSQL y MongoDB, crear dos adaptadores.
4. **No mezcles capas**: El dominio nunca debe importar de adaptadores.
5. **Usa inyección de dependencias**: Facilita el testing y la flexibilidad.
6. **Mantencion del dominio puro**: Sin anotaciones de frameworks, solo lógica de negocio.
7. **Tests unitarios en el dominio**: Sin mocks, solo lógica pura.