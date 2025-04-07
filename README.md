# 📡 Práctica de Observabilidad con Prometheus y Grafana

Este proyecto proporciona un entorno completo de observabilidad utilizando **Docker Compose**, ideal para prácticas de monitoreo y visualización de métricas.

Incluye:

- 🔁 Tres servicios de prueba (`prometheus-service-demo-{0,1,2}`)
- 📈 Prometheus para la recolección de métricas
- 📊 Grafana para visualización de dashboards
- 🖥️ Node Exporter para métricas del sistema anfitrión

---

## 🚀 Puesta en marcha

Levantar todos los servicios:

```bash
docker-compose up -d
```

Esto inicia:

| Servicio                        | Puerto        |
|--------------------------------|---------------|
| prometheus-service-demo-0       | `localhost:10000` |
| prometheus-service-demo-1       | `localhost:20000` |
| prometheus-service-demo-2       | `localhost:30000` |
| Prometheus                      | `localhost:9090`  |
| Grafana                         | `localhost:3000`  |
| Node Exporter                   | `localhost:9100`  |

> 🧑‍💻 **Grafana**: Usuario `admin` / Contraseña `admin`

---

## 📂 Dashboards incluidos

Los dashboards están definidos en la carpeta:

```bash
grafana/dashboards/
```

Y se cargan automáticamente mediante la configuración de **provisioning**.

### 🧭 Estructura recomendada

- `dashboard-metricas-http.json`: métricas HTTP de la API (latencias P90, P95 y promedio)
- `dashboard-recursos-computo-cpus-logicos.json`: métricas de recursos del sistema (`CPUs` y disco raíz `/`)

---

## 📈 Métricas observadas

### 🔹 HTTP de la aplicación demo

- Percentil 90 (P90) y 95 (P95) de duración de solicitudes
- Promedio de latencia por método y ruta
- Agrupadas por `method`, `path` y `status`

> 📌 Importante: las métricas reales del demo tienen prefijos `demo_`, por ejemplo `demo_api_request_duration_seconds_bucket`.

### 🔹 Recursos del sistema (Node Exporter)

- Número de CPUs (lógicos)
- Porcentaje de uso del sistema de archivos raíz `/`
  - 🟠 Advertencia a partir del 80%
  - 🔴 Crítico a partir del 90%

---

## ❗ Dificultades encontradas

### 🔸 1. Métricas con nombres no estándar

Aunque el enunciado hace referencia a métricas como `http_request_duration_seconds_bucket`, los servicios de prueba (`julius/prometheus-demo-service`) exponen métricas con el prefijo `demo_`, por ejemplo:

```
demo_api_request_duration_seconds_bucket
```

Esto obligó a adaptar todas las consultas y dashboards para reflejar el nombre real de las métricas.

### 🔸 2. Node Exporter sin acceso a los recursos del host

Por defecto, si `node-exporter` se ejecuta en un contenedor **sin los volúmenes adecuados montados**, solamente ve los recursos del contenedor y no del sistema real. Esto afecta paneles como:

- Número total de CPUs (`count(count(node_cpu_seconds_total) by (cpu))`)
- Espacio disponible en `/`

Para solucionar esto, se añadieron los siguientes volúmenes y parámetros al `docker-compose.yml`:

```yaml
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    command:
      - '--path.procfs=/host/proc'
      - '--path.sysfs=/host/sys'
      - '--path.rootfs=/rootfs'
```

De este modo, `node-exporter` accede correctamente a los datos del sistema anfitrión.

---

## ✅ Requisitos

- Docker + Docker Compose instalados
- Sistema con acceso a puertos: `10000`, `20000`, `30000`, `9090`, `9100`, `3000`

---

## 🧪 Tips útiles

Podés simular tráfico hacia los servicios demo para generar métricas útiles con:

```bash
while true; do
  curl -s http://localhost:10000/api/foo > /dev/null
  curl -s http://localhost:10000/api/bar > /dev/null
  curl -s http://localhost:10000/api/nonexistent > /dev/null
  sleep 0.5
done
```

Esto activa las métricas `demo_api_request_duration_seconds_*` necesarias para los dashboards.

---

## 🧾 Licencia

Este proyecto es parte de una práctica educativa. Libre de uso con fines formativos.