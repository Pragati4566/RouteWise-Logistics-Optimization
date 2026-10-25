# RouteWise — AI-Powered Last-Mile Delivery Optimization

RouteWise is an end-to-end logistics optimization platform that combines **mathematical optimization, machine learning, predictive analytics, and interactive visualization** to support cost-efficient last-mile delivery planning.

The platform models a synthetic delivery network based on Atlanta, with customer locations, a central depot, vehicle capacity constraints, and delivery time windows. It combines exact optimization methods for smaller benchmark problems with scalable routing techniques for larger delivery scenarios.

Alongside route optimization, RouteWise includes demand forecasting, travel-time prediction, multi-solver benchmarking, scenario analysis, and an interactive dashboard for evaluating operational trade-offs.

## Key Features

- **Vehicle Routing Optimization:** Plans delivery routes while accounting for vehicle capacity and customer time windows.
- **Multi-Solver Benchmarking:** Compares Gurobi, IBM CPLEX, SCIP, and CBC using a common mathematical formulation.
- **Scalable Routing:** Uses Google OR-Tools and guided local search for larger vehicle-routing scenarios.
- **Demand Forecasting:** Compares XGBoost, Random Forest, and Linear Regression for package-volume prediction.
- **Travel-Time Prediction:** Evaluates regression models to estimate journey duration under varying traffic conditions.
- **Scenario Analysis:** Examines how fleet size affects total delivery cost and optimization feasibility.
- **Interactive Route Visualization:** Displays vehicle routes and stop-level delivery information on maps.
- **Operational KPI Dashboard:** Summarizes delivery distance, cost, vehicle utilization, and solver runtime.
- **Automated Testing:** Validates routing constraints, delivery coverage, solver consistency, and ML model behavior.

## System Architecture

RouteWise is organized into five main components:

1. **Data Generation:** Creates synthetic customer, vehicle, and depot datasets.
2. **Optimization Engine:** Constructs and solves vehicle-routing and assignment problems.
3. **Machine Learning Models:** Predicts demand and travel times using alternative regression algorithms.
4. **Visualization Layer:** Generates route maps and interactive analytical views.
5. **Testing and Evaluation:** Checks optimization feasibility, compares solver outputs, and validates model behavior.

This separation keeps the data, optimization, prediction, visualization, and evaluation workflows modular.

## Optimization Engine

### 1. Capacitated Vehicle Routing with Time Windows

The core optimization problem is the Capacitated Vehicle Routing Problem with Time Windows (CVRPTW).

The model aims to minimize delivery cost while satisfying operational constraints, including:

- Vehicle capacity limits.
- Customer delivery time windows.
- Valid route connectivity and vehicle-flow constraints.
- Customer visit and delivery-coverage requirements.
- Fleet availability and depot-related routing constraints.

The mathematical formulation uses decision variables to represent vehicle movement between locations. The objective and constraints are constructed to produce feasible delivery plans.

### 2. Exact Optimization

For smaller benchmark instances, the platform uses a mixed-integer programming formulation solved with Gurobi.

The formulation represents vehicle movements using arc variables and incorporates routing, capacity, and time-window constraints. Exact optimization provides a benchmark for evaluating solution quality on instances that are computationally manageable.

### 3. Multi-Solver Comparison

The same CVRPTW formulation can be evaluated using four solver backends:

- Gurobi
- IBM CPLEX
- SCIP
- CBC

The comparison examines solution cost, feasibility, and execution time under a consistent mathematical formulation.

On the current small benchmark instance, the solver runs have produced matching optimal costs, while runtime differs across backends. The observed execution times are approximately below 0.2 seconds for Gurobi and CPLEX, below one second for SCIP, and around 11 seconds for CBC.

These timings are environment-dependent and should be remeasured when hardware, solver configurations, or problem instances change.

### 4. Large-Scale Routing with Google OR-Tools

For larger delivery scenarios, the platform uses Google OR-Tools with constraint programming and guided local search.

This approach focuses on finding high-quality feasible routes efficiently. Unlike an exact solver, a heuristic search does not necessarily prove that the returned solution is globally optimal.

Using both approaches makes it possible to compare solution quality against benchmark instances while retaining a practical routing approach for larger scenarios.

### 5. Fleet Assignment Baseline

A separate assignment model provides a simpler baseline for assigning customers to vehicles under capacity constraints.

This supports comparison between basic assignment decisions and more detailed route-level optimization.

## Machine Learning and Predictive Analytics

### Demand Forecasting

The demand-prediction module compares three regression approaches:

- XGBoost
- Random Forest
- Linear Regression

It supports 14-day-ahead package-volume forecasting and enables the models to be compared using backtesting metrics.

In the current synthetic-data evaluation, the tree-based models have achieved an approximate Mean Absolute Percentage Error (MAPE) of 5%.

MAPE measures prediction error as a percentage of actual values. Results depend on the dataset, forecast horizon, and evaluation methodology.

### Travel-Time Prediction

The travel-time module compares:

- Random Forest
- Gradient Boosting
- Linear Regression

The goal is to estimate journey duration more realistically than a constant-speed assumption, enabling travel-time estimates to account for patterns represented in the input features.

On the current evaluation setup, the tree-based models have achieved an approximate R² of 0.97, compared with approximately 0.88 for the linear baseline.

R² measures how much variation in the target variable is explained by a model. These results are specific to the available dataset and should not be interpreted as guaranteed real-world traffic prediction accuracy.

Both predictive modules allow alternative models to be evaluated using a consistent workflow.

## Scenario Analysis and Operational KPIs

RouteWise includes scenario analysis to investigate the relationship between fleet size and delivery cost.

The platform can re-evaluate routing under different fleet configurations and visualize the resulting cost-versus-fleet-size trade-off.

Infeasible configurations are surfaced explicitly, along with the associated reason where available, rather than being silently omitted.

The dashboard summarizes operational metrics such as:

- Total delivery distance.
- Estimated operating cost.
- Number of vehicles used.
- Optimization runtime.
- Feasibility across fleet configurations.

These metrics help compare routing strategies and understand the operational trade-offs involved in delivery planning.

## Interactive Dashboard

The application is built with Streamlit and Plotly, with Folium providing route-map visualization.

The dashboard supports exploration of model predictions, optimization outputs, and scenario comparisons.

Route maps display vehicle-specific paths with stop-level details such as delivery priority, package weight, and delivery time windows.

The visualization layer is intended to make optimization outcomes easier to inspect and compare, rather than relying only on numerical solver output.

## Technology Stack

**Programming and Data Processing**
- Python
- Pandas
- NumPy

**Mathematical Optimization**
- Google OR-Tools
- PuLP
- Gurobi
- IBM CPLEX
- SCIP
- CBC

**Machine Learning**
- scikit-learn
- XGBoost

**Application and Visualization**
- Streamlit
- Plotly
- Folium

**Testing**
- pytest

## Project Structure

```text
RouteWise/
├── app.py
├── data/
│   ├── generate_data.py
│   ├── customers.csv
│   ├── trucks.csv
│   └── depot.csv
├── optimization/
│   ├── vrp_model.py
│   ├── multi_solver_vrp.py
│   ├── ortools_solver.py
│   ├── constraints.py
│   ├── objective.py
│   └── assignment_model.py
├── models/
│   ├── demand_prediction.py
│   └── travel_time_model.py
├── visualization/
│   └── route_map.py
├── tests/
│   └── test_optimizer.py
├── notebooks/
│   └── prototype.ipynb
├── test_gurobi.py
└── requirements.txt
```

## Getting Started

### Prerequisites

- Python 3.10 or a compatible version supported by the dependencies.
- pip.
- A compatible optimization solver for the solver-specific workflows.
- Valid commercial solver access where required for Gurobi or CPLEX.

### Installation

Clone your project repository and move into its directory.

```bash
git clone YOUR_REPOSITORY_URL
cd YOUR_REPOSITORY_DIRECTORY
```

Create and activate a virtual environment.

**macOS/Linux**

```bash
python3 -m venv .venv
source .venv/bin/activate
```

**Windows**

```bash
python -m venv .venv
.venv\Scripts\activate
```

Install the dependencies.

```bash
pip install -r requirements.txt
```

### Run the Application

Launch the interactive dashboard:

```bash
streamlit run app.py
```

### Run Individual Components

Generate a new synthetic delivery dataset:

```bash
python data/generate_data.py
```

Run the exact optimization model:

```bash
python optimization/vrp_model.py
```

Compare solver backends:

```bash
python optimization/multi_solver_vrp.py
```

Run large-scenario routing:

```bash
python optimization/ortools_solver.py
```

Evaluate demand forecasting:

```bash
python models/demand_prediction.py
```

Evaluate travel-time prediction:

```bash
python models/travel_time_model.py
```

Run the automated tests:

```bash
pytest tests/test_optimizer.py -v
```

Some workflows require specific solver installations or valid solver access. Their setup should be verified independently before execution.

## Testing and Validation

The automated tests cover important functional and mathematical properties of the optimization engine.

Current checks include:

- Vehicle-capacity feasibility.
- Delivery-coverage validation.
- Agreement between exact solvers on a common benchmark.
- Routing-solver validation.
- Basic sanity checks for the machine-learning models.

These checks help identify invalid routes, inconsistent optimization outputs, and unexpected model behavior.

A successful test run does not by itself establish real-world performance. Solver benchmarks
