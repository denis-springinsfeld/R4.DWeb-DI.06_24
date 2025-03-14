# R4.DWeb-DI.06_24
# Tailwind avancé

## A/ Installations

### Installation de React

```bash
  # Créer un projet Vite
  npm create vite@latest
  # Retour à react18
  npm i react@18 react-dom@18
```

[Vite Doc](https://vite.dev/guide/)

### Tailwind CSS

```bash
  npm install tailwindcss @tailwindcss/vite
```

[TailwindCss Doc](https://tailwindcss.com/docs/installation/using-vite)

```ts
// vite.config.ts
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import tailwindcss from "@tailwindcss/vite";

// https://vite.dev/config/
export default defineConfig({
  plugins: [react(), tailwindcss()],
});
```

### Prettier

```bash
  npm install -D prettier prettier-plugin-tailwindcss
```

[Prettier Doc](https://github.com/tailwindlabs/prettier-plugin-tailwindcss)

Ajouter au fichier `.prettierrc` à votre projet.

```json
// .prettierrc
{
  "plugins": ["prettier-plugin-tailwindcss"],
  "tailwindStylesheet": "./src/index.css",
  "tailwindFunctions": ["cva"]
}
```

---

## B/ Limitations

### B_1 Etat par defaut :

```javascript
function Button({ children, ...restProps }) {
  return (
    <button
      {...restProps}
      className="rounded-lg bg-emerald-600 px-5 py-2 font-semibold text-white hover:bg-emerald-500 active:bg-emerald-700"
    >
      {children}
    </button>
  );
}

export default function App() {
  return (
    <div className="grid h-screen place-content-center">
      <Button className="">Click me</Button>
    </div>
  );
}
```

### B_2 Ajout d'une props `className` :

- 🚀 Ajouter la classe `bg-blue-500` dans le composant `Button` à l'aide de la props `className`. Qu'observez-vous ?

- 🚀 Déplaçons `restProps` à la fin. Qu'observez-vous ?

### B_3 Concaténation des classes Tailwind avec un template literal :

```javascript
// Dans le composant Button
...
className={`rounded-lg bg-emerald-600 px-5 py-2 font-semibold text-white hover:bg-emerald-500 active:bg-emerald-700 ${className}`}
...
```

- 🚀 Concaténation des classes à l'aide du template littéral. Qu'observez-vous ?


- Idem testez avec `bg-red-500`.

💡 **Remarque** : Les conflits entre les classes Tailwind - apparaissentt avec l'extension _Tailwind CSS IntelliSense_.

> ⚠️ **Attention** au mode de création des classes dans Tailwind : 'just-in-time compiler' : l'order des classes Tailwind compilées est un 'ordre alphabétique' et non l'ordre de déclaration.

---

## C/ TailwindMerge

**TailwindMerge** est un outil qui vous permet de fusionner plusieurs classes CSS Tailwind et résout automatiquement les conflits entre les classes en supprimant les classes en conflit avec une classe définie plus tard. C'est particulièrement utile lorsque vous souhaitez remplacer les classes CSS Tailwind dans vos composants.

https://www.npmjs.com/package/tailwind-merge
https://github.com/dcastil/tailwind-merge/blob/v2.0.0/docs/features.md

- Installation

```bash
npm install tailwind-merge
```

- Utilisation

```javascript
// Dans le composant Button
import {twMerge} from "tailwind-merge";

...
className = {twMerge(
  "rounded-lg bg-emerald-600 px-5 py-2 font-semibold text-white hover:bg-emerald-500 active:bg-emerald-700",
  className)}
...
```

```javascript
// Dans le composant Button
...
className = {twMerge("rounded-lg bg-emerald-600 px-5 py-2 font-semibold text-white hover:bg-emerald-500 active:bg-emerald-700",
  className,
  active && "bg-gray-600")}
...
```

---

## D/ Clsx et `cn()`

Un autre outil - **clsx** - **Rendu de classe conditionnelle**, vous permet de définir conditionnellement des noms de classes react en utilisant un objet ou une liste de chaînes.

https://www.npmjs.com/package/clsx

- Installation

```bash
npm install clsx
```

- Fonction cn()
  ⚠️ Clsx ne résout pas les conflits entre les classes comme **TailwindMerge**.
  💡 Nous allons donc combiner `clsx` et `tailwind-merge` avec la `fonction cn()`

```javascript
// Créer un répertoire libs dans src et créer un fichier utils.ts
import { type ClassValue, clsx } from "clsx";
import { twMerge } from "tailwind-merge";

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}
```

- Utilisation

```javascript
// Dans le composant Button
// Importer la fonction cn() dans le composant Button
...
className = {cn(
  "rounded-lg bg-emerald-600 px-5 py-2 font-semibold text-white hover:bg-emerald-500 active:bg-emerald-700",
  className,
  {"bg-gray-600": active})
  }
```

---

## D/ Variante de classe

### D_1 Méthode 1

```javascript
const baseClasses = "rounded-md font-medium focus:outline-none";

const variantsLookup = {
  primary:
    "bg-cyan-500 text-white shadow-lg hover:bg-cyan-400 focus:bg-cyan-400 focus:ring-cyan-500",
  secondary:
    "bg-slate-200 text-slate-800 shadow hover:bg-slate-300 focus:bg-slate-300 focus:ring-slate-500",
  danger:
    "bg-red-500 text-white shadow-lg uppercase tracking-wider hover:bg-red-400 focus:bg-red-400 focus:ring-red-500",
  text: "text-slate-700 uppercase underline hover:text-slate-600 hover:bg-slate-900/5 focus:text-slate-600 focus:ring-slate-500",
};

const sizesLookup = {
  small: "px-3 py-1.5 text-sm focus:ring-2 focus:ring-offset-1",
  medium: "px-5 py-3 focus:ring-2 focus:ring-offset-2",
  large: "px-8 py-4 text-lg focus:ring focus:ring-offset-2",
};

export default function Button(
  variant = "primary",
  size = "medium",
  children,
  ...rest
) {
  return (
    <button
      {...rest}
      className={`${baseClasses} ${variantsLookup[variant]} ${sizesLookup[size]}`}
    >
      {children}
    </button>
  );
}
```

Avec cn()

```javascript
import {cn} from "../libs/utils";

...
className = {cn(
  baseClasses,
  variantsLookup[variant],
  sizesLookup[size],
  className)}

```

### D_2 Méthode 2 : Class Variance Autority CVA

https://www.npmjs.com/package/class-variance-authority

https://cva.style/docs/examples/react/tailwind-css

- Installation

```bash
  npm i class-variance-authority
```

⚠️ Pour que _Tailwind CSS IntelliSense_ continue à fonctionner, ajoutez au fichier `settings.json`

```json
{
  "tailwindCSS.experimental.classRegex": [
    ["cva\\(([^)]*)\\)", "[\"'`]([^\"'`]*).*?[\"'`]"],
    ["cx\\(([^)]*)\\)", "(?:'|\"|`)([^']*)(?:'|\"|`)"]
  ]
}
```

- Utilisation

```javascript
import { cva } from "class-variance-authority";
const base = "mes classes de base";

const buttonVariants = cva(base, {
  variants: {
    intent: {
      primary: "",
      secondary: "",
      alert: "",
    },
    size: {
      small: "",
      medium: "",
    },
    rounde: {
      rd: "",
      nrd: "",
    },
  },
  compoundVariants: [],
  defaultVariants: {
    intent: "primary",
    size: "medium",
  },
});

export default function ButtonCVA({
  className,
  intent,
  size,
  rounde,
  children,
  ...props
}) {
  return (
    <button
      className={buttonVariants({ intent, size, rounde, className })}
      {...props}
    >
      {children}
    </button>
  );
}
```

---

https://oklch.com/


```jsx
import { cva } from "class-variance-authority";
import { cn } from "./utils/cn";

const buttonVariants = cva("rounded-md font-medium focus:outline-none", {
  variants: {
    variant: {
      default:
        "bg-cyan-500 text-white shadow-lg hover:bg-cyan-400 focus:bg-cyan-400 focus:ring-cyan-500",
      destructive:
        "bg-red-500 text-white shadow-lg hover:bg-red-400 focus:bg-red-400 focus:ring-red-500",
    },
    size: {
      default: "h-10 px-4 py-2",
      sm: "h-9 rounded-md px-3",
      lg: "h-11 rounded-md px-8",
    },
  },

  defaultVariants: {
    variant: "default",
    size: "default",
  },
});

// Interface simplifiée avec uniquement les props nécessaires

interface ButtonProps {
  variant?: "default" | "destructive";
  size?: "default" | "sm" | "lg";
  children?: React.ReactNode;
  className?: string;
}

function Button({
  variant,
  size,

  className,
  children,

  ...props
}: ButtonProps) {
  return (
    <button
      className={cn(buttonVariants({ variant, size, className }))}
      {...props}
    >
      {children}
    </button>
  );
}

export default function App() {
  return (
    <Button variant="destructive" size="lg">
      Click me
    </Button>
  );
}


```
