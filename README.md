# Inventory Management Using Markov Decision Process (MDP)

### Policy-Based Inventory Control and Cost Simulation

## Overview

This project implements a **Markov Decision Process (MDP)-inspired inventory management model** to simulate inventory replenishment decisions under varying demand conditions. It evaluates inventory control strategies by modeling inventory transitions, replenishment quantities, holding costs, ordering costs, and shortage penalties.

The system compares two inventory replenishment strategies through a simulation framework:

- **Fixed-Quantity Policy:** Places a constant replenishment order regardless of the current inventory level.
- **Inventory-Dependent Policy:** Adjusts replenishment quantities according to the current inventory state.

The implementation uses Python and scientific computing libraries to model inventory dynamics, calculate cumulative operational costs, and visualize inventory levels and cost differences between policies.

The primary objective is to investigate how inventory-dependent replenishment decisions affect inventory stability and total operating costs within a controlled simulation environment.

## Key Features

- **State-Dependent Inventory Control:** Represents inventory levels as discrete states used to determine replenishment actions.
- **Inventory Transition Modeling:** Simulates inventory changes based on current stock, replenishment quantity, and demand.
- **Multi-Component Cost Function:** Accounts for ordering expenses, inventory holding costs, and shortage penalties.
- **Policy-Based Simulation:** Evaluates alternative replenishment strategies under a common demand sequence.
- **Demand Data Preprocessing:** Converts order dates into datetime values and aggregates order quantities into daily demand.
- **Demand Distribution Analysis:** Uses histogram-based estimation to visualize the empirical distribution of aggregated demand.
- **Comparative Performance Evaluation:** Calculates cumulative simulation costs for different inventory policies.
- **Data Visualization:** Generates inventory trajectory plots and total-cost comparison charts.

## System Architecture

The implementation follows a modular simulation workflow:

```text
Historical Order Dataset
          |
          v
Data Loading and Exploration
          |
          v
Demand Preprocessing
          |
          v
Daily Demand Aggregation
          |
          v
Inventory Environment
          |
          +----------------------+
          |                      |
          v                      v
    Fixed-Quantity        Inventory-Dependent
        Policy                  Policy
          |                      |
          v                      v
    Inventory Simulation   Inventory Simulation
          |                      |
          +----------+-----------+
                     |
                     v
             Cost Evaluation
                     |
                     v
          Comparative Visualization
```

## Mathematical Formulation

The inventory environment is modeled using state-dependent replenishment decisions and discrete-time inventory transitions.

### 1. Inventory State Transition

Let:

- \(I_t\) denote the inventory available at time step \(t\).
- \(Q_t\) denote the replenishment quantity.
- \(D_t\) denote demand during the current period.
- \(I_{t+1}\) denote the inventory carried into the next period.

The inventory transition is defined as:

\[
I_{t+1} = \max(0, I_t + Q_t - D_t)
\]

The model prevents negative inventory states. However, unmet demand is separately represented through a shortage penalty rather than being carried forward as backlogged demand.

### 2. Cost Function

The total cost for a simulation step is defined as:

\[
C_t = c_o Q_t + c_h \max(I_t + Q_t - D_t, 0)
+ c_s \max(D_t - I_t - Q_t, 0)
\]

Where:

- \(c_o\) is the unit ordering cost.
- \(c_h\) is the unit holding cost.
- \(c_s\) is the unit shortage penalty.

The implementation uses the following default parameters:

| Parameter | Default Value | Description |
|---|---:|---|
| Maximum inventory state | 100 | Upper bound used to construct the policy arrays |
| Initial inventory | 50 units | Starting inventory for each simulation |
| Ordering cost | 1.0 | Cost per unit ordered |
| Holding cost | 0.5 | Cost per unit of remaining inventory |
| Shortage penalty | 2.0 | Penalty per unit of unmet demand |
| Simulation horizon | 100 periods | Number of simulated time steps |

These values are configurable in the simulation functions and `InventoryMDP` class.

### 3. Reward Function

The simulation represents the negative of the operating cost as the reward:

\[
R_t = -C_t
\]

The cumulative cost over a simulation horizon of \(T\) periods is:

\[
C_{\text{total}} = \sum_{t=0}^{T-1} C_t
\]

The current implementation compares policies using cumulative operating cost rather than a discounted long-term reward objective.

## Inventory Control Policies

### Fixed-Quantity Policy

The fixed-quantity policy places an order of 20 units at every time step:

\[
Q_t = 20
\]

This policy provides a simple baseline for evaluating inventory replenishment behavior without considering the current inventory level.

### Inventory-Dependent Policy

The inventory-dependent policy uses the current inventory state to determine the replenishment quantity:

\[
Q_t =
\begin{cases}
50-I_t, & I_t < 50 \\
0, & I_t \geq 50
\end{cases}
\]

Under this policy, replenishment increases as inventory falls below 50 units. No additional order is placed when inventory is at or above that threshold.

**Important:** This is a manually specified inventory-dependent policy. Although the notebook labels it an "Optimal Policy," it does not derive the policy through Bellman equations, value iteration, policy iteration, or reinforcement learning. Therefore, it should not be interpreted as a mathematically proven optimal policy.

## Dataset and Preprocessing

The notebook loads a CSV dataset named `ML-Dataset.csv` containing order, product, customer, warehouse, employee, and geographic information.

The relevant fields used for demand analysis are:

| Column | Purpose |
|---|---|
| `OrderDate` | Timestamp used to group orders by date |
| `OrderItemQuantity` | Quantity aggregated to estimate daily demand |

### Preprocessing Pipeline

1. Load the dataset using Pandas.
2. Inspect dataset columns and preview records.
3. Check for missing values.
4. Generate descriptive statistics.
5. Convert `OrderDate` into a datetime representation.
6. Aggregate `OrderItemQuantity` by order date.
7. Rename the aggregated quantity column to `demand`.
8. Estimate a demand distribution using a histogram.

The notebook also includes a seeded synthetic demand sequence for the inventory policy comparison:

```python
np.random.seed(42)
demand_series = np.random.randint(10, 30, size=100)
```

This generates 100 demand observations with integer values from 10 through 29.

**Demand modeling note:** The notebook constructs an empirical demand distribution from the historical dataset, but the policy comparison uses the synthetic demand sequence shown above. The reported policy costs therefore reflect the synthetic simulation, not a direct evaluation against the historical daily demand series.

## Technology Stack

| Technology | Application |
|---|---|
| Python | Core implementation and simulation logic |
| NumPy | Numerical computation, arrays, and demand generation |
| Pandas | Dataset loading, preprocessing, and aggregation |
| Matplotlib | Inventory trajectory and cost visualizations |
| Seaborn | Statistical distribution visualization |
| Jupyter Notebook | Interactive development and experimentation |

## Project Structure

```text
Inventory-Management-System/
│
├── Inventory_Management_System.ipynb
├── ML-Dataset.csv
└── README.md
```

The filenames above illustrate the recommended repository structure. Use the actual filenames present in your GitHub repository.

- `Inventory_Management_System.ipynb` — Contains data exploration, demand preprocessing, inventory transition logic, policy definitions, simulation, and visualization.
- `ML-Dataset.csv` — Input dataset required to execute the notebook's historical demand analysis.
- `README.md` — Project documentation, mathematical formulation, setup instructions, and implementation details.

## Installation and Setup

### Prerequisites

- Python 3.9 or later
- Jupyter Notebook or JupyterLab
- The input dataset referenced by the notebook

### 1. Clone the Repository

```bash
git clone <YOUR_REPOSITORY_URL>
cd Inventory-Management-System
```

Replace `<YOUR_REPOSITORY_URL>` with your actual GitHub repository URL.

### 2. Create a Virtual Environment

```bash
python -m venv .venv
```

Activate the environment.

**Windows:**

```bash
.venv\Scripts\activate
```

**Linux/macOS:**

```bash
source .venv/bin/activate
```

### 3. Install Dependencies

```bash
python -m pip install numpy pandas matplotlib seaborn jupyter
```

### 4. Launch the Notebook

```bash
jupyter notebook
```

Open the inventory management notebook and execute its cells sequentially.

Ensure that `ML-Dataset.csv` is available at the path expected by the notebook before running the dataset-loading cell.

## Simulation Workflow

The notebook implements the following execution sequence:

1. Initialize the inventory environment with configurable cost parameters.
2. Define a fixed-quantity replenishment policy.
3. Define an inventory-dependent replenishment policy.
4. Initialize the inventory state.
5. Select the replenishment action using the active policy.
6. Apply the inventory transition function for the current demand.
7. Calculate the negative-cost reward.
8. Accumulate operating costs and record inventory, order, and demand histories.
9. Repeat the process for the simulation horizon.
10. Compare policy costs and inventory trajectories.

The simulation uses the same demand sequence and initial inventory for both policies, making the resulting comparison reproducible under the configured parameters.

## Results and Evaluation

In the notebook's recorded execution, the two policies produced the following cumulative costs:

| Policy | Simulation Horizon | Initial Inventory | Total Cost |
|---|---:|---:|---:|
| Fixed-Quantity Policy | 100 periods | 50 units | 5,900.00 |
| Inventory-Dependent Policy | 100 periods | 50 units | 3,436.50 |

The recorded simulation shows that the inventory-dependent policy incurred a lower cumulative cost than the fixed-quantity baseline under the tested configuration.

The absolute cost difference is:

\[
5900.00 - 3436.50 = 2463.50
\]

The percentage reduction relative to the fixed-quantity baseline is approximately:

\[
\frac{5900.00-3436.50}{5900.00}\times100
\approx 41.75\%
\]

### Interpretation

Within this particular simulation run, the inventory-dependent policy reduced cumulative operating cost by approximately **41.75%** compared with the fixed-quantity policy.

This result demonstrates the potential benefit of adapting replenishment decisions to the current inventory state. However, it does not establish that the policy is globally optimal or that the same cost reduction will occur with different demand sequences, cost parameters, or real-world inventory data.

The results are simulation-specific and should be validated across multiple demand scenarios before drawing broader conclusions.

## Limitations

- The inventory-dependent policy is manually defined rather than learned through an optimization algorithm.
- The simulation comparison uses synthetic demand instead of the aggregated historical demand series.
- The demand sequence is fixed and reused for both policies; uncertainty across multiple demand scenarios is not evaluated.
- The model assumes immediate replenishment and does not account for supplier lead times.
- Unmet demand incurs a penalty but is not modeled as backlogged demand.
- Ordering costs are linear and do not include fixed per-order charges.
- The notebook does not implement capacity constraints beyond the policy-state array's indexing behavior.
- The evaluation uses a single cumulative-cost measure without statistical analysis across repeated simulation runs.

These limitations define the current scope of the prototype and provide opportunities for future development.

## Future Enhancements

Potential extensions include:

- **Dynamic Programming:** Implement value iteration or policy iteration to derive policies from the Bellman optimality equations.
- **Reinforcement Learning:** Explore Q-learning or other suitable reinforcement learning algorithms for inventory control.
- **Stochastic Demand Modeling:** Estimate demand distributions from historical observations and sample from them during simulation.
- **Lead-Time Modeling:** Introduce replenishment delays and pipeline inventory.
- **Service-Level Metrics:** Track stockout frequency, fill rate, and demand satisfaction.
- **Multi-Product Inventory:** Extend the simulation to manage multiple products and inventory locations.
- **Parameter Sensitivity Analysis:** Evaluate the effect of ordering, holding, and shortage costs on policy performance.
- **Repeated Experiments:** Run multiple seeded simulations and compare mean costs and variability.
- **Policy Evaluation Framework:** Separate policy generation from simulation and support additional inventory control strategies.

## Learning Outcomes

This project explores the intersection of inventory management, stochastic decision modeling, and simulation-based evaluation.

Key concepts demonstrated include:

- State-action-based decision logic
- Discrete-time inventory transitions
- Cost and reward formulation
- Inventory-dependent replenishment strategies
- Demand aggregation and exploratory data analysis
- Reproducible simulation experiments
- Quantitative policy comparison
- Visualization of inventory behavior and operating costs

## Conclusion

This project provides a Python-based framework for studying inventory replenishment strategies through state-dependent decision rules and simulation.

By combining demand preprocessing, inventory transition modeling, a multi-component cost function, and comparative policy evaluation, the implementation demonstrates how computational models can be used to investigate inventory control decisions.

The current version establishes a foundation for more advanced inventory optimization, including dynamic programming, stochastic control, and reinforcement learning.

---

## Author

**Priyadharsini M.**

B.Tech — Computer Science and Engineering  
Specialization: Artificial Intelligence and Data Analytics

## License

No license is specified by the current notebook. If you intend to distribute this project publicly, consider adding a `LICENSE` file and selecting an appropriate open-source license.
