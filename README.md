# agentic-repo-tree

Workspace de trabajo para diseñar y publicar scaffolding agentic.

## Contenido

- `scaffolding-guidelines.md`: especificación base del harness (Seed -> Distill -> Spec -> Ship -> QA).
- `create-agentic-repo/`: paquete npm público (CLI + template minimal) para generar repos agentic.

## Paquete principal: `create-agentic-repo`

`create-agentic-repo` genera un repositorio con:

- estructura Harness v2 (`00_inbox` a `05_scratch`)
- `shared/skills/` on-demand
- `shared/agents/` por rol
- `runners/` para Claude, Codex y Antigravity
- skill estratégica `skill-creator` incluida en el template minimal

## Uso local

```bash
cd create-agentic-repo
npm run smoke
node bin/create-agentic-repo.js demo --template minimal --yes --no-git
```

## Publicación

Desde `create-agentic-repo/`:

```bash
git push -u origin main
npm login
npm publish --access public
```

## Estado actual

- scaffold implementado y smoke test pasando
- commit inicial creado en `create-agentic-repo`
- pendiente: crear/autorizar repo remoto GitHub y autenticación npm para publicar
