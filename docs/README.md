# 📚 Documentación de FixitHub

Este directorio contiene toda la documentación técnica y de referencia del proyecto FixitHub.

---

## 📑 Documentos Disponibles

### 🏗️ [ARCHITECTURE.md](./ARCHITECTURE.md)
Descripción completa de la arquitectura técnica del sistema.

**Incluye**:
- Diagrama general de arquitectura
- Flujo de datos completo
- Estructura de backend (Node.js + Express)
- Estructura de frontend (React y React Native)
- Implementación de WebSockets
- Patrones de diseño
- Modelo de datos MongoDB

**Lee esto si quieres**: Entender cómo funciona todo el sistema.

---

### ⚙️ [SETUP.md](./SETUP.md)
Guía paso a paso para configurar el entorno de desarrollo local.

**Incluye**:
- Pre-requisitos a instalar
- Configuración de Docker
- Instalación de backend, web y móvil
- Verificación de que todo funciona
- Solución de problemas comunes
- Comandos útiles para desarrollo
- Estructura de carpetas post-instalación

**Lee esto si quieres**: Poner a andar FixitHub en tu máquina.

---

### 📡 [API.md](./API.md)
Documentación completa de todos los endpoints REST.

**Incluye**:
- Autenticación y JWT
- Endpoints de usuarios
- Endpoints de trabajos/tickets
- Endpoints de calificaciones
- Endpoints de categorías
- Códigos de error comunes
- WebSocket events
- Ejemplos de flujos completos

**Lee esto si quieres**: Entender cómo consumir la API desde frontend.

---

### 🔐 [BRANCH_PROTECTION.md](./BRANCH_PROTECTION.md)
Guía para configurar protecciones de ramas en GitHub.

**Incluye**:
- Cómo proteger `main` y `develop`
- Reglas de merge automático
- CI/CD con GitHub Actions

**Lee esto si quieres**: Mantener la calidad del código en GitHub.

---

## 🔍 Documentación en el Raíz

### 📖 [README.md](../README.md)
Descripción general del proyecto, stack tecnológico e instrucciones rápidas.

**Lee esto primero** si es tu primera vez en el proyecto.

---

### 📋 [CONTRIBUTING.md](../CONTRIBUTING.md)
Guía completa de contribución, estructura de código y buenas prácticas.

**Lee esto si quieres**: Contribuir al proyecto siguiendo los estándares del equipo.

---

## 🎯 Guía Rápida por Rol

### 👨‍💻 Si Eres Developer Backend

1. Lee [README.md](../README.md) - Visión general
2. Lee [SETUP.md](./SETUP.md) - Configura tu entorno
3. Lee [ARCHITECTURE.md](./ARCHITECTURE.md) - Entiende la estructura
4. Lee [API.md](./API.md) - Referencia de endpoints
5. Lee [CONTRIBUTING.md](../CONTRIBUTING.md) - Convenciones de código

---

### 🎨 Si Eres Developer Frontend (Web)

1. Lee [README.md](../README.md) - Visión general
2. Lee [SETUP.md](./SETUP.md) - Configura tu entorno
3. Lee [ARCHITECTURE.md](./ARCHITECTURE.md#-frontend-web-react) - Estructura web
4. Lee [API.md](./API.md) - Cómo consumir endpoints
5. Lee [CONTRIBUTING.md](../CONTRIBUTING.md) - Convenciones de código

---

### 📱 Si Eres Developer Mobile (React Native)

1. Lee [README.md](../README.md) - Visión general
2. Lee [SETUP.md](./SETUP.md) - Configura tu entorno
3. Lee [ARCHITECTURE.md](./ARCHITECTURE.md#-frontend-móvil-react-native--expo) - Estructura móvil
4. Lee [API.md](./API.md) - Cómo consumir endpoints
5. Lee [CONTRIBUTING.md](../CONTRIBUTING.md) - Convenciones de código

---

### 🔧 Si Eres Tech Lead

1. Lee todo en orden (README → SETUP → ARCHITECTURE → API)
2. Configura [BRANCH_PROTECTION.md](./BRANCH_PROTECTION.md)
3. Organiza las tareas en GitHub Projects
4. Revisa PRs usando [CONTRIBUTING.md](../CONTRIBUTING.md)

---

## 📞 Preguntas Frecuentes

**P: ¿Cómo empiezo con FixitHub?**
R: Lee [README.md](../README.md) y [SETUP.md](./SETUP.md)

**P: ¿Cuál es la estructura del código?**
R: Lee [ARCHITECTURE.md](./ARCHITECTURE.md)

**P: ¿Cómo consumo la API?**
R: Lee [API.md](./API.md)

**P: ¿Cómo contribuyo?**
R: Lee [CONTRIBUTING.md](../CONTRIBUTING.md)

**P: Mi máquina no conecta a MongoDB**
R: Ve a [SETUP.md - Solución de Problemas](./SETUP.md#-solución-de-problemas-comunes)

---

## 🔗 Enlaces Útiles

- [GitHub Repo](https://github.com/TerrazasJr316/FixitHub)
- [Gemini Conversation](https://gemini.google.com/share/bb639b415c59) - Especificaciones iniciales
- [TypeScript Docs](https://www.typescriptlang.org/docs/)
- [React Docs](https://react.dev/)
- [Mongoose Docs](https://mongoosejs.com/)
- [Express Docs](https://expressjs.com/)

---

## 📝 Convenciones de Este Proyecto

- **Lenguaje**: English para código, español para docs
- **Commits**: Conventional Commits
- **TypeScript**: Strict mode activado
- **Branches**: Git Flow Simplificado (main, develop, feature/*, fix/*)
- **Ramas**: Protegidas con reglas de merge

---

## 🚀 Timeline del Proyecto

| Semana | Período | Documentación |
|--------|---------|---------------|
| 1 | 5-11 mayo | SETUP + Configuración |
| 2 | 12-18 mayo | ARCHITECTURE + API finalizados |
| 3 | 19-25 mayo | Todos los docs completos |
| 4 | 26-10 junio | En mantenimiento durante entrega |

<div align="center">

### Preguntas? Consulta con el Tech Lead 🚀

</div>
