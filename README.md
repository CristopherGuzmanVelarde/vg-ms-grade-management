# vg-ms-grade-management

Este proyecto es un microservicio de gestión de calificaciones, desarrollado con Spring WebFlux y MongoDB, siguiendo una arquitectura hexagonal. Está diseñado para manejar de manera eficiente las operaciones relacionadas con las calificaciones de estudiantes, cursos y la generación de secuencias de IDs.

**Novedad:** Los mensajes y campos de notificación están personalizados en español y muestran los nombres reales de estudiante y curso.

## Tecnologías Utilizadas
- Java 17
- Spring Boot 3.2.5
- Spring WebFlux (para programación reactiva)
- Spring Data MongoDB (para persistencia de datos)
- Lombok (para reducir código boilerplate)
- Maven (para gestión de dependencias y construcción)

## Arquitectura Hexagonal
El proyecto sigue una arquitectura hexagonal (Ports and Adapters) para asegurar una clara separación de responsabilidades y facilitar la mantenibilidad y la testabilidad. Esto se refleja en la estructura de paquetes:

- `domain`: Contiene la lógica de negocio central y las entidades del dominio (agnósticas a la tecnología).
- `application`: Define los servicios de aplicación (ports) y sus implementaciones (adapters), orquestando las operaciones del dominio.
- `infrastructure`: Contiene los adaptadores para tecnologías externas, como la base de datos (MongoDB) y la capa REST (controladores).

Para detalles específicos de cada microservicio (Estudiantes, Cursos, Calificaciones), por favor, consulta los READMEs dedicados:

- [README_Course.md](README_Course.md)
- [README_Grade.md](README_Grade.md)

## Configuración

### Base de Datos MongoDB
El microservicio se conecta a una instancia de MongoDB. La configuración de la conexión se encuentra en `src/main/resources/application.yml`:

```yaml
spring:
  data:
    mongodb:
      host: localhost
      port: 27017
      database: vg_ms_grade_management_db
  application:
    name: vg-ms-grade-management
server:
  port: 8080
```

Asegúrate de que una instancia de MongoDB esté corriendo en `localhost:27017` o actualiza la configuración según tu entorno.

## Cómo Ejecutar el Proyecto

1.  **Clonar el Repositorio**:
    ```bash
    git clone https://github.com/tu-usuario/vg-ms-grade-management.git
    cd vg-ms-grade-management
    ```

2.  **Configurar MongoDB**:
    Asegúrate de tener una instancia de MongoDB corriendo localmente en el puerto 27017, o actualiza la configuración en `src/main/resources/application.yml`.

3.  **Compilar y Ejecutar con Maven**:
    ```bash
    mvn clean install
    mvn spring-boot:run
    ```

    El microservicio se iniciará en `http://localhost:8080`.

## Cómo Probar el Proyecto

Puedes usar herramientas como Postman, Insomnia o `curl` para interactuar con los endpoints REST. Aquí hay algunos ejemplos:

### Estudiantes

-   **Crear Estudiante**:
    ```bash
    curl -X POST -H "Content-Type: application/json" -d '{"firstName": "Ana", "lastName": "Lopez"}' http://localhost:8080/api/students
    ```

-   **Obtener Todos los Estudiantes**:
    ```bash
    curl http://localhost:8080/api/students
    ```

### Cursos

-   **Crear Curso**:
    ```bash
    curl -X POST -H "Content-Type: application/json" -d '{"name": "Historia", "description": "Historia Universal"}' http://localhost:8080/api/courses
    ```

-   **Obtener Todos los Cursos**:
    ```bash
    curl http://localhost:8080/api/courses
    ```

### Calificaciones

-   **Crear Calificación**:
    ```bash
    curl -X POST -H "Content-Type: application/json" -d '{"studentId": "<ID_ESTUDIANTE>", "courseId": "<ID_CURSO>", "score": 95.0}' http://localhost:8080/api/grades
    ```

-   **Obtener Todas las Calificaciones**:
    ```bash
    curl http://localhost:8080/api/grades
    ```
