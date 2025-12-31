# Atomic Design - Frontend

## Descripción

Atomic Design es una metodología creada por Brad Frost para diseñar y desarrollar sistemas de diseño de manera sistemática y escalable. Se basa en la química como metáfora: los componentes se construyen desde elementos pequeños e indivisibles (átomos) hasta estructuras completas (páginas).

### Principios fundamentales

- **Jerarquía clara**: Cinco niveles bien definidos de componentes.
- **Reutilización**: Los componentes se reutilizan en diferentes contextos.
- **Consistencia**: Sistema de diseño unificado.
- **Escalabilidad**: Fácil mantener y expandir.
- **Pensamiento modular**: Construir desde lo simple a lo complejo.

### Los 5 Niveles de Atomic Design

```
Átomos → Moléculas → Organismos → Templates → Páginas
```

1. **Átomos**: Elementos HTML básicos (botones, inputs, labels).
2. **Moléculas**: Grupos simples de átomos (campo de búsqueda, card básica).
3. **Organismos**: Componentes complejos reutilizables (header, formulario completo, footer).
4. **Templates**: Estructura de página sin contenido real.
5. **Páginas**: Instancias de templates con contenido real.

## 🗂️ Estructura del Proyecto

```
project-example/
├── src/
│   ├── components/
│   │   ├── atoms/                        # Elementos más básicos
│   │   │   ├── Button/
│   │   │   │   ├── Button.tsx
│   │   │   │   ├── Button.module.css
│   │   │   │   ├── Button.test.tsx
│   │   │   │   ├── Button.stories.tsx    # Storybook
│   │   │   │   └── index.ts
│   │   │   ├── Input/
│   │   │   │   ├── Input.tsx
│   │   │   │   ├── Input.module.css
│   │   │   │   └── index.ts
│   │   │   ├── Label/
│   │   │   ├── Icon/
│   │   │   ├── Avatar/
│   │   │   ├── Badge/
│   │   │   ├── Checkbox/
│   │   │   ├── Radio/
│   │   │   ├── Text/
│   │   │   ├── Heading/
│   │   │   ├── Link/
│   │   │   ├── Image/
│   │   │   └── Spinner/
│   │   │
│   │   ├── molecules/                    # Combinaciones simples
│   │   │   ├── SearchBar/
│   │   │   │   ├── SearchBar.tsx
│   │   │   │   ├── SearchBar.module.css
│   │   │   │   └── index.ts
│   │   │   ├── FormField/                # Label + Input + Error
│   │   │   │   ├── FormField.tsx
│   │   │   │   └── index.ts
│   │   │   ├── Card/
│   │   │   │   ├── Card.tsx
│   │   │   │   ├── CardHeader.tsx
│   │   │   │   ├── CardBody.tsx
│   │   │   │   ├── CardFooter.tsx
│   │   │   │   └── index.ts
│   │   │   ├── UserInfo/                 # Avatar + Name + Role
│   │   │   ├── PriceTag/                 # Price + Currency + Badge
│   │   │   ├── SocialShare/
│   │   │   ├── Breadcrumb/
│   │   │   ├── Pagination/
│   │   │   ├── Rating/
│   │   │   ├── Tabs/
│   │   │   └── Accordion/
│   │   │
│   │   ├── organisms/                    # Componentes complejos
│   │   │   ├── Header/
│   │   │   │   ├── Header.tsx
│   │   │   │   ├── Header.module.css
│   │   │   │   ├── Navigation.tsx
│   │   │   │   ├── UserMenu.tsx
│   │   │   │   └── index.ts
│   │   │   ├── Footer/
│   │   │   │   ├── Footer.tsx
│   │   │   │   ├── FooterLinks.tsx
│   │   │   │   ├── SocialLinks.tsx
│   │   │   │   └── index.ts
│   │   │   ├── ProductCard/
│   │   │   │   ├── ProductCard.tsx
│   │   │   │   └── index.ts
│   │   │   ├── ProductList/
│   │   │   ├── LoginForm/
│   │   │   │   ├── LoginForm.tsx
│   │   │   │   ├── LoginForm.module.css
│   │   │   │   └── index.ts
│   │   │   ├── RegisterForm/
│   │   │   ├── ShoppingCart/
│   │   │   ├── CommentSection/
│   │   │   ├── Sidebar/
│   │   │   └── Hero/
│   │   │
│   │   └── templates/                    # Layouts de página
│   │       ├── MainLayout/
│   │       │   ├── MainLayout.tsx
│   │       │   ├── MainLayout.module.css
│   │       │   └── index.ts
│   │       ├── DashboardLayout/
│   │       ├── AuthLayout/
│   │       └── ProductLayout/
│   │
│   ├── pages/                            # Páginas con contenido real
│   │   ├── Home/
│   │   │   ├── HomePage.tsx
│   │   │   └── index.ts
│   │   ├── Products/
│   │   │   ├── ProductsPage.tsx
│   │   │   ├── ProductDetailPage.tsx
│   │   │   └── index.ts
│   │   ├── Auth/
│   │   │   ├── LoginPage.tsx
│   │   │   ├── RegisterPage.tsx
│   │   │   └── index.ts
│   │   └── Dashboard/
│   │       ├── DashboardPage.tsx
│   │       └── index.ts
│   │
│   ├── styles/                           # Estilos globales
│   │   ├── tokens/                       # Design tokens
│   │   │   ├── colors.css
│   │   │   ├── typography.css
│   │   │   ├── spacing.css
│   │   │   └── breakpoints.css
│   │   ├── global.css
│   │   └── reset.css
│   │
│   ├── hooks/                            # Custom hooks
│   │   ├── useForm.ts
│   │   ├── useDebounce.ts
│   │   └── useMediaQuery.ts
│   │
│   ├── utils/
│   │   ├── validators.ts
│   │   └── formatters.ts
│   │
│   └── types/
│       └── index.ts
│
│
└── package.json
```

## Ejemplos de Código

### 1. Átomos - Button

```tsx
// src/components/atoms/Button/Button.tsx
import { ButtonHTMLAttributes, ReactNode } from 'react';
import styles from './Button.module.css';

export type ButtonVariant = 'primary' | 'secondary' | 'outline' | 'ghost' | 'danger';
export type ButtonSize = 'small' | 'medium' | 'large';

export interface ButtonProps extends ButtonHTMLAttributes<HTMLButtonElement> {
  children: ReactNode;
  variant?: ButtonVariant;
  size?: ButtonSize;
  fullWidth?: boolean;
  isLoading?: boolean;
  icon?: ReactNode;
  iconPosition?: 'left' | 'right';
}

export const Button = ({
  children,
  variant = 'primary',
  size = 'medium',
  fullWidth = false,
  isLoading = false,
  icon,
  iconPosition = 'left',
  className = '',
  disabled,
  ...props
}: ButtonProps) => {
  const buttonClasses = [
    styles.button,
    styles[variant],
    styles[size],
    fullWidth && styles.fullWidth,
    isLoading && styles.loading,
    className
  ].filter(Boolean).join(' ');

  return (
    <button
      className={buttonClasses}
      disabled={disabled || isLoading}
      {...props}
    >
      {isLoading ? (
        <span className={styles.spinner} />
      ) : (
        <>
          {icon && iconPosition === 'left' && (
            <span className={styles.iconLeft}>{icon}</span>
          )}
          {children}
          {icon && iconPosition === 'right' && (
            <span className={styles.iconRight}>{icon}</span>
          )}
        </>
      )}
    </button>
  );
};
```

```css
/* src/components/atoms/Button/Button.module.css */
.button {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
  font-family: inherit;
  font-weight: 600;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  transition: all 0.2s ease;
  text-decoration: none;
}

.button:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

/* Tamaños */
.small {
  padding: 0.5rem 1rem;
  font-size: 0.875rem;
}

.medium {
  padding: 0.75rem 1.5rem;
  font-size: 1rem;
}

.large {
  padding: 1rem 2rem;
  font-size: 1.125rem;
}

/* Variantes */
.primary {
  background-color: var(--color-primary);
  color: white;
}

.primary:hover:not(:disabled) {
  background-color: var(--color-primary-dark);
}

.secondary {
  background-color: var(--color-secondary);
  color: white;
}

.outline {
  background-color: transparent;
  border: 2px solid var(--color-primary);
  color: var(--color-primary);
}

.ghost {
  background-color: transparent;
  color: var(--color-text);
}

.danger {
  background-color: var(--color-danger);
  color: white;
}

.fullWidth {
  width: 100%;
}

.loading {
  position: relative;
  color: transparent;
}

```

### 2. Átomos - Input

```tsx
// src/components/atoms/Input/Input.tsx
import { InputHTMLAttributes, forwardRef } from 'react';
import styles from './Input.module.css';

export interface InputProps extends InputHTMLAttributes<HTMLInputElement> {
  error?: boolean;
  fullWidth?: boolean;
  icon?: React.ReactNode;
}

export const Input = forwardRef<HTMLInputElement, InputProps>(
  ({ error, fullWidth, icon, className = '', ...props }, ref) => {
    const inputClasses = [
      styles.input,
      error && styles.error,
      fullWidth && styles.fullWidth,
      icon && styles.withIcon,
      className
    ].filter(Boolean).join(' ');

    return (
      <div className={styles.wrapper}>
        {icon && <span className={styles.icon}>{icon}</span>}
        <input ref={ref} className={inputClasses} {...props} />
      </div>
    );
  }
);

Input.displayName = 'Input';
```

### 3. Moléculas - FormField

```tsx
// src/components/molecules/FormField/FormField.tsx
import { InputHTMLAttributes } from 'react';
import { Input } from '../../atoms/Input';
import { Label } from '../../atoms/Label';
import { Text } from '../../atoms/Text';
import styles from './FormField.module.css';

export interface FormFieldProps extends InputHTMLAttributes<HTMLInputElement> {
  label: string;
  error?: string;
  helperText?: string;
  required?: boolean;
  icon?: React.ReactNode;
}

export const FormField = ({
  label,
  error,
  helperText,
  required,
  id,
  icon,
  ...inputProps
}: FormFieldProps) => {
  const inputId = id || `field-${label.toLowerCase().replace(/\s+/g, '-')}`;

  return (
    <div className={styles.formField}>
      <Label htmlFor={inputId} required={required}>
        {label}
      </Label>
      
      <Input
        id={inputId}
        error={!!error}
        icon={icon}
        aria-invalid={!!error}
        aria-describedby={error ? `${inputId}-error` : undefined}
        {...inputProps}
      />
      
      {error && (
        <Text
          id={`${inputId}-error`}
          variant="small"
          color="danger"
          className={styles.errorText}
        >
          {error}
        </Text>
      )}
      
      {!error && helperText && (
        <Text variant="small" color="muted" className={styles.helperText}>
          {helperText}
        </Text>
      )}
    </div>
  );
};
```

### 4. Moléculas - SearchBar

```tsx
// src/components/molecules/SearchBar/SearchBar.tsx
import { useState, ChangeEvent, FormEvent } from 'react';
import { Input } from '../../atoms/Input';
import { Button } from '../../atoms/Button';
import { Icon } from '../../atoms/Icon';
import styles from './SearchBar.module.css';

export interface SearchBarProps {
  placeholder?: string;
  onSearch: (query: string) => void;
  initialValue?: string;
}

export const SearchBar = ({
  placeholder = 'Buscar...',
  onSearch,
  initialValue = ''
}: SearchBarProps) => {
  const [query, setQuery] = useState(initialValue);

  const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
    setQuery(e.target.value);
  };

  const handleSubmit = (e: FormEvent) => {
    e.preventDefault();
    onSearch(query);
  };

  const handleClear = () => {
    setQuery('');
    onSearch('');
  };

  return (
    <form className={styles.searchBar} onSubmit={handleSubmit}>
      <Input
        type="search"
        value={query}
        onChange={handleChange}
        placeholder={placeholder}
        icon={<Icon name="search" />}
        fullWidth
      />
      
      {query && (
        <Button
          type="button"
          variant="ghost"
          size="small"
          onClick={handleClear}
          className={styles.clearButton}
        >
          <Icon name="x" />
        </Button>
      )}
      
      <Button type="submit" size="medium">
        Buscar
      </Button>
    </form>
  );
};
```

### 5. Moléculas - Card

```tsx
// src/components/molecules/Card/Card.tsx
import { ReactNode } from 'react';
import styles from './Card.module.css';

export interface CardProps {
  children: ReactNode;
  variant?: 'default' | 'elevated' | 'outlined';
  padding?: 'none' | 'small' | 'medium' | 'large';
  onClick?: () => void;
  className?: string;
}

export const Card = ({
  children,
  variant = 'default',
  padding = 'medium',
  onClick,
  className = ''
}: CardProps) => {
  const cardClasses = [
    styles.card,
    styles[variant],
    styles[`padding-${padding}`],
    onClick && styles.clickable,
    className
  ].filter(Boolean).join(' ');

  const Component = onClick ? 'button' : 'div';

  return (
    <Component className={cardClasses} onClick={onClick}>
      {children}
    </Component>
  );
};

// Subcomponentes
export const CardHeader = ({ children, className = '' }: { children: ReactNode; className?: string }) => (
  <div className={`${styles.cardHeader} ${className}`}>{children}</div>
);

export const CardBody = ({ children, className = '' }: { children: ReactNode; className?: string }) => (
  <div className={`${styles.cardBody} ${className}`}>{children}</div>
);

export const CardFooter = ({ children, className = '' }: { children: ReactNode; className?: string }) => (
  <div className={`${styles.cardFooter} ${className}`}>{children}</div>
);
```

### 6. Organismos - LoginForm

```tsx
// src/components/organisms/LoginForm/LoginForm.tsx
import { useState, FormEvent } from 'react';
import { FormField } from '../../molecules/FormField';
import { Button } from '../../atoms/Button';
import { Heading } from '../../atoms/Heading';
import { Text } from '../../atoms/Text';
import { Link } from '../../atoms/Link';
import { Icon } from '../../atoms/Icon';
import styles from './LoginForm.module.css';

export interface LoginFormProps {
  onSubmit: (email: string, password: string) => Promise<void>;
  onForgotPassword?: () => void;
}

export const LoginForm = ({ onSubmit, onForgotPassword }: LoginFormProps) => {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [errors, setErrors] = useState<{ email?: string; password?: string }>({});
  const [isLoading, setIsLoading] = useState(false);

  const validate = () => {
    const newErrors: { email?: string; password?: string } = {};

    if (!email) {
      newErrors.email = 'El email es requerido';
    } else if (!/\S+@\S+\.\S+/.test(email)) {
      newErrors.email = 'Email inválido';
    }

    if (!password) {
      newErrors.password = 'La contraseña es requerida';
    } else if (password.length < 6) {
      newErrors.password = 'La contraseña debe tener al menos 6 caracteres';
    }

    setErrors(newErrors);
    return Object.keys(newErrors).length === 0;
  };

  const handleSubmit = async (e: FormEvent) => {
    e.preventDefault();

    if (!validate()) return;

    setIsLoading(true);
    try {
      await onSubmit(email, password);
    } catch (error) {
      setErrors({
        email: 'Credenciales inválidas',
        password: ' '
      });
    } finally {
      setIsLoading(false);
    }
  };

  return (
    <form className={styles.loginForm} onSubmit={handleSubmit}>
      <div className={styles.header}>
        <Heading level={2}>Iniciar Sesión</Heading>
        <Text color="muted">
          Ingresa tus credenciales para acceder
        </Text>
      </div>

      <div className={styles.fields}>
        <FormField
          label="Email"
          type="email"
          value={email}
          onChange={(e) => setEmail(e.target.value)}
          error={errors.email}
          icon={<Icon name="mail" />}
          required
          autoComplete="email"
        />

        <FormField
          label="Contraseña"
          type="password"
          value={password}
          onChange={(e) => setPassword(e.target.value)}
          error={errors.password}
          icon={<Icon name="lock" />}
          required
          autoComplete="current-password"
        />
      </div>

      {onForgotPassword && (
        <div className={styles.forgotPassword}>
          <Link onClick={onForgotPassword}>
            ¿Olvidaste tu contraseña?
          </Link>
        </div>
      )}

      <Button
        type="submit"
        fullWidth
        size="large"
        isLoading={isLoading}
      >
        Ingresar
      </Button>

      <div className={styles.footer}>
        <Text color="muted">
          ¿No tienes cuenta?{' '}
          <Link href="/register">Regístrate aquí</Link>
        </Text>
      </div>
    </form>
  );
};
```

### 7. Organismos - Header

```tsx
// src/components/organisms/Header/Header.tsx
import { useState } from 'react';
import { Button } from '../../atoms/Button';
import { Icon } from '../../atoms/Icon';
import { Avatar } from '../../atoms/Avatar';
import { SearchBar } from '../../molecules/SearchBar';
import styles from './Header.module.css';

export interface HeaderProps {
  user?: {
    name: string;
    avatar?: string;
  };
  onSearch?: (query: string) => void;
  onLogin?: () => void;
  onLogout?: () => void;
}

export const Header = ({ user, onSearch, onLogin, onLogout }: HeaderProps) => {
  const [mobileMenuOpen, setMobileMenuOpen] = useState(false);

  return (
    <header className={styles.header}>
      <div className={styles.container}>
        <div className={styles.logo}>
          <Icon name="package" size="large" />
          <span className={styles.logoText}>MyStore</span>
        </div>

        {onSearch && (
          <div className={styles.search}>
            <SearchBar onSearch={onSearch} placeholder="Buscar productos..." />
          </div>
        )}

        <nav className={styles.nav}>
          <Button variant="ghost" icon={<Icon name="home" />}>
            Inicio
          </Button>
          <Button variant="ghost" icon={<Icon name="grid" />}>
            Productos
          </Button>
          <Button variant="ghost" icon={<Icon name="shopping-cart" />}>
            Carrito
          </Button>
        </nav>

        <div className={styles.actions}>
          {user ? (
            <div className={styles.userMenu}>
              <Avatar src={user.avatar} alt={user.name} size="small" />
              <span className={styles.userName}>{user.name}</span>
              <Button variant="ghost" size="small" onClick={onLogout}>
                Salir
              </Button>
            </div>
          ) : (
            <Button onClick={onLogin}>Ingresar</Button>
          )}
        </div>

        <button
          className={styles.mobileMenuButton}
          onClick={() => setMobileMenuOpen(!mobileMenuOpen)}
        >
          <Icon name={mobileMenuOpen ? 'x' : 'menu'} />
        </button>
      </div>

      {mobileMenuOpen && (
        <div className={styles.mobileMenu}>
          <nav className={styles.mobileNav}>
            <Button variant="ghost" fullWidth>Inicio</Button>
            <Button variant="ghost" fullWidth>Productos</Button>
            <Button variant="ghost" fullWidth>Carrito</Button>
          </nav>
        </div>
      )}
    </header>
  );
};
```

### 8. Template - MainLayout

```tsx
// src/components/templates/MainLayout/MainLayout.tsx
import { ReactNode } from 'react';
import { Header } from '../../organisms/Header';
import { Footer } from '../../organisms/Footer';
import styles from './MainLayout.module.css';

export interface MainLayoutProps {
  children: ReactNode;
  user?: {
    name: string;
    avatar?: string;
  };
  onSearch?: (query: string) => void;
  onLogin?: () => void;
  onLogout?: () => void;
}

export const MainLayout = ({
  children,
  user,
  onSearch,
  onLogin,
  onLogout
}: MainLayoutProps) => {
  return (
    <div className={styles.layout}>
      <Header
        user={user}
        onSearch={onSearch}
        onLogin={onLogin}
        onLogout={onLogout}
      />
      
      <main className={styles.main}>
        <div className={styles.container}>
          {children}
        </div>
      </main>
      
      <Footer />
    </div>
  );
};
```

### 9. Página - HomePage

```tsx
// src/pages/Home/HomePage.tsx
import { MainLayout } from '../../components/templates/MainLayout';
import { Hero } from '../../components/organisms/Hero';
import { ProductList } from '../../components/organisms/ProductList';
import { useAuth } from '../../hooks/useAuth';
import { useProducts } from '../../hooks/useProducts';

export const HomePage = () => {
  const { user, login, logout } = useAuth();
  const { products, searchProducts } = useProducts();

  return (
    <MainLayout
      user={user}
      onSearch={searchProducts}
      onLogin={login}
      onLogout={logout}
    >
      <Hero
        title="Bienvenido a MyStore"
        subtitle="Encuentra los mejores productos al mejor precio"
        ctaText="Ver productos"
        ctaLink="/products"
      />
      
      <ProductList products={products} />
    </MainLayout>
  );
};
```

## 🎨 Design Tokens

```css
/* src/styles/tokens/colors.css */
:root {
  /* Primary */
  --color-primary: #3b82f6;
  --color-primary-dark: #2563eb;
  --color-primary-light: #60a5fa;
  
  /* Secondary */
  --color-secondary: #8b5cf6;
  
  /* Neutrals */
  --color-gray-50: #f9fafb;
  --color-gray-100: #f3f4f6;
  --color-gray-200: #e5e7eb;
  --color-gray-300: #d1d5db;
  --color-gray-500: #6b7280;
  --color-gray-700: #374151;
  --color-gray-900: #111827;
  
  /* Semantic */
  --color-success: #10b981;
  --color-warning: #f59e0b;
  --color-danger: #ef4444;
  --color-info: #3b82f6;
  
  /* Text */
  --color-text: var(--color-gray-900);
  --color-text-muted: var(--color-gray-500);
}
```

## Ventajas

- **Sistema de diseño coherente**: Todos los componentes siguen las mismas reglas.
- **Altamente reutilizable**: Los componentes se pueden usar en múltiples contextos.
- **Fácil de mantener**: Cambios en átomos se propagan automáticamente.
- **Escalable**: Agregar nuevos componentes es sistemático.
- **Documentación clara**: Storybook permite visualizar cada componente.
- **Testeable**: Cada nivel se puede testear independientemente.
- **Colaboración diseño-desarrollo**: Lenguaje común entre equipos.

## Desventajas

- **Curva de aprendizaje**: Requiere entender la metodología.
- **Sobre-abstracción**: Puede llevar a componentes demasiado genéricos.
- **Decisiones de categorización**: No siempre es claro dónde va cada componente.
- **Boilerplate inicial**: Muchos archivos y estructura desde el inicio.

## Casos de Uso Recomendados

- Aplicaciones con Design System definido.
- Proyectos medianos a grandes con múltiples páginas.
- Equipos que trabajan con diseñadores.
- Productos que requieren consistencia visual.
- Aplicaciones que se expandirán con el tiempo.
- Proyectos que necesitan documentación de componentes.

## Flujo de Trabajo

1. **Diseño**: El diseñador crea el sistema de diseño.
2. **Átomos**: Desarrollar los componentes más básicos.
3. **Moléculas**: Combinar átomos en grupos funcionales.
4. **Organismos**: Crear secciones complejas.
5. **Templates**: Definir layouts de página.
6. **Páginas**: Implementar con contenido real.
7. **Documentación**: Documentar en Storybook.
8. **Testing**: Testear cada nivel.

## Recursos Adicionales

- [Atomic Design by Brad Frost](https://atomicdesign.bradfrost.com/)
- [Pattern Lab](https://patternlab.io/)
- [Storybook Documentation](https://storybook.js.org/)
- [Design Systems](https://www.designsystems.com/)

## Tips y Mejores Prácticas

1. **Empezar con los átomos**: Primero construir la base.
2. **Usar Storybook**: Para documentar cada componente a medida que se crea.
3. **Design tokens primero**: Definir colores, tipografía y espaciado antes de empezar.
4. **Composición sobre herencia**: Combinacion de componentes en lugar de heredar.
5. **Props consistentes**: Usar nombres de props similares en todos los componentes.
6. **Accesibilidad**: Implementar ARIA labels en los átomos.
7. **Mobile first**: Usar este enfoque apra disenar primero para mobile y luego ir escalando.
8. **No te obsesiones con la clasificación**: Si no sabes dónde va, empieza como organismo.
9. **Atomos puros**: Sin lógica de negocio, solo presentación.