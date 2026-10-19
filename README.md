# Numerical modeling of neuronal cultures in two and three dimensions

**A computational framework for constructing, simulating and analysing two- and three-dimensional neuronal cultures.**

This repository contains the C simulation programs, Python analysis utilities, example notebook and selected datasets associated with the manuscript **“Spatial Organization and Collective Dynamics in a Computational Model of Neuronal Cultures”** by Ander Gabarrús-Ordóñez *et al.* It investigates how spatial dimensionality, neuronal aggregation, axonal growth and synaptic excitation shape structural connectivity, emergent collective activity and the extent to which structural links can be inferred from neuronal activity.

The workflow combines biologically inspired, spatially embedded network growth with the **Izhikevich spiking-neuron model**. It provides examples for both **2D and 3D cultures**, as well as code for computing structural and functional network properties and visualising results discussed in the manuscript.

## Features

- **Spatial network generation:** random or Gaussian-clustered neuron placement, soma exclusion, dendritic territories and persistent-random-walk axonal growth.
- **2D and 3D geometries:** square/circular 2D cultures and 3D volumetric cultures, with boundary-handling implementations including sticky walls and periodic boundaries in supported geometries.
- **Probabilistic connectivity:** directed structural connections formed when growing axons intersect dendritic territories, with a tunable synapse-formation probability, `alpha`.
- **Spiking dynamics:** excitatory and inhibitory Izhikevich neurons, heterogeneous synaptic weights and stochastic drive.
- **Collective activity analysis:** spike rasters, global network activity (GNA), synchronisation measures and Fano-factor analysis.
- **Network characterisation:** degree distributions, giant-component analysis, spatial Gini coefficient, clustering and modularity.
- **Structure–function comparison:** Pearson-correlation-based functional networks, community analysis, receiver operating characteristic (ROC) and precision–recall (PR) evaluation.
- **Reproducible figure exploration:** a Jupyter notebook with examples and visualisations built from simulated and included summary data.

## Scientific overview

### 1. Construct spatially embedded neuronal networks

Neuron positions are generated either approximately uniformly or around randomly placed aggregation centres. The spatial Gini coefficient, denoted by **Λ** in the manuscript, quantifies heterogeneity in neuronal occupancy. Individual axons follow persistent random walks, with lengths sampled from a Rayleigh distribution. Candidate synapses arise where an axonal trajectory intersects another neuron's dendritic region; their acceptance probability is controlled by **α**.

To compare dimensionalities consistently, the manuscript matches characteristic inter-neuronal spacing, using the scaling **ρ₃D ∼ ρ₂D³ᐟ²**. Representative densities in the study are **71 neurons/mm² in 2D** and **600 neurons/mm³ in 3D**.

### 2. Simulate neuronal activity

The generated adjacency matrices are used by C-based implementations of the Izhikevich model. The model uses a mixture of excitatory and inhibitory neurons (80% and 20%, respectively, in the manuscript). Synaptic excitation, inhibition and noise can be adjusted to examine different dynamical regimes.

### 3. Analyse collective dynamics and functional connectivity

The workflow supports extracting spike events, studying global network activity and constructing correlation-based functional connectivity from neuronal spike trains. The **GNA is evaluated with respect to the giant component** in the manuscript, normalising activity by the number of neurons in that component. Functional and structural networks can then be compared at the level of graph statistics or individual connections, including ROC and PR metrics.

The research explores transitions between weakly coordinated, variable and strongly synchronised regimes and examines how their organisation differs between 2D and 3D cultures.

## Repository structure

```text
Code_NumericalSims2D3D/
├── 2D_tune_positions.c       # Generate 2D neuron configurations
├── 2D_create_network.c      # Grow 2D axons and construct candidate connections
├── 2D_dynamics.c            # Simulate 2D network spiking activity
├── 2D_functions.c           # Shared 2D C implementations
├── 2D_head.h                # 2D declarations and constants
├── 3D_tune_positions.c       # Generate 3D neuron configurations
├── 3D_create_network.c      # Grow 3D axons and construct candidate connections
├── 3D_dynamics.c            # Simulate 3D network spiking activity
├── 3D_functions.c           # Shared 3D C implementations
├── 3D_head.h                # 3D declarations and constants
├── functions_create_networks.py # Python wrappers, analysis and plotting utilities
├── final_notebook.ipynb     # Worked examples and manuscript-related analyses
├── 2D_initial_configurations/  # Generated 2D neuron positions
├── 3D_initial_configurations/  # Generated 3D neuron positions
├── 2D_created_networks/    # Candidate 2D connectivity output
├── 3D_created_networks/    # Candidate 3D connectivity output
├── 2D_axon_simulation/     # Example 2D axonal trajectories
├── 3D_axon_simulation/     # Example 3D axonal trajectories
├── 2D_dynamics/            # Example 2D spike data and network inputs
├── 3D_dynamics/            # Example 3D spike data and network inputs
├── GNA_map_vsLength/       # Precomputed activity maps vs. axonal length
├── GNA_map_vsGini/         # Precomputed activity maps vs. aggregation
├── data_for_figures/       # Additional precomputed data for figures
└── README.md
```

The ZIP also contains compiled Windows `.exe` files, a `.git` directory and some generated Python cache files. These are not necessary to understand the source code. The data directories may contain large files; some folder names describe **output destinations**, rather than populated example datasets.

## Requirements

- **Python 3** with Jupyter Notebook or JupyterLab.
- **GCC** (or another compatible C compiler) for building the simulation executables.
- Python packages used by the notebook and helper module: `numpy`, `scipy`, `pandas`, `matplotlib`, `networkx`, `numba`, `scikit-learn` and `pypalettes`.

Install the Python packages, for example, in a virtual environment:

```bash
python -m venv .venv
# Windows PowerShell:
.venv\Scripts\Activate.ps1
# Linux / macOS:
# source .venv/bin/activate
python -m pip install numpy scipy pandas matplotlib networkx numba scikit-learn pypalettes jupyterlab
```

**Important platform note:** The `compile_c_codes()` and `compile_c_codes3D()` helpers currently call GCC using the hard-coded Windows path `C:/msys64/ucrt64/bin/gcc.exe` and produce `.exe` executables. They therefore require a matching compiler installation or modification before they can be used on a different system. The Python wrappers also invoke these `.exe` filenames directly. **Cross-platform execution is not configured out of the box.** The presence of precompiled executables in the repository does not guarantee their compatibility with every Windows machine.

## Quick start

1. Clone the repository:

   ```bash
   git clone https://github.com/underDD/Code_NumericalSims2D3D.git
   cd Code_NumericalSims2D3D
   ```

2. Install the Python dependencies and ensure a compatible GCC compiler is available. If necessary, update the `gcc` path inside `compile_c_codes()` / `compile_c_codes3D()` in `functions_create_networks.py`.
3. Launch the example notebook **from the repository root**, so relative input and output paths resolve correctly:

   ```bash
   jupyter lab final_notebook.ipynb
   ```

4. Run **“Create 2D configuration example”** or **“Create 3D network configuration”**. These cells compile the corresponding C programs and illustrate position generation, axonal network construction, structural adjacency generation and plotting.
5. To also simulate spikes, set `simulate_dynamics = True` in the corresponding notebook example **before running the cell**. The default is `False` to avoid running potentially expensive dynamical simulations.
6. Explore the following notebook sections for structural analysis, Gini maps, activity maps and functional connectivity comparisons.

**Note:** Generating new simulations with `new_sim = True` recalculates results. Where supported, use `new_sim = False` to reuse already existing files. Large 3D simulations and all-pairs functional connectivity calculations can have substantial runtime and memory requirements.

## Example: constructing a 2D culture

The notebook uses parameter dictionaries to configure the simulations. A representative setup is:

```python
import functions_create_networks as fcn

fcn.compile_c_codes()  # Requires the GCC path configured in the helper

parameters2D = {
    "L": 3.0,
    "rho": 71,
    "soma_diameter": 0.015,
    "d_mean": 0.15,
    "d_sigma": 0.02,
    "l_mean": 1.0,
    "segment_length": 0.01,
    "sigma_axon_angle": 0.1,
    "agg": 1,
    "n_centers": 10,
    "base_sigma": 0.2,
    "BC_type": "SW",
    "geometry": "square",
    "seed": 0,
}

alpha = 0.2

position_file = fcn.run_2D_tune_positions(parameters2D, new_sim=True)
trial_matrix_file = fcn.create_network2D(parameters2D, new_sim=True)
trial_matrix = fcn.load_A(trial_matrix_file)
adjacency, adjacency_file = fcn.modify_A_alpha2D(
    trial_matrix, alpha, parameters2D, new_sim=True, GC=True
)

fcn.plot_spatial_distr_AND_adjacency(position_file, adjacency, parameters2D)
```

The 3D notebook example follows the analogous workflow using `run_3D_tune_positions()`, `create_network3D()`, `load_A3D()` and `modify_A_alpha3D_fast()`.

For dynamics, the notebook additionally specifies `max_exc_weight`, `max_inh_weight`, `noise_max`, `sim_time` and `seed`, supplies the adjacency file in `parametersDynamics["out_filename"]`, and invokes `run_dynamics_2D()` or `run_dynamics_3D()`.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `L` | Lateral culture extent (mm) |
| `height` | Thickness of the 3D culture (mm) |
| `rho` | Neuronal density (neurons/mm² in 2D; neurons/mm³ in 3D) |
| `soma_diameter` | Soma diameter (mm) |
| `d_mean`, `d_sigma` | Mean and standard deviation of dendritic territory diameter (mm) |
| `l_mean` | Mean axonal length (mm) |
| `segment_length` | Segment size for axonal growth (mm) |
| `sigma_axon_angle` | Angular spread of 2D axonal trajectories |
| `sigma_pol`, `sigma_azi` | Angular parameters for 3D axonal trajectories |
| `agg` | Whether to use an aggregated (`1`) or non-aggregated (`0`) placement |
| `n_centers`, `base_sigma` | Number and spatial spread of aggregation centres |
| `BC_type` | Boundary-condition choice, e.g. `SW` or `PBC`, where supported |
| `geometry` | Culture geometry (e.g. `square`, `circle`, `cube`) |
| `alpha` | Probability of accepting a candidate synaptic connection |
| `max_exc_weight`, `max_inh_weight` | Excitatory and inhibitory synaptic-weight bounds |
| `noise_max` | Input-noise amplitude |
| `sim_time` | Number of steps requested in the dynamics simulation |
| `seed` | Random seed parameter |

See the notebook and C program arguments for implementation details and geometry-specific options. The numerical defaults shown above are **example settings**, not a complete definition of the manuscript's parameter sweeps.

## Analyses and figures

`final_notebook.ipynb` contains sections corresponding to key analyses in the manuscript:

| Notebook section | Purpose |
| --- | --- |
| Create 2D configuration example | Construct and inspect a 2D structural network; optionally run dynamics |
| Create 3D network configuration | Construct and inspect a 3D structural network; optionally run dynamics |
| Structural properties (Fig. 1) | Structural organisation and connectivity characteristics |
| Gini color map (Fig. 2) | Spatial aggregation in 2D and 3D |
| Dependency of degree / giant component on `alpha` and axonal length (Supplementary Fig. 1) | Structural sensitivity to growth and synapse formation |
| Axonal length color map (Fig. 4) | Collective-activity regimes as axonal length and excitation vary |
| Gini color map (Fig. 5) | Synchronisation and activity variability versus aggregation |
| Functional analysis (Fig. 6) | Structural–functional graph comparisons, ROC, PR and TPR at fixed FPR |

Some sections read precomputed `.txt` files from `data_for_figures/`, `GNA_map_vsLength/` and `GNA_map_vsGini/`. Those plots can be explored without rerunning the full underlying parameter sweeps, provided the required data files are present.

The manuscript reports, **for the parameter regimes studied**, that spatial aggregation tends to enhance connectivity and lower the threshold for collective activation; aggregated cultures show richer variability near dynamical transitions; and 3D networks exhibit more stable collective dynamics and improved recovery of structural links from activity-derived correlations. These are simulation findings, not general claims about all neuronal cultures.

## Reproducibility and practical considerations

- **Stochastic model:** independent realisations may differ even at identical physical parameters. Save the chosen parameters and seeds when comparing outputs.
- **Computational cost:** 3D networks contain many more neurons under the spacing-matched setup. Matrix storage, network growth and correlation calculations may be expensive.
- **Different stages of the workflow:** the initial axon-intersection/trial matrix, probability-filtered adjacency matrix, dynamical spike output and functional correlation matrix represent different objects; do not treat them as interchangeable.
- **Notebook execution:** some figure-analysis cells depend on variables defined in earlier cells or on bundled input files. Run the necessary setup cells in order.
- **Reproduction scope:** the repository includes worked examples and selected precomputed datasets; it should not be assumed to include a fully automated command that regenerates every manuscript result from scratch.

## Associated manuscript

**Gabarrús-Ordóñez, A., Faci-Lázaro, S., Della Mea, L., Piga, A., Mazo, J. J., Gómez-Gardeñes, J., and Soriano, J.** *Spatial Organization and Collective Dynamics in a Computational Model of Neuronal Cultures.* Manuscript supplied with this repository's documentation.

Please cite the associated article when using this code for research. A DOI and final bibliographic details are not specified here; update this section when the article's final citation is available.

## License

**No software license was identified in the supplied repository.** The license applying to the manuscript does not automatically establish a software license for the code. Add a `LICENSE` file and update this section if a specific code license is intended.
