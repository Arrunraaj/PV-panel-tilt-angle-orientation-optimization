# PV Panel Orientation and Tilt Angle Optimization

![Python](https://img.shields.io/badge/python-3.8%2B-blue)
![Jupyter](https://img.shields.io/badge/jupyter-notebook-orange)
![License](https://img.shields.io/badge/license-MIT-green)

Finds the fixed tilt (β) and azimuth (γ) that maximise annual plane-of-array (POA) irradiance for a solar PV installation, using 8,760-hour weather data for five sites in India.

Rather than assuming the usual rule of thumb — tilt equal to latitude, facing due south — this notebook computes the full annual irradiance sum for any orientation, searches numerically for the optimum, and quantifies how much is actually gained. A bifacial-module case estimates the additional rear-side yield.

## Features

- Hourly solar geometry: declination, equation of time, local apparent time and hour angle (IST reference, 82.5°E)
- Angle of incidence and zenith angle from latitude, declination and hour angle
- Liu–Jordan isotropic sky model: beam, isotropic diffuse and ground-reflected components (albedo 0.2)
- Bounded optimisation of β ∈ [0°, 90°] and γ ∈ [−180°, 180°] via `scipy.optimize.minimize` (L-BFGS-B)
- Bifacial model with rear-side gain (bifaciality 0.95, view factor 0.5 on GHI)
- Azimuth sweep in 45° steps with an annual-irradiance-vs-orientation plot



## Sites

| Site | Latitude | Longitude |
|---|---|---|
| Erode, Tamil Nadu | 11.56° N | 77.57° E |
| Miryalaguda, Telangana | 16.88° N | 79.57° E |
| Srikakulam, Andhra Pradesh | 18.28° N | 83.91° E |
| IIT Bombay, Mumbai | 19.13° N | 72.90° E |
| Muzaffarpur, Bihar | 26.12° N | 85.39° E |



## Results

| Site | Standard β / γ | Optimal β / γ | Baseline POA (kWh/m²·yr) | Optimised POA | Bifacial POA |
|---|---|---|---|---|---|
| Erode | 11.56° / 0° | 13.57° / −1.45° | 2,089 | 2,089 | 2,284 |
| IIT Bombay | 19.13° / 0° | 24.85° / −21.66° | 1,866 | 1,882 | 2,037 |
| Muzaffarpur | 26.12° / 0° | 26.50° / 1.54° | 1,889 | 1,889 | 2,058 |

At these latitudes the latitude-tilt, due-south rule lands within roughly 1% of optimal, while bifacial modules add close to 10% on the same structure.

## Limitations

- Isotropic sky only
- Fixed-tilt systems only, no single- or dual-axis tracking
- Irradiance is not converted to electrical yield (no module efficiency,shading or inverter losses)
- Fixed ground albedo and a constant bifacial view factor, so row spacing and mounting height are not represented


