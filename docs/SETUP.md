# ⚙️ Guía de Configuración Local - FixitHub

> Instrucciones detalladas para configurar el ambiente de desarrollo local.

---

## 📋 Pre-requisitos

### Instalaciones Requeridas

- **Node.js 18+** → [nodejs.org](https://nodejs.org/)
- **npm 9+** (viene con Node.js)
- **Git** → [git-scm.com](https://git-scm.com/)
- **Docker Desktop** → [docker.com](https://www.docker.com/products/docker-desktop)
- **MongoDB Compass** (opcional) → [mongodb.com/products/compass](https://www.mongodb.com/products/compass) (para visualizar BD)

### Verificar Instalaciones

```bash
node --version      # Debe mostrar v18.x.x o superior
npm --version       # Debe mostrar 9.x.x o superior
git --version       # Debe mostrar 2.x.x o superior
docker --version    # Debe mostrar 20.x.x o superior
```

---

## 🚀 Paso 1: Clonar el Repositorio

```bash
# Clonar desde GitHub
git clone https://github.com/TerrazasJr316/FixitHub.git

# Entrar al directorio
cd FixitHub

# Verificar que estamos en la rama main
git status
```

---

## 🐳 Paso 2: Configurar MongoDB con Docker

Docker es fundamental para evitar el clásico "en mi máquina funciona". Todos tenemos el mismo MongoDB.

### Iniciar MongoDB

```bash
# Levantar los servicios definidos en docker-compose.yml
docker-compose up -d

# Verificar que el contenedor está corriendo
docker ps

# Deberías ver algo como:
# CONTAINER ID   IMAGE                COMMAND
# abc123xyz      mongo:latest         mongod --auth
```

### Verificar Conexión a MongoDB

```bash
# Conectarse al contenedor
docker exec -it fixithub-mongo mongosh

# En la prompt de MongoDB:
> show dbs
admin    0.000GB
config   0.000GB
local    0.000GB

# Salir
> exit
```

### Visualizar BD con MongoDB Compass (Opcional)

```
Connection String: mongodb://localhost:27017/fixithub

Abre MongoDB Compass y pega la URL anterior.
Verás las colecciones que se creen.
```

### Detener MongoDB

```bash
docker-compose down
```

---

## 🔧 Paso 3: Configurar Backend (Node.js + Express)

### 3.1 Copiar Variables de Entorno

```bash
cd backend

# Copiar el archivo de ejemplo
cp .env.example .env

# El archivo .env debe verse así:
# MONGODB_URI=mongodb://localhost:27017/fixithub
# JWT_SECRET=tu-clave-secreta-aqui
# NODE_ENV=development
# PORT=5000
# CORS_ORIGIN=http://localhost:3000
```

### 3.2 Instalar Dependencias

```bash
# Dentro de /backend
npm install

# Esto descargará ~200MB de paquetes (mongoose, express, socket.io, etc.)
# Puede tardar 2-5 minutos
```

### 3.3 Compilar TypeScript (Opcional)

```bash
# Compila .ts a .js en carpeta dist/
npm run build

# Valida tipos sin compilar
npm run type-check
```

### 3.4 Iniciar Backend en Desarrollo

```bash
# Con hot reload (se recarga automáticamente al cambiar código)
npm run dev

# Deberías ver algo como:
# ✓ Server running on http://localhost:5000
# ✓ Connected to MongoDB
```

### ✅ Verificar Backend

Abre en tu navegador o con curl:

```bash
# Debe retornar 200 OK
curl http://localhost:5000/

# Resultado esperado:
# {"message":"FixitHub API is running"}
```

---

## 🌐 Paso 4: Configurar Frontend Web (React)

### 4.1 Copiar Variables de Entorno (Opcional)

```bash
cd ../frontend-web

# Si existe .env.example
cp .env.example .env

# Normalmente React infiere la URL base del backend
```

### 4.2 Instalar Dependencias

```bash
npm install

# ~150MB de paquetes
# Puede tardar 2-3 minutos
```

### 4.3 Iniciar Frontend Web

```bash
# En desarrollo con hot reload
npm start

# Se abrirá automáticamente en http://localhost:3000
# Si no, abre manualmente
```

### ✅ Verificar Frontend Web

- Deberías ver la app cargando en `http://localhost:3000`
- Abre DevTools (F12) y verifica la consola
- No debe haber errores rojos graves

---

## 📱 Paso 5: Configurar Frontend Móvil (React Native)

### 5.1 Instalar Expo CLI (Primera Vez)

```bash
npm install -g expo-cli

# Verificar
expo --version
```

### 5.2 Instalar Dependencias

```bash
cd ../frontend-mobile

npm install

# ~200MB
# Puede tardar 3-5 minutos
```

### 5.3 Iniciar Expo

```bash
npm start

# Verás un menú interactivo:
# ›   Press a to open Android
# ›   Press i to open iOS (macOS)
# ›   Press w to open web preview
# ›   Press r to reload the app
# ›   Press q to quit
```

### ✅ Verificar Frontend Móvil - Opción A: Simulador

#### Android (Cualquier OS)

```bash
# Si tienes Android Studio instalado:
npm run android

# Se abre el emulador automáticamente
```

#### iOS (Solo macOS)

```bash
npm run ios

# Se abre el simulador de iOS
```

### ✅ Verificar Frontend Móvil - Opción B: Dispositivo Real

1. Descarga **Expo Go** en tu teléfono (App Store / Play Store)
2. En la terminal, verás un código QR
3. Abre Expo Go → Escanea el código QR
4. La app se descargará y ejecutará en tu teléfono

---

## 🎯 Verificación Completa (Todos los Servicios Corriendo)

Abre 3 terminales diferentes:

### Terminal 1: MongoDB

```bash
cd FixitHub
docker-compose up -d
# Verificar con: docker ps
```

### Terminal 2: Backend

```bash
cd FixitHub/backend
npm run dev

# Esperado:
# ✓ Server running on http://localhost:5000
# ✓ Connected to MongoDB
```

### Terminal 3: Frontend Web

```bash
cd FixitHub/frontend-web
npm start

# Se abrirá automáticamente en http://localhost:3000
```

### Terminal 4 (Bonus): Frontend Móvil

```bash
cd FixitHub/frontend-mobile
npm start

# Escanea el código QR con Expo Go
```

---

## 🧪 Tests Funcionales Básicos

### Backend - Prueba de Autenticación

```bash
# Registrar nuevo usuario
curl -X POST http://localhost:5000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"nombre":"Juan","email":"juan@test.com","password":"123456","role":"CLIENTE"}'

# Resultado esperado:
# {"token":"eyJhbGc...","user":{"id":"...","email":"juan@test.com"}}
```

### Frontend Web - Verificar Conexión a Backend

Abre DevTools (F12) → Console:

```javascript
// Debe conectarse al backend
fetch('http://localhost:5000/')
  .then(r => r.json())
  .then(d => console.log(d))

// Resultado esperado:
// {message: "FixitHub API is running"}
```

---

## 🛑 Solución de Problemas Comunes

### ❌ Error: "Port 5000 already in use"

El puerto 5000 ya está siendo usado.

```bash
# Encuentra qué proceso está usando el puerto
lsof -i :5000

# Mata el proceso
kill -9 <PID>

# O usa un puerto diferente en .env
PORT=5001
```

### ❌ Error: "Cannot connect to MongoDB"

MongoDB no está corriendo.

```bash
# Verifica que Docker está activo
docker ps

# Si no está, levántalo
docker-compose up -d

# Verifica logs
docker-compose logs mongo
```

### ❌ Error: "npm ERR! code E404"

La dependencia no existe o hay typo.

```bash
# Limpia cache
npm cache clean --force

# Reinstala todo
rm -rf node_modules package-lock.json
npm install
```

### ❌ Error: "TypeScript compilation error"

Hay problemas de tipos.

```bash
# Verifica tipos sin compilar
npm run type-check

# O ve a VS Code y revisa los squiggles rojos
```

### ❌ La app no se recarga (Hot Reload no funciona)

React/Node está cacheando.

```bash
# Para Backend (en la terminal del servidor)
npm run dev

# Presiona Ctrl+C y vuelve a ejecutar

# Para Frontend
# Presiona Ctrl+C y ejecuta de nuevo npm start
```

---

## 💡 Comandos Útiles para Desarrollo

### Backend

```bash
cd backend

npm run dev            # Desarrollo con hot reload
npm run build          # Compilar TypeScript
npm run type-check     # Validar tipos
npm test               # Ejecutar tests
npm run lint           # Linter (ESLint)
npm start              # Producción
```

### Frontend Web

```bash
cd frontend-web

npm start              # Desarrollo
npm run build          # Build para producción
npm run lint           # Linter
npm run eject          # ⚠️ Irreversible, evitar
```

### Frontend Móvil

```bash
cd frontend-mobile

npm start              # Expo dev server
npm run android        # Emulador Android
npm run ios            # Simulador iOS (macOS)
npm run build          # Build para release
```

### Docker

```bash
docker-compose up -d           # Levantar servicios
docker-compose down            # Detener servicios
docker-compose logs -f         # Ver logs en vivo
docker-compose down -v         # Borrar volúmenes (BD)
docker ps                      # Listar contenedores activos
docker exec -it <container> bash  # Conectarse al contenedor
```

---

## 📁 Estructura de Carpetas (Post-Instalación)

```
FixitHub/
├── backend/
│   ├── node_modules/         # ← npm install descargará aquí
│   ├── src/                  # Código TypeScript
│   ├── dist/                 # ← npm run build crea esto (JS compilado)
│   ├── .env                  # ← Crea esto desde .env.example
│   ├── package.json
│   └── tsconfig.json
│
├── frontend-web/
│   ├── node_modules/         # ← npm install descargará aquí
│   ├── src/
│   ├── public/
│   ├── build/                # ← npm run build crea esto
│   ├── .env                  # ← Variables de entorno (opcional)
│   └── package.json
│
├── frontend-mobile/
│   ├── node_modules/
│   ├── src/
│   ├── .env                  # ← Crea esto si necesitas
│   └── package.json
│
└── docker-compose.yml        # Define servicios (MongoDB)
```

---

## 🔄 Ciclo Típico de Desarrollo

1. **Mañana**: Llego a la oficina, abro 3-4 terminales

```bash
# Terminal 1: Docker + MongoDB
docker-compose up -d

# Terminal 2: Backend
cd backend && npm run dev

# Terminal 3: Frontend Web
cd frontend-web && npm start

# Terminal 4 (opcional): Frontend Móvil
cd frontend-mobile && npm start
```

2. **Edito código** en mis archivos `.ts` o `.tsx`

3. **Se recarga automáticamente** (hot reload)

4. **Hago cambios en BD** → El backend sincroniza automáticamente

5. **Hago commit** → Los cambios se suben a GitHub

6. **Fin del día**: Presiono `Ctrl+C` en las terminales

---

## 🎓 Próximos Pasos

Después de configurar todo:

1. Lee [CONTRIBUTING.md](../CONTRIBUTING.md) para entender la estructura de ramas
2. Revisa [ARCHITECTURE.md](./ARCHITECTURE.md) para entender el diseño
3. Abre [API.md](./API.md) para ver los endpoints disponibles


¿Problemas? Pregunta al Tech Lead. 🚀
