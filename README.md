# PV Panel Tilt and Orientation Optimization

A Python model that calculates the optimal tilt (β) and azimuth (γ) angles for fixed photovoltaic panels to maximize annual solar energy yield, using real hourly weather data across multiple locations in India.

## Overview

Fixed-mount PV panels are typically installed at a tilt equal to the site's latitude, facing due south (in the northern hemisphere). This project tests whether that "standard" configuration is actually optimal, or whether a numerically optimized tilt and azimuth can capture more solar energy over a full year.

The model:
1. Computes solar geometry (declination angle, hour angle) for every hour of the year at a given site
2. Estimates plane-of-array (POA) irradiance using the **Liu-Jordan isotropic sky diffuse model**, combining direct normal irradiance (DNI) and diffuse horizontal irradiance (DHI)
3. Uses **SciPy's L-BFGS-B optimizer** to solve for the tilt and azimuth angles that maximize total annual POA irradiance
4. Compares the optimized configuration against the standard configuration (tilt = latitude, azimuth = 0°)
5. Extends the analysis to **bifacial panels**, which also capture reflected irradiance on their rear side

## Locations Analyzed

| Site | Latitude | Longitude |
|---|---|---|
| Anthiyur, Tamil Nadu | 11.56° | 77.57° |
| Miryalaguda, Telangana | 16.88° | 79.57° |
| Srikakulam, Andhra Pradesh | 18.28° | 83.91° |
| IIT Bombay, Maharashtra | 19.13° | 72.9° |
| Muzaffarpur, Bihar | 26.12° | 85.39° |

Each site uses a full year of hourly TMY (Typical Meteorological Year) weather data, including DNI and DHI values.

## Methodology

**Solar geometry**
- Declination angle (δ) is calculated per day using the standard Cooper's equation.
- The hour angle (ω) is derived from local apparent time, corrected for the equation of time and longitude offset from the site's standard meridian.

**Irradiance model (Liu-Jordan isotropic model)**
- Beam irradiance on the tilted surface is scaled by the cosine of the angle of incidence (cos θ).
- Diffuse irradiance is scaled by a sky-view factor, (1 + cos β) / 2.
- Ground-reflected irradiance is added using a fixed ground reflectivity (albedo) of 0.2.

**Optimization**
- Objective function: total annual POA irradiance (summed hourly `EPOA` values), negated for minimization.
- Variables: tilt (β, bounded 0°–90°) and azimuth (γ, bounded -180° to 180°).
- Solver: `scipy.optimize.minimize` with the `L-BFGS-B` method.

**Bifacial extension**
- Adds a rear-side irradiance contribution based on ground-reflected and diffuse components, to compare monofacial vs. bifacial annual yield at the standard configuration.

## Sample Results

| Site | Standard (β = lat, γ = 0°) | Optimized β, γ | Improvement |
|---|---|---|---|
| Anthiyur | 11.56°, 0° | 13.57°, -1.45° | ~0.04% |
| Miryalaguda | 16.88°, 0° | 19.09°, -2.33° | ~0.05% |
| Srikakulam | 18.28°, 0° | 20.11°, 1.14° | ~0.03% |
| IIT Bombay | 19.13°, 0° | 24.85°, -21.66° | ~0.87% |
| Muzaffarpur | 26.12°, 0° | 26.50°, 1.54° | ~0.005% |

Gains from re-optimizing tilt and azimuth are generally small at sites close to their latitude-optimal baseline, but more meaningful at sites with atypical local weather/cloud patterns (e.g., IIT Bombay), where the optimizer shifts the azimuth substantially away from due south.

## Tech Stack

- Python (pandas, NumPy, SciPy, math)
- Weather data source: TMY hourly `.xlsx` files (DNI, DHI, GHI)

## Repository Structure

- * [PV panel tilt and orientation optimization.ipynb](https://github.com/Arrunraaj/PV-panel-tilt-angle-orientation-optimization/blob/main/PV%20Panel%20tilt%20and%20Orientation%20optimization.ipynb) — main analysis notebook: data loading, solar geometry, irradiance modeling, optimization, and results for all five sites (monofacial and bifacial)

## Possible Extensions

- Replace the isotropic sky model with an anisotropic model (e.g., Perez or Hay-Davies) for more accurate diffuse irradiance estimates
- Add seasonal or monthly tilt adjustment instead of a single fixed annual optimum
- Validate modeled irradiance against measured on-site generation data
