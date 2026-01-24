---
name: models-structure
description: Estructura de modelos y relaciones del backend Edutecnia. Usa este skill cuando necesites entender o crear modelos relacionados con el dominio educativo. Rails 7 + PostgreSQL.
---

# Edutecnia Models Structure

Este skill documenta la estructura de modelos del backend Edutecnia para crear nuevos modelos o entender las relaciones existentes.

## Stack Técnico

- **Framework**: Ruby on Rails 7.0.8
- **Ruby**: 3.2.2
- **Base de datos**: PostgreSQL
- **Soft Delete**: Paranoia (campo `deleted_at`)
- **Auditoría**: Binnacle
- **Encriptación**: Lockbox

## Modelo Central: Establishment

Todo el sistema es multi-tenant basado en `Establishment` (establecimiento educacional).

```ruby
class Establishment < ApplicationRecord
  # Relaciones principales
  has_many :students
  has_many :employees
  has_many :courses
  has_many :subjects
  has_many :establishment_periods
  has_many :student_enrollments
  has_many :users, through: :user_establishments

  # Configuraciones
  has_one :establishment_configuration
  has_many :sige_course_codes
end
```

## Períodos Académicos

```ruby
class EstablishmentPeriod < ApplicationRecord
  belongs_to :establishment

  # Campos principales
  # year: integer (ej: 2026)
  # start_date: date
  # end_date: date
  # status: string (active, inactive)

  has_many :student_enrollments
  has_many :course_periods
  has_many :observations
  has_many :qualifications
end
```

## Estudiantes

### Student (Datos personales)

```ruby
class Student < ApplicationRecord
  belongs_to :establishment
  belongs_to :commune, optional: true
  belongs_to :region, optional: true
  belongs_to :health_institution, optional: true

  has_many :student_enrollments
  has_many :parents, through: :student_parents
  has_many :student_observations

  # Campos principales
  # rut: string (sin DV, ej: "12345678")
  # rut_dv: string (dígito verificador)
  # document_type: string (run, pasaporte)
  # name: string
  # last_name: string
  # second_last_name: string
  # birthday: date
  # gender: string (male, female)
  # address: string
  # phone: string
  # email: string
  # blood_group: string
  # diseases: text
  # allergic_to: text

  # Becas
  # native_beca: boolean
  # president_beca: boolean
  # junaeb_beca: boolean
end
```

### StudentEnrollment (Matrícula por año)

```ruby
class StudentEnrollment < ApplicationRecord
  belongs_to :establishment
  belongs_to :establishment_period
  belongs_to :student
  belongs_to :course
  belongs_to :sige_course_code, optional: true

  has_many :student_observations
  has_many :daily_attendances
  has_many :student_qualifications
  has_many :enrollment_update_requests

  # Campos principales
  # number: integer (número de lista)
  # registration_number: string (número de matrícula)
  # status: string (created, registered, retired, rejected)
  # enrollment_date: date
  # retired_date: date
  # retired_comment: text

  # Estados
  enum status: {
    created: 'created',
    registered: 'registered',
    retired: 'retired',
    rejected: 'rejected'
  }
end
```

## Cursos y Asignaturas

### Course

```ruby
class Course < ApplicationRecord
  belongs_to :establishment
  belongs_to :sige_course_code, optional: true

  has_many :student_enrollments
  has_many :course_subjects
  has_many :subjects, through: :course_subjects
  has_many :course_periods
  has_many :class_schedules
  has_many :observations

  # Campos principales
  # description: string (ej: "1° Básico", "Pre-Kinder")
  # letter: string (ej: "A", "B")
  # capacity: integer
  # code: string
end
```

### Subject (Asignatura)

```ruby
class Subject < ApplicationRecord
  belongs_to :establishment

  has_many :course_subjects
  has_many :courses, through: :course_subjects
  has_many :employees_subject_courses

  # Campos principales
  # name: string (ej: "Matemáticas", "Lenguaje")
  # code: string
  # hours_per_week: integer
  # is_evaluable: boolean
end
```

### CourseSubject (Relación Curso-Asignatura)

```ruby
class CourseSubject < ApplicationRecord
  belongs_to :course
  belongs_to :subject
  belongs_to :establishment_period

  has_many :employees_subject_courses
  has_many :student_qualifications
  has_many :student_qualification_averages

  # Campos principales
  # hours_per_week: integer
  # is_evaluable: boolean
end
```

## Empleados

### Employee

```ruby
class Employee < ApplicationRecord
  belongs_to :establishment
  belongs_to :user, optional: true

  has_many :employee_contracts
  has_many :employees_subject_courses
  has_many :course_subjects, through: :employees_subject_courses
  has_many :course_periods, foreign_key: :teacher_boss_id

  # Campos principales
  # rut: string
  # rut_dv: string
  # name: string
  # last_name: string
  # second_last_name: string
  # email: string
  # phone: string
  # address: string
  # birthday: date
  # gender: string
  # nacionality: string

  # Tipo de empleado
  # employee_type: string (teacher, administrative, assistant)
end
```

### EmployeesSubjectCourse (Asignación docente)

```ruby
class EmployeesSubjectCourse < ApplicationRecord
  belongs_to :employee
  belongs_to :course_subject
  belongs_to :establishment_period

  # Un profesor puede tener múltiples asignaturas en múltiples cursos
end
```

## Calificaciones

### StudentQualification (Nota individual)

```ruby
class StudentQualification < ApplicationRecord
  belongs_to :student_enrollment
  belongs_to :course_subject
  belongs_to :establishment_period
  belongs_to :note_scale_value, optional: true

  # Campos principales
  # value: decimal (nota numérica)
  # percentage: integer (ponderación)
  # description: string (nombre de la evaluación)
  # evaluation_date: date
  # period: integer (1, 2, 3, 4 para trimestres/semestres)
end
```

### StudentQualificationAverage (Promedio)

```ruby
class StudentQualificationAverage < ApplicationRecord
  belongs_to :student
  belongs_to :course_subject
  belongs_to :establishment_period
  belongs_to :note_scale_value, optional: true

  # Campos principales
  # value: decimal (promedio calculado)
  # period: integer (período académico)
  # is_final: boolean (si es promedio final del año)
end
```

### NoteScale (Escala de evaluación)

```ruby
class NoteScale < ApplicationRecord
  belongs_to :establishment

  has_many :note_scale_values

  # Campos principales
  # name: string (ej: "Escala 1.0 - 7.0")
  # min_value: decimal
  # max_value: decimal
  # approval_value: decimal (nota mínima de aprobación)
end
```

## Asistencia

### DailyAttendance

```ruby
class DailyAttendance < ApplicationRecord
  belongs_to :student_enrollment
  belongs_to :establishment_period

  # Campos principales
  # date: date
  # status: string (present, absent, late, justified)
  # comment: text
  # justified: boolean

  enum status: {
    present: 'present',
    absent: 'absent',
    late: 'late',
    justified: 'justified'
  }
end
```

## Observaciones

### Observation

```ruby
class Observation < ApplicationRecord
  belongs_to :course
  belongs_to :establishment_period
  belongs_to :employee, optional: true

  has_many :student_observations

  # Campos principales
  # date: date
  # title: string
  # description: text
  # observation_type: string (positive, negative, neutral)
  # is_general: boolean (si aplica a todo el curso)
end
```

### StudentObservation

```ruby
class StudentObservation < ApplicationRecord
  belongs_to :observation
  belongs_to :student

  # Observación específica para un estudiante
end
```

## Horarios

### ClassSchedule

```ruby
class ClassSchedule < ApplicationRecord
  belongs_to :course
  belongs_to :establishment_period
  belongs_to :course_subject, optional: true
  belongs_to :employee, optional: true

  # Campos principales
  # day_of_week: integer (0-6, domingo-sábado)
  # block_number: integer (número de bloque)
  # start_time: time
  # end_time: time
  # room: string (sala)
end
```

## Usuarios y Permisos

### User

```ruby
class User < ApplicationRecord
  has_many :user_establishments
  has_many :establishments, through: :user_establishments
  has_many :user_roles

  # Autenticación con Devise
  # email: string
  # encrypted_password: string
end
```

### UserEstablishment

```ruby
class UserEstablishment < ApplicationRecord
  belongs_to :user
  belongs_to :establishment

  # Un usuario puede pertenecer a múltiples establecimientos
  # active: boolean
end
```

## Patrones de Código

### Soft Delete con Paranoia

```ruby
class Model < ApplicationRecord
  acts_as_paranoid

  # Incluye automáticamente:
  # default_scope { where(deleted_at: nil) }
  # scope :with_deleted, -> { unscope(where: :deleted_at) }
  # scope :only_deleted, -> { unscope(where: :deleted_at).where.not(deleted_at: nil) }
end
```

### Scopes Comunes

```ruby
class Model < ApplicationRecord
  # Por establecimiento
  scope :for_establishment, ->(establishment_id) {
    where(establishment_id: establishment_id)
  }

  # Por período
  scope :for_period, ->(period_id) {
    where(establishment_period_id: period_id)
  }

  # Activos
  scope :active, -> { where(status: 'active') }

  # Ordenamiento
  scope :recent, -> { order(created_at: :desc) }
  scope :alphabetical, -> { order(:name) }
end
```

### Validaciones Comunes

```ruby
class Model < ApplicationRecord
  # RUT chileno
  validates :rut, presence: true,
                  uniqueness: { scope: :establishment_id }
  validates :rut_dv, presence: true

  # Email
  validates :email, format: { with: URI::MailTo::EMAIL_REGEXP },
                    allow_blank: true

  # Relaciones requeridas
  validates :establishment, presence: true
  validates :establishment_period, presence: true
end
```

### Callbacks Comunes

```ruby
class Model < ApplicationRecord
  before_validation :normalize_rut
  before_save :set_defaults
  after_create :create_related_records

  private

  def normalize_rut
    self.rut = rut.to_s.gsub(/[^0-9]/, '') if rut.present?
  end

  def set_defaults
    self.status ||= 'active'
  end
end
```

## Estructura de Carpetas

```
app/
├── models/
│   ├── concerns/              # Módulos compartidos
│   │   ├── auditable.rb
│   │   └── soft_deletable.rb
│   ├── establishment.rb       # Modelo central
│   ├── student.rb
│   ├── student_enrollment.rb
│   ├── course.rb
│   ├── subject.rb
│   └── ...
├── services/
│   ├── student_service.rb     # Lógica de negocio
│   ├── enrollment_service.rb
│   └── ...
└── controllers/
    └── api/
        └── v1/
            └── portal/        # API principal
```

## Convenciones de Nombres

- **Modelos**: `CamelCase` singular (ej: `StudentEnrollment`)
- **Tablas**: `snake_case` plural (ej: `student_enrollments`)
- **Foreign keys**: `modelo_id` (ej: `student_id`, `course_id`)
- **Join tables**: `modelo1_modelo2` alfabético (ej: `course_subjects`)
- **Estados**: usar `enum` con valores string
- **Fechas**: sufijo `_date` o `_at` (ej: `enrollment_date`, `created_at`)
