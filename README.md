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

| Column 1 | Column 2 | Column 3 |
|----------|----------|----------|
| Row 1    | Cell 2   | Cell 3   |
| Row 2    | Cell 5   | Cell 6   |
| Row 3    | Cell 8   | Cell 9   |

