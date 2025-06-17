# Pavics Tools: Forcing Data Downloaders

This repository provides scripts and functions to download and prepare gridded forcing datasets for hydrologic modeling with Raven. The tools support multiple data sources including DAYMET reanalysis, PAVICS RDRS v2.1 reanalysis, and CMIP6 bias-adjusted projections.

---

## 1. Prerequisites

* **R** (>= 4.0) with packages: `sf`, `dplyr`, `progress`, `ncdf4`, `rmapshaper`, `lubridate`, `imputeTS`, `raster`.
* **Python** (>= 3.7) with libraries: `numpy`, `xarray`, `geopandas`, `siphon`, `tqdm`, `pandas`, `scipy`, `rioxarray`, `birdy`, `ravenpy`, `pyproj`, `shapely`, `rasterio`.
* `git`, and a Jupyter environment for Python or R kernels.

---

## 2. Getting the HRU Shapefile

Download and unzip the HRU grid shapefiles entirely within your script.

### 2.1 R

````r
# Define URL and local paths
shp_zip_url <- "https://github.com/rarabzad/Pavics_tools/raw/refs/heads/main/SMM_grids_hrus.zip"
dest_zip    <- file.path(out_dir, "SMM_grids_hrus.zip")
out_dir     <- "hru_shps"

# Create output directory if needed
if (!dir.exists(out_dir)) dir.create(out_dir)

# Download zip file
download.file(shp_zip_url, destfile = dest_zip, mode = "wb")

# Unzip into a subfolder (SMM_grids_hrus) within out_dir
unzip(dest_zip, exdir = out_dir)

# Path to the key shapefile inside the unzipped folder
hru_shp_path <- file.path(out_dir, "SMM_grids_hrus", "hru.shp")
````

---

## 3. Downloading Function Scripts

Fetch function scripts within R or Python without shell utilities.

### 3.1 R (DAYMET -> Raven)

```r
url  <- "https://raw.githubusercontent.com/rarabzad/daymet2Raven/refs/heads/main/daymet2Raven_nc.R"
dest <- "daymet2Raven_nc.R"
if (!file.exists(dest)) download.file(url, destfile = dest, mode = "wb")
source(dest)
```

### 3.2 Python (PAVICS & CMIP6)

```python
import urllib.request, os

def fetch_script(url, filename):
    if not os.path.exists(filename):
        content = urllib.request.urlopen(url).read()
        with open(filename, 'wb') as f:
            f.write(content)

# PAVICS RDRS
fetch_script(
    "https://raw.githubusercontent.com/rarabzad/Pavics_tools/refs/heads/main/RDRS/pavics_rdrs.py",
    "pavics_rdrs.py"
)

# CMIP6 ESPO-G6-R2 Downloader v2
fetch_script(
    "https://raw.githubusercontent.com/rarabzad/Pavics_tools/refs/heads/main/ESPO_G6_R2_Downloader_V2.py",
    "ESPO_G6_R2_Downloader_V2.py"
)
```

---

## 4. Installation

### 4.1 Python Dependencies

```bash
pip install numpy xarray geopandas siphon tqdm pandas scipy rioxarray birdy ravenpy pyproj shapely rasterio
```

### 4.2 R Dependencies

```r
install.packages(c(
  "sf", "dplyr", "progress", "ncdf4", "rmapshaper", "lubridate", "imputeTS", "raster"
))
```

---

## 5. Usage Examples

### 5.1 R: DAYMET to Raven (`daymet2Raven_nc`)

```r
# In R or R notebook
source('daymet2Raven_nc.R')

daymet2Raven_nc(
  hru_shp_file = 'hru_shps/SMM_grids_hrus/hru.shp',
  start_date   = '1980-01-01',
  end_date     = '2018-12-31',
  grid_size    = c(0.05, 0.05),
  HRU_ID       = 'HRU_ID',
  outdir       = 'outputs/daymet',
  plot         = TRUE
)
```

### 5.2 Python: PAVICS RDRS Reanalysis (`pavics_rdrs.py`)

```python
# In Python notebook
import urllib.request
exec(urllib.request.urlopen(
    "https://raw.githubusercontent.com/rarabzad/Pavics_tools/refs/heads/main/RDRS/pavics_rdrs.py"
).read())

from pavics_rdrs import process_climate_data
process_climate_data(shapefile_path='hru_shps/SMM_grids_hrus/hru.shp'))
```

### 5.3 Python: CMIP6 Projections (`ESPO_G6_R2_Downloader_V2.py`)

```python
# In Python notebook
import urllib.request
exec(urllib.request.urlopen(
    "https://raw.githubusercontent.com/rarabzad/Pavics_tools/refs/heads/main/ESPO_G6_R2_Downloader_V2.py"
).read())

from ESPO_G6_R2_Downloader_V2 import ESPO_G6_R2_Downloader
ESPO_G6_R2_Downloader(
  model_name   = 'AS-RCEC_TaiESM1',
  scenario     = 'ssp245',
  hrufile_path = 'hru_shps/SMM_grids_hrus/hru.shp',
  HRU_ID       = 'HRU_ID'
),
  HRU_ID       = 'HRU_ID'
)
```

---

## 6. Jupyter Notebook Workflows

### 6.1 PAVICS RDRS Reanalysis

```python
# Dependencies cell
!pip install numpy xarray geopandas siphon tqdm pandas scipy rioxarray birdy ravenpy pyproj shapely rasterio

# Load and run script
ing i = 'https://raw.githubusercontent.com/rarabzad/Pavics_tools/refs/heads/main/RDRS/pavics_rdrs.py'
exec(urllib.request.urlopen(i).read())
from pavics_rdrs import process_climate_data
process_climate_data(shapefile_path='hru_shps/SMM_grids_hrus/hru.shp'))
```

### 6.2 CMIP6 ESPO-G6-R2 Projections

```python
!pip install numpy xarray geopandas siphon tqdm pandas scipy rioxarray birdy ravenpy pyproj shapely rasterio

u = 'https://raw.githubusercontent.com/rarabzad/Pavics_tools/refs/heads/main/ESPO_G6_R2_Downloader_V2.py'
exec(urllib.request.urlopen(u).read())
from ESPO_G6_R2_Downloader_V2 import ESPO_G6_R2_Downloader
ESPO_G6_R2_Downloader(model_name='AS-RCEC_TaiESM1', scenario='ssp245', hrufile_path='hru_shps/SMM_grids_hrus.shp', HRU_ID='HRU_ID')
```

### 6.3 R: DAYMET to Raven Notebook

```r
install.packages(c("daymetr","sf","dplyr","progress","ncdf4","rmapshaper","lubridate","imputeTS","raster"))

url  <- "https://raw.githubusercontent.com/rarabzad/daymet2Raven/refs/heads/main/daymet2Raven_nc.R"
f   <- "daymet2Raven_nc.R"
if (!file.exists(f)) download.file(url, destfile=f, mode="wb")
source(f)

daymet2Raven_nc(
  hru_shp_file='hru_shps/SMM_grids_hrus.shp',
  start_date='1980-01-01',
  end_date='2018-12-31',
  grid_size=c(0.05,0.05),
  HRU_ID='HRU_ID',
  outdir='outputs/daymet',
  plot=TRUE
)
```

---

## 7. Configuration

* Adjust `grid_size` or buffer values in downloader parameters.
* Change `model_name`/`scenario` for other CMIP6 simulations.

---

## 8. License

MIT © Rezgar Arabzadeh
