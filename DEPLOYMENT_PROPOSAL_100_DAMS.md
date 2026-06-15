# Simple System Requirements for 100 Dams

Date: 2026-06-15

## Purpose

This document gives a short and simple infrastructure requirement plan for running this dam dashboard system for approximately 100 dams.

The system is expected to store dam-wise files, DEM data, corrected DEMs, CSV files, shapefiles, watershed outputs, maps, reports, QA outputs, and ZIP archives.

## Recommended System

For 100 dams, the recommended system is:

| Item | Recommended Requirement |
|---|---:|
| CPU | 16 vCPU |
| RAM | 64 GB |
| Main disk | 500 GB SSD |
| Storage | 10 TB usable storage |
| Network | 1 Gbps minimum |
| Operating system | Ubuntu Linux |
| GPU | Not required |

This is the best practical starting configuration for stable use with 100 dams.

## Minimum System

This can run the system, but may become slow for large DEM files or many repeated runs.

| Item | Minimum Requirement |
|---|---:|
| CPU | 8 vCPU |
| RAM | 32 GB |
| Main disk | 250 GB SSD |
| Storage | 5 TB usable storage |
| Network | 1 Gbps |
| GPU | Not required |

## Heavy Usage System

Use this if many users will work at the same time or if large DEM processing will be frequent.

| Item | Heavy Usage Requirement |
|---|---:|
| CPU | 24 to 32 vCPU |
| RAM | 128 GB |
| Main disk | 1 TB SSD |
| Storage | 15 TB to 20 TB usable storage |
| Network | 10 Gbps preferred |
| GPU | Not required |

## Storage Requirement

For 100 dams, storage is the most important requirement.

Recommended storage:

```text
10 TB usable storage
```

Minimum storage:

```text
5 TB usable storage
```

Heavy usage storage:

```text
15 TB to 20 TB usable storage
```

## Why 10 TB Storage Is Recommended

Each dam may include:

- DEM files
- Corrected DEM files
- Watershed outputs
- Shapefiles
- GeoJSON files
- CSV files
- Excel files
- PDF reports
- QA reports
- Map outputs
- ZIP archives
- Multiple versions of corrected data

For one dam, storage may range from a few GB to more than 100 GB depending on DEM size and number of processing runs.

For 100 dams:

| Usage Type | Estimated Storage |
|---|---:|
| Light usage | 1 TB |
| Normal usage | 3 TB to 5 TB |
| Heavy usage | 8 TB to 10 TB+ |

Therefore, **10 TB** is recommended so the system has enough working space and future growth capacity.

## Computation Requirement

The system does not need a GPU.

The main processing load comes from:

- DEM processing
- Raster correction
- Watershed generation
- Map generation
- File compression
- PDF/QA report checking

Recommended compute:

```text
16 vCPU and 64 GB RAM
```

This gives enough capacity for normal production use with 100 dams.

## Backup Requirement

Backups are required because dam data, reports, and generated outputs are important project records.

Recommended backup plan:

| Backup Type | Frequency |
|---|---|
| Important CSV/JSON/report backup | Daily |
| Full storage backup | Weekly |
| Archive snapshot | Monthly |

Recommended backup storage:

```text
At least same size as main storage, preferably 10 TB or more
```

## Free Space Requirement

Always keep at least:

```text
20% to 30% storage free
```

For a 10 TB system, try to keep:

```text
2 TB to 3 TB free
```

This is important because DEM processing and ZIP creation need temporary working space.

## Final Simple Recommendation

For 100 dams, use:

```text
16 vCPU
64 GB RAM
500 GB SSD main disk
10 TB usable storage
1 Gbps network minimum
Ubuntu Linux
No GPU required
Daily and weekly backups
```

This configuration is simple, practical, and suitable for production use for approximately 100 dams.
