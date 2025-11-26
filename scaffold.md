---
argument-hint: "<type> <name>"
description: "Generate project scaffolding following existing patterns"
model: claude-opus-4-5-20251101
allowed-tools: ["Bash", "Read", "Write", "Edit", "Glob", "Grep", "AskUserQuestion"]
---

**If `$ARGUMENTS` is empty or not provided:**

Show available scaffold types and usage.

**Usage:** `/scaffold <type> <name>`

**Examples:**

- `/scaffold component Button` - React/Vue component
- `/scaffold api users` - API endpoint with CRUD
- `/scaffold model User` - Database model
- `/scaffold test auth` - Test file
- `/scaffold hook useAuth` - Custom hook
- `/scaffold service payment` - Service class

**Workflow:**

1. Detect project framework and patterns
2. Generate files following conventions
3. Add tests if applicable
4. Update exports/routes

What would you like to scaffold?

---

**If `$ARGUMENTS` is provided:**

Generate scaffolding for the specified type and name.

## Configuration

Parse arguments:

- **Type**: First word (component, api, model, etc.)
- **Name**: Second word (the thing to create)
- **Options**: Additional flags

## Steps

1. **Detect Project Context**

   ```bash
   # Check for frameworks
   cat package.json 2>/dev/null | jq '.dependencies,.devDependencies' | grep -i "react\|vue\|angular\|express\|fastify\|nestjs"

   # Check for existing patterns
   ls -la src/ app/ lib/ 2>/dev/null
   ```

   Identify:
   - Framework (React, Vue, Express, etc.)
   - Language (TypeScript, JavaScript, Go)
   - Project structure (src/, app/, etc.)
   - Naming conventions (camelCase, kebab-case)

2. **Find Existing Patterns**

   For each scaffold type, find similar existing files:

   ```bash
   # Find existing components
   fd -e tsx -e jsx -p "components/"

   # Find existing API routes
   fd -e ts -e js -p "routes/\|api/"

   # Find existing models
   fd -e ts -e js -p "models/"
   ```

   Analyze:
   - File structure
   - Import patterns
   - Export patterns
   - Naming conventions
   - Test file locations

3. **Generate Scaffolding**

   ### Component (React/Vue)

   **React Component:**

   ```typescript
   // src/components/Button/Button.tsx
   import React from 'react';
   import styles from './Button.module.css';

   export interface ButtonProps {
     children: React.ReactNode;
     onClick?: () => void;
     variant?: 'primary' | 'secondary';
     disabled?: boolean;
   }

   export const Button: React.FC<ButtonProps> = ({
     children,
     onClick,
     variant = 'primary',
     disabled = false,
   }) => {
     return (
       <button
         className={`${styles.button} ${styles[variant]}`}
         onClick={onClick}
         disabled={disabled}
       >
         {children}
       </button>
     );
   };
   ```

   **Style file:**

   ```css
   /* src/components/Button/Button.module.css */
   .button {
     padding: 8px 16px;
     border-radius: 4px;
     cursor: pointer;
   }

   .primary {
     background: #007bff;
     color: white;
   }

   .secondary {
     background: #6c757d;
     color: white;
   }
   ```

   **Test file:**

   ```typescript
   // src/components/Button/Button.test.tsx
   import { render, screen, fireEvent } from '@testing-library/react';
   import { Button } from './Button';

   describe('Button', () => {
     it('renders children', () => {
       render(<Button>Click me</Button>);
       expect(screen.getByText('Click me')).toBeInTheDocument();
     });

     it('calls onClick when clicked', () => {
       const handleClick = jest.fn();
       render(<Button onClick={handleClick}>Click</Button>);
       fireEvent.click(screen.getByText('Click'));
       expect(handleClick).toHaveBeenCalled();
     });
   });
   ```

   **Index file:**

   ```typescript
   // src/components/Button/index.ts
   export { Button } from './Button';
   export type { ButtonProps } from './Button';
   ```

   ---

   ### API Endpoint

   **Express Route:**

   ```typescript
   // src/routes/users.ts
   import { Router } from 'express';
   import { userService } from '../services/user.service';
   import { validateRequest } from '../middleware/validate';
   import { createUserSchema, updateUserSchema } from '../schemas/user.schema';

   const router = Router();

   // GET /users
   router.get('/', async (req, res, next) => {
     try {
       const users = await userService.findAll(req.query);
       res.json(users);
     } catch (error) {
       next(error);
     }
   });

   // GET /users/:id
   router.get('/:id', async (req, res, next) => {
     try {
       const user = await userService.findById(req.params.id);
       if (!user) {
         return res.status(404).json({ error: 'User not found' });
       }
       res.json(user);
     } catch (error) {
       next(error);
     }
   });

   // POST /users
   router.post('/', validateRequest(createUserSchema), async (req, res, next) => {
     try {
       const user = await userService.create(req.body);
       res.status(201).json(user);
     } catch (error) {
       next(error);
     }
   });

   // PUT /users/:id
   router.put('/:id', validateRequest(updateUserSchema), async (req, res, next) => {
     try {
       const user = await userService.update(req.params.id, req.body);
       res.json(user);
     } catch (error) {
       next(error);
     }
   });

   // DELETE /users/:id
   router.delete('/:id', async (req, res, next) => {
     try {
       await userService.delete(req.params.id);
       res.status(204).send();
     } catch (error) {
       next(error);
     }
   });

   export default router;
   ```

   ---

   ### Database Model

   **Prisma Model:**

   ```prisma
   // Add to schema.prisma
   model User {
     id        String   @id @default(cuid())
     email     String   @unique
     name      String?
     createdAt DateTime @default(now())
     updatedAt DateTime @updatedAt
   }
   ```

   **TypeORM Entity:**

   ```typescript
   // src/models/User.ts
   import { Entity, PrimaryGeneratedColumn, Column, CreateDateColumn, UpdateDateColumn } from 'typeorm';

   @Entity('users')
   export class User {
     @PrimaryGeneratedColumn('uuid')
     id: string;

     @Column({ unique: true })
     email: string;

     @Column({ nullable: true })
     name: string;

     @CreateDateColumn()
     createdAt: Date;

     @UpdateDateColumn()
     updatedAt: Date;
   }
   ```

   ---

   ### Service Class

   ```typescript
   // src/services/payment.service.ts
   import { prisma } from '../lib/prisma';
   import { stripe } from '../lib/stripe';

   export class PaymentService {
     async createPaymentIntent(amount: number, currency: string) {
       return stripe.paymentIntents.create({
         amount,
         currency,
       });
     }

     async processPayment(paymentIntentId: string) {
       const intent = await stripe.paymentIntents.retrieve(paymentIntentId);
       // Process logic...
       return intent;
     }

     async refund(paymentId: string, amount?: number) {
       return stripe.refunds.create({
         payment_intent: paymentId,
         amount,
       });
     }
   }

   export const paymentService = new PaymentService();
   ```

   ---

   ### Custom Hook

   ```typescript
   // src/hooks/useAuth.ts
   import { useState, useEffect, useContext, createContext } from 'react';
   import { User } from '../types';
   import { authService } from '../services/auth.service';

   interface AuthContextType {
     user: User | null;
     loading: boolean;
     login: (email: string, password: string) => Promise<void>;
     logout: () => Promise<void>;
   }

   const AuthContext = createContext<AuthContextType | undefined>(undefined);

   export const useAuth = () => {
     const context = useContext(AuthContext);
     if (!context) {
       throw new Error('useAuth must be used within AuthProvider');
     }
     return context;
   };

   export const AuthProvider: React.FC<{ children: React.ReactNode }> = ({ children }) => {
     const [user, setUser] = useState<User | null>(null);
     const [loading, setLoading] = useState(true);

     useEffect(() => {
       authService.getCurrentUser()
         .then(setUser)
         .finally(() => setLoading(false));
     }, []);

     const login = async (email: string, password: string) => {
       const user = await authService.login(email, password);
       setUser(user);
     };

     const logout = async () => {
       await authService.logout();
       setUser(null);
     };

     return (
       <AuthContext.Provider value={{ user, loading, login, logout }}>
         {children}
       </AuthContext.Provider>
     );
   };
   ```

4. **Update Related Files**

   - Add to index exports
   - Register routes
   - Update barrel files
   - Add to navigation (if component)

5. **Generate Tests**

   Create test file alongside the scaffolded code:
   - Unit tests for services
   - Integration tests for APIs
   - Component tests for UI

6. **Output Summary**

   ```markdown
   # Scaffolding Complete

   ## Created Files

   | File | Type |
   |------|------|
   | src/components/Button/Button.tsx | Component |
   | src/components/Button/Button.module.css | Styles |
   | src/components/Button/Button.test.tsx | Test |
   | src/components/Button/index.ts | Export |

   ## Updated Files

   - src/components/index.ts (added export)

   ## Next Steps

   1. Implement component logic
   2. Run tests: `npm test Button`
   3. Import: `import { Button } from '@/components'`
   ```

## Scaffold Types

| Type | Creates |
|------|---------|
| component | React/Vue component with styles and tests |
| api | Express/Fastify route with CRUD |
| model | Database model/entity |
| service | Service class with methods |
| hook | React custom hook |
| test | Test file for existing code |
| middleware | Express middleware |
| schema | Validation schema |
| migration | Database migration |

## Notes

- Follows existing project conventions
- Creates test files automatically
- Updates barrel exports
- Handles TypeScript/JavaScript
- Adapts to framework in use
