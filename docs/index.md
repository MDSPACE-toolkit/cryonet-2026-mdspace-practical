# Overview

Welcome to the MDSPACE practical session for the CryoNET Advanced Image Processing Workshop 2026.

In this practical, we will use the MDSPACE desktop to explore how cryo-EM image analysis and molecular dynamics simulation can be combined to study conformational variability.

The session is built around a controlled TmrAB (simplified) recovery experiment described in [Vuillemot, Rémi, et al. "MDSPACE: Extracting continuous conformational landscapes from cryo-EM single particle datasets using 3D-to-2D flexible fitting based on Molecular Dynamics simulation." Journal of Molecular Biology 435.9 (2023): 167951.](https://www.sciencedirect.com/science/article/pii/S0022283623000074)

We will first generate a synthetic cryo-EM dataset from a single known TmrAB conformation using normal modes analysis. Then, we will initialize MDSPACE from a different TmrAB conformation and test whether the workflow can recover the conformations that generated the dataset.

---

## Practical objective

The objective of this session is to understand the complete MDSPACE workflow, from structure-based data generation to conformational recovery and interpretation.

By the end of the practical, you should understand:

- How to create and configure an MDSPACE project.
- How a synthetic cryo-EM dataset can be generated from a molecular structure inside MDSPACE desktop.
- How MDSPACE combines image analysis and molecular dynamics to explore conformational change.
- How to inspect intermediate and final results.

---

## Prerequisites

The practical will be performed using MDSPACE desktop, and the data analysis will be performed using Python and the mdspace-analysis library.

Before starting, participants should be comfortable with:

- Basic concepts of single-particle cryo-EM.
- Molecular structures in PDB format.
- The idea of conformational variability.
- Navigating files, folders, and a graphical scientific software interface.
- Basic Python programming.

## Software and data

The practical will use the following material:

- MDSPACE desktop and its external dependencies that should already be installed.
- 6RAF a TmrAB target conformation used to generate the synthetic cryo-EM dataset.
- 6RAH a TmrAB starting conformation used to initialize the MDSPACE analysis.
- A complete, already-generated dataset and analysis folder will be provided as a backup.

The practical uses two TmrAB conformations with distinct roles:

| Structure | Used for dataset generation | Used as initial MDSPACE model | Used for final analysis |
| --------- | --------------------------- | ----------------------------- | ----------------------- |
| 6RAF      | Yes                         | No                            | Yes                     |
| 6RAH      | No                          | Yes                           | Yes                     |

---

## Practical plan

The practical is organized as a guided 2 h 30 session, during which every participant should be able to perform all the steps.

| Time      | Step                               | Goal                                                  |
| --------- | ---------------------------------- | ----------------------------------------------------- |
| 0:00–0:10 | Introduction and environment check | Open MDSPACE and verify access to the data            |
| 0:10–0:40 | Generate the synthetic dataset     | Create cryo-EM data from the target structure         |
| 0:40–1:00 | Inspect the generated dataset      | Check images, metadata, and expected outputs          |
| 1:00–2:00 | Run the MDSPACE workflow           | Process the dataset and launch the analysis           |
| 2:00–2:25 | Post-processing and visualization  | Inspect recovered structures and conformational space |
| 2:25–2:30 | Wrap-up                            | Discuss interpretation, limitations, and next steps   |

---

## Reference outputs and fallback material

Reference outputs are provided for all workflow stages.

These files serve two purposes:

1. They allow participants to continue if their own run fails or takes too long.
2. They provide expected results for comparison and browsing during processing time.

The reference material includes:

- A precomputed synthetic TmrAB dataset.
- A completed MDSPACE project.
- Post-processing results.
- Example visualizations with the full Python code.
