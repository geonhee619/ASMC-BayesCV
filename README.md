# ASMC-BayesCV

This repository contains computer codes, output files, and figures for the paper:

Han, G. and Gelman, A. (2025) **"Adaptive sequential Monte Carlo for cross-validation in structural Bayesian hierarchical models"**. <https://arxiv.org/abs/2501.07685>

```bibtex
@misc{HanGelman2025adaptiveSequentialMonteCarlo,
      title={Adaptive sequential Monte Carlo for automated cross validation in structural Bayesian hierarchical models}, 
      author={Geonhee Han and Andrew Gelman},
      year={2025},
      eprint={2501.07685},
      archivePrefix={arXiv},
      primaryClass={stat.CO},
      url={https://arxiv.org/abs/2501.07685}, 
}
```

**Table of contents**:
- [Contents](#contents)
- [System notes](#system-notes)
- [Instructions (Default)](#instructions-default)
- [Instructions (Google Colab setup)](#instructions-google-colab-setup)
- [Execution flow](#execution-flow)

---

## Contents

- `data/`: Folder containing source data used in the examples.
- `{LGO, LEO, LSO}.ipynb`: Jupyter notebooks to be run in Julia (1.10.4, ideally with multi-threading) top-to-bottom for computation, saving output, and figure generation.
- `output/`: Contains `.jld` files with numerical simulation/computation results.
- `img/`: Generated figures included in the paper.
- Newly generated files will be saved in  `output_[session datetime]/` and  `img_[session datetime]/`.

## System notes

The codes were developed and tested on the following environment.

- **OS**: Windows 11
- **CPU**: 24-core 12th Gen Intel(R) Core(TM) i9-12900K
- **RAM**: 32 GB
- **Julia**: v1.10.4
- **Multithreading configuration**:
  - `JULIA_NUM_THREADS=8` for `{LGO.ipynb, LSO.ipynb}` (w/ HMC)
  - `JULIA_NUM_THREADS=12` for `LEO.ipynb` (w/ full conditional Gibbs sampling)
  - Multithreading is optional.
  - However, it is strongly recommended; the simulations take advantage of parallelizability for efficient computing, and thread counts may directly affect results.

## Instructions (Default)

### Download/install **Julia v1.10.4**
   - Link: [https://julialang.org/downloads/oldreleases/](https://julialang.org/downloads/oldreleases/#:~:text=bf8f45f85d7c615f01aa46db427c2435b397ec58f2c7ee6d4b0785481a747d98-,v1.10.4,-%2C%20on%202024%2D06)
   - Other versions would likely work, but this is the tested environment.


### Setup multithreading via Jupyter(Lab)

Configure Jupyter kernel with Threads as follows.

1. Open Julia and install custom kernels:
   ```julia
   using IJulia
   installkernel("Julia (8 Threads)", env=Dict("JULIA_NUM_THREADS" => "8"))
   installkernel("Julia (12 Threads)", env=Dict("JULIA_NUM_THREADS" => "12"))
   ```

2. Launch Jupyter:
   ```julia
   using IJulia
   jupyterlab()
   ```

3. Select the desired kernel in JupyterLab on the top right:
   - `"Julia (8 Threads)"` for `LGO.ipynb`, `LSO.ipynb`
   - `"Julia (12 Threads)"` for `LEO.ipynb`
   - Or some other thread counts that suits your system's capabilities.

#### Confirm thread count

To verify how many threads are running, the following code block is placed for all notebooks at the top under **Setup**:
```julia
using Threads
println("Running on ", Threads.nthreads(), " threads.")
```

For details on multithreading in Julia, please see [Julia v1.10 documentation on multi-threading](https://docs.julialang.org/en/v1/manual/multi-threading/).

## Instructions (Google Colab setup)

This section also describes how to run the notebook in Google Colab.

### Tested Colab environment

- **OS**: Linux (`x86_64-linux-gnu`)
- **CPU**: 2 × Intel(R) Xeon(R) CPU @ 2.20GHz  
- **RAM**: 12 GB
- **Julia**: v1.11.5
- **Threads:** 2
  - Note the default environment in Google Colab differs from the aforementioned original and recommended (8-12 thread) setup.
  
### Setup instructions

1. **Upload** the entire folder and place it anywhere you like in your Google Drive.

2. **Open the notebook** in Google Colab using the **Python kernel**: ("Runtime" → "Change runtime type" → "Python").

3. In the Python kernel, mount your Google Drive:
   ```python
   from google.colab import drive
   drive.mount('/content/drive')
   ```

4. **Restart** the runtime with the **Julia kernel**: ("Runtime" → "Change runtime type" → "Julia").

5. In Julia, navigate to the repo directory (to enable `.csv` data loading and access to `.jld` output):
   ```julia
   cd("/content/drive/MyDrive/ [ your chosen directory ] /ASMC-BayesCV")
   ```

6. You're all set!

## Execution flow

Each notebook `{LGO, LSO, LEO}.ipynb`, when run top-to-bottom, executes:

1. MCMC (Markov-chain Monte Carlo) simulation multiple times,
2. SMC (Sequential Monte Carlo) simulation multiple times, and finally
3. replicate figures to the folder `img-[session datetime]/`.

Step 1 and 2 can be computationally intensive, especially MCMC in step 1 (which is the core motivation for the paper!).

To save time and allow for replication, we have included pre-computed output files in `output/`; you can skip the simulation steps and go directly to steps 2 and/or 3 if desired.

Execution options:

| Mode | Set | MCMC | SMC | Figures |
|------|-------|-----------------|----------------|-------------------|
| **1. Full run** | In **Setup** block:<br>`RUN_MCMC = true`<br>`RUN_aSMC = true` | Run and save to `output_[datetime]/` | Run and save to `output_[datetime]/` | Replicate and save to `img_[datetime]/` |
| **2. SMC only (default)** | In **Setup** block:<br>`RUN_MCMC = false`<br>`RUN_aSMC = true` |  | Run and save to `output_[datetime]/` | Replicate and save to `img_[datetime]/` |
| **3. Replicate figures** | In **Setup** block:<br>`RUN_MCMC = false`<br>`RUN_aSMC = false` |  |  | Replicate and save to `img_[datetime]/` |
