# Service Eureka

Eureka Server - Service Discovery para la plataforma de microservicios bancario.

## Descripción

Service Eureka es el servidor de descubrimiento de servicios basado en Netflix Eureka. Permite que todos los microservicios se registren automáticamente y se descubran entre sí sin necesidad de configurar URLs manualmente.

## Características

- 🔍 **Service Discovery** - Registro y descubrimiento automático de microservicios
- 💚 **Health Checks** - Monitoreo continuo de la salud de los servicios
- 🔄 **Auto-registration** - Los servicios se registran automáticamente
- 🏠 **Standalone** - No se registra a sí mismo
- 📊 **Dashboard** - UI web en `http://localhost:8761/`

## Configuración

### application.yaml (Desarrollo)
```yaml
server:
  port: 8761

eureka:
  instance:
    hostname: localhost
  client:
    register-with-eureka: false
    fetch-registry: false
```

### application-prod.yaml (Producción)
Configuración local del servicio (no depende de config-server)

## Docker

### Build
```bash
docker build -t service-eureka:latest .
```

### Run (Docker Compose)
```bash
docker compose up -d
```

### Verificar
```bash
curl http://localhost:8761/actuator/health
```

## Acceso

- **Dashboard**: http://localhost:8761/
- **Health Check**: http://localhost:8761/actuator/health
- **Registrados**: http://localhost:8761/eureka/apps

## Microservicios registrados

Estos servicios se registran automáticamente en Eureka:

- `ms-accounts` - Gestión de cuentas
- `ms-customer` - Gestión de clientes
- `ms-credits` - Gestión de créditos
- `ms-transaction` - Gestión de transacciones
- `ms-balance` - Consulta de saldos
- `service-gateway` - API Gateway

## Troubleshooting

### El dashboard no muestra servicios registrados
- Verificar que los microservicios tienen Eureka en su pom.xml
- Revisar que `eureka.client.service-url.defaultZone` apunta a este servicio
- Ver logs: `docker logs service-eureka`

### Errores de conexión
```bash
# Verificar que el puerto 8761 está abierto
netstat -tuln | grep 8761

# Ping al contenedor
docker exec service-eureka wget -O- http://localhost:8761/actuator/health
```

## URLs importantes

- Dashboard: http://localhost:8761/
- Actuator: http://localhost:8761/actuator
- Apps registradas: http://localhost:8761/eureka/apps
- Instancias de un servicio: http://localhost:8761/eureka/apps/MS-CUSTOMER

## Configuración de clientes

En cada microservicio, agregar a `application.yaml`:

```yaml
eureka:
  client:
    service-url:
      defaultZone: http://service-eureka:8761/eureka/
  instance:
    preferIpAddress: true
    lease-renewal-interval-in-seconds: 10
    lease-expiration-duration-in-seconds: 30
```
