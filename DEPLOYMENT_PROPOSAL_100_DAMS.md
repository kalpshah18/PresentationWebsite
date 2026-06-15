# Deployment Proposal for 100-Dam HydroDEM Dashboard

Date: 2026-06-15

## 1. Executive Summary

This proposal defines the recommended infrastructure for deploying the HydroDEM watershed dashboard for approximately 100 dams. The software includes a React frontend, FastAPI backend, DEM and watershed processing tools, dam workspace storage, initial-data management, geospatial outputs, and an optional DBA/EAP quality checker module.

The system is designed to support dam-wise data storage, DEM uploads/downloads, DEM correction, watershed delineation, reservoir polygon extraction, publication-map generation, initial-data versioning, and QA report generation.

For 100 dams, the recommended production setup is:

| Component | Recommendation |
|---|---:|
| Backend server | 16 vCPU, 64 GB RAM |
| Backend system disk | 500 GB SSD |
| Application storage | 10 TB NAS or mounted storage |
| Operating system | Ubuntu 22.04 LTS or Ubuntu 24.04 LTS |
| Python version | Python 3.12 |
| Frontend hosting | Vercel or equivalent static hosting |
| Reverse proxy | Nginx with HTTPS |
| Backup | Daily metadata backup, weekly full storage backup |

This configuration provides enough capacity for 100 dam workspaces, DEM rasters, corrected DEMs, shapefiles, watershed outputs, initial-data versions, QA reports, PDF reports, generated maps, and archive ZIP files.

## 2. Application Overview

The HydroDEM dashboard is a web-based platform for dam and watershed data processing. The current software stack includes:

- Frontend: React with Vite
- Backend: FastAPI with Uvicorn
- Geospatial processing: Rasterio, GeoPandas, Shapely, PyProj, WhiteboxTools, GDAL
- Data handling: Pandas, NumPy, OpenPyXL
- Storage model: file-based dam workspaces and job outputs
- Optional QA module: DBA/EAP quality checker for PDF, CSV, table, figure, and report-readiness review

The backend stores all user uploads, generated datasets, corrected DEMs, watershed outputs, maps, reports, and ZIP archives under a mounted storage directory.

## 3. Recommended Deployment Architecture

The recommended production architecture is:

```text
User Browser
    |
    v
Frontend Hosting
Vercel / Static Hosting
    |
    v
Public API Domain
Nginx + HTTPS
    |
    v
FastAPI Backend Server
Uvicorn workers
    |
    v
Mounted Storage / NAS
/home/watershed-dashboard/storage
```

### 3.1 Frontend

The frontend should be deployed separately on Vercel or another static hosting provider.

Recommended frontend environment variable:

```text
VITE_API_BASE_URL=https://your-api-domain.com/api
```

The frontend does not require access to NAS credentials or backend filesystem paths.

### 3.2 Backend

The backend should run on a Linux server using:

- FastAPI
- Uvicorn
- systemd service
- Nginx reverse proxy
- HTTPS certificate
- Mounted NAS or attached storage

Recommended backend code path:

```text
/opt/watershed-dashboard
```

Recommended production storage path:

```text
/home/watershed-dashboard/storage
```

### 3.3 Storage

All application data should be stored under:

```text
/home/watershed-dashboard/storage
```

The backend should not use the NAS browser/admin URL as a file path. The NAS must be mounted as a local filesystem path on the backend server.

## 4. Server Requirements for 100 Dams

### 4.1 Recommended Production Server

| Resource | Recommendation |
|---|---:|
| CPU | 16 vCPU |
| RAM | 64 GB |
| System disk | 500 GB SSD |
| Storage | 10 TB NAS or mounted storage |
| Network | 1 Gbps minimum, 10 Gbps preferred |
| OS | Ubuntu 22.04 LTS or Ubuntu 24.04 LTS |
| Python | 3.12 |
| Uvicorn workers | 2 to 4 |

This configuration is suitable for 100 dam workspaces and moderate concurrent usage.

### 4.2 Minimum Acceptable Server

| Resource | Minimum |
|---|---:|
| CPU | 8 vCPU |
| RAM | 32 GB |
| System disk | 250 GB SSD |
| Storage | 5 TB NAS or mounted storage |
| Network | 1 Gbps |

The minimum configuration can run the system, but it may become slow for large DEM processing, multiple concurrent users, or repeated watershed runs.

### 4.3 Heavy Usage Configuration

| Resource | Heavy Usage |
|---|---:|
| CPU | 16 to 32 vCPU |
| RAM | 64 to 128 GB |
| System disk | 500 GB to 1 TB SSD |
| Storage | 10 TB to 20 TB NAS |
| Network | 10 Gbps preferred |
| Extra | Background worker queue recommended |

This configuration is recommended if many large DEMs are processed frequently or if multiple users run jobs at the same time.

## 5. Storage Requirements

Storage is the most important sizing factor for 100 dams. Each dam may contain:

- Original DEM files
- Corrected DEM files
- Projected DEM files
- Pour-point CSV files
- Reservoir polygons
- Correction polygons
- Watershed job outputs
- Raster overlays
- GeoJSON and shapefile outputs
- Publication maps
- Initial-data XLSX uploads
- Versioned CSV outputs
- QA reports
- Archive ZIP files

### 5.1 Recommended Storage Size

| Usage Pattern | Estimated Storage for 100 Dams |
|---|---:|
| Light usage: PDFs, CSVs, small outputs | 500 GB to 1 TB |
| Normal usage: DEMs, maps, reports, ZIPs | 3 TB to 5 TB |
| Heavy usage: multiple DEM versions and repeated runs | 8 TB to 10 TB+ |

Recommended storage allocation:

```text
10 TB NAS or mounted storage
```

The system should not be deployed with less than 5 TB for a 100-dam production workload.

### 5.2 Recommended Storage Layout

```text
/home/watershed-dashboard/
  storage/
    dams/
      <dam_id>/
        dam.json
        workbooks/
        initial-data/
        quality-checks/
    jobs/
    datasets/
      dems/
      pour-points/
      correction-polygons/
    reservoir-polygons/
    matplotlib/
    tmp/
    archive/
    backups/
    logs/
```

### 5.3 Storage Growth Planning

DEM and watershed processing can create several derived files from one source file. A safe estimate is:

```text
Final storage per dam = 3x to 10x original input size
```

For example, if a dam has 2 GB of original DEM and source files, the generated outputs may consume 6 GB to 20 GB after correction, processing, maps, reports, and archives.

## 6. Computation Requirements

### 6.1 DEM and Watershed Processing

DEM and geospatial workflows are CPU and memory intensive. They use libraries such as Rasterio, GDAL, GeoPandas, Shapely, PyProj, and WhiteboxTools.

Recommended compute capacity:

- 16 vCPU for production
- 64 GB RAM for reliable large DEM handling
- SSD system disk for temporary processing and fast application startup
- NAS or mounted storage for permanent outputs

No GPU is required.

### 6.2 PDF and DBA/EAP Quality Checker

The optional quality checker reads DBA/EAP PDFs, CSVs, figures, and report content. It generates QA summaries, internal review reports, and filled review PDFs.

This workload is less compute-heavy than DEM processing but can consume memory for large PDFs.

Recommended for quality checker:

- 4 to 8 CPU cores available during review runs
- 8 to 16 GB free RAM for large PDFs
- PDF libraries installed on backend

## 7. Required Software Packages

### 7.1 System Packages

Install the following on the backend server:

```bash
sudo apt-get update
sudo apt-get install -y python3.12 python3.12-venv gdal-bin libgdal-dev libspatialindex-dev nginx
```

### 7.2 Backend Python Packages

Core backend dependencies include:

```text
fastapi
uvicorn
python-multipart
httpx
pandas
openpyxl
geopandas
pyogrio
rasterio
shapely
pyproj
whitebox
matplotlib
numpy
rapidfuzz
```

### 7.3 Additional Quality Checker Packages

If the DBA/EAP quality checker is integrated, add:

```text
pypdf
PyMuPDF
Pillow
PyYAML
reportlab
pdfplumber
```

Streamlit is not required for integration into the current React/FastAPI dashboard. Streamlit is only needed for the standalone demo dashboard.

## 8. Backend Environment Configuration

Production backend environment should include:

```text
CORS_ORIGINS=https://your-frontend-domain.com
WATERSHED_NAS_ROOT=/home/watershed-dashboard
WATERSHED_STORAGE_DIR=/home/watershed-dashboard/storage
WATERSHED_STORAGE_REQUIRED_PREFIX=/home/watershed-dashboard/storage
WATERSHED_LULC_TIF=/home/watershed-dashboard/storage/reference-data/lulc/lulc.tif
MAX_UPLOAD_MB=512
SERVE_FRONTEND=false
FRONTEND_DIST_DIR=/opt/watershed-dashboard/frontend/dist
OPENTOPO_API_KEY=
```

Secrets and credentials must not be committed to the source repository. They should be stored in:

```text
/etc/watershed-dashboard/backend.env
```

## 9. Service Deployment

The backend should run as a systemd service using Uvicorn.

Recommended process:

```text
Nginx public HTTPS endpoint
    -> 127.0.0.1:8000
    -> Uvicorn
    -> FastAPI backend
```

Recommended Uvicorn workers:

```text
2 to 4 workers
```

For very heavy processing, long-running DEM jobs should eventually be moved to a background worker queue so that API requests remain responsive.

## 10. Backup and Retention Plan

For 100 dams, backup planning is essential.

### 10.1 Recommended Backup Schedule

| Backup Type | Frequency | Contents |
|---|---|---|
| Metadata backup | Daily | JSON, CSV, dam records, run manifests |
| Report backup | Daily | QA reports, final PDFs, generated summaries |
| Full storage backup | Weekly | Complete storage directory |
| Archive snapshot | Monthly | Long-term archive of all dam data |

### 10.2 Suggested Retention

| Backup | Retention |
|---|---:|
| Daily metadata/report backups | 30 days |
| Weekly full backups | 8 to 12 weeks |
| Monthly archive snapshots | 12 months or more |

## 11. Security Requirements

Recommended security practices:

- Use HTTPS for all public traffic.
- Keep backend behind Nginx.
- Do not expose Uvicorn directly to the public internet.
- Store environment variables in protected server files.
- Do not commit `.env` files or NAS credentials.
- Restrict write access to the mounted storage folder.
- Configure CORS only for approved frontend domains.
- Keep NAS credentials outside the repository.
- Use server firewall rules to allow only required ports.

Recommended open ports:

```text
80   HTTP, for redirect/certificate renewal
443  HTTPS
22   SSH, restricted to admins
```

## 12. Monitoring and Maintenance

Recommended monitoring:

- Backend health endpoint: `/api/health`
- Disk usage of `/home/watershed-dashboard/storage`
- NAS mount availability
- CPU and RAM usage during DEM jobs
- Nginx logs
- Backend systemd logs
- Failed upload or job records

Recommended routine checks:

```bash
systemctl status watershed-dashboard-api
curl http://127.0.0.1:8000/api/health
df -h /home/watershed-dashboard/storage
test -w /home/watershed-dashboard/storage
```

## 13. Scalability Plan

The proposed 16 vCPU, 64 GB RAM, 10 TB storage configuration is suitable for the first production deployment for 100 dams.

If usage increases, scale in this order:

1. Increase NAS/storage capacity.
2. Increase backend RAM from 64 GB to 128 GB.
3. Add background workers for DEM and QA jobs.
4. Separate API server and processing worker server.
5. Add job queue and job status tracking.
6. Add database if file-based metadata becomes difficult to manage.

## 14. Proposed Final Configuration

The recommended final configuration for 100 dams is:

```text
Application server:
  16 vCPU
  64 GB RAM
  500 GB SSD
  Ubuntu 22.04 or 24.04 LTS
  Python 3.12
  Nginx + Uvicorn + FastAPI

Storage:
  10 TB NAS or mounted storage
  Mounted at /home/watershed-dashboard
  Application storage at /home/watershed-dashboard/storage

Frontend:
  Vercel or equivalent static hosting
  API base URL pointed to backend HTTPS domain

Backup:
  Daily metadata and report backup
  Weekly full storage backup
  Monthly archive snapshot
```

## 15. Conclusion

For a 100-dam deployment, the system should be deployed with a dedicated backend server and a dedicated mounted storage location. The recommended production configuration is 16 vCPU, 64 GB RAM, 500 GB SSD, and 10 TB NAS storage. This provides sufficient capacity for DEM processing, dam workspace management, watershed job outputs, report generation, initial-data versioning, and optional DBA/EAP quality review.

This configuration is production-ready while still allowing future scaling through larger storage, more RAM, background workers, and separation of API and processing workloads.
