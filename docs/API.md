# 📡 Documentación de API - FixitHub

> Referencia completa de todos los endpoints REST de FixitHub.

---

## 🔐 Autenticación

Todos los endpoints protegidos requieren un JWT en el header:

```
Authorization: Bearer <tu-token-jwt>
```

### POST `/api/auth/register`

Registrar un nuevo usuario.

**Request Body**:
```json
{
  "nombre": "Juan Pérez",
  "email": "juan@example.com",
  "password": "securePassword123",
  "role": "CLIENTE"
}
```

**Roles válidos**: `CLIENTE`, `TECNICO`, `ADMIN`

**Response** (201 Created):
```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": "65a9c8b7d4e2f9a1b2c3d4e5",
    "nombre": "Juan Pérez",
    "email": "juan@example.com",
    "role": "CLIENTE"
  }
}
```

**Errores**:
- `400`: Email ya registrado
- `400`: Campos requeridos faltantes

---

### POST `/api/auth/login`

Iniciar sesión.

**Request Body**:
```json
{
  "email": "juan@example.com",
  "password": "securePassword123"
}
```

**Response** (200 OK):
```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": "65a9c8b7d4e2f9a1b2c3d4e5",
    "nombre": "Juan Pérez",
    "email": "juan@example.com",
    "role": "CLIENTE"
  }
}
```

**Errores**:
- `401`: Email o contraseña incorrectos
- `404`: Usuario no encontrado

---

### POST `/api/auth/refresh`

Refrescar token JWT (obtener uno nuevo).

**Request Body**:
```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

**Response** (200 OK):
```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

---

## 👤 Usuarios

### GET `/api/users/me`

Obtener perfil del usuario autenticado.

**Headers**: Requiere JWT

**Response** (200 OK):
```json
{
  "id": "65a9c8b7d4e2f9a1b2c3d4e5",
  "nombre": "Juan Pérez",
  "email": "juan@example.com",
  "role": "CLIENTE",
  "createdAt": "2026-05-01T10:00:00Z",
  "updatedAt": "2026-05-01T10:00:00Z"
}
```

---

### GET `/api/users/:id`

Obtener perfil de otro usuario (público, sin JWT si está permitido).

**Response** (200 OK):
```json
{
  "id": "65a9c8b7d4e2f9a1b2c3d4e5",
  "nombre": "Juan Pérez",
  "role": "CLIENTE",
  "rating": 4.8,
  "completedJobs": 12
}
```

---

### PUT `/api/users/me/location`

Actualizar ubicación GPS del usuario (usado por técnicos).

**Headers**: Requiere JWT

**Request Body**:
```json
{
  "latitude": 40.7128,
  "longitude": -74.0060,
  "estado": "disponible"
}
```

**Response** (200 OK):
```json
{
  "success": true,
  "message": "Ubicación actualizada",
  "location": {
    "type": "Point",
    "coordinates": [-74.0060, 40.7128]
  }
}
```

---

### GET `/api/users/:id/ratings`

Obtener calificaciones de un usuario.

**Response** (200 OK):
```json
{
  "userId": "65a9c8b7d4e2f9a1b2c3d4e5",
  "averageRating": 4.8,
  "totalRatings": 12,
  "ratings": [
    {
      "jobId": "66a9c8b7d4e2f9a1b2c3d4e6",
      "rating": 5,
      "comment": "Excelente servicio, muy profesional",
      "ratedBy": "Cliente XYZ"
    }
  ]
}
```

---

## 📋 Trabajos/Tickets

### GET `/api/jobs`

Listar todos los trabajos (con filtros opcionales).

**Query Parameters**:
- `status`: `PENDIENTE`, `ACEPTADO`, `EN_CAMINO`, `COMPLETADO`, `CANCELADO`
- `page`: Número de página (default: 1)
- `limit`: Trabajos por página (default: 10)

**Response** (200 OK):
```json
{
  "jobs": [
    {
      "id": "66a9c8b7d4e2f9a1b2c3d4e6",
      "cliente_id": "65a9c8b7d4e2f9a1b2c3d4e5",
      "tecnico_id": null,
      "descripcion": "Se rompió la llave de agua en la cocina",
      "categoria": "Plomería",
      "ubicacion": {
        "type": "Point",
        "coordinates": [-74.0060, 40.7128]
      },
      "status": "PENDIENTE",
      "presupuesto": 150,
      "createdAt": "2026-05-10T14:30:00Z"
    }
  ],
  "total": 45,
  "page": 1,
  "pages": 5
}
```

---

### GET `/api/jobs/nearby`

Obtener trabajos cercanos a la ubicación del técnico.

**Headers**: Requiere JWT (usuario debe ser TECNICO)

**Query Parameters**:
- `radius`: Radio en metros (default: 5000)
- `limit`: Máximo de resultados (default: 20)

**Response** (200 OK):
```json
{
  "jobs": [
    {
      "id": "66a9c8b7d4e2f9a1b2c3d4e6",
      "descripcion": "Reparar aire acondicionado",
      "categoria": "Electricidad",
      "ubicacion": {
        "type": "Point",
        "coordinates": [-74.0060, 40.7128]
      },
      "status": "PENDIENTE",
      "distancia": 1200,
      "cliente": {
        "nombre": "María López",
        "rating": 4.9
      },
      "presupuesto": 200
    }
  ]
}
```

---

### POST `/api/jobs`

Crear una nueva solicitud de trabajo.

**Headers**: Requiere JWT (usuario debe ser CLIENTE)

**Request Body**:
```json
{
  "descripcion": "Se rompió la llave de agua en la cocina",
  "categoria_id": "65a9c8b7d4e2f9a1b2c3d4aa",
  "ubicacion": {
    "latitude": 40.7128,
    "longitude": -74.0060
  },
  "presupuesto": 150
}
```

**Response** (201 Created):
```json
{
  "id": "66a9c8b7d4e2f9a1b2c3d4e6",
  "cliente_id": "65a9c8b7d4e2f9a1b2c3d4e5",
  "descripcion": "Se rompió la llave de agua en la cocina",
  "categoria_id": "65a9c8b7d4e2f9a1b2c3d4aa",
  "ubicacion": {
    "type": "Point",
    "coordinates": [-74.0060, 40.7128]
  },
  "status": "PENDIENTE",
  "presupuesto": 150,
  "createdAt": "2026-05-10T14:35:22Z"
}
```

**Errores**:
- `400`: Datos incompletos o inválidos
- `401`: No autenticado

---

### GET `/api/jobs/:id`

Obtener detalles de un trabajo específico.

**Response** (200 OK):
```json
{
  "id": "66a9c8b7d4e2f9a1b2c3d4e6",
  "cliente_id": "65a9c8b7d4e2f9a1b2c3d4e5",
  "cliente": {
    "nombre": "Juan Pérez",
    "email": "juan@example.com",
    "rating": 4.8,
    "phone": "+1234567890"
  },
  "tecnico_id": "65a9c8b7d4e2f9a1b2c3d5e5",
  "tecnico": {
    "nombre": "Carlos González",
    "rating": 4.9,
    "especialidad": "Plomería"
  },
  "descripcion": "Se rompió la llave de agua en la cocina",
  "categoria": "Plomería",
  "ubicacion": {
    "type": "Point",
    "coordinates": [-74.0060, 40.7128],
    "address": "123 Main St, New York, NY"
  },
  "status": "EN_CAMINO",
  "presupuesto": 150,
  "timelineUpdates": [
    {
      "status": "PENDIENTE",
      "timestamp": "2026-05-10T14:35:22Z"
    },
    {
      "status": "ACEPTADO",
      "timestamp": "2026-05-10T14:45:00Z",
      "message": "Carlos ha aceptado tu solicitud"
    }
  ],
  "createdAt": "2026-05-10T14:35:22Z"
}
```

---

### PUT `/api/jobs/:id/status`

Actualizar el estado de un trabajo.

**Headers**: Requiere JWT (debe ser el técnico asignado)

**Request Body**:
```json
{
  "status": "EN_CAMINO",
  "notes": "Llegando en 10 minutos"
}
```

**Estados válidos**:
- `PENDIENTE` → `ACEPTADO`
- `ACEPTADO` → `EN_CAMINO`
- `EN_CAMINO` → `COMPLETADO`
- Cualquiera → `CANCELADO`

**Response** (200 OK):
```json
{
  "id": "66a9c8b7d4e2f9a1b2c3d4e6",
  "status": "EN_CAMINO",
  "notes": "Llegando en 10 minutos",
  "updatedAt": "2026-05-10T14:50:30Z"
}
```

**Errores**:
- `403`: Solo el técnico asignado puede actualizar
- `400`: Transición de estado no válida

---

### DELETE `/api/jobs/:id`

Cancelar un trabajo (solo cliente o admin).

**Headers**: Requiere JWT

**Request Body**:
```json
{
  "reason": "Encontré otro técnico"
}
```

**Response** (200 OK):
```json
{
  "success": true,
  "message": "Trabajo cancelado",
  "id": "66a9c8b7d4e2f9a1b2c3d4e6"
}
```

---

## ⭐ Calificaciones

### POST `/api/ratings`

Crear una calificación después de completar un trabajo.

**Headers**: Requiere JWT (debe ser cliente del trabajo)

**Request Body**:
```json
{
  "job_id": "66a9c8b7d4e2f9a1b2c3d4e6",
  "rating": 5,
  "comment": "Excelente trabajo, muy profesional",
  "rated_user_id": "65a9c8b7d4e2f9a1b2c3d5e5"
}
```

**Rating válido**: 1-5

**Response** (201 Created):
```json
{
  "id": "66a9c8b7d4e2f9a1b2c3d4e7",
  "job_id": "66a9c8b7d4e2f9a1b2c3d4e6",
  "from_user_id": "65a9c8b7d4e2f9a1b2c3d4e5",
  "to_user_id": "65a9c8b7d4e2f9a1b2c3d5e5",
  "rating": 5,
  "comment": "Excelente trabajo, muy profesional",
  "createdAt": "2026-05-10T16:00:00Z"
}
```

---

## 📂 Categorías

### GET `/api/categories`

Listar todas las categorías de servicios.

**Response** (200 OK):
```json
{
  "categories": [
    {
      "id": "65a9c8b7d4e2f9a1b2c3d4aa",
      "nombre": "Plomería",
      "icono_url": "https://...",
      "descripcion": "Servicios de reparación y mantenimiento de tuberías"
    },
    {
      "id": "65a9c8b7d4e2f9a1b2c3d4ab",
      "nombre": "Electricidad",
      "icono_url": "https://...",
      "descripcion": "Reparaciones eléctricas y instalación de sistemas"
    }
  ]
}
```

---

## ⚠️ Códigos de Error Comunes

### 400 Bad Request
```json
{
  "error": "Email already registered",
  "code": "EMAIL_ALREADY_EXISTS"
}
```

### 401 Unauthorized
```json
{
  "error": "Invalid or expired token",
  "code": "INVALID_TOKEN"
}
```

### 403 Forbidden
```json
{
  "error": "You don't have permission for this action",
  "code": "FORBIDDEN"
}
```

### 404 Not Found
```json
{
  "error": "Job not found",
  "code": "JOB_NOT_FOUND"
}
```

### 500 Internal Server Error
```json
{
  "error": "Internal server error",
  "code": "INTERNAL_ERROR"
}
```

---

## 🔄 WebSocket Events (Real-time)

### Cliente → Servidor

```typescript
socket.emit('NEW_JOB_CREATED', {
  jobId: '66a9c8b7d4e2f9a1b2c3d4e6',
  location: { lat: 40.7128, lng: -74.0060 }
});

socket.emit('JOB_ACCEPTED', {
  jobId: '66a9c8b7d4e2f9a1b2c3d4e6',
  technicianId: '65a9c8b7d4e2f9a1b2c3d5e5'
});

socket.emit('LOCATION_UPDATE', {
  userId: '65a9c8b7d4e2f9a1b2c3d5e5',
  location: { lat: 40.7150, lng: -74.0050 }
});

socket.emit('JOB_COMPLETED', {
  jobId: '66a9c8b7d4e2f9a1b2c3d4e6'
});
```

### Servidor → Cliente

```typescript
socket.on('NEW_JOB_NEARBY', (data) => {
  // Nuevo trabajo disponible cerca
  console.log(data);
});

socket.on('JOB_ACCEPTED', (data) => {
  // Un técnico aceptó tu trabajo
  console.log(data);
});

socket.on('TECH_LOCATION_UPDATE', (data) => {
  // Ubicación del técnico actualizada
  console.log(data);
});

socket.on('JOB_COMPLETED', (data) => {
  // El trabajo fue completado
  console.log(data);
});

socket.on('NOTIFICATION', (data) => {
  // Notificación general
  console.log(data);
});
```

---

## 📊 Ejemplos de Flujo Completo

### Caso: Cliente solicita servicio

```
1. POST /api/auth/login
   ↓ (obtiene token)

2. POST /api/jobs
   Body: { descripcion, categoria_id, ubicacion, presupuesto }
   ↓ (crea job en BD)

3. WebSocket: "NEW_JOB_NEARBY" emitido a técnicos cercanos
   ↓

4. GET /api/jobs/nearby (técnico busca trabajos)
   ↓ (ve el nuevo job)

5. PUT /api/jobs/:id/status
   Body: { status: "ACEPTADO" }
   ↓ (técnico acepta)

6. WebSocket: "JOB_ACCEPTED" enviado al cliente
   ↓ (cliente ve que fue aceptado)

7. PUT /api/users/me/location (técnico actualiza GPS)
   ↓ (cada 10 segundos aprox.)

8. WebSocket: "TECH_LOCATION_UPDATE" al cliente
   ↓ (cliente ve técnico acercándose)

9. PUT /api/jobs/:id/status
   Body: { status: "COMPLETADO" }
   ↓ (técnico completa)

10. POST /api/ratings
    Body: { rating: 5, comment: "..." }
    ↓ (cliente califica)
```
