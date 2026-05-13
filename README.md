# 🛠️ FixitHub - Marketplace de Servicios Locales

> **Plataforma digital tipo "Uber de Oficios"** que conecta a clientes con técnicos y profesionales independientes para servicios de reparación, mantenimiento y asistencia en el hogar.

[![Estado del Proyecto](https://img.shields.io/badge/Estado-En%20Desarrollo-yellow?style=flat-square)](https://github.com)
[![MERN Stack](https://img.shields.io/badge/Stack-MERN-brightgreen?style=flat-square)](https://mern.io/)

---

## 📱 Descripción del Proyecto

FixitHub es una aplicación web y móvil que permite:

- **Usuarios Clientes**: Solicitar servicios de reparación en su hogar (electricidad, plomería, etc.)
- **Técnicos/Profesionales**: Recibir notificaciones de trabajos cercanos, aceptar solicitudes y actualizar estado de tareas
- **Administradores**: Gestionar usuarios, categorías de servicios y reportes

### 🎯 Características Principales (MVP)

- ✅ Autenticación segura con JWT
- ✅ Búsqueda geoespacial de técnicos cercanos
- ✅ Notificaciones en tiempo real con WebSockets
- ✅ Sistema de tickets para solicitud de servicios
- ✅ Panel administrativo de control
- ✅ Aplicación móvil con GPS
- ✅ Historial de trabajos y calificaciones

---

## 🏗️ Arquitectura Técnica

### Stack Tecnológico

```
FRONTEND
├── Web: React 18 + TypeScript
└── Móvil: React Native + Expo

BACKEND
├── Servidor: Node.js + Express
├── Lenguaje: JavaScript/TypeScript
├── Base de Datos: MongoDB
├── Autenticación: JWT (jsonwebtoken)
├── Tiempo Real: Socket.io
├── Validación: Mongoose + Custom Validators
└── Criptografía: bcryptjs

INFRAESTRUCTURA
├── Containerización: Docker + Docker Compose
└── Control de Versiones: Git + GitHub
```

### Estructura de Monorepo

```
FixitHub/
├── backend/           # API REST con microservicios
├── frontend-web/      # Aplicación web (React)
├── frontend-mobile/   # Aplicación móvil (React Native)
├── docs/             # Documentación técnica
└── docker-compose.yml # Servicios compartidos
```

---

## 🚀 Inicio Rápido

### Requisitos Previos

- **Node.js** 18.0 o superior
- **npm** 9.0 o superior
- **Docker** y **Docker Compose** (para MongoDB local)
- **Git** (para control de versiones)

### ⚙️ Instalación Local

#### 1. Clonar el Repositorio

```bash
git clone https://github.com/TerrazasJr316/FixitHub.git
cd FixitHub
```

#### 2. Configurar Docker y MongoDB

```bash
# Levantar MongoDB en un contenedor
docker-compose up -d

# Verificar que MongoDB está corriendo
docker ps
```

MongoDB estará disponible en: `mongodb://localhost:27017/fixithub`

#### 3. Configurar Backend

```bash
cd backend

# Copiar archivo de ejemplo
cp .env.example .env

# Instalar dependencias
npm install

# Compilar TypeScript
npm run build

# Ejecutar en desarrollo (con hot reload)
npm run dev
```

**Backend disponible en**: `http://localhost:5000`

#### 4. Configurar Frontend Web

```bash
cd ../frontend-web

# Instalar dependencias
npm install

# Iniciar servidor de desarrollo
npm start
```

**Frontend Web disponible en**: `http://localhost:3000`

#### 5. Configurar Frontend Móvil

```bash
cd ../frontend-mobile

# Instalar dependencias
npm install

# Iniciar Expo (escanea el código QR con la app Expo Go)
npm start

# O simular en emulador Android/iOS
npm run android    # Android
npm run ios        # iOS (solo en macOS)
```

### ✅ Verificar que Todo Funciona

```bash
# Terminal 1: Backend
cd backend && npm run dev

# Terminal 2: Frontend Web
cd frontend-web && npm start

# Terminal 3: Frontend Móvil
cd frontend-mobile && npm start
```

Deberías ver:
- 🟢 Backend ejecutándose en `http://localhost:5000`
- 🟢 Web ejecutándose en `http://localhost:3000`
- 🟢 Aplicación móvil en Expo

---

## 📚 Documentación

### Guías Detalladas

- [**CONTRIBUTING.md**](./CONTRIBUTING.md) - Guía completa de contribución, estructura de código y buenas prácticas
- [**ARCHITECTURE.md**](./docs/ARCHITECTURE.md) - Diseño arquitectónico del sistema
- [**API.md**](./docs/API.md) - Documentación completa de endpoints
- [**SETUP.md**](./docs/SETUP.md) - Guía detallada de configuración local

### Convenciones en Este Proyecto

#### 🌳 Ramas Git

Seguimos una estrategia de ramas clara y organizada:

- **`main`**: Rama de producción (código 100% estable)
- **`develop`**: Rama de integración (donde se fusionan features)
- **`feature/*`**: Nuevas funcionalidades (`feature/nombre-descriptivo`)
- **`fix/*`**: Correcciones de bugs (`fix/nombre-descriptivo`)

**Ver**: [Estrategia de Ramas](./CONTRIBUTING.md#-estrategia-de-ramas-git-flow-simplificado)

#### 📝 Commits

Usamos **Conventional Commits** para mensajes claros y semánticos:

```bash
git commit -m "feat(backend): agregar autenticación JWT"
git commit -m "fix(mobile): corregir crash en notificaciones"
```

**Ver**: [Commits Convencionales](./CONTRIBUTING.md#3️⃣-hacer-commits-frecuentes)

#### 🏗️ Estructura de Carpetas

Cada módulo (backend, web, mobile) tiene una estructura clara y consistente:

**Backend**:
```
backend/src/
├── models/      # Esquemas Mongoose
├── controllers/ # Lógica de rutas
├── routes/      # Definición de endpoints
├── middleware/  # Middleware personalizado
├── services/    # Lógica de negocio
└── utils/       # Funciones utilitarias
```

**Frontend**:
```
frontend-{web,mobile}/src/
├── components/  # Componentes reutilizables
├── pages/       # Páginas principales
├── hooks/       # Custom hooks
├── services/    # Llamadas a API
├── context/     # Estado global
└── types/       # Tipos TypeScript
```

**Ver**: [Estructura Completa](./CONTRIBUTING.md#-estructura-del-monorepositorio)

## 🛠️ Comandos Útiles

### Backend

```bash
cd backend

# Desarrollo con hot reload
npm run dev

# Compilar TypeScript
npm run build

# Validar tipos sin compilar
npm run type-check

# Ejecutar tests
npm test

# Ejecutar con coverage
npm run test:coverage

# Linter
npm run lint

# Iniciar en producción
npm start
```

### Frontend Web

```bash
cd frontend-web

# Desarrollo
npm start

# Build para producción
npm run build

# Eject (no recomendado)
npm run eject

# Linter
npm run lint
```

### Frontend Móvil

```bash
cd frontend-mobile

# Inicio con Expo
npm start

# Emulador Android
npm run android

# Emulador iOS (macOS)
npm run ios

# Build para producción
npm run build
```

### Docker

```bash
# Levantar servicios
docker-compose up -d

# Ver logs
docker-compose logs -f

# Detener servicios
docker-compose down

# Eliminar todo incluyendo volúmenes
docker-compose down -v
```

---

## 📖 API Endpoints (Vista Rápida)

### Autenticación
- `POST /api/auth/register` - Registrar nuevo usuario
- `POST /api/auth/login` - Iniciar sesión
- `POST /api/auth/refresh` - Refrescar token

### Trabajos/Tickets
- `GET /api/jobs` - Listar trabajos
- `GET /api/jobs/nearby` - Trabajos cercanos (para técnicos)
- `POST /api/jobs` - Crear nuevo trabajo
- `PUT /api/jobs/:id/status` - Actualizar estado

### Usuarios
- `GET /api/users/:id` - Obtener perfil
- `PUT /api/users/me/location` - Actualizar ubicación GPS
- `GET /api/users/me/ratings` - Ver calificaciones

**Ver documentación completa**: [API.md](./docs/API.md)

---

## 🤝 Contribuir al Proyecto

### Proceso de Contribución

1. **Crea una rama** desde `develop`:
   ```bash
   git checkout develop
   git pull origin develop
   git checkout -b feature/nombre-descriptivo
   ```

2. **Hace tus cambios** y commitea con mensajes claros:
   ```bash
   git commit -m "feat(backend): agregar nuevo endpoint"
   ```

3. **Push y abre un Pull Request**:
   ```bash
   git push origin feature/nombre-descriptivo
   ```

4. **Espera revisión** y solicita cambios si es necesario

5. **Merge a `develop`** una vez aprobado

### Estándares de Código

- ✅ TypeScript con `strict: true`
- ✅ Nombres descriptivos y claros
- ✅ Funciones pequeñas (<30 líneas)
- ✅ Props tipadas en React
- ✅ Comentarios en lógica compleja
- ✅ Sin `any` en TypeScript (a menos que sea absolutamente necesario)
- ✅ Tests para funcionalidad crítica

**Guía completa**: [CONTRIBUTING.md](./CONTRIBUTING.md)

---

## 📋 Variables de Entorno

### Backend (.env)

```env
# MongoDB
MONGODB_URI=mongodb://localhost:27017/fixithub
NODE_ENV=development

# JWT
JWT_SECRET=tu-clave-secreta-super-segura-aqui
JWT_EXPIRE=7d

# CORS
CORS_ORIGIN=http://localhost:3000

# Server
PORT=5000
HOST=localhost

# Socket.io
SOCKET_IO_CORS=http://localhost:3000
```

### Frontend Web (.env)

```env
REACT_APP_API_URL=http://localhost:5000/api
REACT_APP_SOCKET_URL=http://localhost:5000
```

### Frontend Móvil (.env)

```env
EXPO_PUBLIC_API_URL=http://localhost:5000/api
EXPO_PUBLIC_SOCKET_URL=http://localhost:5000
```

---

## 🐛 Solución de Problemas

### MongoDB no se conecta

```bash
# Verifica que Docker está corriendo
docker ps

# Si no está, levántalo
docker-compose up -d

# Verifica los logs
docker-compose logs mongodb
```

### Puerto ya en uso

```bash
# Backend (5000)
lsof -i :5000
kill -9 <PID>

# Frontend (3000)
lsof -i :3000
kill -9 <PID>
```

## 📞 Contacto y Soporte

- 👨‍💻 **Tech Lead**: [Nombre del Lead]
- 📧 **Email**: [email del proyecto]
- 💬 **Chat del Equipo**: Discord/Slack
- 📝 **Tablero de Tareas**: GitHub Projects

---

## 📄 Licencia

Este proyecto está bajo la licencia **MIT**. Ver [LICENSE](./LICENSE) para más detalles.

<div align="center">

### Hecho con ❤️ por el equipo de FixitHub

</div>
