# Diseño de Endpoints REST API

## Descripción General
Documentación de los endpoints REST para el sistema de gestión de salud. Esta API sigue los principios RESTful y utiliza JSON como formato de intercambio de datos.

## URL Base
```
https://api.sistema-salud.gob.bo/v1
```

## Autenticación
Todas las solicitudes requieren un token JWT en el header:
```
Authorization: Bearer <token>
```

## Endpoints

### 1. Pacientes

#### GET /pacientes
Obtiene la lista de pacientes registrados.

- **Método:** GET
- **URL:** `/v1/pacientes`
- **Query Params:**
  - `page` (int): Número de página (default: 1)
  - `limit` (int): Registros por página (default: 20)
- **Código 200 - Respuesta exitosa:**
```json
{
  "data": [
    {
      "id": 1,
      "nombre": "Juan Pérez",
      "ci": "1234567",
      "fecha_nacimiento": "1990-05-15",
      "estado": "activo"
    }
  ],
  "total": 150,
  "page": 1,
  "limit": 20
}
```
- **Código 401:** No autorizado

#### GET /pacientes/:id
Obtiene el detalle de un paciente específico.

- **Método:** GET
- **URL:** `/v1/pacientes/:id`
- **Código 200 - Respuesta exitosa:**
```json
{
  "id": 1,
  "nombre": "Juan Pérez",
  "ci": "1234567",
  "fecha_nacimiento": "1990-05-15",
  "direccion": "Av. Camacho #123, La Paz",
  "telefono": "71234567",
  "tipo_seguro": "titular",
  "estado": "activo"
}
```
- **Código 404:** Paciente no encontrado

#### POST /pacientes
Registra un nuevo paciente.

- **Método:** POST
- **URL:** `/v1/pacientes`
- **Body:**
```json
{
  "nombre": "María López",
  "ci": "7654321",
  "fecha_nacimiento": "1985-10-20",
  "direccion": "Calle Murillo #456, La Paz",
  "telefono": "76543210",
  "tipo_seguro": "beneficiario"
}
```
- **Código 201:** Paciente creado exitosamente
- **Código 400:** Datos inválidos

#### PUT /pacientes/:id
Actualiza los datos de un paciente.

- **Método:** PUT
- **URL:** `/v1/pacientes/:id`
- **Body:** Campos a actualizar
- **Código 200:** Paciente actualizado
- **Código 404:** Paciente no encontrado

#### DELETE /pacientes/:id
Elimina (baja lógica) un paciente.

- **Método:** DELETE
- **URL:** `/v1/pacientes/:id`
- **Código 200:** Paciente eliminado
- **Código 404:** Paciente no encontrado

### 2. Consultas Médicas

#### GET /consultas
Lista las consultas médicas registradas.

- **Método:** GET
- **URL:** `/v1/consultas`
- **Query Params:** `paciente_id`, `fecha_desde`, `fecha_hasta`, `page`, `limit`
- **Código 200 - Respuesta exitosa:**
```json
{
  "data": [
    {
      "id": 101,
      "paciente_id": 1,
      "medico": "Dra. Ana García",
      "especialidad": "Medicina General",
      "fecha": "2025-02-10",
      "diagnostico": "Resfriado común",
      "estado": "completada"
    }
  ],
  "total": 45,
  "page": 1
}
```

#### POST /consultas
Registra una nueva consulta médica.

- **Método:** POST
- **URL:** `/v1/consultas`
- **Body:**
```json
{
  "paciente_id": 1,
  "medico_id": 15,
  "especialidad": "Medicina General",
  "diagnostico": "Resfriado común",
  "receta": "Paracetamol 500mg cada 8 horas",
  "observaciones": "Reposo por 3 días"
}
```
- **Código 201:** Consulta registrada
- **Código 400:** Datos inválidos

### 3. Cotizaciones

#### GET /cotizaciones
Lista las cotizaciones de los asegurados.

- **Método:** GET
- **URL:** `/v1/cotizaciones`
- **Query Params:** `asegurado_id`, `periodo`, `estado`

#### POST /cotizaciones
Registra una nueva cotización.

- **Método:** POST
- **URL:** `/v1/cotizaciones`

## Códigos de Estado HTTP

| Código | Descripción |
|--------|-------------|
| 200 | Solicitud exitosa |
| 201 | Recurso creado |
| 400 | Solicitud inválida |
| 401 | No autorizado |
| 404 | Recurso no encontrado |
| 500 | Error interno del servidor |
