---
title: "Building a CLI Tool with TypeScript"
tags: [guide, cli, typescript, commander, inquirer, chalk, node]
content_type: guide
domain: typescript-fullstack
---

# Building a CLI Tool with TypeScript

## Step 1: Project Setup

```bash
mkdir my-cli && cd my-cli
pnpm init
pnpm add commander chalk ora
pnpm add -D typescript tsx @types/node
```

```json
// tsconfig.json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "strict": true,
    "outDir": "dist",
    "rootDir": "src",
    "esModuleInterop": true,
    "declaration": true
  },
  "include": ["src"]
}
```

```json
// package.json
{
  "name": "my-cli",
  "version": "1.0.0",
  "type": "module",
  "bin": {
    "my-cli": "./dist/index.js"
  },
  "scripts": {
    "dev": "tsx src/index.ts",
    "build": "tsc",
    "start": "node dist/index.js"
  }
}
```

## Step 2: Create the CLI

```typescript
// src/index.ts
#!/usr/bin/env node

import { Command } from "commander";
import chalk from "chalk";
import ora from "ora";

const program = new Command();

program
  .name("my-cli")
  .description("A useful CLI tool")
  .version("1.0.0");

program
  .command("init")
  .description("Initialize a new project")
  .option("-t, --template <template>", "Project template", "default")
  .action(async (options) => {
    const spinner = ora("Creating project...").start();

    try {
      await createProject(options.template);
      spinner.succeed(chalk.green("Project created successfully!"));
    } catch (error) {
      spinner.fail(chalk.red("Failed to create project"));
      console.error(error);
      process.exit(1);
    }
  });

program
  .command("deploy")
  .description("Deploy the project")
  .option("--dry-run", "Show what would be deployed", false)
  .action(async (options) => {
    if (options.dryRun) {
      console.log(chalk.yellow("Dry run — no changes will be made"));
    }
    await deploy(options);
  });

program.parse();
```

## Step 3: Interactive Prompts

```typescript
// Using @inquirer/prompts (modern, tree-shakeable)
import { input, select, confirm } from "@inquirer/prompts";

async function interactiveInit() {
  const name = await input({ message: "Project name:" });

  const template = await select({
    message: "Choose a template:",
    choices: [
      { name: "Next.js App Router", value: "nextjs" },
      { name: "Express API", value: "express" },
      { name: "CLI Tool", value: "cli" },
    ],
  });

  const useDocker = await confirm({
    message: "Add Docker support?",
    default: false,
  });

  return { name, template, useDocker };
}
```

## Step 4: Build and Publish

```bash
# Build
pnpm build

# Test locally
chmod +x dist/index.js
node dist/index.js init --template nextjs

# Link for local testing
pnpm link --global
my-cli init

# Publish to npm
npm publish
```

## Step 5: Bundle for Distribution (Optional)

For a single-file distributable:

```bash
pnpm add -D tsup

# tsup.config.ts
import { defineConfig } from "tsup";
export default defineConfig({
  entry: ["src/index.ts"],
  format: ["esm"],
  target: "node20",
  banner: { js: "#!/usr/bin/env node" },
  clean: true,
});
```

## See Also

- `hub/typescript-strict-patterns.md` — Type patterns for CLI args
- `entities/zod-schema-patterns.md` — Validating CLI input
