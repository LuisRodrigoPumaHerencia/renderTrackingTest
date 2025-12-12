# 📊 Monitoreo con Prometheus y Grafana en Render.com

## 🚀 Despliegue en Render.com

### Paso 1: Preparar el repositorio

1. Asegúrate de que el archivo `render.yaml` esté en la **raíz del proyecto** (no en `monitoring/`)
2. Sube todos los cambios a GitHub

### Paso 2: Desplegar en Render.com

1. **Crear cuenta en Render.com**: https://render.com
2. **Conectar repositorio GitHub**:
   - Dashboard → New → Blueprint
   - Selecciona tu repositorio
   - Render detectará automáticamente el `render.yaml`

3. **O desplegar manualmente**:

   **Prometheus:**
   - New → Web Service
   - Conecta tu repositorio
   - Configura:
     - **Name**: `prometheus-tracking`
     - **Environment**: Docker
     - **Dockerfile Path**: `monitoring/Dockerfile.prometheus`
     - **Docker Context**: `monitoring`
     - **Health Check Path**: `/-/healthy`
     - **Plan**: Free

   **Grafana:**
   - New → Web Service
   - Conecta tu repositorio
   - Configura:
     - **Name**: `grafana-tracking`
     - **Environment**: Docker
     - **Dockerfile Path**: `monitoring/Dockerfile.grafana`
     - **Docker Context**: `monitoring`
     - **Health Check Path**: `/api/health`
     - **Environment Variables**:
       - `GF_SECURITY_ADMIN_PASSWORD`: `admin123` (o la que prefieras)
     - **Plan**: Free

### Paso 3: Configurar Grafana (IMPORTANTE)

Después de desplegar ambos servicios, debes conectar Grafana con Prometheus:

**Opción 1: Desde la UI de Grafana (Recomendado)**
1. Accede a Grafana: `https://grafana-tracking.onrender.com`
2. Login: `admin` / `admin123`
3. Ve a: **Configuration** → **Data Sources** → **Prometheus**
4. En **URL**, reemplaza `http://localhost:9090` con la URL de tu Prometheus:
   - Ejemplo: `https://prometheus-tracking.onrender.com`
5. Click en **Save & Test** (debe mostrar "Data source is working")

**Opción 2: Editar archivo y redeployar**
1. Edita `monitoring/grafana/provisioning/datasources/prometheus.yml`
2. Cambia la línea `url: http://localhost:9090` por:
   ```yaml
   url: https://prometheus-tracking.onrender.com  # Tu URL de Prometheus
   ```
3. Commit y push (Render redeployará automáticamente)

### Paso 4: Acceder

- **Grafana**: `https://grafana-tracking.onrender.com`
  - Usuario: `admin`
  - Contraseña: `admin123` (o la que configuraste)

- **Prometheus**: `https://prometheus-tracking.onrender.com`

## 📝 Configuración Actual

### API Monitoreada
- **URL**: `https://api-cluster-tracking.sumax.pe/prod/sumax-erp-backend-tracking2/actuator/prometheus`
- Configurado en: `monitoring/prometheus.yml`

### Métricas Disponibles
- HTTP Requests (cantidad, tiempo de respuesta, códigos de estado)
- JVM (memoria, garbage collection, threads)
- Sistema (CPU, memoria)
- Base de datos (conexiones HikariCP)
- Spring Security

## 🎨 Dashboards Recomendados

En Grafana, importa estos dashboards:
1. **Spring Boot Dashboard** (ID: 12900)
2. **JVM Dashboard** (ID: 4701)
3. **Spring Boot Statistics** (ID: 6756)

Para importar: Grafana → "+" → Import → Pegar ID → Load

## 🔧 Troubleshooting

### Prometheus no scrapea métricas
- Verifica que la URL en `prometheus.yml` sea correcta
- Revisa los logs en Render: Service → Logs
- Verifica en Prometheus: Status → Targets

### Grafana no conecta con Prometheus
- Verifica que la URL en `datasources/prometheus.yml` sea la correcta
- Asegúrate de que Prometheus esté desplegado y funcionando
- Revisa los logs de Grafana en Render

## 📚 Archivos Importantes

- `prometheus.yml` - Configuración de Prometheus
- `Dockerfile.prometheus` - Imagen Docker para Prometheus
- `Dockerfile.grafana` - Imagen Docker para Grafana
- `grafana/provisioning/` - Configuración automática de Grafana
- `render.yaml` - Configuración de despliegue en Render (debe estar en la raíz)

