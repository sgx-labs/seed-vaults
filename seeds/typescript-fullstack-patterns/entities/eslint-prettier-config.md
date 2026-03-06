---
title: "ESLint + Prettier Config Reference"
tags: [eslint, prettier, linting, formatting, config, reference, entity]
content_type: entity
domain: typescript-fullstack
---

# ESLint + Prettier Config Reference

## ESLint Flat Config (v9+)

```typescript
// eslint.config.mjs
import { dirname } from "path";
import { fileURLToPath } from "url";
import { FlatCompat } from "@eslint/eslintrc";
import tseslint from "typescript-eslint";

const __dirname = dirname(fileURLToPath(import.meta.url));
const compat = new FlatCompat({ baseDirectory: __dirname });

export default tseslint.config(
  ...compat.extends("next/core-web-vitals", "next/typescript"),
  {
    rules: {
      "@typescript-eslint/no-unused-vars": ["error", { argsIgnorePattern: "^_" }],
      "@typescript-eslint/no-explicit-any": "error",
      "@typescript-eslint/consistent-type-imports": ["error", { prefer: "type-imports" }],
      "prefer-const": "error",
      "no-console": ["warn", { allow: ["warn", "error"] }],
    },
  },
  {
    ignores: [".next/", "node_modules/", "dist/", "coverage/"],
  }
);
```

## Prettier Config

```json
// .prettierrc
{
  "semi": true,
  "singleQuote": false,
  "tabWidth": 2,
  "trailingComma": "all",
  "printWidth": 100,
  "plugins": ["prettier-plugin-tailwindcss"],
  "tailwindFunctions": ["cn", "cva"]
}
```

```
// .prettierignore
.next
node_modules
dist
coverage
pnpm-lock.yaml
```

## VS Code Settings

```json
// .vscode/settings.json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit",
    "source.organizeImports": "explicit"
  },
  "typescript.tsdk": "node_modules/typescript/lib"
}
```

## Git Hooks (Husky + lint-staged)

```json
// package.json
{
  "lint-staged": {
    "*.{ts,tsx}": ["eslint --fix", "prettier --write"],
    "*.{json,md,css}": ["prettier --write"]
  }
}
```

```bash
# Setup
pnpm add -D husky lint-staged
npx husky init
echo "npx lint-staged" > .husky/pre-commit
```

## Required Packages

```bash
pnpm add -D eslint prettier typescript-eslint @eslint/eslintrc prettier-plugin-tailwindcss husky lint-staged
```

## See Also

- `entities/package-json-scripts.md` — Lint/format scripts
- `guides/setting-up-nextjs-project.md` — Full project setup
