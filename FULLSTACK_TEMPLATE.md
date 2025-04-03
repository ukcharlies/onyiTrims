# Full-Stack E-commerce Template

## Project Structure

```
project-root/
├── 01-frontend/                 # React Frontend
│   ├── src/
│   │   ├── assets/            # Static assets and images
│   │   ├── components/        # Reusable components
│   │   │   ├── Navbar.jsx    # Navigation component
│   │   │   └── ProductCard.jsx # Product display component
│   │   ├── config/           # Configuration files
│   │   │   └── config.js     # Environment and app config
│   │   ├── context/          # React Context providers
│   │   │   └── CartContext.jsx # Shopping cart state management
│   │   ├── hooks/            # Custom React hooks
│   │   │   └── useCategories.js # Category fetching hook
│   │   ├── pages/            # Page components
│   │   │   ├── Home.jsx     # Home page with featured products
│   │   │   ├── About.jsx    # About page
│   │   │   └── CategoryPage.jsx # Category-specific products
│   │   ├── shared/           # Shared components and layouts
│   │   │   └── Layout.jsx   # Main layout with Outlet
│   │   ├── router.jsx       # Router configuration
│   │   ├── main.jsx        # Entry point
│   │   └── index.css       # Global styles
│   ├── public/             # Static files
│   ├── .env               # Frontend environment variables
│   ├── package.json       # Frontend dependencies
│   ├── tailwind.config.js # Tailwind configuration
│   └── vite.config.js     # Vite configuration
│
└── 02-backend/            # Node.js Backend
    ├── controllers/       # Route controllers
    │   ├── productController.js  # Product operations
    │   └── categoryController.js # Category operations
    ├── routes/           # API routes
    │   ├── productRoutes.js     # Product endpoints
    │   └── categoryRoutes.js    # Category endpoints
    ├── middleware/       # Custom middleware
    │   └── error.js     # Error handling middleware
    ├── prisma/          # Database schema and migrations
    │   └── schema.prisma # Database schema
    ├── server.js        # Main server file
    ├── package.json     # Backend dependencies
    └── .env            # Backend environment variables
```

## Frontend Setup (01-frontend)

### 1. Create Vite Project

```bash
npm create vite@latest 01-frontend -- --template react
cd 01-frontend
```

### 2. Install Dependencies

```bash
# Core dependencies
npm install react-router-dom flowbite flowbite-react

# Development dependencies
npm install -D tailwindcss postcss autoprefixer
```

### 3. Environment Setup

```env
# .env
VITE_API_URL=http://localhost:5000
VITE_APP_NAME="E-commerce Store"
```

### 4. Config Setup

```javascript
// src/config/config.js
const config = {
  apiUrl: import.meta.env.VITE_API_URL,
  appName: import.meta.env.VITE_APP_NAME,
};

export default config;
```

### 5. Custom Hooks

```javascript
// src/hooks/useCategories.js
import { useState, useEffect } from "react";
import config from "../config/config";

export const useCategories = () => {
  const [categories, setCategories] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchCategories = async () => {
      try {
        const response = await fetch(`${config.apiUrl}/categories`);
        if (!response.ok) throw new Error("Failed to fetch categories");
        const data = await response.json();
        setCategories(data);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };

    fetchCategories();
  }, []);

  return { categories, loading, error };
};
```

### 6. Router Setup

```javascript
// src/router.jsx
import { createBrowserRouter, RouterProvider } from "react-router-dom";
import Layout from "./shared/Layout";
import Home from "./pages/Home";
import About from "./pages/About";
import CategoryPage from "./pages/CategoryPage";
import ErrorPage from "./pages/ErrorPage";

export const router = createBrowserRouter([
  {
    path: "/",
    element: <Layout />,
    errorElement: <ErrorPage />,
    children: [
      {
        index: true,
        element: <Home />,
        loader: async () => {
          // Fetch featured products for homepage
          const res = await fetch(`${config.apiUrl}/products/featured`);
          return res.json();
        },
      },
      {
        path: "about",
        element: <About />,
      },
      {
        path: "category/:categoryId",
        element: <CategoryPage />,
        loader: async ({ params }) => {
          // Fetch category products
          const res = await fetch(
            `${config.apiUrl}/products/category/${params.categoryId}`
          );
          return res.json();
        },
      },
    ],
  },
]);

// src/main.jsx
import { RouterProvider } from "react-router-dom";
import { router } from "./router";

ReactDOM.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>
);
```

### 7. Layout Component

```javascript
// src/shared/Layout.jsx
import { Outlet } from "react-router-dom";
import Navbar from "../components/Navbar";

const Layout = () => {
  return (
    <div className="min-h-screen bg-gray-50 dark:bg-gray-900">
      <Navbar />
      <main className="container mx-auto px-4 py-8">
        <Outlet />
      </main>
    </div>
  );
};

export default Layout;
```

## Advanced Frontend Setup

### Router and Context Integration

1. **Enhanced Router Setup with Providers**

```javascript
// src/router.jsx
import { createBrowserRouter, RouterProvider } from "react-router-dom";
import { AppProvider } from "./context/AppContext";
import Layout from "./shared/Layout";
// ... other imports

// 1. Create router configuration
const router = createBrowserRouter([
  {
    path: "/",
    element: <Layout />,
    errorElement: <ErrorPage />,
    children: [
      // ... your routes
    ],
  },
]);

// 2. Create wrapper function for router and providers
export function AppRouter() {
  return (
    <AppProvider>
      <RouterProvider router={router} />
    </AppProvider>
  );
}
```

2. **Simplified Main Entry Point**

```javascript
// src/main.jsx
import React from "react";
import ReactDOM from "react-dom/client";
import { AppRouter } from "./router";
import "./index.css";

ReactDOM.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    <AppRouter />
  </React.StrictMode>
);
```

3. **Multiple Providers Setup**

```javascript
// src/providers/index.jsx
import { AppProvider } from "../context/AppContext";
import { ThemeProvider } from "../context/ThemeContext";
import { CartProvider } from "../context/CartContext";

export function Providers({ children }) {
  return (
    <AppProvider>
      <ThemeProvider>
        <CartProvider>{children}</CartProvider>
      </ThemeProvider>
    </AppProvider>
  );
}

// Then update router.jsx to use the Providers component
import { Providers } from "./providers";

export function AppRouter() {
  return (
    <Providers>
      <RouterProvider router={router} />
    </Providers>
  );
}
```

### Key Benefits of This Structure:

1. **Clean Separation of Concerns**

   - Router configuration is isolated
   - Provider logic is centralized
   - Main entry point remains simple

2. **Easy Provider Management**

   - Add new providers without modifying router logic
   - Clear provider hierarchy
   - Simple to maintain and update

3. **Flexible Context Access**

   - All routes have access to context
   - Providers can be reordered as needed
   - Easy to add or remove providers

4. **Best Practices**
   - Follows React Router's recommended patterns
   - Maintains clean component tree
   - Simplifies testing and maintenance

## Backend Setup (02-backend)

### 1. Initialize Project

```bash
mkdir 02-backend
cd 02-backend
npm init -y

# Install dependencies
npm install express cors dotenv @prisma/client
npm install -D prisma nodemon
```

### 2. Environment Setup

```env
# .env
DATABASE_URL="postgresql://user:password@localhost:5432/ecommerce"
PORT=5000
CORS_ORIGIN=http://localhost:3000
```

### 3. Prisma Schema

```prisma
// prisma/schema.prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model ProductCategory {
  id          String    @id @default(cuid())
  name        String
  description String
  products    Product[]
  createdAt   DateTime  @default(now())
  updatedAt   DateTime  @updatedAt

  @@map("product_categories")
}

model Product {
  id           String          @id @default(cuid())
  title        String
  description  String?
  price        Float
  stock        Int            @default(0)
  isFeatured   Boolean        @default(false)
  productImage String         @default("https://via.placeholder.com/150")
  categoryId   String
  category     ProductCategory @relation(fields: [categoryId], references: [id])
  createdAt    DateTime       @default(now())
  updatedAt    DateTime       @updatedAt

  @@map("products")
}
```

### 4. Error Middleware

```javascript
// middleware/error.js
const errorHandler = (err, req, res, next) => {
  console.error(`
    ============= Error Summary =============
    Message: ${err.message}
    Details: ${JSON.stringify(err.details || {})}
    Status Code: ${err.statusCode || 500}
    Stack: ${err.stack}
    ========================================
  `);

  res.status(err.statusCode || 500).json({
    success: false,
    error: {
      message: err.message || "Internal server error",
      code: err.code,
      details: err.details,
    },
  });
};

module.exports = errorHandler;
```

### 5. Server Setup

```javascript
// server.js
require("dotenv").config();
const express = require("express");
const cors = require("cors");
const errorHandler = require("./middleware/error");

const app = express();

// Middleware
app.use(express.json());
app.use(
  cors({
    origin: process.env.CORS_ORIGIN,
    credentials: true,
  })
);

// Routes
app.use("/products", require("./routes/productRoutes"));
app.use("/categories", require("./routes/categoryRoutes"));

// Error handling
app.use(errorHandler);

const PORT = process.env.PORT || 5000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```

## Development Workflow

### 1. Start Development

```bash
# Terminal 1 - Frontend
cd 01-frontend
npm run dev

# Terminal 2 - Backend
cd 02-backend
npm run dev
```

### 2. Database Operations

```bash
# Initialize Prisma
npx prisma init

# Create migration
npx prisma migrate dev --name init

# Generate Prisma Client
npx prisma generate

# Open Prisma Studio
npx prisma studio
```

## Best Practices

1. **Frontend**

   - Use custom hooks for data fetching and state management
   - Implement proper loading and error states
   - Use environment variables for configuration
   - Follow component composition patterns
   - Implement responsive design with Tailwind

2. **Backend**

   - Use proper error handling middleware
   - Implement input validation
   - Use environment variables for sensitive data
   - Follow RESTful API conventions
   - Implement proper CORS configuration

3. **Database**

   - Use migrations for schema changes
   - Document relationships in schema
   - Use proper indexing
   - Implement soft deletes where appropriate

4. **Security**
   - Validate all inputs
   - Sanitize all outputs
   - Use environment variables
   - Implement proper CORS
   - Use proper error handling

## Quick Start Commands

```bash
# Frontend
npm run dev        # Start development server
npm run build      # Build for production
npm run preview    # Preview production build

# Backend
npm run dev        # Start development server with nodemon
npm start          # Start production server

# Database
npx prisma studio  # Open database GUI
npx prisma migrate dev  # Run migrations
npx prisma generate    # Generate Prisma Client
```

## Frontend Deep Dive

### 1. Routing System

```javascript
// src/router.jsx
import { createBrowserRouter, RouterProvider } from "react-router-dom";
import Layout from "./shared/Layout";
import Home from "./pages/Home";
import About from "./pages/About";
import CategoryPage from "./pages/CategoryPage";
import ErrorPage from "./pages/ErrorPage";

export const router = createBrowserRouter([
  {
    path: "/",
    element: <Layout />,
    errorElement: <ErrorPage />,
    children: [
      {
        index: true,
        element: <Home />,
        loader: async () => {
          // Fetch featured products for homepage
          const res = await fetch(`${config.apiUrl}/products/featured`);
          return res.json();
        },
      },
      {
        path: "about",
        element: <About />,
      },
      {
        path: "category/:categoryId",
        element: <CategoryPage />,
        loader: async ({ params }) => {
          // Fetch category products
          const res = await fetch(
            `${config.apiUrl}/products/category/${params.categoryId}`
          );
          return res.json();
        },
      },
    ],
  },
]);

// src/main.jsx
import { RouterProvider } from "react-router-dom";
import { router } from "./router";

ReactDOM.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>
);
```

#### Routing Workflow

1. **Layout Structure**:

   - `Layout` component serves as the root layout
   - Contains persistent UI elements (Navbar, Footer)
   - Uses `Outlet` to render child routes

2. **Route Types**:

   - Index Route: Homepage (`/`)
   - Static Routes: About page (`/about`)
   - Dynamic Routes: Category page (`/category/:categoryId`)

3. **Route Features**:
   - Data Loading: Using `loader` functions
   - Error Handling: Using `errorElement`
   - Params Access: Using `useParams` hook

### 2. Flowbite & Tailwind Setup

```css
/* src/index.css */
@tailwind base;
@tailwind components;
@tailwind utilities;

/* Custom styles */
@layer components {
  .card {
    @apply bg-white rounded-lg shadow-md dark:bg-gray-800 overflow-hidden;
  }

  .btn-primary {
    @apply text-white bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:ring-blue-300 font-medium rounded-lg text-sm px-5 py-2.5 dark:bg-blue-600 dark:hover:bg-blue-700 focus:outline-none dark:focus:ring-blue-800;
  }
}
```

```javascript
// tailwind.config.js
const flowbite = require("flowbite/plugin");

//Simple flow to get you started
module.exports = {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
    "./node_modules/flowbite-react/**/*.js",
    flowbite.content(),
  ],
  plugins: [flowbite.plugin()],
};

module.exports = {
  content: [
    "./index.html",
    "./src/**/*.{js,jsx,ts,tsx}",
    "node_modules/flowbite-react/**/*.{js,jsx,ts,tsx}",
  ],
  darkMode: "class",
  theme: {
    extend: {
      colors: {
        primary: { 50: "#eff6ff" /* ... other shades */ },
      },
    },
  },
  plugins: [flowbite],
};
```

### 3. Custom Hooks Architecture

```javascript
// src/hooks/useApi.js
import { useState, useCallback } from "react";
import config from "../config/config";

export const useApi = () => {
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);

  const fetchData = useCallback(async (endpoint, options = {}) => {
    setLoading(true);
    setError(null);
    try {
      const response = await fetch(`${config.apiUrl}${endpoint}`, {
        ...options,
        headers: {
          "Content-Type": "application/json",
          ...options.headers,
        },
      });

      if (!response.ok) throw new Error("API request failed");

      const data = await response.json();
      return data;
    } catch (err) {
      setError(err.message);
      throw err;
    } finally {
      setLoading(false);
    }
  }, []);

  return { fetchData, loading, error };
};

// Example usage in a component
const ProductList = () => {
  const { fetchData, loading, error } = useApi();
  const [products, setProducts] = useState([]);

  useEffect(() => {
    const getProducts = async () => {
      try {
        const data = await fetchData("/products");
        setProducts(data);
      } catch (err) {
        console.error("Failed to fetch products:", err);
      }
    };
    getProducts();
  }, [fetchData]);

  if (loading) return <LoadingSpinner />;
  if (error) return <ErrorMessage message={error} />;

  return <ProductGrid products={products} />;
};
```

### 4. Component Architecture

```javascript
// src/components/ProductCard/index.jsx
import { Button } from "flowbite-react";
import { useCart } from "../../hooks/useCart";
import { formatPrice } from "../../utils/format";

export const ProductCard = ({ product }) => {
  const { addToCart } = useCart();

  return (
    <div className="card">
      {product.isFeatured && (
        <div className="absolute top-2 right-2">
          <span className="featured-badge">Featured</span>
        </div>
      )}
      <img
        src={product.productImage}
        alt={product.title}
        className="w-full h-48 object-cover"
      />
      <div className="p-4">
        <h3 className="text-lg font-semibold">{product.title}</h3>
        <p className="text-gray-600 dark:text-gray-400">
          {product.description}
        </p>
        <div className="mt-4 flex justify-between items-center">
          <span className="text-xl font-bold">
            {formatPrice(product.price)}
          </span>
          <Button
            gradientDuoTone="purpleToBlue"
            onClick={() => addToCart(product)}
          >
            Add to Cart
          </Button>
        </div>
      </div>
    </div>
  );
};

// Usage in a grid
export const ProductGrid = ({ products }) => (
  <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
    {products.map((product) => (
      <ProductCard key={product.id} product={product} />
    ))}
  </div>
);
```

## Backend Architecture Deep Dive

### 1. Controller-Route-Service Pattern

```javascript
// controllers/productController.js
const { PrismaClient } = require("@prisma/client");
const prisma = new PrismaClient();

class ProductController {
  // Get products by category
  async getProductsByCategory(req, res, next) {
    try {
      const { categoryId } = req.params;

      // Validate category exists
      const category = await prisma.productCategory.findUnique({
        where: { id: categoryId },
      });

      if (!category) {
        return res.status(404).json({
          success: false,
          error: "Category not found",
        });
      }

      // Get products
      const products = await prisma.product.findMany({
        where: { categoryId },
        include: {
          category: {
            select: {
              name: true,
            },
          },
        },
      });

      res.json({
        success: true,
        data: products,
        category: category.name,
      });
    } catch (error) {
      next(error);
    }
  }

  // Create new product
  async createProduct(req, res, next) {
    try {
      const { title, price, categoryId, description } = req.body;

      // Validation
      if (!title || !price || !categoryId) {
        return res.status(400).json({
          success: false,
          error: "Missing required fields",
        });
      }

      const product = await prisma.product.create({
        data: {
          title,
          price: parseFloat(price),
          categoryId,
          description,
        },
      });

      res.status(201).json({
        success: true,
        data: product,
      });
    } catch (error) {
      next(error);
    }
  }
}

module.exports = new ProductController();

// routes/productRoutes.js
const express = require("express");
const router = express.Router();
const productController = require("../controllers/productController");

router.get("/category/:categoryId", productController.getProductsByCategory);
router.post("/", productController.createProduct);
// ... other routes

module.exports = router;
```

### 2. Server-Route-Controller Flow

1. **Request Flow**:

   ```
   Client Request
   → Server (server.js)
   → Route (productRoutes.js)
   → Controller (productController.js)
   → Database (via Prisma)
   → Response
   ```

2. **Error Handling Flow**:

   ```
   Controller Error
   → Next(error)
   → Error Middleware
   → Formatted Error Response
   ```

3. **Middleware Chain**:
   ```javascript
   // server.js
   app.use(express.json());
   app.use(
     cors({
       /* config */
     })
   );
   app.use("/api/v1/products", productRoutes);
   app.use(errorHandler);
   ```

### 3. API Response Standards

```javascript
// Success Response
{
  success: true,
  data: {/* response data */},
  message?: string
}

// Error Response
{
  success: false,
  error: {
    message: string,
    code?: string,
    details?: object
  }
}
```
