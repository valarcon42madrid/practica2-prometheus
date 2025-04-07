# Práctica de Observabilidad

Este proyecto contiene un entorno de monitoreo basado en **Docker Compose** que levanta:

- Tres servicios de prueba (`prometheus-service-demo-{0,1,2}`)
- Prometheus (para recolección de métricas)
- Grafana (para visualización)
- Node Exporter (para métricas del sistema)

---

## 📦 Servicios levantados

```bash
docker-compose up -d
```

Esto inicia:

- `prometheus-service-demo-0` en `localhost:10000`
- `prometheus-service-demo-1` en `localhost:20000`
- `prometheus-service-demo-2` en `localhost:30000`
- `prometheus` en `localhost:9090`
- `grafana` en `localhost:3000` (usuario: `admin`, contraseña: `admin`)
- `node-exporter` en `localhost:9100`

---

## 📊 Dashboards de Grafana

Los dashboards están en la carpeta:

```
grafana/dashboards/
```

Y se cargan automáticamente gracias al provisioning de Grafana.

---

## 📈 Métricas incluidas

El sistema mide:

- **Duración de solicitudes HTTP**
  - Percentil 90 (P90), Percentil 95 (P95)
  - Promedio por ruta y método
- **Recursos de cómputo del sistema**
  - Número de CPUs (núcleos lógicos)
  - Uso del sistema de archivos raíz `/`, con alertas visuales al superar 80% y 90%

---

## 📂 Estructura recomendada de dashboards

- `dashboard-metricas-http.json` → Mide P90, P95 y promedio de latencia HTTP
- `dashboard-recursos-computo-cpus-logicos.json` → Mide CPUs y uso del disco `/` en una fila dedicada

---

## ✅ Requisitos

- Docker y Docker Compose instalados
- Acceso a puertos `10000`, `20000`, `30000`, `9090`, `9100`, `3000`

---
