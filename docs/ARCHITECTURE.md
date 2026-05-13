# 🏗️ Arquitectura Técnica - FixitHub

> Documento que describe la arquitectura, componentes y decisiones técnicas del sistema FixitHub.

---

## 📐 Diagrama General de Arquitectura

```
┌──────────────────────────────────────────────────────────────────┐
│                         CLIENTES                                 │
├──────────────────────────────────────────────────────────────────┤
│ Web Browser (React)      │      Móvil (React Native + Expo)      │
└────────────┬─────────────────────────────────────┬───────────────┘
             │                                     │
             │  HTTP + WebSocket                   │
             │ (JWT en Headers)                    │
             │                                     │
    ┌────────▼──────────────────────────────────────▼────────────┐
    │              API REST + WebSocket                          │
    │           (Node.js + Express)                              │
    │                                                            │
    │  ├─ Autenticación (JWT)                                    │
    │  ├─ CRUD de Usuarios, Trabajos, Categorías                 │
    │  ├─ Geolocalización ($near)                                │
    │  ├─ Notificaciones (Socket.io)                             │
    │  └─ Validación de datos                                    │
    └────────┬────────────────────────────────────────┬──────────┘
             │                                        │
             │ Driver MongoDB                         │
             │ (Mongoose ORM)                         │
             │                                        │
    ┌────────▼────────────────────────────────────────▼──────────┐
    │                   MongoDB                                  │
    │                                                            │
    │  Colecciones:                                              │
    │  ├─ Users       (roles: CLIENTE, TECNICO, ADMIN)           │
    │  ├─ Jobs        (tickets de solicitud)                     │
    │  ├─ Categories  (tipos de servicios)                       │
    │  └─ Ratings     (calificaciones)                           │
    │                                                            │
    │  Índices:                                                  │
    │  ├─ location (2dsphere) para búsquedas cercanas            │
    │  ├─ email (unique) para autenticación                      │
    │  └─ status (para filtros rápidos)                          │
    └────────────────────────────────────────────────────────────┘
```

---

## 🔄 Flujo de Datos

### Caso de Uso: Cliente solicita servicio

```
1. CLIENTE abre app web/móvil
   └─ Inicia sesión (JWT)
   
2. CLIENTE ingresa ubicación + categoría de servicio
   └─ Envía POST /api/jobs al backend
   
3. BACKEND recibe solicitud
   ├─ Valida JWT
   ├─ Valida datos
   └─ Crea Job en MongoDB
   
4. BACKEND emite evento WebSocket
   └─ Socket.io: "NEW_JOB" a todos los TÉCNICOS en la zona
   
5. TÉCNICOS reciben notificación
   ├─ Ven la solicitud en tiempo real
   └─ TÉCNICO acepta: PUT /api/jobs/:id/status
   
6. BACKEND actualiza Job
   └─ Emite evento: "JOB_ACCEPTED"
   
7. CLIENTE recibe confirmación
   ├─ Sabe que un técnico aceptó
   └─ Puede ver datos del técnico (nombre, calificación)
   
8. TÉCNICO se traslada (actualiza GPS)
   └─ PUT /api/users/me/location cada X segundos
   
9. CLIENTE ve técnico acercándose en mapa
   └─ Socket.io actualiza ubicación en tiempo real
   
10. TÉCNICO llega y completa trabajo
    └─ PUT /api/jobs/:id/status = COMPLETADO
    
11. CLIENTE califica trabajo
    └─ POST /api/ratings
```

---

## 📦 Estructura Backend (Node.js + Express)

### Carpeta `src/models/`

Define esquemas Mongoose para cada colección MongoDB.

```typescript
// User.ts
interface IUser extends Document {
  nombre: string;
  email: string;
  password: string; // hasheada con bcryptjs
  role: 'CLIENTE' | 'TECNICO' | 'ADMIN';
  oficio?: string;              // Para TÉCNICOS
  estado?: 'disponible' | 'ocupado';
  location?: {
    type: 'Point';
    coordinates: [number, number]; // [lon, lat]
  };
  createdAt: Date;
  updatedAt: Date;
}

// Job.ts
interface IJob extends Document {
  cliente_id: ObjectId;         // Ref a User
  tecnico_id?: ObjectId;        // Ref a User (nulo si no asignado)
  descripcion: string;
  categoria_id: ObjectId;       // Ref a Category
  ubicacion: {
    type: 'Point';
    coordinates: [number, number];
  };
  status: 'PENDIENTE' | 'ACEPTADO' | 'EN_CAMINO' | 'COMPLETADO' | 'CANCELADO';
  presupuesto?: number;
  createdAt: Date;
  updatedAt: Date;
}

// Category.ts
interface ICategory extends Document {
  nombre: string;
  icono_url: string;
  descripcion: string;
}
```

### Carpeta `src/controllers/`

Contiene la lógica de las rutas. Cada controlador maneja un recurso.

```typescript
// authController.ts
export const register = async (req: Request, res: Response) => {
  // 1. Validar datos
  // 2. Verificar que email no exista
  // 3. Hashear contraseña
  // 4. Crear usuario en BD
  // 5. Generar JWT
  // 6. Retornar token
};

export const login = async (req: Request, res: Response) => {
  // 1. Validar email/contraseña
  // 2. Buscar usuario
  // 3. Comparar contraseña
  // 4. Generar JWT
  // 5. Retornar token
};

// jobController.ts
export const createJob = async (req: Request, res: Response) => {
  // 1. Validar JWT (del middleware)
  // 2. Validar datos del job
  // 3. Crear job en BD
  // 4. Emitir evento Socket.io
  // 5. Retornar job creado
};

export const getNearbyJobs = async (req: Request, res: Response) => {
  // 1. Obtener ubicación del técnico
  // 2. Buscar jobs con $near (geolocalización)
  // 3. Retornar jobs cercanos ordenados por distancia
};
```

### Carpeta `src/routes/`

Define los endpoints de la API.

```typescript
// auth.ts
router.post('/register', authController.register);
router.post('/login', authController.login);
router.post('/refresh', authController.refreshToken);

// jobs.ts
router.get('/', authMiddleware, jobController.getJobs);
router.get('/nearby', authMiddleware, jobController.getNearbyJobs);
router.post('/', authMiddleware, jobController.createJob);
router.put('/:id/status', authMiddleware, jobController.updateJobStatus);

// users.ts
router.get('/me', authMiddleware, userController.getProfile);
router.put('/me/location', authMiddleware, userController.updateLocation);
router.get('/:id/ratings', userController.getRatings);
```

### Carpeta `src/middleware/`

Middleware personalizado para validación, autenticación, etc.

```typescript
// auth.ts
export const authMiddleware = (req, res, next) => {
  const token = req.headers.authorization?.split(' ')[1];
  if (!token) return res.status(401).json({ error: 'No token' });
  
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded;
    next();
  } catch (err) {
    res.status(401).json({ error: 'Invalid token' });
  }
};

// errorHandler.ts
export const errorHandler = (err, req, res, next) => {
  console.error(err);
  res.status(err.status || 500).json({
    error: err.message || 'Internal server error'
  });
};
```

### Carpeta `src/services/`

Lógica de negocio compleja, separada de controladores.

```typescript
// authService.ts
export const registerUser = async (userData) => {
  // Validar datos
  // Hashear contraseña
  // Crear usuario
  // Retornar usuario (sin contraseña)
};

// jobService.ts
export const findNearbyJobs = async (coords, radius = 5000) => {
  // Búsqueda con $near de MongoDB
  // Retorna trabajos dentro del radio
};

// notificationService.ts
export const notifyTechniciansNearby = async (jobData, io) => {
  // Emitir evento Socket.io a técnicos en la zona
};
```

### Carpeta `src/config/`

Configuración centralizada.

```typescript
// database.ts
export const connectDB = async () => {
  await mongoose.connect(process.env.MONGODB_URI);
};

// jwt.ts
export const generateToken = (userId: string) => {
  return jwt.sign({ userId }, process.env.JWT_SECRET, {
    expiresIn: process.env.JWT_EXPIRE
  });
};
```

---

## 🌐 Frontend Web (React)

### Estructura de Carpetas

```
frontend-web/src/
├── components/
│   ├── Header.tsx        # Encabezado
│   ├── JobCard.tsx       # Card individual de trabajo
│   └── Map.tsx           # Mapa con ubicaciones
│
├── pages/
│   ├── Dashboard.tsx     # Panel principal
│   ├── JobDetails.tsx    # Detalle de trabajo
│   └── UserProfile.tsx   # Perfil del usuario
│
├── hooks/
│   ├── useJobs.ts        # Lógica para obtener jobs
│   ├── useAuth.ts        # Manejo de autenticación
│   └── useSocket.ts      # Conexión a WebSocket
│
├── services/
│   ├── api.ts            # Configuración axios
│   ├── authService.ts    # Llamadas de auth
│   └── jobService.ts     # Llamadas de jobs
│
├── context/
│   ├── AuthContext.tsx   # Estado global de autenticación
│   └── JobContext.tsx    # Estado global de jobs
│
├── types/
│   ├── User.ts
│   ├── Job.ts
│   └── common.ts
│
└── App.tsx
```

### Flujo de Datos en React

```
App.tsx
├─ AuthContext Provider
│  └─ State: { user, isLoading, error }
│
├─ Router (React Router)
│  ├─ ProtectedRoute
│  │  ├─ Dashboard
│  │  ├─ JobDetails
│  │  └─ UserProfile
│  │
│  └─ PublicRoute
│     ├─ Login
│     └─ Register

Componentes
├─ Usan AuthContext para verificar login
├─ Llaman jobService.getJobs() para datos
├─ Se suscriben a Socket.io para actualizaciones
└─ Actualizan estado local con useState/useReducer
```

---

## 📱 Frontend Móvil (React Native + Expo)

### Estructura Similar a Web

```
frontend-mobile/src/
├── screens/           # En lugar de "pages"
│   ├── LoginScreen.tsx
│   ├── JobListScreen.tsx
│   └── JobDetailScreen.tsx
│
├── components/
│   ├── JobCard.tsx
│   ├── TechnicianLocation.tsx
│   └── NotificationBanner.tsx
│
├── hooks/
│   ├── useAuth.ts
│   ├── useLocation.ts      # Acceso a GPS
│   └── useNotifications.ts
│
├── services/
│   ├── api.ts
│   ├── geoService.ts       # Servicios de geolocalización
│   └── pushNotifications.ts
│
└── App.tsx
```

### Servicios Específicos de Mobile

```typescript
// geoService.ts
export const startLocationTracking = () => {
  // Usa react-native-geolocation-service
  // Actualiza ubicación cada X segundos
  // Envía PUT /api/users/me/location
};

// pushNotifications.ts
export const setupPushNotifications = () => {
  // Usa Expo Notifications
  // Recibe pushes del backend
  // Abre screen correspondiente
};
```

---

## 🔌 WebSocket (Socket.io)

### Eventos Principales

#### Cliente → Servidor

```typescript
// Técnico se conecta
socket.emit('TECH_CONNECTED', { userId, location });

// Técnico acepta un trabajo
socket.emit('JOB_ACCEPTED', { jobId });

// Técnico actualiza ubicación
socket.emit('LOCATION_UPDATE', { coords });

// Técnico completa trabajo
socket.emit('JOB_COMPLETED', { jobId });
```

#### Servidor → Cliente

```typescript
// Nuevos trabajos disponibles (a técnicos cercanos)
socket.on('NEW_JOB_NEARBY', (jobData) => {});

// Técnico aceptó el trabajo
socket.on('JOB_ACCEPTED', (technicianData) => {});

// Técnico está en camino (actualización de ubicación)
socket.on('TECH_LOCATION_UPDATE', (coords) => {});

// Trabajo completado
socket.on('JOB_COMPLETED', () => {});
```

### Implementación en Backend

```typescript
// index.ts (Main server)
import { Server } from 'socket.io';

const io = new Server(app, {
  cors: { origin: process.env.CORS_ORIGIN }
});

io.on('connection', (socket) => {
  console.log('Client connected:', socket.id);
  
  socket.on('TECH_CONNECTED', (data) => {
    // Agregar técnico a "sala" geográfica
    socket.join(`geo:${data.region}`);
  });
  
  socket.on('JOB_ACCEPTED', async (data) => {
    // Actualizar BD
    // Notificar al cliente
    io.to(`job:${data.jobId}`).emit('JOB_ACCEPTED', {
      technicianName: data.name
    });
  });
});
```

---

## 🔐 Seguridad

### JWT (JSON Web Tokens)

```
Header: Authorization: Bearer <token>

Token contiene:
{
  userId: "65a9c8b7d4e2f9a1b2c3d4e5",
  iat: 1705398247,
  exp: 1705484647  // 24 horas
}

Verificado en cada endpoint protegido
```

### Contraseñas

```typescript
// Registro
const hashedPassword = await bcryptjs.hash(password, 10);

// Login
const isValid = await bcryptjs.compare(password, user.password);
```

### CORS

```typescript
app.use(cors({
  origin: process.env.CORS_ORIGIN,
  credentials: true
}));
```

---

## 🗄️ MongoDB y Geolocalización

### Índice Geoespacial

```typescript
// En modelo User.ts
userSchema.index({ location: '2dsphere' });

// En modelo Job.ts
jobSchema.index({ ubicacion: '2dsphere' });
```

### Búsqueda de Puntos Cercanos

```typescript
// Encontrar técnicos a 5km de un punto
db.users.find({
  location: {
    $near: {
      $geometry: {
        type: "Point",
        coordinates: [-73.9857, 40.7484]
      },
      $maxDistance: 5000  // 5km en metros
    }
  }
});
```

---

## 📊 Patrones de Diseño Utilizados

### MVC (Model-View-Controller)

```
Models    → Esquemas Mongoose
Views     → React Components
Controllers → Express Controllers
```

### Separación de Concernimientos

```
Models       → Definen estructura
Services     → Lógica de negocio
Controllers  → Orquestar servicios
Routes       → Mapear URLs
Middleware   → Validaciones
```

### Inversión de Dependencias

```typescript
// Las funciones reciben dependencias como parámetros
export const createJobController = (jobService, io) => {
  return async (req, res) => {
    const job = await jobService.create(req.body);
    io.emit('NEW_JOB', job);
    res.json(job);
  };
};
```

---

## 🚀 Despliegue (Futuro)

```
Backend:  Node.js en Heroku / Railway / DigitalOcean
Frontend: React en Vercel / Netlify
Mobile:   Expo EAS Build para App Store / Play Store
BD:       MongoDB Atlas (cloud)
```

---

## 📝 Variables de Entorno Backend

```env
# Database
MONGODB_URI=mongodb://localhost:27017/fixithub

# Authentication
JWT_SECRET=your-super-secret-key-min-32-chars
JWT_EXPIRE=7d

# CORS
CORS_ORIGIN=http://localhost:3000

# Server
PORT=5000
NODE_ENV=development

# Socket.io
SOCKET_IO_CORS=http://localhost:3000
```
