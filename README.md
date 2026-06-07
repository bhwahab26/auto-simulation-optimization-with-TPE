## Auto Simulation Optimization with TPE (Optuna + Eclipse Reservoir Simulation)

# Overview

This project presents an automated workflow for reservoir simulation and well control optimization using the Tree-structured Parzen Estimator (TPE) within the Optuna framework. The main objective is to optimize dynamic well control parameters (bottom-hole pressure and water injection rates) in order to maximize oil recovery while minimizing water production.

The system integrates reservoir simulation software (Eclipse 100) with Python-based automation to create a closed-loop optimization pipeline.

# Workflow Description

# 1. Auto Simulation & Optimization Loop

A Python script is used to automatically control reservoir simulations by:

Modifying Eclipse input files (.DATA)
Updating well control parameters:
Bottom-hole Pressure (BHP)
Water Injection Rate (Qinj)
Running simulations in Eclipse 100
Extracting results from output files (.RSM)
Feeding results into the optimization algorithm

This creates a modify → simulate → evaluate → optimize cycle.

# 2. Optimization Engine (Optuna + MOTPE)

The optimization process is implemented using Optuna (v4.7.0) with the Multi-Objective Tree-structured Parzen Estimator (MOTPE) algorithm.

Each optimization trial corresponds to:

Updating the .DATA file
Running a full reservoir simulation
Extracting performance metrics:
Oil production (FOPT)
Water production (FWPT)
Evaluating the objective function

The framework solves a multi-objective optimization problem:

Maximize oil production (FOPT)
Minimize water production (FWPT)

# 3. Dynamic Well Control Strategy

The reservoir life is divided into 2-year control intervals, allowing time-dependent optimization.

Within each interval:

4 producing wells → optimized BHP values
8 injection wells → optimized injection rates

Total decision variables:

12 parameters per interval
5 intervals over 10 years
Total = 60 control parameters

# 4. Optimization Problem Formulation

Subject to:

240 bar < BHP < 320 bar
0 < Qinj < 150

Objective:

Maximize FOPT (oil production)
Minimize FWPT (water production)
Reservoir Model

The workflow is applied to the EGG reservoir model (from OilReservoirBench).

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
