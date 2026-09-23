# 🏛️ Modular Backend Template
### Clean Architecture & Domain-Driven Design (DDD) Starter Template

> **Plantilla base de arquitectura modular lista para clonar y comenzar a programar.**  
> Diseñada para eliminar el código acoplado, facilitar la escalabilidad y permitir que el equipo de desarrollo se enfoque inmediatamente en la lógica de negocio sin perder tiempo en configuraciones repetitivas ni tener que limpiar código ficticio de ejemplo.

---

## 🧼 ¿Por qué los archivos de código están completamente vacíos (0 bytes)?

Esta plantilla está concebida como un **andamio estructural puro (Clean Scaffold)**:
- **Cero tiempo perdido eliminando código ajeno**: No incluye datos falsos, clases dummy ni implementaciones de prueba que debas borrar manualmente para empezar tu proyecto.
- **Libertad absoluta de programación**: Tanto si programas en Python como en cualquier otro lenguaje, eres libre de estructurar tus clases, funciones o tipos como prefieras, teniendo el árbol de carpetas como brújula arquitectónica.
- **Adaptación inmediata a otros stacks**: Si desarrollas en TypeScript (.ts), Go (.go), Java (.java), C# (.cs) o Rust (.rs), solo renombras las extensiones de los archivos sin tener que limpiar sintaxis previa.


---

## 🧭 Filosofía Arquitectónica: 100% Agnóstica al Lenguaje

Aunque los archivos de esta plantilla cuenten con la extensión `.py` (referencia en Python), **esta arquitectura es universal y agnóstica a cualquier lenguaje o ecosistema**:

| Lenguaje / Stack | Extensión | Equivalente Web (🔴) | Equivalente Datos (🟡) |
| :--- | :--- | :--- | :--- |
| **Python** | `.py` | FastAPI / Flask | SQLAlchemy / Pydantic |
| **TypeScript / Node** | `.ts` | NestJS / Express / Fastify | Prisma / TypeORM / Zod |
| **Go** | `.go` | Gin / Fiber / Chi | GORM / Ent / SQLX |
| **Java** | `.java` | Spring Boot / Quarkus | Hibernate / JPA / Jackson |
| **C# / .NET** | `.cs` | ASP.NET Core Web API | Entity Framework Core |
| **Rust** | `.rs` | Axum / Actix-web | SQLx / Diesel / Serde |

Simplemente cambiando la extensión y usando las herramientas de tu lenguaje favorito, los límites arquitectónicos, la separación de responsabilidades y las reglas de aislamiento se mantienen intactos.

---

## ⚖️ ¿Por qué Modular ("Package by Feature") y NO por Capas ("Package by Layer")?

### El colapso de las 20+ entidades en proyectos reales
La mayoría de tutoriales iniciales enseñan una arquitectura horizontal por capas:
```text
❌ ARQUITECTURA HORIZONTAL (Package by Layer):
src/
├── controllers/  (usuarios_controller, productos_controller, facturas_controller...)
├── services/     (usuarios_service, productos_service, facturas_service...)
└── models/       (usuarios_model, productos_model, facturas_model...)
```
Cuando el proyecto supera las 10 o 20 entidades de negocio, esta estructura se vuelve **insostenible**:
1. **Sobrecarga cognitiva**: Para implementar o modificar una sola característica (ej. `usuarios`), el desarrollador debe abrir 5 carpetas distintas y saltar entre decenas de archivos desconectados.
2. **Conflictos de fusión (Merge Conflicts)**: Múltiples desarrolladores modifican permanentemente los mismos directorios centralizados (`controllers/`, `services/`), generando fricción constante en Git.
3. **Alto Acoplamiento y Dependencias Circulares**: Es fácil que un servicio acceda accidentalmente a modelos de otro dominio sin control, convirtiendo la base de código en un "monolito de espagueti".

### La Solución: Paquetes por Característica / Contextos Delimitados (DDD)
```text
✅ ARQUITECTURA MODULAR (Package by Feature):
app/
└── modulos/
    ├── usuarios/   (routes, schemas, services, models, repository)
    ├── productos/  (routes, schemas, services, models, repository)
    └── facturacion/(routes, schemas, services, models, repository)
```
- **Alta Cohesión Interna**: Todo lo relativo a un dominio vive junto. Entender cómo funciona un módulo requiere inspeccionar una sola carpeta.
- **Bajo Acoplamiento Externo**: Los módulos se comunican entre sí a través de interfaces de servicio bien definidas, no importando detalles internos ni tablas de base de datos de otros dominios.

---

## 🚀 Camino Natural hacia Microservicios (Evolución sin Trauma)

Uno de los mayores beneficios de esta estructura modular es que prepara al proyecto para el crecimiento futuro:

```text
[ MONOLITO MODULAR ACTUAL ]                [ FUTURA ARQUITECTURA DISTRIBUIDA ]
app/                                       
├── core/                                  
└── modulos/                               
    ├── usuarios/    ───────────────────►   Microservicio A (Auth & Users API)
    ├── productos/   ───────────────────►   Microservicio B (Catalog Service)
    └── facturacion/ ───────────────────►   Microservicio C (Billing Service)
```

- **Si tu aplicación es un monolito**: Se ejecuta de forma eficiente en un solo proceso, con despliegue sencillo y sin la complejidad de red ni orquestación de microservicios.
- **Si un módulo específico demanda escalar de forma independiente** (por ejemplo, `facturacion` requiere escalado horizontal propio): **Extraerlo es trivial**. Basta con mover la carpeta `modulos/facturacion/` a su propio repositorio o contenedor, ya que sus rutas, reglas de negocio, modelos y persistencia están completamente autocontenidos.
- En una estructura horizontal tradicional por capas, separar un microservicio requiere "hacer arqueología" de código y desenredar dependencias cruzadas durante semanas.

---

## 🚦 La Matriz de Impacto: El Semáforo Arquitectónico

Cada archivo de este proyecto respeta una regla estricta de aislamiento visualizada mediante colores:

```text
mi_proyecto/
│
├── app/
│   ├── main.py              🔴 IMPACTADO (Arranque, lifespan y ensamble del framework web)
│   ├── middleware.py        🔴 IMPACTADO (Interceptores de transporte: CORS, Seguridad, Tracing)
│   │
│   ├── core/
│   │   ├── config.py        🟢 NATIVO (Lectura de variables de entorno y configuración tipada)
│   │   ├── database.py      🟡 MIXTO (Sesión global y conexión de persistencia agnóstica a la web)
│   │   └── security.py      🟢 NATIVO (Criptografía, hashing y utilidades de autenticación puras)
│   │
│   ├── errors/
│   │   ├── exceptions.py    🟢 NATIVO (Excepciones de negocio y dominio puras sin HTTP)
│   │   └── handlers.py      🔴 IMPACTADO (Traductores de excepciones a respuestas HTTP)
│   │
│   └── modulos/             ← Contextos Delimitados (Bounded Contexts)
│       └── modulo_base/
│           ├── routes.py    🔴 IMPACTADO (Puntos de entrada HTTP / Controladores)
│           ├── schemas.py   🟡 MIXTO (Esquemas DTO y contratos de validación de datos)
│           ├── services.py  🟢 NATIVO (Reglas de negocio puras, CERO imports del framework web)
│           ├── models.py    🟡 MIXTO (Definición de tablas con el ORM)
│           └── repository.py🟡 MIXTO (Consultas y operaciones de base de datos)
```

### Significado de los colores:
- 🟢 **Verde (Core & Dominio Puro - 100% Agnóstico)**: No tiene dependencias de transporte web (sin `Request`, `HTTPException`, etc.). Es código de negocio puro, reutilizable y testeable en milisegundos mediante pruebas unitarias puras.
- 🟡 **Amarillo (Persistencia y Contratos de Datos - Acoplado a Bibliotecas)**: Depende de bibliotecas especializadas (ORM, validador de esquemas), pero permanece aislado del protocolo web. Si cambias de FastAPI a Flask o a un comando CLI, esta capa no cambia.
- 🔴 **Rojo (Transporte Web y Framework - Capa Externa Volátil)**: Es el "pegamento" que recibe peticiones del exterior (HTTP, WebSockets, gRPC). Es la única capa que cambia si sustituyes o actualizas el framework web.
