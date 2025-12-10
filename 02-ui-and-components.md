# Part 2: UI & Component Design
## Understanding Modern CSS and Component Architecture

> **Goal:** Understand how modern UI systems work and why they're built this way  
> **Time:** 5-7 hours  
> **Prerequisites:** Completed Part 1

[← Back to Index](./README.md) | [Next: Part 3 - Database →](./03-database-and-data.md)

---

## What We'll Learn

By the end of this part, you'll understand:

✅ **Why Tailwind CSS** over traditional CSS  
✅ **Component composition** patterns  
✅ **Design systems** and consistency  
✅ **shadcn architecture** and when to use it  
✅ **Building reusable** UI components  
✅ **Layout strategies** for modern apps  

---

## The Problem: Styling Web Apps

### Traditional CSS Challenges 🔴

**Scenario:** You're building a web app with traditional CSS.

```css
/* styles.css */
.button {
  padding: 12px 24px;
  background-color: #3b82f6;
  color: white;
  border-radius: 6px;
  font-weight: 600;
}

.button-large {
  padding: 16px 32px;
  font-size: 18px;
}

.button-small {
  padding: 8px 16px;
  font-size: 14px;
}

.button-danger {
  background-color: #ef4444;
}

/* ... 50 more button variations ... */
```

**Problems:**
1. **Naming Hell** - What do you call things?
   - `.btn` vs `.button` vs `.Button`
   - `.button-primary` vs `.primary-button`
   - Inconsistency across team

2. **CSS Bloat** - File gets huge
   - Thousands of lines
   - Hard to find specific styles
   - Lots of duplication

3. **Specificity Wars** - Styles override unexpectedly
   ```css
   .button { color: blue; }
   .primary { color: red; }
   .button.primary { color: ??? } /* Which wins? */
   ```

4. **Dead Code** - Unused styles pile up
   - Remove button → CSS stays
   - 6 months later: "What does `.btn-legacy` do?"

5. **No TypeScript** - No autocomplete or safety
   ```html
   <div class="buton"> <!-- Typo! No error! -->
   ```

### Modern Requirements 🎯

Today's web apps need:

✅ **Fast Development** - Build UI quickly  
✅ **Consistency** - Same spacing, colors everywhere  
✅ **Responsive** - Works on all screen sizes  
✅ **Maintainable** - Easy to change later  
✅ **Type-Safe** - Catch errors early  
✅ **Performant** - Small bundle size  
✅ **Beautiful** - Professional appearance  

**Can we solve all these?** Yes! Let's understand how.

---

## Understanding CSS Evolution

### The Journey

```
1990s: Inline styles
└─> <div style="color: red; padding: 10px">

2000s: External CSS files
└─> .my-class { color: red; padding: 10px; }

2010s: CSS Preprocessors (Sass/Less)
└─> $primary: red; .my-class { color: $primary; }

2015: CSS Modules
└─> import styles from './Button.module.css'

2017: CSS-in-JS (styled-components)
└─> const Button = styled.button`color: red;`

2020: Utility-First CSS (Tailwind)
└─> <button className="text-red-500 p-4">

2023: Type-safe Components (shadcn)
└─> <Button variant="destructive" size="lg">
```

### Why Utility-First CSS? (Tailwind)

**Traditional Approach:**
```jsx
// Button.css
.button {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 12px 24px;
  background: blue;
  color: white;
  border-radius: 6px;
}

// Button.jsx
<button className="button">Click me</button>
```

**Problems:**
- Write CSS → Switch to HTML → Back to CSS
- Name everything (naming is hard!)
- Changes affect all buttons (scary!)

**Utility-First Approach:**
```jsx
<button className="flex items-center justify-center px-6 py-3 bg-blue-500 text-white rounded-md">
  Click me
</button>
```

**Benefits:**
- ✅ All styling in one place (HTML)
- ✅ No naming required
- ✅ Changes only affect this button
- ✅ Smaller CSS bundle (shared utilities)
- ✅ Faster development

**But wait, isn't that messy?** 

Yes! That's why we use **components**:

```jsx
// Good: Extract to component
function Button({ children }) {
  return (
    <button className="flex items-center justify-center px-6 py-3 bg-blue-500 text-white rounded-md hover:bg-blue-600 transition">
      {children}
    </button>
  );
}

// Use it:
<Button>Click me</Button>
```

### Tailwind Mental Model

Think of Tailwind like **Lego blocks**:

```
Traditional CSS = Custom carpentry
- Measure, cut, sand each piece
- Takes time
- Hard to modify

Tailwind = Lego blocks
- Pre-made pieces
- Snap together
- Easy to change
```

**Common Tailwind Patterns:**

```jsx
// Spacing
p-4    = padding: 1rem (16px)
px-6   = padding-left + padding-right: 1.5rem
mt-8   = margin-top: 2rem

// Layout
flex            = display: flex
items-center    = align-items: center
justify-between = justify-content: space-between
gap-4          = gap: 1rem

// Typography
text-lg    = font-size: 1.125rem
font-bold  = font-weight: 700
text-blue-500 = color: rgb(59, 130, 246)

// Responsive
md:text-xl  = Large text on medium screens+
lg:grid-cols-3 = 3 columns on large screens+

// Hover/Focus
hover:bg-blue-600 = Background on hover
focus:ring-2      = Ring on focus
```

**The Pattern:** `[property]-[value]`

---

## Setting Up shadcn UI

### What is shadcn?

**shadcn is NOT a component library.** It's different.

**Traditional Component Library (like Material-UI):**
```bash
npm install @mui/material
import { Button } from '@mui/material'

# Problems:
- Bundle includes entire library (heavy!)
- Locked into their API
- Hard to customize deeply
- Version updates break things
```

**shadcn Approach:**
```bash
npx shadcn@latest add button

# This COPIES the component code to your project!
# You OWN the code
# You can modify anything
# No dependency on the library
```

**Benefits:**
- ✅ Only install components you use
- ✅ Full control over code
- ✅ Easy customization
- ✅ No library lock-in
- ✅ Copy-paste friendly

### Why shadcn + Radix?

**shadcn** = Beautiful styling + DX  
**Radix** = Accessible, unstyled primitives  

```
Radix provides:
- Keyboard navigation
- Screen reader support
- ARIA attributes
- Focus management
- WAI-ARIA compliance

shadcn adds:
- Beautiful default styling
- Tailwind classes
- Animation
- Variants (size, color, etc.)
```

**Mental Model:**

```
Radix = The engine (accessibility)
└─> shadcn = The body (styling)
    └─> Your customizations = The paint job
```

### Initialize shadcn

```bash
npx shadcn@latest init
```

**You'll see prompts:**

```
✔ Which style would you like to use?
→ Default

Why: Clean, professional look
Other options: New York (denser)
```

```
✔ Which color would you like to use as base color?
→ Slate

Why: Neutral, works with any brand
Others: Zinc (warmer), Stone (lighter)
```

```
✔ Would you like to use CSS variables for colors?
→ Yes

Why: Easy theme customization later
Example: Change --primary color → entire app updates
```

**What just happened?**

1. Created `components.json` - shadcn configuration
2. Updated `tailwind.config.ts` - Added theme colors
3. Created `src/components/ui/` - Components go here
4. Updated `globals.css` - Added CSS variables

**Let's understand each file:**

#### `components.json`

```json
{
  "$schema": "https://ui.shadcn.com/schema.json",
  "style": "default",
  "rsc": true,     // React Server Components support
  "tsx": true,     // TypeScript
  "tailwind": {
    "config": "tailwind.config.ts",
    "css": "src/app/globals.css",
    "baseColor": "slate",
    "cssVariables": true
  },
  "aliases": {
    "components": "@/components",
    "utils": "@/lib/utils"
  }
}
```

**What this means:**
- shadcn knows where your files are
- Knows you're using TypeScript
- Knows your Tailwind config location
- Can generate components with correct paths

#### Updated `tailwind.config.ts`

```typescript
import type { Config } from "tailwindcss"

const config = {
  darkMode: ["class"],  // Can toggle dark mode
  content: [
    './pages/**/*.{ts,tsx}',
    './components/**/*.{ts,tsx}',
    './app/**/*.{ts,tsx}',
    './src/**/*.{ts,tsx}',
  ],
  theme: {
    extend: {
      colors: {
        border: "hsl(var(--border))",
        input: "hsl(var(--input))",
        ring: "hsl(var(--ring))",
        background: "hsl(var(--background))",
        foreground: "hsl(var(--foreground))",
        primary: {
          DEFAULT: "hsl(var(--primary))",
          foreground: "hsl(var(--primary-foreground))",
        },
        // ... more colors
      },
      borderRadius: {
        lg: "var(--radius)",
        md: "calc(var(--radius) - 2px)",
        sm: "calc(var(--radius) - 4px)",
      },
    },
  },
  plugins: [require("tailwindcss-animate")],
}

export default config
```

**Understanding CSS Variables:**

```css
/* In globals.css */
:root {
  --background: 0 0% 100%;     /* HSL: white */
  --foreground: 222.2 84% 4.9%; /* HSL: almost black */
  --primary: 221.2 83.2% 53.3%; /* HSL: blue */
}

/* Now in Tailwind */
.bg-background { background-color: hsl(var(--background)); }
.bg-primary { background-color: hsl(var(--primary)); }
```

**Why HSL?**
- HSL = Hue, Saturation, Lightness
- Easier to create color palettes
- Better for dark mode

**Example: Change entire theme**

```css
/* Just change these variables */
:root {
  --primary: 142 71% 45%;  /* Change blue → green */
}

/* All primary buttons update automatically! */
```

---

## Installing Components

### Understanding the Process

When you run:

```bash
npx shadcn@latest add button
```

**What happens:**

1. **Downloads** component code from shadcn registry
2. **Copies** to `src/components/ui/button.tsx`
3. **Installs** any peer dependencies
4. **Updates** imports if needed

**You now OWN this code.** Modify it freely!

### Let's Add Components

```bash
npx shadcn@latest add button
npx shadcn@latest add card
npx shadcn@latest add input
npx shadcn@latest add textarea
npx shadcn@latest add label
npx shadcn@latest add navigation-menu
npx shadcn@latest add avatar
npx shadcn@latest add dropdown-menu
npx shadcn@latest add dialog
npx shadcn@latest add toast
npx shadcn@latest add alert
```

**Or install multiple at once:**

```bash
npx shadcn@latest add button card input textarea label navigation-menu avatar dropdown-menu dialog toast alert
```

### Anatomy of a shadcn Component

Let's examine `src/components/ui/button.tsx`:

```typescript
import * as React from "react"
import { Slot } from "@radix-ui/react-slot"
import { cva, type VariantProps } from "class-variance-authority"
import { cn } from "@/lib/utils"

// Define variants using CVA (Class Variance Authority)
const buttonVariants = cva(
  // Base styles (always applied)
  "inline-flex items-center justify-center rounded-md text-sm font-medium ring-offset-background transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 disabled:pointer-events-none disabled:opacity-50",
  {
    variants: {
      // Different visual styles
      variant: {
        default: "bg-primary text-primary-foreground hover:bg-primary/90",
        destructive: "bg-destructive text-destructive-foreground hover:bg-destructive/90",
        outline: "border border-input bg-background hover:bg-accent hover:text-accent-foreground",
        secondary: "bg-secondary text-secondary-foreground hover:bg-secondary/80",
        ghost: "hover:bg-accent hover:text-accent-foreground",
        link: "text-primary underline-offset-4 hover:underline",
      },
      // Different sizes
      size: {
        default: "h-10 px-4 py-2",
        sm: "h-9 rounded-md px-3",
        lg: "h-11 rounded-md px-8",
        icon: "h-10 w-10",
      },
    },
    // Default values
    defaultVariants: {
      variant: "default",
      size: "default",
    },
  }
)

export interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {
  asChild?: boolean
}

const Button = React.forwardRef<HTMLButtonElement, ButtonProps>(
  ({ className, variant, size, asChild = false, ...props }, ref) => {
    const Comp = asChild ? Slot : "button"
    return (
      <Comp
        className={cn(buttonVariants({ variant, size, className }))}
        ref={ref}
        {...props}
      />
    )
  }
)
Button.displayName = "Button"

export { Button, buttonVariants }
```

**Let's break this down:**

#### 1. CVA (Class Variance Authority)

```typescript
const buttonVariants = cva(
  "base-classes",  // Always applied
  {
    variants: {
      variant: {
        default: "classes",
        destructive: "classes",
      },
      size: {
        default: "classes",
        sm: "classes",
      },
    },
  }
)
```

**What is CVA?**
- Manages component variants systematically
- Type-safe variant selection
- Composable styles

**Mental Model:**

```
Button = Base Style + Variant + Size

Base:
└─> Rounded corners, padding, transitions, etc.

+ Variant (color):
  ├─> default (blue)
  ├─> destructive (red)
  └─> outline (border only)

+ Size:
  ├─> sm (small)
  ├─> default (medium)
  └─> lg (large)

= Final Button
```

#### 2. The `cn` Utility

```typescript
import { cn } from "@/lib/utils"

// Used like:
className={cn(buttonVariants({ variant, size, className }))}
```

**What does `cn` do?**

```typescript
// In src/lib/utils.ts
import { clsx } from "clsx"
import { twMerge } from "tailwind-merge"

export function cn(...inputs) {
  return twMerge(clsx(inputs))
}
```

**Breaking it down:**

```typescript
// 1. clsx - Combines classnames conditionally
clsx('px-4', 'py-2', isActive && 'bg-blue-500')
// → "px-4 py-2 bg-blue-500" (if isActive true)

// 2. twMerge - Merges Tailwind classes intelligently
twMerge('px-4 px-6') 
// → "px-6" (later value wins)

// 3. cn - Does both!
cn('px-4 py-2', 'px-6')
// → "py-2 px-6" (merged correctly)
```

**Why is this important?**

```typescript
// Without twMerge:
<Button className="px-8">  
  {/* Has px-4 (from button) AND px-8 (from prop) */}
  {/* Which wins? Undefined behavior! */}
</Button>

// With twMerge:
<Button className="px-8">
  {/* Correctly merges to px-8 */}
</Button>
```

#### 3. The `asChild` Prop

```typescript
const Comp = asChild ? Slot : "button"
```

**What is Slot?**

```typescript
// Normal usage:
<Button>Click me</Button>
// Renders: <button>Click me</button>

// With asChild:
<Button asChild>
  <Link href="/about">Go to About</Link>
</Button>
// Renders: <a href="/about" class="button-styles">Go to About</a>
```

**Why is this useful?**

```typescript
// Need a link that LOOKS like a button?
<Button asChild>
  <Link href="/login">Sign In</Link>
</Button>

// Need a div that LOOKS like a button?
<Button asChild>
  <div onClick={handleClick}>Custom</div>
</Button>
```

**The Slot component from Radix:**
- Takes button's props (className, etc.)
- Applies them to child element
- Child element's own props are preserved
- Merges intelligently

#### 4. TypeScript Props

```typescript
export interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {
  asChild?: boolean
}
```

**What this means:**

```typescript
// Gets all standard button props:
React.ButtonHTMLAttributes<HTMLButtonElement>
// ↓
onClick, disabled, type, form, etc.

// Gets variant props from CVA:
VariantProps<typeof buttonVariants>
// ↓
variant: "default" | "destructive" | "outline" ...
size: "sm" | "default" | "lg" | "icon"

// Plus custom prop:
asChild?: boolean
```

**Result: Full TypeScript autocomplete!**

```typescript
<Button 
  variant="destructive"  // ✅ Autocomplete
  size="lg"              // ✅ Autocomplete
  onClick={handleClick}  // ✅ Autocomplete
  disabled={isLoading}   // ✅ Autocomplete
  varaint="wrong"        // ❌ TypeScript error!
/>
```

---

## 🧠 CHECKPOINT: Understand the Button

Before moving on, make sure you can answer:

1. **What is CVA and why do we use it?**
2. **What does the `cn` utility do?**
3. **When would you use the `asChild` prop?**
4. **How does TypeScript help with component props?**
5. **What's the difference between `variant` and `size`?**

**Explain each concept out loud. If you can't, re-read those sections.**

---

## Building the Navigation Component

Now let's build our first custom component using what we learned.

### Understanding the Requirements

Our navigation needs to:
- Show logo/brand
- Display nav links (Articles, etc.)
- Show auth state (logged in/out)
- Be responsive (mobile-friendly)
- Look professional

### Component Structure

```
Nav Component
├── Container (max-width, padding)
├── Left Section
│   ├── Logo/Brand
│   └── Navigation Links
└── Right Section
    ├── New Article Button (if logged in)
    └── User Menu
        ├── Avatar
        └── Dropdown
            ├── Profile
            └── Sign Out
```

### Create the Component

Create `src/components/nav.tsx`:

```typescript
'use client';

import Link from 'next/link';
import { useUser } from '@stackframe/stack';
import { Button } from '@/components/ui/button';
import {
  NavigationMenu,
  NavigationMenuItem,
  NavigationMenuLink,
  NavigationMenuList,
} from '@/components/ui/navigation-menu';
import { Avatar, AvatarFallback, AvatarImage } from '@/components/ui/avatar';
import {
  DropdownMenu,
  DropdownMenuContent,
  DropdownMenuItem,
  DropdownMenuTrigger,
} from '@/components/ui/dropdown-menu';
import { PenSquare } from 'lucide-react';

export function Nav() {
  const user = useUser();

  return (
    <nav className="border-b">
      <div className="container mx-auto px-4 py-4 flex items-center justify-between">
        {/* Left section */}
        <div className="flex items-center gap-8">
          <Link href="/" className="text-2xl font-bold">
            📚 WikiApp
          </Link>
          
          <NavigationMenu>
            <NavigationMenuList>
              <NavigationMenuItem>
                <Link href="/articles" legacyBehavior passHref>
                  <NavigationMenuLink className="px-4 py-2 hover:bg-accent rounded-md">
                    Articles
                  </NavigationMenuLink>
                </Link>
              </NavigationMenuItem>
            </NavigationMenuList>
          </NavigationMenu>
        </div>

        {/* Right section */}
        <div className="flex items-center gap-4">
          {user ? (
            <>
              <Link href="/articles/new">
                <Button className="gap-2">
                  <PenSquare className="h-4 w-4" />
                  New Article
                </Button>
              </Link>
              
              <DropdownMenu>
                <DropdownMenuTrigger>
                  <Avatar>
                    <AvatarImage src={user.profileImageUrl || ''} />
                    <AvatarFallback>
                      {user.displayName?.charAt(0) || 'U'}
                    </AvatarFallback>
                  </Avatar>
                </DropdownMenuTrigger>
                <DropdownMenuContent align="end">
                  <DropdownMenuItem asChild>
                    <Link href="/profile">Profile</Link>
                  </DropdownMenuItem>
                  <DropdownMenuItem asChild>
                    <Link href="/handler/sign-out">Sign Out</Link>
                  </DropdownMenuItem>
                </DropdownMenuContent>
              </DropdownMenu>
            </>
          ) : (
            <>
              <Link href="/handler/sign-in">
                <Button variant="ghost">Sign In</Button>
              </Link>
              <Link href="/handler/sign-up">
                <Button>Sign Up</Button>
              </Link>
            </>
          )}
        </div>
      </div>
    </nav>
  );
}
```

### Understanding This Code

#### 1. The `'use client'` Directive

```typescript
'use client';
```

**Why do we need this?**

```
By default in Next.js App Router:
- All components are SERVER components
- Can't use hooks (useState, useEffect, etc.)
- Can't use browser APIs
- But faster, better SEO

'use client' makes it a CLIENT component:
- Can use hooks ✅
- Can use browser APIs ✅
- Interactive ✅
- But renders in browser (slightly slower initial load)
```

**When to use Client Components:**
- ✅ Need interactivity (onClick, etc.)
- ✅ Need hooks (useState, useEffect, etc.)
- ✅ Need browser APIs (localStorage, etc.)
- ✅ Third-party libraries that need browser

**Our Nav needs:**
- `useUser` hook from Stack Auth ✅
- Dropdown interactions ✅
- → Must be Client Component

#### 2. The useUser Hook

```typescript
const user = useUser();
```

**What does this return?**

```typescript
user = {
  id: "user_abc123",
  email: "john@example.com",
  displayName: "John Doe",
  profileImageUrl: "https://...",
  // ... more fields
} || null  // null if not logged in
```

**Conditional Rendering:**

```typescript
{user ? (
  // Logged in: Show avatar and New Article button
) : (
  // Not logged in: Show Sign In / Sign Up
)}
```

#### 3. Layout Patterns

```typescript
<nav className="border-b">
  <div className="container mx-auto px-4 py-4 flex items-center justify-between">
```

**Understanding the classes:**

```
border-b
└─> Adds bottom border (separates nav from content)

container mx-auto
├─> container: Sets max-width (responsive)
│   - sm: 640px
│   - md: 768px
│   - lg: 1024px
│   - xl: 1280px
└─> mx-auto: Centers horizontally (margin-left/right: auto)

px-4 py-4
├─> px-4: padding-left and padding-right: 1rem
└─> py-4: padding-top and padding-bottom: 1rem

flex items-center justify-between
├─> flex: display: flex
├─> items-center: align-items: center (vertical center)
└─> justify-between: justify-content: space-between (spread apart)
```

**Visual Result:**

```
┌─────────────────────────────────────────────┐
│ container (max-width, centered)             │
│ ┌─────────────────────────────────────────┐ │
│ │ [Logo  Links]     [Button  Avatar]      │ │
│ │  └─left section─┘  └─right section─┘    │ │
│ └─────────────────────────────────────────┘ │
└─────────────────────────────────────────────┘
```

#### 4. The NavigationMenu Component

```typescript
<NavigationMenu>
  <NavigationMenuList>
    <NavigationMenuItem>
      <Link href="/articles" legacyBehavior passHref>
        <NavigationMenuLink className="...">
          Articles
        </NavigationMenuLink>
      </Link>
    </NavigationMenuItem>
  </NavigationMenuList>
</NavigationMenu>
```

**Why so nested?**

This is a Radix primitive with:
- Proper ARIA roles
- Keyboard navigation
- Screen reader support
- Focus management

**Understanding `legacyBehavior passHref`:**

```typescript
// Next.js <Link> normally renders its own <a> tag
<Link href="/about">About</Link>
// → <a href="/about">About</a>

// But NavigationMenuLink expects to BE the <a> tag
// So we use legacyBehavior to prevent double <a> tags
<Link href="/about" legacyBehavior passHref>
  <NavigationMenuLink>About</NavigationMenuLink>
</Link>
// → <a href="/about" class="...">About</a> (correct!)
```

#### 5. Icons with Lucide

```typescript
import { PenSquare } from 'lucide-react';

<PenSquare className="h-4 w-4" />
```

**Lucide React:**
- Collection of beautiful icons
- Tree-shakeable (only import what you use)
- Consistent sizing
- Accessible

**Size classes:**
```
h-4 w-4  = 16px × 16px (small)
h-5 w-5  = 20px × 20px (medium)
h-6 w-6  = 24px × 24px (large)
```

---

## 🧪 EXERCISE: Customize the Nav

Before moving on, try these:

### Exercise 1: Add Another Nav Link

Add a "About" link to the navigation.

**Hint:** Copy the existing NavigationMenuItem and modify.

### Exercise 2: Change Button Variants

Try different button combinations:
- Sign In: `variant="outline"`
- Sign Up: `variant="default"`

See how they look!

### Exercise 3: Add an Icon

Add a Home icon to the logo.

```typescript
import { Home } from 'lucide-react';
<Home className="h-5 w-5" />
```

### Exercise 4: Make it Responsive

On mobile, the nav should stack vertically.

**Hint:** Use Tailwind responsive prefixes:
```typescript
className="flex flex-col md:flex-row"
// Mobile: column
// Tablet+: row
```

---

## Building Article Card Component

Now let's build a reusable card component for displaying articles.

### Requirements

Article cards need to show:
- Featured image (if available)
- Title
- Summary/excerpt
- Author info
- View count
- Creation date

### Component Design

Create `src/components/article-card.tsx`:

```typescript
import Link from 'next/link';
import Image from 'next/image';
import { Avatar, AvatarFallback, AvatarImage } from '@/components/ui/avatar';
import { Card, CardContent, CardFooter, CardHeader } from '@/components/ui/card';
import { Eye, Calendar } from 'lucide-react';
import type { Article } from '@/lib/db/schema';

interface ArticleCardProps {
  article: Article & {
    author: {
      id: string;
      displayName: string | null;
      profileImageUrl: string | null;
    };
  };
}

export function ArticleCard({ article }: ArticleCardProps) {
  return (
    <Card className="overflow-hidden hover:shadow-lg transition-shadow">
      <Link href={`/articles/${article.id}`}>
        {article.imageUrl && (
          <div className="relative aspect-video w-full overflow-hidden">
            <Image
              src={article.imageUrl}
              alt={article.title}
              fill
              className="object-cover hover:scale-105 transition-transform duration-300"
            />
          </div>
        )}
        
        <CardHeader>
          <h3 className="text-xl font-bold line-clamp-2 hover:text-primary">
            {article.title}
          </h3>
        </CardHeader>
        
        <CardContent>
          {article.summary && (
            <p className="text-muted-foreground line-clamp-3">
              {article.summary}
            </p>
          )}
        </CardContent>
        
        <CardFooter className="flex items-center justify-between text-sm text-muted-foreground">
          <div className="flex items-center gap-2">
            <Avatar className="h-6 w-6">
              <AvatarImage src={article.author.profileImageUrl || ''} />
              <AvatarFallback>
                {article.author.displayName?.charAt(0) || 'U'}
              </AvatarFallback>
            </Avatar>
            <span>{article.author.displayName || 'Anonymous'}</span>
          </div>
          
          <div className="flex items-center gap-4">
            <span className="flex items-center gap-1">
              <Eye className="h-4 w-4" />
              {article.views.toLocaleString()}
            </span>
            <span className="flex items-center gap-1">
              <Calendar className="h-4 w-4" />
              {new Date(article.createdAt).toLocaleDateString()}
            </span>
          </div>
        </CardFooter>
      </Link>
    </Card>
  );
}
```

### Understanding the Code

**1. TypeScript Props:**
```typescript
interface ArticleCardProps {
  article: Article & {
    author: { ... }  // Include related author data
  };
}
```

This ensures the component receives an article with author info.

**2. Image Component:**
```typescript
<Image
  src={article.imageUrl}
  alt={article.title}
  fill  // Fill parent container
  className="object-cover"  // Crop to fit
/>
```

Next.js Image component:
- Automatic optimization
- Lazy loading
- Responsive images
- Better performance

**3. Utility Classes:**
```typescript
line-clamp-2  // Show max 2 lines, ellipsis after
line-clamp-3  // Show max 3 lines
hover:scale-105  // Scale up 5% on hover
transition-transform  // Smooth animation
```

**4. Date Formatting:**
```typescript
new Date(article.createdAt).toLocaleDateString()
// Output: "12/10/2025" (locale-aware)
```

---

## Responsive Design Patterns

### Mobile-First Approach

Tailwind is mobile-first by default:

```typescript
// Base styles (mobile)
className="text-sm p-4"

// Tablet and up (md: 768px+)
className="text-sm md:text-base p-4 md:p-6"

// Desktop (lg: 1024px+)
className="text-sm md:text-base lg:text-lg p-4 md:p-6 lg:p-8"
```

**Breakpoints:**
```
sm: 640px   // Small tablets
md: 768px   // Tablets
lg: 1024px  // Laptops
xl: 1280px  // Desktops
2xl: 1536px // Large displays
```

### Grid Layouts

**Responsive Grid:**

```typescript
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
  {articles.map((article) => (
    <ArticleCard key={article.id} article={article} />
  ))}
</div>
```

**Result:**
```
Mobile (< 768px):
[Card]
[Card]
[Card]
(1 column)

Tablet (768px - 1024px):
[Card] [Card]
[Card] [Card]
(2 columns)

Desktop (1024px+):
[Card] [Card] [Card]
[Card] [Card] [Card]
(3 columns)
```

### Container Pattern

```typescript
<div className="container mx-auto px-4 py-8 max-w-6xl">
  {/* Content */}
</div>
```

**What this does:**
- `container`: Responsive max-width
- `mx-auto`: Center horizontally
- `px-4`: Horizontal padding
- `py-8`: Vertical padding
- `max-w-6xl`: Maximum width (1152px)

---

## Layout Strategies

### Page Layout Pattern

```typescript
// src/app/articles/page.tsx
import { ArticleCard } from '@/components/article-card';
import { db } from '@/lib/db';
import { articles } from '@/lib/db/schema';
import { desc } from 'drizzle-orm';

export default async function ArticlesPage() {
  const allArticles = await db.query.articles.findMany({
    with: { author: true },
    orderBy: [desc(articles.createdAt)],
  });
  
  return (
    <div className="container mx-auto px-4 py-8">
      <div className="mb-8">
        <h1 className="text-4xl font-bold mb-2">All Articles</h1>
        <p className="text-muted-foreground">
          Browse our collection of {allArticles.length} articles
        </p>
      </div>
      
      <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
        {allArticles.map((article) => (
          <ArticleCard key={article.id} article={article} />
        ))}
      </div>
    </div>
  );
}
```

### Two-Column Layout

```typescript
<div className="container mx-auto px-4 py-8">
  <div className="grid grid-cols-1 lg:grid-cols-12 gap-8">
    {/* Main content (8 columns on desktop) */}
    <div className="lg:col-span-8">
      <ArticleContent />
    </div>
    
    {/* Sidebar (4 columns on desktop) */}
    <aside className="lg:col-span-4">
      <TableOfContents />
      <RelatedArticles />
    </aside>
  </div>
</div>
```

---

## Component Composition

### Building Blocks Pattern

```typescript
// Small, reusable components
<Button />
<Card />
<Avatar />

// Combine into larger components
<ArticleCard>
  <Card>
    <Image />
    <CardHeader>
      <Button />
    </CardHeader>
    <CardContent>
      <Avatar />
    </CardContent>
  </Card>
</ArticleCard>

// Use in pages
<ArticlesPage>
  {articles.map(article => (
    <ArticleCard article={article} />
  ))}
</ArticlesPage>
```

**Benefits:**
- ✅ Reusable components
- ✅ Consistent design
- ✅ Easy to maintain
- ✅ Testable in isolation

---

## 🧠 CHECKPOINT: Understanding UI

Before moving on, explain:

1. **Why Tailwind over traditional CSS?**
2. **What does shadcn do differently from other libraries?**
3. **What is the `cn` utility function for?**
4. **When would you use the `asChild` prop?**
5. **What's mobile-first design?**
6. **How do responsive breakpoints work?**

Take time to explain each clearly.

---

## Summary

You now understand:

✅ **Why Tailwind CSS** - Utility-first approach  
✅ **shadcn architecture** - Copy components, own the code  
✅ **Component patterns** - Composition and reusability  
✅ **Responsive design** - Mobile-first breakpoints  
✅ **Layout strategies** - Grid, container, columns  
✅ **Next.js Image** - Automatic optimization  

### Key Takeaways

1. **Utility-first CSS** is faster to write and maintain
2. **shadcn gives you ownership** - modify anything
3. **Components should be composable** - small pieces
4. **Design mobile-first** - then scale up
5. **Use semantic HTML** - Better accessibility
6. **Next.js optimizes images** - Use Image component
7. **Consistent spacing** - Use design system

### What's Next?

In Part 3, we'll build the data layer:
- Database fundamentals
- Schema design
- Drizzle ORM
- Migrations
- CRUD operations
- Query patterns

[→ Continue to Part 3: Database & Data Layer](./03-database-and-data.md)

---

**Great work on Part 2!** Your UI foundation is solid. Take a break, then continue when ready. 🚀
