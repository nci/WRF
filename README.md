# WRF Compilation Guide (Gadi)

This guide describes how to configure and compile WRF on Gadi using PBS.

---

## 0. Clone the repo
```bash
git clone git@github.com:nci/WRF.git
```

---

## 1. Load Environment

```bash
cd WRF
source build.env
```

Optional:

### Enable NetCDF4
```bash
export NETCDF4=1
```

### Enable WRF-Chem
```bash
export WRF_CHEM=1
```

---

## 2. Clean Previous Build (Optional but Recommended)

```bash
./clean -a
```

---

## 3. Configure WRF

Run:

```bash
./configure
```

When prompted, enter:

- **Architecture option** (e.g. `79`)
- **Nest option** (e.g. `1`)

### Common Architecture Options (Gadi)

| Option | Parallel Mode | Optimisation |
|--------|--------------|--------------|
| 76 | serial | -O2 |
| 77 | smpar | -O2 |
| 78 | dmpar | -O2 |
| 79 | sm+dm | -O2 |
| 72 | serial | -O3 |
| 73 | smpar | -O3 |
| 74 | dmpar | -O3 |
| 75 | sm+dm | -O3 |

**Recommended (most users) and Verified:**

```
79
1
```

It will create a file named "configure.wrf".

---

## 4. Submit Compilation Job

Revise the PBS job file compile.pbs:

```bash
nano compile.pbs
```

Change the YOUR_PROEJCT to your own NCI project with SU allocations:

```bash
#!/bin/bash
#PBS -l walltime=3:30:00
#PBS -l mem=28GB
#PBS -l ncpus=7
#PBS -j oe
#PBS -q normalsr
#PBS -l wd
#PBS -W umask=0022
#PBS -l software=intel-compiler
#PBS -l storage=gdata/YOUR_PORJECT

source build.env

# Use half available CPUs for make
export J="-j $(( PBS_NCPUS / 2 ))"

echo "Starting WRF compilation..."
./compile em_real
```

Submit the job:

```bash
qsub compile.pbs
```

---

## 5. Monitor Compilation

```bash
qstat
```

After completion, check for:

```
WRF/main/wrf.exe
WRF/main/real.exe
WRF/main/tc.exe
WRF/main/ndown.exe

```

---

## 7. Quick Workflow (Minimal Commands)

```bash
source build.env
./clean -a
./configure
qsub compile.pbs
```

---

## Notes

- Do **not** compile on login nodes.
- Use `normalsr` or an appropriate queue.
- Typical compile time: 30–40 minutes.
- Use `-O3` options for maximum performance builds.
- Ensure your project storage is correctly declared in the PBS `#PBS -l storage=` line if required.




