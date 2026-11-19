# Project Wildfire

PHYSM0070 Data Intensive Physics (University of Bristol) – Earth Monitoring project.
Analysis of Earth-observation datasets (e.g. NASA GLDAS land-surface data and fire
observations) to study relationships between environmental variables and wildfire
occurrence, and to compare analysis methods. Runs on the Blue Pebble HPC cluster
using Slurm.

## Structure

```
Project_Wildfire/
├── README.md
├── environment.yml                     # mamba environment recipe (env: wildfire)
├── .gitignore                          # excludes data, logs and build files
│
├── notes/
│   ├── progress.md                     # dated log: work done, job IDs, results
│   └── data_sources.md                 # each source: origin, download method, format
│
├── templates/
│   └── slurm_serial.sh                 # base Slurm job script, copied into jobs/
│
├── jobs/                               # Slurm job scripts, submitted from repo root
│   └── explore_temperature_gldas.sh    # runs scripts/explore_temperature_gldas.py
│
├── scripts/                            # Python entry points, one task each
│   ├── explore_temperature_gldas.py    # inspect raw temperature data
│   ├── process_temperature_gldas.py    # raw → data/processed/temperature/
│   └── align.py                        # processed → data/aligned/
│
├── src/                                # reusable functions imported by scripts/
│   ├── __init__.py
│   ├── loading.py                      # file readers (NetCDF, HDF5, CSV)
│   ├── processing.py                   # subsetting, regridding, aggregation
│   └── plotting.py                     # shared plotting functions
│
├── figures/                            # plots saved by scripts
├── report/                             # report drafts
├── logs/                               # job output, <job>-<jobid>.out (not tracked)
│
└── data/                               # symlink to /user/work/es23367/datasets (not tracked)
    ├── raw/                            # original downloads, never modified
    │   ├── temperature/
    │   │   └── gldas/
    │   └── fire/                       # one subfolder per source
    ├── processed/                      # cleaned data, one folder per variable
    │   ├── temperature/
    │   └── fire/
    ├── aligned/                        # all variables on a common grid and time axis
    └── results/                        # large analysis outputs
```

## Naming

- Scripts: `<step>_<variable>_<source>.py`, e.g. `process_fire_modis.py`
- Job scripts: same name as the script they run, with `.sh`
- Raw data: `data/raw/<variable>/<source>/`

## Pipeline

1. **Download** to `data/raw/<variable>/<source>/` and record in `notes/data_sources.md`
2. **Explore** with `explore_<variable>_<source>.py`
3. **Process** to the study region and period with `process_<variable>_<source>.py`
4. **Align** all processed variables with `align.py`
5. **Analyse** with `analysis_<method>.py` → `figures/`, `data/results/`

Raw data is never modified, so every later stage can be regenerated from the scripts.

## Running jobs

From the repository root:

```bash
sbatch jobs/<job>.sh
squeue -u $USER
```

Printed output and errors go to `logs/<job>-<jobid>.out`; figures and data files are
saved where each script specifies. Jobs use partition `teach_cpu`, account `phys040684`.

