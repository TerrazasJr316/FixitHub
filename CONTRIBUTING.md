# Guía de Contribución - Proyecto Marketplace (Servicios Locales)

¡Bienvenido al repositorio del proyecto! Para asegurar que el desarrollo sea ágil, evitar conflictos de código y llegar a nuestra fecha de entrega con un sistema estable, todos los miembros del equipo deben seguir estas reglas de contribución.

Nuestra arquitectura utiliza un **Monorepositorio** que aloja microservicios en el backend (Node.js/JavaScript) y las aplicaciones frontend (React y React Native).

## 📂 Estructura del Monorepositorio

Todo el código vive en este único repositorio, organizado en tres áreas principales:

```text
/
├── apps/                  # Aplicaciones cliente (Frontend)
│   ├── web/               # Panel admin y web (React)
│   └── mobile/            # App cliente y técnico (React Native)
│
├── services/              # Microservicios del Backend (Node.js / Express)
│   ├── auth-service/      # Manejo de usuarios, login y JWT
│   ├── jobs-service/      # Gestión de tickets, solicitudes y geolocalización
│   └── notify-service/    # WebSockets y notificaciones push
│
└── packages/              # Código compartido (Librerías internas)
    ├── database/          # Configuración de Mongoose y modelos compartidos
    └── config/            # Configuraciones comunes (ESLint, Prettier, etc.)
```

## 🌿 Estrategia de Ramas (Branching)

Utilizamos un flujo de trabajo simplificado enfocado en la integración continua. **No se permiten commits directos a `main` o `develop`.**

*   **`main`**: Código de producción. 100% estable.
*   **`develop`**: Entorno principal de integración. Todas las nuevas características se fusionan aquí primero.
*   **Ramas de trabajo**: Deben crearse siempre a partir de `develop` siguiendo esta nomenclatura:
    *   `feature/<área>-<descripción>`: Para nueva funcionalidad.
    *   `fix/<área>-<descripción>`: Para corrección de errores.
    *   `docs/<descripción>`: Para cambios en documentación.

**Ejemplos válidos:**
*   `feature/auth-login-endpoint`
*   `feature/mobile-map-view`
*   `fix/jobs-gps-calculation`

---

## 💬 Convención de Commits

Utilizamos [Conventional Commits](https://www.conventionalcommits.org/). Esto nos ayuda a entender el historial de un vistazo y aislar qué microservicio se modificó.

El formato debe ser: `<tipo>(<alcance>): <descripción corta>`

**Tipos permitidos:**
*   `feat`: Una nueva característica.
*   `fix`: Solución a un bug.
*   `refactor`: Cambios de código que no corrigen bugs ni añaden características.
*   `chore`: Tareas de mantenimiento, actualización de dependencias, etc.

**Alcances (Scopes) sugeridos:** `auth`, `jobs`, `notify`, `web`, `mobile`, `db`.

**Ejemplos válidos:**
*   `feat(auth): implementar generación de token JWT`
*   `fix(mobile): corregir desbordamiento de UI en pantalla de registro`
*   `chore(db): actualizar versión de mongoose`

---

## 🚀 Flujo de Trabajo Diario

1.  **Sincronizar:** Asegúrate de estar en la rama base actualizada.
    ```bash
    git checkout develop
    git pull origin develop
    ```
2.  **Crear rama:**
    ```bash
    git checkout -b feature/tu-nueva-funcionalidad
3. **Desarrollar:** Haz commits pequeños y atómicos siguiendo la convención mencionada.
4. **Subir cambios:**
    ```bash
    git push origin feature/tu-nueva-funcionalidad
    ```

5.  **Abrir un Pull Request (PR):** Ve a GitHub y abre un PR apuntando hacia la rama `develop`.

---

## ✅ Reglas para Pull Requests (Checklist)

Antes de solicitar la revisión de un PR, asegúrate de cumplir con lo siguiente:

- [ ] El PR tiene un título claro y descriptivo.
- [ ] Mi código sigue el estilo del proyecto (Prettier/ESLint pasados).
- [ ] Si creé un nuevo microservicio, añadí su respectivo archivo `.env.example` y actualicé el `README.md` principal o el `docker-compose.yml`.
- [ ] Probé los cambios localmente y no rompen la comunicación con otros microservicios.
- [ ] No estoy subiendo contraseñas, tokens reales ni la carpeta `node_modules/`.

*Nota: Todo PR requiere la aprobación de al menos otro miembro del equipo antes de hacer merge a `develop`.*

---

## 🛠️ Buenas Prácticas para Microservicios en JS

1.  **Aislamiento:** Un microservicio no debe acceder directamente a la base de datos de otro microservicio. Si `jobs-service` necesita datos de usuarios, debe comunicarse con `auth-service` vía HTTP o a través de los modelos compartidos en `/packages/database`.
2.  **Manejo de Errores:** Todos los servicios deben devolver las respuestas con la misma estructura JSON para facilitar el trabajo del Frontend.
3.  **Variables de Entorno:** Nunca quemar URLs en el código. Usar siempre `process.env.SERVICE_URL`.

¡Mucho éxito con el desarrollo!