# TERRAI — Plataforma de Inteligencia Ambiental

TERRAI permite estudiar un territorio, reconocer sus problemas ambientales,
analizar sus cambios en el tiempo y generar informes técnicos y presentaciones
académicas a partir de evidencia real: fotografías de campo, imágenes
satelitales y datos geoespaciales institucionales.

> Estado actual: **MVP funcional (v0.1.0)**. Ver [docs/architecture.md](docs/architecture.md)
> para el estado real de cada módulo y [docs/data_sources.md](docs/data_sources.md)
> para las condiciones verificadas de cada fuente de datos.

## Módulos

- **ECO-SCAN** — detección de objetos/problemas ambientales en fotografías de campo.
- **TERRA-VISION** — análisis de imágenes satelitales (Sentinel-2 vía Copernicus Data Space Ecosystem).
- **GEO-MONITOR** — mapa interactivo con capas geoespaciales institucionales (Geoservidor MINAM).
- **ECO-REPORT** — generación de informes técnicos reales en .docx.
- **ECO-PRESENTATION** — generación de presentaciones académicas reales en .pptx.
- **ECO-INTELLIGENCE** — (próxima fase) síntesis narrativa con LLM sobre los resultados cuantitativos.

## Requisitos

- Docker y Docker Compose
- Cuenta gratuita en [Copernicus Data Space Ecosystem](https://dataspace.copernicus.eu/) para TERRA-VISION

## Instalación

```bash
git clone <url-de-este-repositorio>
cd terrai
cp .env.example .env
# Edita .env y completa al menos SECRET_KEY y, si quieres usar TERRA-VISION,
# SENTINEL_HUB_CLIENT_ID / SENTINEL_HUB_CLIENT_SECRET.

docker compose up --build
```

- Backend (API + documentación interactiva): http://localhost:8000/docs
- Frontend: http://localhost:3000
- Consola de MinIO (almacenamiento): http://localhost:9001

## Obtener credenciales de Sentinel Hub (gratis)

1. Regístrate en https://dataspace.copernicus.eu/
2. Ve a tu Dashboard → User Settings → OAuth clients → crea un nuevo cliente.
3. Copia el `client_id` y `client_secret` a tu `.env`.
4. Cuota gratuita: 10,000 processing units y 10,000 requests al mes — suficiente
   para pruebas y uso moderado (ver docs/data_sources.md para detalles).

## Fine-tuning del detector de residuos (ECO-SCAN)

Por defecto, ECO-SCAN usa un modelo YOLOv8 genérico (COCO) y lo indica
explícitamente en cada respuesta. Para detección específica de residuos:

```bash
# 1. Descarga TACO (http://tacodataset.org/) o un dataset de Roboflow Universe
#    en formato YOLO.
# 2. Edita backend/app/ml/train_waste_detector.py con la ruta a tu dataset.yaml
cd backend
python -m app.ml.train_waste_detector
# 3. Copia el resultado (best.pt) a:
cp runs/.../best.pt app/ml/models/waste_detector.pt
```

## Estructura del repositorio

```
terrai/
├── backend/        # FastAPI + PostGIS + servicios de IA/satelitales
├── frontend/        # Next.js + MapLibre
├── docs/
│   ├── architecture.md    # Arquitectura y estado real de cada módulo
│   └── data_sources.md    # Condiciones verificadas de cada fuente de datos
├── docker-compose.yml
└── .env.example
```

## Limitaciones conocidas de este MVP

- Solo Sentinel-2 está integrado en TERRA-VISION (Landsat planificado).
- ECO-SCAN usa un modelo genérico hasta que se haga fine-tuning con datos de residuos.
- Las imágenes satelitales (10-60 m/píxel) permiten detectar deforestación, cambios
  de cobertura vegetal y cuerpos de agua — **no objetos pequeños** como botellas o bolsas.
- SINIA, ANA, OEFA y SENAMHI aún no están integrados como fuentes en tiempo real
  (ver docs/data_sources.md).
- ECO-INTELLIGENCE (síntesis narrativa con LLM) está planificado para la siguiente fase.

## Licencia

MIT — ver [LICENSE](LICENSE).
