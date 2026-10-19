---
title: "RC shared cluster: software (modules, Python, containers, databases)"
topic: Cloud
description: "Find and load software on the RC shared cluster: Lmod modules, compilers and MPI, the site Python environments, containers, shared reference databases, the astronomy stack and NAMD on the GPU."
audience: "Research Computing staff and close collaborators who have been given access to the RC shared cluster"
reviewed: 2026-10-19
owner: Research Computing
unlisted: true
sitemap: false
review_notes:
  - "Unlisted on purpose (2026-10-19): same audience and mechanism as the getting-started page in this set."
  - "Module names and versions follow the cluster's module tree, its quick start and its tool index as read on 2026-10-19. Each astronomy, gravitational-wave and molecular-dynamics module was published only after a functional test on the cluster (2026-10-18)."
  - "Versions change: module avail and module spider on the cluster are the source of truth; this page names a version only where users need it in a load line."
---

The RC shared cluster has a large shared software stack: compilers and MPI, about 500 modules built with Spack, site Python environments, containers, and public reference databases. You load what you need with Lmod. This page shows how to find things and how the main pieces fit.

Other guides in this set: [Getting started](../rc-cluster-getting-started/), [Web portal](../rc-cluster-web-portal/), [AI assistant](../rc-cluster-ai-assistant/).

## Find and load software

```bash
module avail                  # what can be loaded now
module spider gromacs         # find anything, including MPI builds, and how to load it
module keyword assembly       # search descriptions
module load gcc/13.5.0        # compilers: gcc 12.2, 13.5 (default), 14.4, 15.3, 16.2, Intel oneAPI
module list                   # what is loaded
ursa-software                 # a short summary of the stack
```

A module brings what it needs: `module load nextflow` also loads Java, and an MPI loads the compiler it was built with.

Every module's installed `--help` text, plus a one-line summary, is in the tool index at `/apps/docs/tools/` on the cluster.

Check software inside a job or a `check` partition test, not on the login node. In a batch script, `module load` works in a login-style shell (`#!/bin/bash` scripts submitted with `sbatch` are fine).

## MPI and libraries

MPI libraries and the packages built against them sit one level down. Load an MPI first, then they appear:

```bash
module load openmpi           # or mpich, or intel-oneapi-mpi
module load hdf5 fftw petsc   # MPI builds become visible
```

MPI applications load Open MPI for you: `module load lammps` works on its own. Load `mpich` first to get the MPICH builds instead. Always start MPI programs with `srun`.

When you compile against a library, load its module and link against its root (or use the package's own wrapper), because the operating system has older copies of some libraries:

```bash
module load openmpi; module load hdf5
h5pcc my.c -o my              # or: mpicc my.c -L$HDF5_ROOT/lib -Wl,-rpath,$HDF5_ROOT/lib -lhdf5
```

## Python

Load one site Python environment per job. Each is a complete environment whose packages were installed and tested together:

| Module | What it is for |
| --- | --- |
| `python-sci` (default `2026.09`) | CPU science: numpy, scipy, pandas, matplotlib, xarray, astropy, geopandas, biopython, JupyterLab |
| `python-sci/2026.10` | everything above plus astronomy: healpy, CAMB, CLASS, Cobaya, emcee, dynesty, yt, photutils, REBOUND, GalSim, lenstronomy, python-casacore |
| `python-gw/2026.10` | gravitational waves: LALSuite, Bilby, PyCBC, GWpy (kept separate because its versions clash with the general stack) |
| `python-ml` | PyTorch with CUDA, transformers; use `gpul4` for the GPU |

To add packages, layer a virtual environment on top:

```bash
module load python-sci
uv venv --system-site-packages ~/venvs/myproj
source ~/venvs/myproj/bin/activate
uv pip install somepackage
```

That suits pure-Python packages. A wheel that ships its own C++ runtime (OR-Tools, for example) can crash against the module's libraries; give it a plain venv instead. For your own environments, `uv`, `pixi` and `module load miniforge3` (conda) all work.

## Containers

Apptainer runs containers without root:

```bash
module load apptainer
apptainer exec /apps/containers/python-3.12.sif python3 --version
apptainer exec --nv /apps/containers/pytorch-2.5.1-cuda12.4.sif python3 my.py   # --nv on gpul4
```

Shared images are in `/apps/containers` (PyTorch, vLLM, R tidyverse, AlphaFold/ColabFold, DeepVariant, a CUDA development image and others). You can pull your own from Docker Hub with `apptainer pull docker://...`; keep big images in `$SCRATCH`.

## Reference databases

Public reference databases are in `/data/shared`, read-only, one `db-*` module each. Don't download your own copy. Each module sets the variable its tool reads:

```bash
module avail db-
module load blast-plus db-ncbi-blast     # BLASTDB: core_nt, nr, swissprot, pdbaa, taxdb
blastn -db core_nt -query q.fa -out hits.tsv -outfmt 6 -num_threads $SLURM_CPUS_PER_TASK
module load diamond db-diamond-nr        # DIAMOND_DB
module load gtdbtk db-gtdbtk             # GTDBTK_DATA_PATH
```

Also available: UniRef90, Swiss-Prot, Kraken2 standard, Kaiju, Bakta, eggNOG, InterProScan, Pfam, PANTHER, Rfam, MEROPS, CheckV, CheckM2, BUSCO and dbCAN.

The databases are read from one shared disk that the whole cluster shares, so put all your queries in one job rather than many small ones. A DIAMOND search of nr takes about two hours however many proteins you send. Kraken2 and Kaiju load their whole index into memory (110 and 145 GB): use `highmem`.

## Astronomy

| Module | What it is |
| --- | --- |
| `casa/6.7.6-14` | radio interferometry (ALMA, VLA) |
| `heasoft/6.37.1` | X-ray astronomy: XSPEC, FTOOLS |
| `mesa/26.04.1` | stellar evolution |
| `source-extractor`, `swarp`, `scamp`, `psfex`, `astrometry` | image catalogs, mosaics, astrometric calibration (astrometry.net) |
| `casacore`, `wcslib`, `healpix-cxx`, `montage`, `kokkos` | libraries |

The MESA module sets `MESA_DIR`. Copy a work directory to scratch and run there:

```bash
module load mesa/26.04.1
cp -r $MESA_DIR/star/work $SCRATCH/my_star && cd $SCRATCH/my_star
./mk && ./rn
```

Simulation codes such as GADGET-4, SWIFT or Athena++ are not central modules: build them for your project against the site MPI, HDF5, FFTW and GSL modules. We can help.

## Molecular dynamics on the GPU

NAMD 3 runs from a container on the `gpul4` partition:

```bash
#!/bin/bash
#SBATCH --partition=gpul4
#SBATCH --gres=gpu:1
#SBATCH --cpus-per-task=8
#SBATCH --time=02:00:00
module load namd/3.0.1
namd-run namd3 +p8 +devices 0 +setcpuaffinity my.namd
```

Add `GPUresident on` to your NAMD configuration for the fastest single-GPU runs. The ApoA1 benchmark runs at roughly 20 to 40 ns/day on one L4. `namd-run` also works from a terminal in a [web portal](../rc-cluster-web-portal/) Desktop.

GROMACS is also installed (`module load gromacs`).

`vmd/1.9.4a44` is a build without a display, for scripted analysis and rendering:

```bash
module load vmd/1.9.4a44
vmd-run vmd -dispdev text -e script.tcl
```

## Asking for software

If something is missing, email [research-computing@ucr.edu](mailto:research-computing@ucr.edu) with the package, the version and what you will run. Many tools can also go in your own environment with `uv`, `pixi` or conda, or in a container, without waiting for us.
