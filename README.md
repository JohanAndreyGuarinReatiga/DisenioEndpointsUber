# Uner
### Por Johan Guarin

## Preguntas guía

1. ¿Qué entiendes por “endpoint” en el contexto de una API?
- Un endpoint es la representación de una ruta o recurso específico en una URL y permite interactuar con metodos http.
  
2. ¿Cuál es la diferencia entre un endpoint público y uno privado?
- Un endpoint público puede ser accedido sin dificultad y es simple pero con la problemática de que no es seguro, al contrario de uno privado que puede ser más seguro y con un control de acceso más preciso, esto con la desventaja de que puede ser más complejo y costoso.

3. ¿Qué información de un usuario consideras confidencial y no debería exponerse?
- Contraseñas, ubicación en tiempo real, datos de pago, direcciones exactas, tokens de autenticación y datos personales como correo o número de teléfono.

4. ¿Por qué es importante definir bien los métodos HTTP (GET, POST, PUT/PATCH, DELETE) en cada endpoint?
- Para mantener buenas prácticas restful, mantener seguridad, claridad en las operaciones y evitar errores de implementación.

5. ¿Qué tipo de información requiere autenticación en este sistema?
- Solicitud de viajes, ubicación, historial de pagos, calificaciones, información personal y cualquier manejo o administración de información personal.

6. ¿Cómo manejarías la seguridad de la ubicación de conductores y pasajeros?
- Cifrado de datos (HTTPS), tokens de acceso con expiración, limitar visibilidad solo durante viajes activos y registro mínimo.

7. ¿Qué pasaría si un viaje es solicitado y no hay conductores disponibles? ¿Cómo debería responder la API?
- El api debería responder con un codigo de estado no encontrado o servicio no disponible los cuales son 404 y 503 dependiendo el caso y pues mostrando que no hay conductores disponibles.

8. ¿Cómo identificarías los recursos principales de esta aplicación?
- Son las entidades claves que representan objetos del dominio: usuarios, viajes, pagos, calificaciones y vehículos.

9. ¿Qué ventajas tendría versionar la API (por ejemplo, `/v1/...`) desde el inicio?
- Para mantener escalabilidad dentro del api y no tener interferencias entre versiones y cambios 

10. ¿Por qué es importante documentar las respuestas de error y no solo las exitosas?
- Para saber con exactitud el tipo de error que se tiene y darle solución

## Roles y permisos 

Pasajero	
- solicitar viaje
- ver historial
- pagar
- calificar y cancelar viaje

Conductor
- Aceptar
- rechazar viajes
- actualizar estado
- ver historial
- calificar
  
Administrador	
- Ver
- modificar usuarios
- viajes
- estadísticas
- bloquear cuentas
- auditar

## Recursos principales
auth: inicio de sesión y registro.

users: gestión de cuentas.

admin: control de sistema por parte del administrador.

rides: solicitudes y estados de viajes.

ratings: evaluaciones mutuas entre pasajeros y conductores.

vehicles: datos de vehículos registrados.

payments: gestión y confirmación de pagos.

## Tabla de endpoints

| Método | Endpoint                         | Descripción                                      | Parámetros                                 | Autenticación           |
|--------|----------------------------------|--------------------------------------------------|---------------------------------------------|--------------------------|
| POST   | /v1/auth/register                | Registro de usuario                              | body: {name, email, password, role}         | No                       |
| POST   | /v1/auth/login                   | Login de usuario                                 | body: {email, password}                     | No                       |
| GET    | /v1/users/me                     | Obtener datos del usuario autenticado            | headers: token                              | Sí                       |
| PUT    | /v1/users/me                     | Actualizar perfil del usuario                    | body: {name, phone}                         | Sí                       |
| DELETE | /v1/users/me                     | Eliminar cuenta                                  | headers: token                              | Sí                       |
| GET    | /v1/vehicles                     | Listar vehículos del conductor                   | headers: token                              | Rol: Conductor           |
| POST   | /v1/vehicles                     | Registrar vehículo                               | body: {marca, modelo, placa}                | Rol: Conductor           |
| DELETE | /v1/vehicles/:id                 | Eliminar vehículo                                | path: id                                    | Rol: Conductor           |
| POST   | /v1/rides                        | Solicitar viaje                                  | body: {origen, destino}                     | Rol: Pasajero            |
| GET    | /v1/rides                        | Ver mis viajes                                   | query: status                               | Sí                       |
| GET    | /v1/rides/nearby                 | Buscar viajes cercanos (para conductor)          | query: location                             | Rol: Conductor           |
| PATCH  | /v1/rides/:id/accept             | Aceptar viaje                                    | path: id                                    | Rol: Conductor           |
| PATCH  | /v1/rides/:id/cancel             | Cancelar viaje                                   | path: id                                    | Sí                       |
| PATCH  | /v1/rides/:id/start              | Marcar inicio del viaje                          | path: id                                    | Rol: Conductor           |
| PATCH  | /v1/rides/:id/finish             | Finalizar viaje                                  | path: id                                    | Rol: Conductor           |
| POST   | /v1/payments                     | Procesar pago                                    | body: {ride_id, método}                     | Rol: Pasajero            |
| GET    | /v1/payments/history             | Ver historial de pagos                           | query: fecha                                | Sí                       |
| POST   | /v1/ratings                      | Calificar viaje                                  | body: {ride_id, score, comentario}          | Sí                       |
| GET    | /v1/admin/users                  | Listar usuarios                                  | query: rol                                  | Rol: Admin               |
| PATCH  | /v1/admin/users/:id/block        | Bloquear usuario                                 | path: id                                    | Rol: Admin               |

## Flujos de uso

### Registro y eliminación de un vehículo (Conductor)
Objetivo: Un conductor registra su vehículo para comenzar a recibir viajes, y luego decide eliminarlo.

- El conductor inicia sesión mediante POST /v1/auth/login.
- Usa POST /v1/vehicles enviando los datos del vehículo (marca, modelo, placa).
- La API valida y registra el vehículo en su cuenta.
- El conductor verifica sus vehículos registrados con GET /v1/vehicles.
- Decide eliminar un vehículo con DELETE /v1/vehicles/:id.
- La API confirma y elimina el registro del vehículo.

###  Pago fallido y manejo de error
Objetivo: Un pasajero intenta pagar, pero el método de pago falla. La API responde apropiadamente.

- Tras finalizar un viaje, el pasajero intenta pagar con POST /v1/payments.
- Envía ride_id y método de pago (por ejemplo, tarjeta vencida).
- La API detecta un fallo en la transacción y responde con 402 Payment Required.
- El usuario es notificado en la app y puede intentar otro método de pago.
- El sistema guarda el intento fallido en el historial de pagos.

### Bloqueo de un usuario por comportamiento inapropiado
Objetivo: El administrador detecta comportamiento inapropiado y bloquea una cuenta.

- El administrador accede al panel mediante POST /v1/auth/login.
- Usa GET /v1/admin/users para buscar usuarios con comportamiento reportado.
- Identifica a un conductor con múltiples calificaciones negativas.
- Ejecuta PATCH /v1/admin/users/:id/block para bloquearlo.
- El sistema marca al usuario como bloqueado y revoca su token.
- Si el usuario intenta acceder, la API responde con 403 Forbidden.

## Decisiones de diseño y justificación
- Versionado (/v1): permite escalar sin romper integraciones.
- Roles separados: evita mezclar permisos y mejora la seguridad.
- Seguridad: tokens, HTTPS obligatorio, acceso mínimo necesario.
- Privacidad: ubicación solo visible durante el viaje.
- Errores explícitos: facilita el desarrollo del cliente.
- Modularidad RESTful: facilita mantenimiento y comprensión.

## Manejo de errores
Formato general:
``` json
{
  "error": "Descripción del error",
  "code": "ERR_CODE"
}
```
Código HTTP	Error	Situación
- 400	INVALID_DATA	Datos mal enviados en el body
- 401	UNAUTHORIZED	Token inválido o ausente
- 403	FORBIDDEN	Acción no permitida según rol
- 404	NOT_FOUND	Recurso no encontrado (ej. viaje inexistente)
- 503	NO_DRIVERS_AVAILABLE	No hay conductores cercanos


## Propuestas de mejora 
Notificaciones push en tiempo real.
Opción de pago en efectivo.
Soporte para rutas compartidas.
Integración con mapas para actualizacion en tiempo real.
Reportes automáticos de actividad para el administrador.
