# Arquitectura del Sistema

Documentación de la arquitectura siguiendo el modelo de vistas **4+1** de Philippe Kruchten. Cada vista aborda un conjunto de preocupaciones de los distintos interesados y, en conjunto, describen la arquitectura del sistema.

## Vistas

| Vista | Propósito | Interesados principales |
| --- | --- | --- |
| [Contexto del sistema](context-system/context.md) | Delimita el sistema, actores y sistemas externos. | Todos |
| [Vista lógica](logical-view/view.md) | Estructura de componentes, responsabilidades y relaciones. | Analistas, diseñadores |
| [Vista de procesos](process-view/view.md) | Concurrencia, rendimiento, disponibilidad y comunicación. | Integradores, administradores |
| [Vista de desarrollo](development-view/view.md) | Organización del código, módulos y dependencias. | Programadores, gestores |
| [Vista física](physical-view/view.md) | Despliegue en hardware e infraestructura. | Administradores, operaciones |
| [Escenarios](scenarios/view.md) | Casos de uso que validan y cohesionan las vistas. | Todos |

## Relación entre vistas

```
                 +-------------------+
                 |     Escenarios    |
                 +---------+---------+
                           |
     +---------------------+---------------------+
     |           |                   |           |
+----v----+ +----v----+        +-----v-----+ +---v------+
| Lógica  | | Procesos |        | Desarrollo | | Física  |
+---------+ +----------+        +-----------+ +----------+
                           |
                 +---------v---------+
                 | Contexto sistema  |
                 +-------------------+
```

## Cómo mantener esta documentación

- Cada vista vive en su propia carpeta y se actualiza de forma independiente.
- Los escenarios referencian los elementos de las demás vistas para demostrar que la arquitectura cubre los requisitos.
- Usar diagramas como código (PlantUML/Mermaid) cuando sea posible para mantenerlos versionados.
