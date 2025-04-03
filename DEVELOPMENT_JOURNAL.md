# E-commerce Project Development Journal

## Project Timeline and Milestones

### March 9, 2024 - Initial Setup

- Created project structure with frontend and backend separation
- Initialized Git repository
- Set up basic project configuration files

### March 10, 2024 - Backend Foundation

- Set up Node.js backend with Express
- Configured Prisma ORM with PostgreSQL
- Created initial database schema for products and categories
- Implemented basic CRUD operations
  using
  npm install express prisma @prisma/client cors dotenv
  then mkdir routes controllers config
  touch server.js .env

### March 11, 2024 - Frontend Foundation

- Initialized React project using Vite
- Set up Tailwind CSS and Flowbite
- Created basic component structure
- Implemented responsive navigation

### March 12, 2024 - API Integration

- Set up environment variables
- Implemented API communication layer
- Created custom hooks for data fetching
- Added error handling and loading states

### March 13, 2024 - State Management & UI Enhancement

- Implemented Context API for global state
- Created reusable components
- Enhanced UI with consistent styling
- Added image handling and static file serving

## Technical Implementation Details

### 1. Project Structure

```
Ecommerce/
├── 01-frontend/          # React frontend
│   ├── src/
│   │   ├── components/   # Reusable UI components
│   │   ├── pages/        # Route components
│   │   ├── hooks/        # Custom React hooks
│   │   ├── context/      # Global state management
│   │   ├── shared/       # Shared components
│   │   └── config/       # Configuration files
│   └── public/           # Static assets
└── 02-backend/           # Node.js backend
    ├── controllers/      # Route controllers
    ├── routes/          # API routes
    ├── middleware/      # Custom middleware
    ├── prisma/         # Database schema
    └── public/         # Static file serving
```

### 2. Key Concepts and Implementation

#### A. Environment Configuration

```javascript
// .env (Frontend)
VITE_API_URL=http://localhost:5000

// .env (Backend)
DATABASE_URL="postgresql://user:password@localhost:5432/ecommerce"
PORT=5000
```

#### B. Database Schema (Prisma)

```prisma
model Product {
  id          String   @id @default(cuid())
  title       String
  price       Int
  featured    Boolean  @default(false)
  productImage String  @default("https://via.placeholder.com/150")
  categoryId  String
  category    ProductCategory @relation(fields: [categoryId], references: [id])
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
  @@map("products")
}

model ProductCategory {
  id          String   @id @default(cuid())
  name        String
  description String
  products    Product[]
  @@map("product_categories")
}
```

#### C. Global State Management (Context API)

```javascript
// AppContext.jsx
const initialState = {
  cart: [],
  user: null,
  theme: "light",
  preferences: {
    showDescription: true,
    itemsPerPage: 12,
  },
};

const reducer = (state, action) => {
  switch (action.type) {
    case "ADD_TO_CART":
    // Cart management logic
    case "SET_THEME":
    // Theme management logic
    default:
      return state;
  }
};
```

#### D. Custom Hooks

```javascript
// useCategories.js
const useCategories = () => {
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

#### E. Reusable Components

```javascript
// ProductCard.jsx
const ProductCard = ({ product, showDescription = true }) => {
  const { addToCart } = useApp();

  return (
    <div className="flex flex-col h-full bg-white border border-gray-200 rounded-lg shadow">
      {/* Image Section */}
      <div className="relative h-48 overflow-hidden rounded-t-lg">
        <img
          className="object-cover w-full h-full"
          src={`${config.apiUrl}${product.productImage}`}
          alt={product.title}
        />
        {product.featured && (
          <span className="absolute top-2 right-2 bg-blue-500 text-white text-xs rounded-full px-2 py-1">
            Featured
          </span>
        )}
      </div>

      {/* Content Section */}
      <div className="p-5">
        <h5 className="text-xl font-semibold">{product.title}</h5>
        <p className="text-2xl font-bold mt-2">${product.price.toFixed(2)}</p>
        <Button
          gradientDuoTone="purpleToBlue"
          className="w-full mt-4"
          onClick={() => addToCart(product)}
        >
          Add to Cart
        </Button>
      </div>
    </div>
  );
};
```

### 3. Best Practices Implemented

#### A. Code Organization

- Separation of concerns (frontend/backend)
- Modular component structure
- Reusable hooks and utilities
- Consistent file naming conventions

#### B. State Management

- Global state for app-wide data
- Local state for component-specific data
- Context API for shared state
- Custom hooks for reusable logic

#### C. Error Handling

- Try-catch blocks in API calls
- Loading states for async operations
- Error boundaries for component errors
- User-friendly error messages

#### D. Performance Optimization

- Lazy loading for routes
- Image optimization
- Efficient state updates
- Proper dependency arrays in useEffect

### 4. Development Workflow

1. **Local Development**

   ```bash
   # Backend
   cd 02-backend
   npm run dev

   # Frontend
   cd 01-frontend
   npm run dev
   ```

2. **Database Management**

   ```bash
   # Generate Prisma client
   npx prisma generate

   # Run migrations
   npx prisma migrate dev
   ```

3. **Testing API Endpoints**
   ```bash
   # Using curl
   curl http://localhost:5000/products
   curl http://localhost:5000/categories
   ```

### 5. Common Issues and Solutions

1. **CORS Issues**

   ```javascript
   // Backend CORS configuration
   app.use(
     cors({
       origin: process.env.CORS_ORIGIN,
       credentials: true,
     })
   );
   ```

2. **Image Handling**

   ```javascript
   // Backend static file serving
   app.use("/uploads", express.static(path.join(__dirname, "public/uploads")));
   ```

3. **Environment Variables**
   ```javascript
   // Frontend config
   export const config = {
     apiUrl: import.meta.env.VITE_API_URL,
   };
   ```

### 6. Future Improvements

1. **Authentication & Authorization**

   - Implement JWT authentication
   - Add role-based access control
   - Secure API endpoints

2. **Performance Enhancements**

   - Implement caching
   - Add pagination
   - Optimize image loading

3. **User Experience**

   - Add loading skeletons
   - Implement infinite scroll
   - Add search functionality

4. **Testing**
   - Add unit tests
   - Implement integration tests
   - Set up E2E testing

## Learning Outcomes

1. **React Concepts**

   - Hooks (useState, useEffect, useContext)
   - Component composition
   - State management patterns
   - Custom hooks creation

2. **Backend Development**

   - RESTful API design
   - Database modeling
   - Error handling
   - File handling

3. **Full Stack Integration**

   - API communication
   - Environment configuration
   - Development workflow
   - Deployment considerations

4. **Best Practices**
   - Code organization
   - Error handling
   - Performance optimization
   - Security considerations

## Backend Setup Steps

### 1. Initialize Node.js Project

```bash
npm init -y
```

### 2. Install Dependencies

```bash
npm install express prisma @prisma/client cors dotenv
npm install nodemon --save-dev
```

### 3. Create Project Structure

```bash
mkdir routes controllers config
touch server.js .env
```

### 4. Basic Server Setup (server.js)

```javascript
const express = require("express");
const cors = require("cors");
require("dotenv").config();

const app = express();

// Middleware
app.use(cors());
app.use(express.json());

// Basic test route
app.get("/api/test", (req, res) => {
  res.json({ message: "Backend server is running!" });
});

const PORT = process.env.PORT || 5000;

app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
```

### 5. Environment Variables (.env)

```
PORT=5000
```

### 6. Update package.json Scripts

```json
{
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js"
  }
}
```

## Prisma Setup Steps (After Basic Server Setup)

### 1. Initialize Prisma

```bash
npx prisma init
```

This will create:

- `prisma` directory
- `prisma/schema.prisma` file
- `.env` file with DATABASE_URL

### 2. Configure Database URL

Update `.env` file with your PostgreSQL credentials:

```env
DATABASE_URL="postgresql://YOUR_USERNAME:YOUR_PASSWORD@localhost:5432/YOUR_DATABASE_NAME"
PORT=5000
```

### 3. Create Prisma Schema

Edit `prisma/schema.prisma`:

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model Product {
  id           String   @id @default(uuid())
  title        String
  description  String
  price        Float
  productImage String
  featured     Boolean  @default(false)
  categoryId   String
  category     Category @relation(fields: [categoryId], references: [id])
  createdAt    DateTime @default(now())
  updatedAt    DateTime @updatedAt
}

model Category {
  id        String    @id @default(uuid())
  name      String
  products  Product[]
  createdAt DateTime  @default(now())
  updatedAt DateTime  @updatedAt
}
```

### 4. Generate Prisma Client

```bash
npx prisma generate
```

This creates the Prisma Client for interacting with your database.

### 5. Create and Apply Migration

```bash
npx prisma migrate dev --name init
```

This will:

- Create the migration file
- Apply the migration to your database
- Generate Prisma Client

### 6. Verify Setup

```bash
npx prisma studio
```

This opens Prisma Studio to view and manage your data.

## Running the Server

- For development (with auto-reload): `npm run dev`
- For production: `npm start`

## Next Steps

- [ ] Set up Prisma ORM
- [ ] Configure PostgreSQL database
- [ ] Create database schema
- [ ] Implement user authentication
- [ ] Create API routes for products
- [ ] Set up image upload functionality

## Common Commands Reference

```bash
# Start development server
npm run dev

# Create a new migration
npx prisma migrate dev

# Open Prisma Studio
npx prisma studio

# Generate Prisma Client
npx prisma generate
```

## Project Structure

```
02-backend/
├── config/
├── controllers/
├── routes/
├── .env
├── server.js
├── package.json
└── prisma/
    └── schema.prisma (to be created)
```
