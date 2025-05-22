# Taller-MySQL

 Taller Práctico: Sistema de Gestión de Cursos Universitarios
  Diagrama Entidad-Relación (MER)

---

 PASO 1: Identificación de Entidades y Atributos

   Entidades Principales

 1. **ESTUDIANTE**
- `estudiante_id` (PK) - INT, Auto-increment
- `numero_matricula` - VARCHAR(20), UNIQUE
- `nombre` - VARCHAR(50), NOT NULL
- `apellido` - VARCHAR(50), NOT NULL
- `fecha_nacimiento` - DATE
- `email` - VARCHAR(100), UNIQUE
- `telefono` - VARCHAR(15)
- `direccion` - TEXT
- `fecha_ingreso` - DATE
- `carrera` - VARCHAR(100)

2. **CURSO**
- `curso_id` (PK) - INT, Auto-increment
- `codigo_curso` - VARCHAR(10), UNIQUE
- `nombre` - VARCHAR(100), NOT NULL
- `descripcion` - TEXT
- `creditos` - INT, NOT NULL
- `semestre` - INT
- `horas_teoricas` - INT
- `horas_practicas` - INT
- `prerequisitos` - TEXT
- `departamento_id` (FK) - INT

3. **PROFESOR**
- `profesor_id` (PK) - INT, Auto-increment
- `cedula` - VARCHAR(20), UNIQUE
- `nombre` - VARCHAR(50), NOT NULL
- `apellido` - VARCHAR(50), NOT NULL
- `email` - VARCHAR(100), UNIQUE
- `telefono` - VARCHAR(15)
- `especializacion` - VARCHAR(100)
- `grado_academico` - VARCHAR(50)
- `fecha_contratacion` - DATE
- `departamento_id` (FK) - INT

4. **DEPARTAMENTO**
- `departamento_id` (PK) - INT, Auto-increment
- `nombre` - VARCHAR(100), NOT NULL
- `codigo_departamento` - VARCHAR(10), UNIQUE
- `edificio` - VARCHAR(50)
- `piso` - INT
- `telefono` - VARCHAR(15)
- `jefe_departamento_id` (FK) - INT
- `presupuesto` - DECIMAL(12,2)

5. **AULA** (Entidad adicional)
- `aula_id` (PK) - INT, Auto-increment
- `numero_aula` - VARCHAR(10), NOT NULL
- `edificio` - VARCHAR(50)
- `capacidad` - INT
- `tipo_aula` - ENUM('teorica', 'laboratorio', 'auditorio')
- `equipamiento` - TEXT

 6. **PERIODO_ACADEMICO** (Entidad adicional)
- `periodo_id` (PK) - INT, Auto-increment
- `nombre` - VARCHAR(50), NOT NULL
- `fecha_inicio` - DATE
- `fecha_fin` - DATE
- `año` - INT
- `activo` - BOOLEAN DEFAULT TRUE

---

PASO 2: Definición de Relaciones y Cardinalidades

 Relaciones Identificadas

 1. **INSCRIPCION** (Estudiante - Curso) [N:M]
 Entidad intermedia necesaria:
- `estudiante_id` (FK, PK)
- `curso_id` (FK, PK)
- `periodo_id` (FK, PK)
- `fecha_inscripcion` - DATE
- `nota_parcial1` - DECIMAL(3,2)
- `nota_parcial2` - DECIMAL(3,2)
- `nota_final` - DECIMAL(3,2)
- `estado` - ENUM('inscrito', 'aprobado', 'reprobado', 'retirado')

**Cardinalidad:** N:M
- Un estudiante puede inscribirse en múltiples cursos
- Un curso puede tener múltiples estudiantes

#### 2. **IMPARTE** (Profesor - Curso) [N:M]
**Entidad intermedia necesaria:**
- `profesor_id` (FK, PK)
- `curso_id` (FK, PK)
- `periodo_id` (FK, PK)
- `horario` - VARCHAR(100)
- `aula_id` (FK)
- `tipo_participacion` - ENUM('titular', 'auxiliar', 'invitado')

**Cardinalidad:** N:M
- Un profesor puede impartir múltiples cursos
- Un curso puede ser impartido por múltiples profesores

#### 3. **PERTENECE** (Curso - Departamento) [N:1]
**Cardinalidad:** N:1
- Múltiples cursos pertenecen a un departamento
- Un curso pertenece a un único departamento

#### 4. **TRABAJA_EN** (Profesor - Departamento) [N:1]
**Cardinalidad:** N:1
- Múltiples profesores trabajan en un departamento
- Un profesor trabaja en un único departamento

#### 5. **DIRIGE** (Profesor - Departamento) [1:1]
**Cardinalidad:** 1:1
- Un profesor puede dirigir un departamento
- Un departamento tiene un único director

#### 6. **SE_DICTA_EN** (Curso - Aula - Periodo) [Relación ternaria]
**Entidad intermedia:**
- `curso_id` (FK, PK)
- `aula_id` (FK, PK)
- `periodo_id` (FK, PK)
- `dias_semana` - VARCHAR(20)
- `hora_inicio` - TIME
- `hora_fin` - TIME

---

## PASO 3: Justificación del Diseño

### Decisiones de Diseño

#### Entidades Adicionales Propuestas:

1. **AULA**: Necesaria para gestionar la asignación de espacios físicos a los cursos, permitiendo un mejor control de recursos.

2. **PERIODO_ACADEMICO**: Fundamental para manejar diferentes semestres/trimestres académicos y mantener un historial temporal de inscripciones y calificaciones.

#### Atributos Expandidos:

- **Estudiante**: Agregué `numero_matricula`, `carrera`, `fecha_ingreso` para una identificación más completa.
- **Curso**: Incluí `codigo_curso`, `horas_teoricas/practicas`, `prerequisitos` para un control académico más detallado.
- **Profesor**: Añadí `cedula`, `especializacion`, `grado_academico` para mejor gestión del personal docente.

#### Cardinalidades Justificadas:

- **Inscripción (N:M)**: Un estudiante típicamente toma múltiples materias por semestre, y cada materia tiene múltiples estudiantes.
- **Imparte (N:M)**: Los profesores pueden enseñar varias materias, y algunas materias pueden requerir múltiples profesores (titular + auxiliares).
- **Pertenece (N:1)**: Los cursos están organizados por departamentos académicos específicos.

---

## PASO 4: Consideraciones de Implementación

### Restricciones de Integridad:
- Llaves primarias auto-incrementales para todas las entidades principales
- Llaves foráneas con restricciones de integridad referencial
- Campos únicos donde sea necesario (emails, códigos, números de matrícula)
- Validaciones de dominio (rangos de notas, fechas coherentes)

### Optimizaciones Sugeridas:
- Índices en campos de búsqueda frecuente
- Particionamiento por períodos académicos para tablas históricas
- Vistas materializadas para consultas complejas de reportes académicos

---

## Conclusión

Este diseño MER proporciona una base sólida para un sistema de gestión académica, permitiendo:
- Registro completo de estudiantes, profesores y cursos
- Gestión de inscripciones y calificaciones por períodos
- Control de recursos físicos (aulas)
- Estructura organizacional por departamentos
- Flexibilidad para futuras expansiones del sistema

 diagrama MER 

 erDiagram
    ESTUDIANTE ||--o{ INSCRIPCION : "se_inscribe"
    CURSO ||--o{ INSCRIPCION : "recibe"
    PERIODO_ACADEMICO ||--o{ INSCRIPCION : "en_periodo"
    
    PROFESOR ||--o{ IMPARTE : "enseña"
    CURSO ||--o{ IMPARTE : "es_impartido"
    PERIODO_ACADEMICO ||--o{ IMPARTE : "en_periodo"
    AULA ||--o{ IMPARTE : "se_usa"
    
    DEPARTAMENTO ||--o{ CURSO : "ofrece"
    DEPARTAMENTO ||--o{ PROFESOR : "emplea"
    PROFESOR ||--|| DEPARTAMENTO : "dirige"
    
    CURSO ||--o{ SE_DICTA_EN : "se_programa"
    AULA ||--o{ SE_DICTA_EN : "aloja"
    PERIODO_ACADEMICO ||--o{ SE_DICTA_EN : "en_periodo"

    ESTUDIANTE {
        int estudiante_id PK
        string numero_matricula UK
        string nombre
        string apellido
        date fecha_nacimiento
        string email UK
        string telefono
        text direccion
        date fecha_ingreso
        string carrera
    }

    CURSO {
        int curso_id PK
        string codigo_curso UK
        string nombre
        text descripcion
        int creditos
        int semestre
        int horas_teoricas
        int horas_practicas
        text prerequisitos
        int departamento_id FK
    }

    PROFESOR {
        int profesor_id PK
        string cedula UK
        string nombre
        string apellido
        string email UK
        string telefono
        string especializacion
        string grado_academico
        date fecha_contratacion
        int departamento_id FK
    }

    DEPARTAMENTO {
        int departamento_id PK
        string nombre
        string codigo_departamento UK
        string edificio
        int piso
        string telefono
        int jefe_departamento_id FK
        decimal presupuesto
    }

    AULA {
        int aula_id PK
        string numero_aula
        string edificio
        int capacidad
        string tipo_aula
        text equipamiento
    }

    PERIODO_ACADEMICO {
        int periodo_id PK
        string nombre
        date fecha_inicio
        date fecha_fin
        int año
        boolean activo
    }

    INSCRIPCION {
        int estudiante_id FK
        int curso_id FK
        int periodo_id FK
        date fecha_inscripcion
        decimal nota_parcial1
        decimal nota_parcial2
        decimal nota_final
        string estado
    }

    IMPARTE {
        int profesor_id FK
        int curso_id FK
        int periodo_id FK
        string horario
        int aula_id FK
        string tipo_participacion
    }

    SE_DICTA_EN {
        int curso_id FK
        int aula_id FK
        int periodo_id FK
        string dias_semana
        time hora_inicio
        time hora_fin
    }

  [Documento Explicativo - Deciciones de Diseño MER.pdf](https://github.com/user-attachments/files/20378934/Documento.Explicativo.-.Deciciones.de.Diseno.MER.pdf)
