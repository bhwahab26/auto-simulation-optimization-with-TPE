## Auto Simulation–Optimization with TPE (Optuna + ECLIPSE Reservoir Simulation)
# Overview

This project presents an automated workflow for reservoir simulation and well-control optimization using the Tree-structured Parzen Estimator (TPE) within the Optuna framework. The main objective is to optimize well-control parameters to maximize cumulative oil production (FOPT) while minimizing cumulative water production (FWPT).

The workflow directly couples ECLIPSE 100 reservoir simulations with Python-based automation, creating a closed-loop simulation–optimization framework in which reservoir simulator input files are automatically modified, simulations are executed, and the resulting production data are extracted and returned to the optimization algorithm.

Four optimization cases are investigated to evaluate the performance and flexibility of the proposed approach:

Case 1 – Global optimization over the entire reservoir production life
Case 2 – Global optimization with a physical BHP-difference constraint
Case 3 – Global optimization with a water-production constraint
Case 4 – Time-dependent optimization with control updates every two years

# Workflow Description

# 1. Automated Simulation–Optimization Loop

A Python script is used to automatically control the reservoir simulations through the following workflow:

Modify → Simulate → Evaluate → Optimize

For each optimization trial, the workflow:

Modifies the ECLIPSE input file (.DATA)
Updates well-control parameters:
Producer bottom-hole pressure (BHP)
Water-injection rate (Qinj)
Runs the reservoir simulation in ECLIPSE 100
Extracts production results from the simulation output (.RSM)
Calculates the objective functions
Returns the results to the Optuna optimization algorithm
Generates a new set of control parameters for the next trial

This process creates a fully automated closed-loop simulation–optimization framework.

# 2. Optimization Engine: Optuna + MOTPE

The optimization process is implemented using Optuna v4.7.0 with the Multi-Objective Tree-structured Parzen Estimator (MOTPE) algorithm.

Each optimization trial consists of:

Updating the ECLIPSE .DATA file
Running a full-physics reservoir simulation
Extracting the resulting production performance
Evaluating the objective functions
Returning the results to the MOTPE optimizer

The optimization problem is formulated as a multi-objective problem:

Maximize cumulative oil production (FOPT)
Minimize cumulative water production (FWPT)

The resulting solutions are represented through a Pareto-optimal front, allowing the trade-off between oil production and water production to be evaluated.

# 3. Optimization Cases

Four optimization cases are investigated to progressively introduce operational and production constraints and to evaluate both global and time-dependent control strategies.

# Case 1 – Global Optimization Over the Entire Reservoir Production Life

In the first approach, the well-control parameters are optimized over the entire reservoir production life.

The optimization variables consist of:

4 producer BHP values
8 water-injector rates

Therefore, the optimization problem contains:

12 decision variables

The same optimized control parameters are maintained throughout the reservoir production period.

Objective
Maximize FOPT
Minimize FWPT
Search space
150 bar < BHP < 390 bar
50 < Qinj < 400

This case provides the baseline optimization scenario without additional physical constraints.

# Case 2 – Global Optimization with a BHP-Difference Constraint

The second case uses the same global optimization strategy as Case 1 but introduces a physical constraint on the producer BHP values.

The maximum difference between the BHP values of the production wells is limited to:

ΔBHP ≤ 50 bar

This constraint is introduced to prevent the optimizer from assigning excessively different pressure controls to the production wells and to ensure that all production wells remain active contributors to field production.

The optimization objectives remain:

Maximize FOPT
Minimize FWPT

while satisfying:

150 bar < BHP < 390 bar
ΔBHPmax ≤ 50 bar
50 < Qinj < 400
# Case 3 – Global Optimization with a Water-Production Constraint

The third case extends the global optimization approach by introducing a constraint on cumulative water production.

The injection-rate search space is modified to obtain an optimal balance between oil production and water production while maintaining cumulative water production below a predefined maximum value:

FWPT ≤ 2 MMSTB

The optimization therefore considers:

Maximum cumulative oil production
Minimum cumulative water production
Maximum allowable cumulative water production of 2 MMSTB

The BHP-difference constraint from Case 2 can also be maintained in this case to ensure that the optimized solution remains physically and operationally consistent.

The corresponding formulation is:

150 bar < BHP < 390 bar
ΔBHPmax ≤ 50 bar
50 < Qinj < 100
FWPT ≤ 2 MMSTB


# Case 4 – Time-Dependent Optimization

The second optimization approach investigates time-dependent well control, in which the optimal control parameters are determined separately for successive periods of the reservoir production life.

The reservoir production period is divided into 2-year control intervals.

For each interval, the optimizer determines a new set of well-control parameters consisting of:

4 producer BHP values
8 injector water rates

Therefore:

12 decision variables per control interval

For a 10-year production period:

5 intervals × 12 parameters = 60 decision variables

The control parameters obtained for each interval are applied during that corresponding two-year period before being updated for the next interval.

Dynamic optimization strategy
Years 0–2   → Optimize BHP and Qinj
Years 2–4   → Optimize BHP and Qinj
Years 4–6   → Optimize BHP and Qinj
Years 6–8   → Optimize BHP and Qinj
Years 8–10  → Optimize BHP and Qinj

This approach allows the optimization strategy to adapt the well controls to the evolving reservoir conditions rather than maintaining a single set of control parameters throughout the entire production life.

# Model Characteristics (EGG Reservoir model):
Grid size: 60 × 60 × 7 (25,200 cells)
Active cells: 18,533
Heterogeneous structure with high-permeability channels
Black-oil model (Eclipse 100)

# Key Parameters:

Property	Value
Oil compressibility	1.0 × 10⁻¹⁰ Pa⁻¹
Water compressibility	1.0 × 10⁻¹⁰ Pa⁻¹
Oil viscosity	5.0 × 10⁻³ Pa·s
Residual oil saturation	0.1
Connate water saturation	0.2
Well radius	0.1 m
Initial reservoir pressure	40 × 10⁶ Pa

# Key Features

Fully automated reservoir simulation pipeline
Integration of Eclipse 100 with Python
Multi-objective Bayesian optimization (MOTPE)
Time-dependent dynamic well control strategy
60-parameter high-dimensional optimization problem
Closed-loop simulation–optimization framework
