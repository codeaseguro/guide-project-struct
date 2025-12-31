# Feature-Sliced Design (FSD) - Frontend

## Descripción

Feature-Sliced Design es una metodología arquitectónica para proyectos frontend que organiza el código por features (características) en lugar de tipos técnicos, promoviendo la escalabilidad y mantenibilidad.

### Principios fundamentales

- **Organización por features**: El código se agrupa por funcionalidad de negocio.
- **Capas jerárquicas**: Estructura clara de capas con dependencias unidireccionales.
- **Bajo acoplamiento**: Cada feature es independiente.
- **Alta cohesión**: Todo lo relacionado está junto.

## 🗂️ Estructura del Proyecto

```
project-root/
├── src/
│   ├── app/                       # Capa de aplicación
│   │   ├── providers/             # Providers globales (Redux, Router, Theme)
│   │   │   ├── StoreProvider.tsx
│   │   │   ├── RouterProvider.tsx
│   │   │   └── ThemeProvider.tsx
│   │   ├── styles/                # Estilos globales
│   │   │   ├── global.scss
│   │   │   └── reset.scss
│   │   ├── App.tsx
│   │   └── index.tsx
│   │
│   ├── processes/                 # Procesos complejos entre features
│   │   ├── checkout/
│   │   │   ├── model/
│   │   │   └── ui/
│   │   └── auth/
│   │       ├── model/
│   │       └── ui/
│   │
│   ├── pages/                     # Páginas de la aplicación
│   │   ├── home/
│   │   │   ├── ui/
│   │   │   │   ├── HomePage.tsx
│   │   │   │   └── HomePage.module.css
│   │   │   └── index.ts
│   │   ├── product-details/
│   │   │   ├── ui/
│   │   │   │   ├── ProductDetailsPage.tsx
│   │   │   │   └── styles.module.css
│   │   │   └── index.ts
│   │   └── cart/
│   │       ├── ui/
│   │       └── index.ts
│   │
│   ├── widgets/                   # Bloques grandes de UI compuestos
│   │   ├── header/
│   │   │   ├── ui/
│   │   │   │   ├── Header.tsx
│   │   │   │   └── Header.module.css
│   │   │   ├── model/
│   │   │   │   └── useHeader.ts
│   │   │   └── index.ts
│   │   ├── product-card-list/
│   │   │   ├── ui/
│   │   │   ├── model/
│   │   │   └── index.ts
│   │   └── footer/
│   │       ├── ui/
│   │       └── index.ts
│   │
│   ├── features/                  # Características con lógica de negocio
│   │   ├── add-to-cart/
│   │   │   ├── ui/
│   │   │   │   ├── AddToCartButton.tsx
│   │   │   │   └── styles.module.css
│   │   │   ├── model/
│   │   │   │   ├── slice.ts
│   │   │   │   └── selectors.ts
│   │   │   ├── api/
│   │   │   │   └── addToCartApi.ts
│   │   │   └── index.ts
│   │   ├── product-filter/
│   │   │   ├── ui/
│   │   │   │   ├── ProductFilter.tsx
│   │   │   │   └── FilterPanel.tsx
│   │   │   ├── model/
│   │   │   │   └── useFilter.ts
│   │   │   └── index.ts
│   │   └── auth-form/
│   │       ├── ui/
│   │       ├── model/
│   │       ├── api/
│   │       └── index.ts
│   │
│   ├── entities/                  # Entidades de negocio
│   │   ├── product/
│   │   │   ├── ui/
│   │   │   │   ├── ProductCard.tsx
│   │   │   │   └── ProductCard.module.css
│   │   │   ├── model/
│   │   │   │   ├── types.ts
│   │   │   │   └── slice.ts
│   │   │   ├── api/
│   │   │   │   └── productApi.ts
│   │   │   └── index.ts
│   │   ├── user/
│   │   │   ├── ui/
│   │   │   ├── model/
│   │   │   ├── api/
│   │   │   └── index.ts
│   │   └── order/
│   │       ├── model/
│   │       ├── api/
│   │       └── index.ts
│   │
│   └── shared/                    # Código compartido reutilizable
│       ├── ui/                    # Componentes UI básicos
│       │   ├── Button/
│       │   │   ├── Button.tsx
│       │   │   ├── Button.module.css
│       │   │   └── index.ts
│       │   ├── Input/
│       │   ├── Modal/
│       │   └── Spinner/
│       ├── lib/                   # Utilidades y helpers
│       │   ├── hooks/
│       │   │   ├── useDebounce.ts
│       │   │   └── useLocalStorage.ts
│       │   ├── utils/
│       │   │   ├── formatDate.ts
│       │   │   └── validateEmail.ts
│       │   └── constants/
│       │       └── routes.ts
│       ├── api/                   # Configuración de API
│       │   ├── baseApi.ts
│       │   └── interceptors.ts
│       └── config/
│           └── env.ts
│
├── public/
└── package.json
```

## Ejemplo de Código

### 1. Entity - Product

```typescript
// src/entities/product/model/types.ts
export interface Product {
  id: string;
  name: string;
  price: number;
  description: string;
  imageUrl: string;
  stock: number;
}

export interface ProductState {
  items: Product[];
  loading: boolean;
  error: string | null;
}
```

```tsx
// src/entities/product/ui/ProductCard.tsx
import { Product } from '../model/types';
import styles from './ProductCard.module.css';

interface ProductCardProps {
  product: Product;
  actions?: React.ReactNode;
}

export const ProductCard = ({ product, actions }: ProductCardProps) => {
  return (
    <div className={styles.card}>
      <img src={product.imageUrl} alt={product.name} />
      <h3>{product.name}</h3>
      <p className={styles.price}>${product.price}</p>
      <p className={styles.description}>{product.description}</p>
      {actions && <div className={styles.actions}>{actions}</div>}
    </div>
  );
};
```

```typescript
// src/entities/product/index.ts
export { ProductCard } from './ui/ProductCard';
export type { Product, ProductState } from './model/types';
export { productSlice, productActions } from './model/slice';
export { useProduct } from './model/hooks';
```

### 2. Feature - Add to Cart

```tsx
// src/features/add-to-cart/ui/AddToCartButton.tsx
import { useCallback } from 'react';
import { Button } from '@/shared/ui/Button';
import { useAddToCart } from '../model/useAddToCart';
import styles from './styles.module.css';

interface AddToCartButtonProps {
  productId: string;
  quantity?: number;
}

export const AddToCartButton = ({ 
  productId, 
  quantity = 1 
}: AddToCartButtonProps) => {
  const { addToCart, isLoading } = useAddToCart();

  const handleClick = useCallback(() => {
    addToCart({ productId, quantity });
  }, [productId, quantity, addToCart]);

  return (
    <Button
      onClick={handleClick}
      disabled={isLoading}
      className={styles.button}
    >
      {isLoading ? 'Agregando...' : 'Agregar al carrito'}
    </Button>
  );
};
```

```typescript
// src/features/add-to-cart/model/useAddToCart.ts
import { useCallback } from 'react';
import { useDispatch } from 'react-redux';
import { cartActions } from '@/entities/cart';
import { addToCartApi } from '../api/addToCartApi';

export const useAddToCart = () => {
  const dispatch = useDispatch();

  const addToCart = useCallback(async ({ productId, quantity }) => {
    try {
      const result = await addToCartApi(productId, quantity);
      dispatch(cartActions.addItem(result));
      // Puedes agregar notificaciones de éxito aquí
    } catch (error) {
      console.error('Error adding to cart:', error);
      // Manejar error
    }
  }, [dispatch]);

  return { addToCart, isLoading: false };
};
```

```typescript
// src/features/add-to-cart/index.ts
export { AddToCartButton } from './ui/AddToCartButton';
```

### 3. Widget - Product List

```tsx
// src/widgets/product-card-list/ui/ProductCardList.tsx
import { ProductCard } from '@/entities/product';
import { AddToCartButton } from '@/features/add-to-cart';
import { Product } from '@/entities/product';
import styles from './ProductCardList.module.css';

interface ProductCardListProps {
  products: Product[];
}

export const ProductCardList = ({ products }: ProductCardListProps) => {
  return (
    <div className={styles.grid}>
      {products.map((product) => (
        <ProductCard
          key={product.id}
          product={product}
          actions={<AddToCartButton productId={product.id} />}
        />
      ))}
    </div>
  );
};
```

### 4. Page - Home

```tsx
// src/pages/home/ui/HomePage.tsx
import { useEffect } from 'react';
import { ProductCardList } from '@/widgets/product-card-list';
import { ProductFilter } from '@/features/product-filter';
import { useProducts } from '@/entities/product';
import styles from './HomePage.module.css';

export const HomePage = () => {
  const { products, loading, fetchProducts } = useProducts();

  useEffect(() => {
    fetchProducts();
  }, [fetchProducts]);

  if (loading) return <div>Cargando...</div>;

  return (
    <div className={styles.container}>
      <h1>Nuestros Productos</h1>
      <ProductFilter />
      <ProductCardList products={products} />
    </div>
  );
};
```

### 5. Shared - Button Component

```tsx
// src/shared/ui/Button/Button.tsx
import { ButtonHTMLAttributes, ReactNode } from 'react';
import styles from './Button.module.css';

interface ButtonProps extends ButtonHTMLAttributes<HTMLButtonElement> {
  children: ReactNode;
  variant?: 'primary' | 'secondary' | 'danger';
  size?: 'small' | 'medium' | 'large';
}

export const Button = ({
  children,
  variant = 'primary',
  size = 'medium',
  className = '',
  ...props
}: ButtonProps) => {
  return (
    <button
      className={`${styles.button} ${styles[variant]} ${styles[size]} ${className}`}
      {...props}
    >
      {children}
    </button>
  );
};
```

## Reglas de Dependencias

```
app → processes → pages → widgets → features → entities → shared
```

- Cada capa solo puede importar de capas inferiores.
- Dentro de la misma capa, los módulos NO pueden importarse entre sí.
- `shared` no puede importar de ninguna otra capa.

## Ventajas

- **Escalabilidad**: Fácil agregar nuevas features sin afectar las existentes.
- **Mantenibilidad**: Código organizado por funcionalidad de negocio.
- **Reusabilidad**: Componentes y lógica fácilmente reutilizables.
- **Testeo**: Cada feature se puede testear de manera aislada.
- **Colaboración**: Equipos pueden trabajar en diferentes features simultáneamente.

## Desventajas

- **Curva de aprendizaje**: Requiere entender bien la metodología.
- **Estructura inicial**: Más carpetas y archivos desde el inicio.
- **Decisiones arquitectónicas**: Requiere pensar dónde va cada pieza de código.
- **Rigidez**: Las reglas estrictas pueden ser limitantes en algunos casos.

## Casos de Uso Recomendados

- Aplicaciones frontend medianas a grandes.
- Proyectos con múltiples desarrolladores.
- Aplicaciones que crecerán significativamente.
- Productos con muchas features independientes.
- Aplicaciones SPA complejas (React, Vue, Angular).

## Recursos Adicionales

- [Feature-Sliced Design Official](https://feature-sliced.design/)
- [FSD Examples](https://github.com/feature-sliced/examples)
- [FSD Documentation](https://feature-sliced.design/docs)

## Tips

1. **Empeza desde abajo**: Construi primero `shared`, luego `entities`, después `features`, etc.
2. **Public API**: Cada feature debe exportar solo lo necesario a través de su `index.ts`.
3. **Composición sobre herencia**: Combina features en widgets y páginas.
4. **Naming conventions**: Usa nombres descriptivos que reflejen la funcionalidad de negocio.