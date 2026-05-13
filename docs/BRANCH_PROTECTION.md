# 🔐 Guía: Protecciones de Ramas en GitHub

> Configuración de reglas de protección para mantener la calidad del código.

---

## ⚙️ Cómo Configurar Protecciones en GitHub

### Paso 1: Ir a Configuración del Repositorio

1. En GitHub, ve a tu repositorio
2. Ve a **Settings** (Configuración)
3. En el menú lateral, selecciona **Branches**

### Paso 2: Agregar Protecciones a `main`

1. Bajo "Branch protection rules", haz clic en **"Add rule"**
2. En "Branch name pattern", escribe: `main`
3. Habilita las siguientes opciones:

#### ✅ Require a pull request before merging

- [x] Require approvals
- Número de aprobaciones requeridas: **1**
- [x] Require review from Code Owners

#### ✅ Require status checks to pass before merging

Selecciona los siguientes checks:
- `build` (CI/CD)
- `tests` (Si lo configuras)
- `lint` (ESLint)

#### ✅ Require branches to be up to date before merging

- [x] Esto asegura que el PR está actualizado con main

#### ✅ Require conversations to be resolved before merging

- [x] Todos los comentarios del review deben resolverse

#### ✅ Require linear history

- [x] Evita merge commits complicados

#### ✅ Include administrators

- [x] Las reglas aplican incluso para administradores

### Paso 3: Agregar Protecciones a `develop`

Repite el proceso anterior pero:
- Branch name pattern: `develop`
- Aprobaciones requeridas: **1**
- Sin necesidad de "linear history" (es rama de integración)

### Paso 4: Agregar Reglas para `feature/*` y `fix/*` (Opcional)

- Branch name pattern: `feature/**`
- Branch name pattern: `fix/**`

Requisitos más lentos (no es tan crítico):
- [x] Require branches to be up to date before merging

---

## 📋 Resumen de Protecciones

| Rama | Aprobaciones | Tests | Linear | Conversaciones |
|------|-------------|-------|--------|----------------|
| `main` | Sí (1) | Sí | Sí | Sí |
| `develop` | Sí (1) | Sí | No | Sí |
| `feature/*` | No | No | No | No |
| `fix/*` | No | No | No | No |

---

## 🚀 CI/CD con GitHub Actions (Futuro)

Cuando configures GitHub Actions, agrega workflows como:

```yaml
# .github/workflows/build.yml
name: Build & Test

on: [pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: 18
      
      - name: Install dependencies
        run: npm install
      
      - name: Build
        run: npm run build
      
      - name: Run tests
        run: npm test
      
      - name: Lint
        run: npm run lint
```

---

## 📝 Consejos para PRs Efectivos

1. **Título claro**: "feat(backend): agregar autenticación JWT"
2. **Descripción detallada**: Qué cambios, por qué, impacto
3. **Screenshots**: Si hay UI changes
4. **Pequeños PRs**: Máximo 400-500 líneas
5. **Sincroniza con develop**: `git rebase origin/develop`

---

## ⚡ Flujo de Merge Automático (Futuro)

Cuando todo esté verde (todas las comprobaciones):

```
Feature PR → Aprobado → Auto-merge a develop
Develop PR → Aprobado → Manual merge a main (más control)
```
