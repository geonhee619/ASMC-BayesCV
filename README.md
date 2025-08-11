# ASMC-BayesCV

This is the [README.md](https://github.com/geonhee619/ASMC-BayesCV/blob/main/README.md) for the repository containing computer code, output files, and figures for the paper:

Han, G. and Gelman, A. (2025) **"Adaptive sequential Monte Carlo for structured cross-validation in Bayesian hierarchical models"**. <https://arxiv.org/abs/2501.07685>

```bibtex
@misc{HanGelman2025adaptiveSequentialMonteCarlo,
      title={Adaptive sequential Monte Carlo for structured cross validation in Bayesian hierarchical models}, 
      author={Geonhee Han and Andrew Gelman},
      year={2025},
      eprint={2501.07685},
      archivePrefix={arXiv},
      primaryClass={stat.CO},
      url={https://arxiv.org/abs/2501.07685}, 
}
```

---

**Table of contents**:
- [Contents](#contents)
- [Instructions (Local JupyterLab on Windows)](#instructions-local-jupyterlab-on-windows)
- [Instructions (Google Colab setup)](#instructions-google-colab-setup)
- [Execution flow](#execution-flow)
- [System notes](#system-notes)

---

## Contents

- `data/`: Source data used in the examples.
- `output/`: Contains `.jld` files with numerical results.
- `{LGO, LEO, LSO}.ipynb`: Jupyter notebooks to be run in Julia (1.10.4, ideally with multi-threading) top-to-bottom for computation, saving output, and figure generation.
- `Figures.ipynb`: To generate Figure 1 in the manuscript.
- `img/`: Generated figures in the paper.
- `output_[session datetime]/` and  `img_[session datetime]/`: Newly generated files will be saved here.

> **Note:** [This GitHub repository](https://github.com/geonhee619/ASMC-BayesCV) does not include large data/outputs in `data/` and `output/` directly (but tracked via Git LFS). The complete folder can be found at [ASMC-BayesCV (Google Drive)](https://drive.google.com/drive/folders/1KHuBDZFao82LK4aOxPRX-MtYJyYGNL0K?usp=sharing).

---

## Instructions (Local JupyterLab on Windows)

1. Download [ASMC-BayesCV (Google Drive)](https://drive.google.com/drive/folders/1KHuBDZFao82LK4aOxPRX-MtYJyYGNL0K?usp=sharing).

> **Note:** To run `LSO.ipynb`, please additionally download `sales_train_validation.csv` from [https://www.kaggle.com/competitions/m5-forecasting-accuracy](https://www.kaggle.com/competitions/m5-forecasting-accuracy/data?select=sales_train_validation.csv), and place it in the following location: `data/m5-forecasting-accuracy/sales_train_validation.csv`.

2. Download/install **Julia v1.10.4**
   - Link: [https://julialang.org/downloads/oldreleases/](https://julialang.org/downloads/oldreleases/#:~:text=bf8f45f85d7c615f01aa46db427c2435b397ec58f2c7ee6d4b0785481a747d98-,v1.10.4,-%2C%20on%202024%2D06)
   - Other versions would likely work, but this is the original tested environment.

3. Setup multithreading via Jupyter(Lab).

> **Note:** Multithreading is optional. However, it is strongly recommended; the simulations take advantage of parallelizability for efficient computing, and thread counts may directly affect results.

   - Open Julia and install custom kernels:
   ```julia
   using IJulia
   installkernel("Julia (8 Threads)", env=Dict("JULIA_NUM_THREADS" => "8"))
   installkernel("Julia (12 Threads)", env=Dict("JULIA_NUM_THREADS" => "12"))
   ```

   - Launch Jupyter:
   ```julia
   using IJulia
   jupyterlab()
   ```

   - Select the desired kernel in JupyterLab on the top right:
     - `"Julia (8 Threads)"` for `LGO.ipynb`, `LSO.ipynb`
     - `"Julia (12 Threads)"` for `LEO.ipynb`
     - Or some other thread counts that suits your system's capabilities.

4. Confirm thread count; to verify how many threads are running, the following code block is placed for all notebooks at the top under **Setup**:

```julia
using Threads
println("Running on ", Threads.nthreads(), " threads.")
```

5. Navigate to the repo directory (to enable `.csv` data loading and access to `.jld` output),
```julia
cd("[ your chosen directory ] /ASMC-BayesCV")
```
then run each notebook from top to bottom.

---

## Instructions (Google Colab setup)

1. Download [ASMC-BayesCV (Google Drive)](https://drive.google.com/drive/folders/1KHuBDZFao82LK4aOxPRX-MtYJyYGNL0K?usp=sharing) and place it anywhere you like in your Google Drive.

> **Note:** To run `LSO.ipynb`, please additionally download `sales_train_validation.csv` from [https://www.kaggle.com/competitions/m5-forecasting-accuracy](https://www.kaggle.com/competitions/m5-forecasting-accuracy/data?select=sales_train_validation.csv), and place it in the following location: `data/m5-forecasting-accuracy/sales_train_validation.csv`.

2. Open the notebook in Google Colab using the **Python kernel**: ("Runtime" → "Change runtime type" → "Python").

3. In the Python kernel, mount your Google Drive:
   ```python
   from google.colab import drive
   drive.mount('/content/drive')
   ```

4. Restart the runtime with the **Julia kernel**: ("Runtime" → "Change runtime type" → "Julia").

5. In Julia, navigate to the repo directory (to enable `.csv` data loading and access to `.jld` output):
   ```julia
   cd("/content/drive/MyDrive/ [ your chosen directory ] /ASMC-BayesCV")
   ```

6. You're all set! You can run each notebook from top to bottom.

---

## Execution flow

Each notebook `{LGO, LSO, LEO}.ipynb`, when run top-to-bottom, executes:

1. MCMC (Markov-chain Monte Carlo) simulation multiple times,
2. SMC (Sequential Monte Carlo) simulation multiple times, and finally
3. replicate figures to the folder `img-[session datetime]/`.

Step 1 and 2 can be computationally intensive, especially MCMC in step 1 (which is the core motivation for the paper!).

To save time and allow for replication, we have included pre-computed output files in `output/`; you can skip the simulation steps and go directly to steps 2 and/or 3 if desired.

Execution options:

| Mode | Set in `Init.jl` | MCMC | SMC | Figures |
|------|-------|-----------------|----------------|-------------------|
| **1. Full run** | `RUN_MCMC = true`<br>`RUN_aSMC = true` | Run and save to `output_[datetime]/` | Run and save to `output_[datetime]/` | Replicate and save to `img_[datetime]/` |
| **2. SMC only** | `RUN_MCMC = false`<br>`RUN_aSMC = true` |  | Run and save to `output_[datetime]/` | Replicate and save to `img_[datetime]/` |
| **3. Replicate figures** | `RUN_MCMC = false`<br>`RUN_aSMC = false` |  |  | Replicate and save to `img_[datetime]/` |

---

## System notes

- The codes were developed and tested on the following **Windows** environment.

  - **OS**: Windows 11
  - **CPU**: 24-core 12th Gen Intel(R) Core(TM) i9-12900K
  - **RAM**: 32 GB
  - **Julia**: v1.10.4
  - **Multithreading configuration**:
    - `JULIA_NUM_THREADS=8` for `{LGO.ipynb, LSO.ipynb}` (w/ HMC)
    - `JULIA_NUM_THREADS=12` for `LEO.ipynb` (w/ full conditional Gibbs sampling)

- The codes were also tested on the following **Google Colab** environment.

  - **OS**: Linux (`x86_64-linux-gnu`)
  - **CPU**: 2 × Intel(R) Xeon(R) CPU @ 2.20GHz  
  - **RAM**: 12 GB
  - **Julia**: v1.11.5
  - **Threads:** 2
