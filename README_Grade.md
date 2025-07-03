# Microservicio de Gestión de Calificaciones (Grade Management)

## Detalle del Microservicio
Este microservicio se encarga de la gestión de calificaciones de estudiantes, permitiendo registrar, consultar, actualizar y eliminar calificaciones. Está diseñado siguiendo una arquitectura hexagonal y utiliza programación reactiva con Spring WebFlux y MongoDB como base de datos.

**Característica especial**: El microservicio incluye funcionalidad transaccional que genera notificaciones automáticas cuando se crean o actualizan calificaciones. Los mensajes y campos de notificación están personalizados en español y muestran los nombres reales de estudiante y curso.

## Estructura del Proyecto
```
src/main/java/pe/edu/vallegrande/vg_ms_grade_management/
├── domain/
│   ├── model/
│   │   └── Grade.java                 // Entidad que representa una nota
│   │   └── DatabaseSequence.java      // Entidad para la secuencia de IDs
│   ├── enums/
│   │   └── ... (otros enums del dominio)
│   └── repository/
│       └── GradeRepository.java       // Interfaz para la persistencia de notas
├── application/
│   ├── service/
│   │   ├── GradeService.java          // Interfaz para los servicios de notas
│   │   ├── GradeNotificationService.java // Servicio para notificaciones transaccionales
│   │   └── SequenceGeneratorService.java // Servicio para generar secuencias de IDs
│   └── impl/
│       ├── GradeServiceImpl.java    // Implementación de los servicios de notas
│       └── GradeNotificationServiceImpl.java // Implementación de notificaciones transaccionales
├── infrastructure/
│   ├── config/
│   │   └── ... (configuraciones específicas de infraestructura)
│   ├── document/
│   │   └── GradeDocument.java         // Documento MongoDB para notas
│   ├── dto/
│   │   ├── request/
│   │   │   └── GradeRequest.java    // DTO para la creación/actualización de notas
│   │   └── response/
│   │       └── GradeResponse.java   // DTO para las respuestas de notas
│   ├── mapper/
│   │   └── GradeMapper.java           // Mapper entre Grade y GradeDocument
│   ├── repository/
│   │   └── GradeRepository.java       // Repositorio Spring Data MongoDB para notas
│   ├── rest/
│   │   └── GradeRest.java             // Controlador REST para las notas
│   └── service/
│       └── ApiService.java            // Servicio de API genérico
└── VgMsGradeManagementApplication.java  // Clase principal de la aplicación
```

## Funcionalidad Transaccional de Notificaciones

### Comportamiento Automático
Cuando se crea o actualiza una calificación, el sistema automáticamente genera notificaciones:

1. **Al crear una calificación** (`POST /api/grades`):
   - Genera notificación tipo `Calificación Publicada` para el estudiante (con su nombre real y el nombre real del curso)
   - Si la calificación es menor a 11, genera notificación `Bajo Rendimiento` para los padres

2. **Al actualizar una calificación** (`PUT /api/grades/{id}`):
   - Genera notificación tipo `Calificación Actualizada` para el estudiante
   - Si la calificación es menor a 11, genera notificación `Bajo Rendimiento` para los padres

### Tipos de Notificaciones Generadas
- **Calificación Publicada**: Cuando se publica una nueva calificación
- **Calificación Actualizada**: Cuando se actualiza una calificación existente
- **Bajo Rendimiento**: Cuando la calificación es menor a 11 puntos (para padres)

## Documentación de API

### Endpoints de Calificaciones (`/api/grades`)

Los endpoints están organizados por método HTTP:

#### GET - Consultas

##### GET `/api/grades` - Obtener todas las calificaciones
**Descripción:** Obtiene todas las calificaciones activas (no eliminadas lógicamente)
**Request:** No requiere cuerpo
**Response:** Array de Grade
```json
[
  {
    "id": "654321",
    "studentId": "4",
    "courseId": "C001",
    "academicPeriod": "Bimester",
    "evaluationType": "Examen",
    "grade": 20.0,
    "evaluationDate": "2024-01-15",
    "remarks": "Excelente trabajo en el examen",
    "deleted": false
  }
]
```

##### GET `/api/grades/{id}` - Obtener una calificación por ID
**Descripción:** Obtiene una calificación específica por su ID
**Parámetros:** `id` (String) - ID de la calificación
**Request:** No requiere cuerpo
**Response:** Grade o 404 si no existe
```json
{
  "id": "654321",
  "studentId": "4",
  "courseId": "C001",
  "academicPeriod": "Bimester",
  "evaluationType": "Examen",
  "grade": 20.0,
  "evaluationDate": "2024-01-15",
  "remarks": "Excelente trabajo en el examen",
  "deleted": false
}
```

##### GET `/api/grades/student/{studentId}` - Obtener calificaciones por estudiante
**Descripción:** Obtiene todas las calificaciones de un estudiante específico
**Parámetros:** `studentId` (String) - ID del estudiante
**Request:** No requiere cuerpo
**Response:** Array de Grade
```json
[
  {
    "id": "654321",
    "studentId": "4",
    "courseId": "C001",
    "academicPeriod": "Bimester",
    "evaluationType": "Examen",
    "grade": 20.0,
    "evaluationDate": "2024-01-15",
    "remarks": "Excelente trabajo",
    "deleted": false
  }
]
```

##### GET `/api/grades/course/{courseId}` - Obtener calificaciones por curso
**Descripción:** Obtiene todas las calificaciones de un curso específico
**Parámetros:** `courseId` (String) - ID del curso
**Request:** No requiere cuerpo
**Response:** Array de Grade
```json
[
  {
    "id": "654321",
    "studentId": "4",
    "courseId": "C001",
    "academicPeriod": "Bimester",
    "evaluationType": "Examen",
    "grade": 20.0,
    "evaluationDate": "2024-01-15",
    "remarks": "Excelente trabajo",
    "deleted": false
  }
]
```

##### GET `/api/grades/student/{studentId}/course/{courseId}` - Obtener calificaciones por estudiante y curso
**Descripción:** Obtiene todas las calificaciones de un estudiante en un curso específico
**Parámetros:** `studentId` (String) - ID del estudiante, `courseId` (String) - ID del curso
**Request:** No requiere cuerpo
**Response:** Array de Grade
```json
[
  {
    "id": "654321",
    "studentId": "4",
    "courseId": "C001",
    "academicPeriod": "Bimester",
    "evaluationType": "Examen",
    "grade": 20.0,
    "evaluationDate": "2024-01-15",
    "remarks": "Excelente trabajo",
    "deleted": false
  }
]
```

##### GET `/api/grades/{id}/notifications` - Obtener notificaciones relacionadas con una calificación
**Descripción:** Obtiene todas las notificaciones generadas para una calificación específica
**Parámetros:** `id` (String) - ID de la calificación
**Request:** No requiere cuerpo
**Response:** Array de NotificationResponse
```json
[
  {
    "id": "65d2a7f1b3e9c8a7b6c5d4e3",
    "recipientId": "4",
    "recipientType": "Amelia Flores Ascencio",
    "message": "Hola Amelia Flores Ascencio, tu calificación de Matemáticas ha sido publicada. Obtuviste 20.0/20 puntos.",
    "notificationType": "Calificación Publicada",
    "status": "Pendiente",
    "channel": "Correo",
    "createdAt": "2024-01-15T10:30:00",
    "sentAt": null,
    "deleted": false
  }
]
```

##### GET `/api/grades/inactive` - Obtener calificaciones eliminadas lógicamente
**Descripción:** Obtiene todas las calificaciones eliminadas lógicamente (deleted=true)
**Request:** No requiere cuerpo
**Response:** Array de Grade con deleted=true
```json
[
  {
    "id": "654323",
    "studentId": "4",
    "courseId": "C002",
    "academicPeriod": "Bimester",
    "evaluationType": "Examen",
    "grade": 8.5,
    "evaluationDate": "2024-01-16",
    "remarks": "Necesita mejorar",
    "deleted": true
  }
]
```

#### POST - Creación

##### POST `/api/grades` - Crear una nueva calificación
**Descripción:** Crea una nueva calificación y genera automáticamente notificaciones
**Request:** Grade (sin ID)
```json
{
  "studentId": "4",
  "courseId": "C001",
  "academicPeriod": "Bimester",
  "evaluationType": "Examen",
  "grade": 20.0,
  "evaluationDate": "2024-01-15",
  "remarks": "Excelente trabajo en el examen"
}
```
**Response (201 CREATED):** Grade creado
```json
{
  "id": "654321",
  "studentId": "4",
  "courseId": "C001",
  "academicPeriod": "Bimester",
  "evaluationType": "Examen",
  "grade": 20.0,
  "evaluationDate": "2024-01-15",
  "remarks": "Excelente trabajo en el examen",
  "deleted": false
}
```
**Notificaciones automáticas generadas:**
- `Calificación Publicada` para el estudiante (con nombre real y curso real)
- `Bajo Rendimiento` para padres (si grade < 11)

#### PUT - Actualización

##### PUT `/api/grades/{id}` - Actualizar una calificación existente
**Descripción:** Actualiza una calificación existente y genera automáticamente notificaciones
**Parámetros:** `id` (String) - ID de la calificación a actualizar
**Request:** Grade (sin ID)
```json
{
  "studentId": "4",
  "courseId": "C001",
  "academicPeriod": "Bimester",
  "evaluationType": "Examen",
  "grade": 18.0,
  "evaluationDate": "2024-01-15",
  "remarks": "Calificación corregida"
}
```
**Response:** Grade actualizado
```json
{
  "id": "654321",
  "studentId": "4",
  "courseId": "C001",
  "academicPeriod": "Bimester",
  "evaluationType": "Examen",
  "grade": 18.0,
  "evaluationDate": "2024-01-15",
  "remarks": "Calificación corregida",
  "deleted": false
}
```
**Notificaciones automáticas generadas:**
- `Calificación Actualizada` para el estudiante (con nombre real y curso real)
- `Bajo Rendimiento` para padres (si grade < 11)

##### PUT `/api/grades/{id}/restore` - Restaurar una calificación eliminada
**Descripción:** Restaura una calificación eliminada lógicamente
**Parámetros:** `id` (String) - ID de la calificación a restaurar
**Request:** No requiere cuerpo
**Response:** Grade restaurado con deleted=false
```json
{
  "id": "654321",
  "studentId": "4",
  "courseId": "C001",
  "academicPeriod": "Bimester",
  "evaluationType": "Examen",
  "grade": 20.0,
  "evaluationDate": "2024-01-15",
  "remarks": "Excelente trabajo",
  "deleted": false
}
```

#### DELETE - Eliminación

##### DELETE `/api/grades/{id}` - Eliminar lógicamente una calificación
**Descripción:** Elimina lógicamente una calificación (marca como deleted=true)
**Parámetros:** `id` (String) - ID de la calificación a eliminar
**Request:** No requiere cuerpo
**Response:** Grade eliminado con deleted=true
```json
{
  "id": "654321",
  "studentId": "4",
  "courseId": "C001",
  "academicPeriod": "Bimester",
  "evaluationType": "Examen",
  "grade": 20.0,
  "evaluationDate": "2024-01-15",
  "remarks": "Excelente trabajo",
  "deleted": true
}
```

## Ejemplos de JSON para DTOs

### Grade Request/Response (usado en POST y PUT)
```json
{
  "id": "654321",
  "studentId": "4",
  "courseId": "C001",
  "academicPeriod": "Bimester",
  "evaluationType": "Examen",
  "grade": 20.0,
  "evaluationDate": "2024-01-15",
  "remarks": "Excelente trabajo en el examen",
  "deleted": false
}
```

### Grade Request para POST (sin ID)
```json
{
  "studentId": "4",
  "courseId": "C001",
  "academicPeriod": "Bimester",
  "evaluationType": "Examen",
  "grade": 20.0,
  "evaluationDate": "2024-01-15",
  "remarks": "Excelente trabajo en el examen"
}
```

## Casos de Uso con Notificaciones Automáticas

### 1. Crear Calificación con Notificación Automática
**POST** `/api/grades`

```json
{
  "studentId": "4",
  "courseId": "C001",
  "grade": 20.0,
  "academicPeriod": "Bimester",
  "evaluationType": "Examen",
  "evaluationDate": "2024-01-15",
  "remarks": "Excelente trabajo"
}
```

**Resultado**: Se crea la calificación y automáticamente se genera una notificación `Calificación Publicada` para el estudiante con su nombre real y el nombre real del curso.

### 2. Crear Calificación con Bajo Rendimiento
**POST** `/api/grades`

```json
{
  "studentId": "4",
  "courseId": "C002",
  "grade": 8.5,
  "academicPeriod": "Bimester",
  "evaluationType": "Examen",
  "evaluationDate": "2024-01-16",
  "remarks": "Necesita mejorar"
}
```

**Resultado**: Se crea la calificación y automáticamente se generan:
- Notificación `Calificación Publicada` para el estudiante
- Notificación `Bajo Rendimiento` para los padres

### 3. Actualizar Calificación
**PUT** `/api/grades/654321`

```json
{
  "studentId": "4",
  "courseId": "C001",
  "grade": 20.0,
  "academicPeriod": "Bimester",
  "evaluationType": "Examen",
  "evaluationDate": "2024-01-15",
  "remarks": "Calificación corregida"
}
```

**Resultado**: Se actualiza la calificación y automáticamente se genera una notificación `Calificación Actualizada` para el estudiante.

### 4. Consultar Notificaciones de una Calificación
**GET** `/api/grades/654321/notifications`

**Resultado**: Retorna todas las notificaciones relacionadas con esa calificación específica, mostrando los nombres reales y los mensajes en español.

## Notas Técnicas
- Los mensajes y campos de notificación están personalizados en español.
- El campo `recipientType` muestra el nombre real del estudiante (excepto para padres, que sigue siendo "PARENT").
- El campo `message` incluye el nombre real del estudiante y del curso.
- Los valores de `notificationType`, `status` y `channel` están en español.