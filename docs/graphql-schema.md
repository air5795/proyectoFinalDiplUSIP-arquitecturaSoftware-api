# Definición de Esquema GraphQL

## Descripción General
Esquema GraphQL para el sistema de gestión de salud. GraphQL permite a los clientes solicitar exactamente los datos que necesitan, eliminando el over-fetching y under-fetching típico de las APIs REST.

## Endpoint
```
POST https://api.sistema-salud.gob.bo/graphql
```

## Tipos (Types)

### Paciente
```graphql
type Paciente {
  id: ID!
  nombre: String!
  ci: String!
  fecha_nacimiento: String!
  direccion: String
  telefono: String
  tipo_seguro: TipoSeguro!
  estado: Estado!
  consultas: [Consulta!]
  cotizaciones: [Cotizacion!]
}
```

### Consulta
```graphql
type Consulta {
  id: ID!
  paciente: Paciente!
  medico: Medico!
  especialidad: String!
  fecha: String!
  diagnostico: String
  receta: String
  observaciones: String
  estado: EstadoConsulta!
}
```

### Medico
```graphql
type Medico {
  id: ID!
  nombre: String!
  especialidad: String!
  matricula: String!
  consultas: [Consulta!]
}
```

### Cotizacion
```graphql
type Cotizacion {
  id: ID!
  asegurado: Paciente!
  periodo: String!
  monto: Float!
  fecha_pago: String
  estado: EstadoPago!
}
```

### Enums
```graphql
enum TipoSeguro {
  TITULAR
  BENEFICIARIO
}

enum Estado {
  ACTIVO
  INACTIVO
  BAJA
}

enum EstadoConsulta {
  PENDIENTE
  EN_CURSO
  COMPLETADA
  CANCELADA
}

enum EstadoPago {
  PENDIENTE
  PAGADO
  MORA
  ANULADO
}
```

## Queries

### Obtener listado de pacientes
```graphql
query {
  pacientes(page: 1, limit: 20) {
    id
    nombre
    ci
    estado
  }
}
```

**Respuesta:**
```json
{
  "data": {
    "pacientes": [
      {
        "id": "1",
        "nombre": "Juan Pérez",
        "ci": "1234567",
        "estado": "ACTIVO"
      }
    ]
  }
}
```

### Obtener paciente con sus consultas (datos relacionados en una sola petición)
```graphql
query {
  paciente(id: "1") {
    nombre
    ci
    fecha_nacimiento
    consultas {
      fecha
      diagnostico
      medico {
        nombre
        especialidad
      }
    }
  }
}
```

**Respuesta:**
```json
{
  "data": {
    "paciente": {
      "nombre": "Juan Pérez",
      "ci": "1234567",
      "fecha_nacimiento": "1990-05-15",
      "consultas": [
        {
          "fecha": "2025-02-10",
          "diagnostico": "Resfriado común",
          "medico": {
            "nombre": "Dra. Ana García",
            "especialidad": "Medicina General"
          }
        }
      ]
    }
  }
}
```

### Obtener cotizaciones de un asegurado
```graphql
query {
  cotizaciones(asegurado_id: "1", periodo: "2025") {
    periodo
    monto
    estado
    asegurado {
      nombre
      ci
    }
  }
}
```

## Mutations

### Registrar nuevo paciente
```graphql
mutation {
  crearPaciente(input: {
    nombre: "María López"
    ci: "7654321"
    fecha_nacimiento: "1985-10-20"
    direccion: "Calle Murillo #456, La Paz"
    telefono: "76543210"
    tipo_seguro: BENEFICIARIO
  }) {
    id
    nombre
    estado
  }
}
```

### Registrar nueva consulta médica
```graphql
mutation {
  crearConsulta(input: {
    paciente_id: "1"
    medico_id: "15"
    diagnostico: "Resfriado común"
    receta: "Paracetamol 500mg cada 8 horas"
    observaciones: "Reposo por 3 días"
  }) {
    id
    fecha
    diagnostico
    paciente {
      nombre
    }
    medico {
      nombre
    }
  }
}
```

### Actualizar estado de paciente
```graphql
mutation {
  actualizarPaciente(id: "1", input: {
    direccion: "Nueva dirección #789"
    telefono: "79999999"
  }) {
    id
    nombre
    direccion
    telefono
  }
}
```

## Ventajas sobre REST
- **Sin over-fetching:** El cliente solicita solo los campos necesarios
- **Sin under-fetching:** Se obtienen datos relacionados en una sola consulta
- **Tipado fuerte:** El esquema define claramente la estructura de datos
- **Autocompletado:** Los playgrounds de GraphQL ofrecen exploración interactiva del esquema
