# SA-Based Floorplan Framework for Fixed-Outline Floorplanning

A floorplanning framework for fixed-outline VLSI design that aims to **maximize utilization** and **minimize routing length**.  
This project combines **Fiduccia-Mattheyses (FM) partitioning**, **Linear Ordering**, a customized **Quad-Btree** representation, and **Fast Simulated Annealing (Fast-SA)** to efficiently place soft modules and fixed modules under fixed-outline constraints.

## Overview

In modern VLSI physical design, floorplanning must consider not only chip area but also fixed-outline constraints, module overlap legality, whitespace utilization, and routing quality. Traditional area-driven approaches are often insufficient when soft modules can deform and fixed modules already occupy parts of the chip.

This work proposes a framework that:

- partitions modules according to connectivity using **FM partitioning**,
- generates a strong initial placement with **Linear Ordering**,
- represents relative placement using a customized **Quad-Btree**,
- improves the layout through **Fast Simulated Annealing**,
- and further reduces whitespace and HPWL through **module deformation**.

The optimization target is **Half-Perimeter Wirelength (HPWL)** while keeping the placement legal inside a fixed chip outline.

## Problem Setting

The framework focuses on floorplanning with the following constraints:

- All modules must be placed within a **fixed outline**.
- Soft modules are assumed to be **rectangular during initialization**.
- The aspect ratio of each soft module is constrained to **0.5 ~ 2**.
- Soft-module area utilization is constrained to **80% ~ 100%**.
- Overlap is not allowed:
  - between soft modules,
  - and between soft modules and fixed modules.

## Method

### 1. FM Partitioning
The chip is first divided into four sub-regions based on module connectivity:

- bottom-left (LD)
- top-left (LU)
- top-right (RU)
- bottom-right (RD)

Using the **Fiduccia-Mattheyses (FM)** algorithm, modules with stronger interconnections are grouped closer together so that the initial partition is more favorable for wirelength reduction.

### 2. Quad-Btree Representation
Instead of using a conventional B*-tree directly, this work proposes **Quad-Btree**, which is more suitable for:

- fixed-outline constraints,
- interaction with fixed modules,
- multi-corner placement,
- and tighter packing in four sub-regions.

Quad-Btree stores the relative positions of modules and supports efficient perturbation during optimization.

### 3. Linear Ordering
A **Linear Ordering Algorithm** is used to determine the sequence of module insertion.

The ordering prefers modules with:

- more terminating nets,
- fewer new nets,
- and better contribution to reducing future wirelength.

This improves the quality of the initial placement before SA begins.

### 4. Global Placement Strategy
Although the chip is initially partitioned into four sub-regions, final optimization is performed in a **global-placement manner** rather than enforcing hard invisible cut boundaries.

This avoids:

- excessive central whitespace,
- module crowding in local regions,
- and poor utilization caused by overly rigid region constraints.

### 5. Fast Simulated Annealing (Fast-SA)
The main optimization stage uses **Fast-SA** with four perturbation operations:

- **Op1:** exchange two nodes within the same subtree
- **Op2:** move a node to another position in the same subtree
- **Op3:** resize a soft module
- **Op4:** change the placement order of sub-regions

The cost function is defined as:

F = α × (W / W_norm) + (1 - α) × P

where:

- **W** = current HPWL
- **W_norm** = normalized HPWL baseline
- **P** = penalty for exceeding chip boundaries
- **α** = user-defined trade-off parameter

This formulation balances wirelength optimization and placement legality.

### 6. Deformation
After placement, soft modules are further **deformed** to better match surrounding contours while satisfying:

- aspect-ratio constraints,
- area-utilization constraints,
- and legality requirements.

This helps reduce internal whitespace and can further improve HPWL.

## Key Contributions

- A **Quad-Btree-based floorplanning representation** for fixed-outline designs.
- Integration of **FM partitioning + Linear Ordering + Fast-SA** into one optimization flow.
- A **global placement strategy** that improves utilization over rigid local placement.
- A **customized cost function** whose parameter α can be adjusted according to whitespace ratio.
- A **deformation stage** that improves packing quality after placement.

## Experimental Findings

According to the paper, the proposed framework shows the following advantages:

- The FM-based initialization produces better starting layouts than single-corner placement.
- Global placement can reduce HPWL significantly compared with purely local placement.
- Fast-SA avoids getting trapped in local minima more effectively than Classical-SA.
- Proper tuning of **α** based on whitespace ratio leads to better HPWL outcomes.
- In some benchmark cases, the method achieved results competitive with or better than the released ICCAD data.

## Project Flow

The full design flow can be summarized as:

1. Read fixed-outline, fixed-module, and soft-module inputs
2. Partition modules by connectivity using FM
3. Build initial placement order with Linear Ordering
4. Construct Quad-Btrees for the sub-regions
5. Perform global placement
6. Optimize with Fast-SA
7. Apply deformation to improve packing
8. Evaluate final HPWL and legality
