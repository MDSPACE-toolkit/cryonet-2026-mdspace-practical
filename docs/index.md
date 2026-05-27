# Overview

Welcome to the MDSPACE practical session for the CryoNET Advanced Image Processing Workshop 2026.

In this practical, we will use the MDSPACE graphical interface to explore how cryo-EM image analysis and molecular dynamics can be combined to study conformational variability.

The session is built around a controlled TmrAB recovery experiment. We will first generate a synthetic cryo-EM dataset from a single known TmrAB conformation using normal modes analysis. Then, we will initialize MDSPACE from a different TmrAB conformation and test whether the workflow can recover the conformations that generated the dataset.

---

## Practical objective

The objective of this session is to understand the complete MDSPACE workflow, from structure-based data generation to conformational recovery and interpretation.

By the end of the practical, you should understand:

* How to create and configure an MDSPACE project.
* How a synthetic cryo-EM dataset can be generated from a molecular structure.
* How MDSPACE combines image analysis and molecular dynamics to explore conformational change.
* How to inspect intermediate and final results.
* How to evaluate whether the recovered structures move toward the expected target conformation.

---

## Scientific question

The practical is organized around the following question:

> Can MDSPACE recover the TmrAB conformations that were used to generate a synthetic cryo-EM dataset when the analysis is initialized from a different TmrAB conformation?

To test this, we will use two TmrAB structures:

* One target conformation, used to generate the synthetic cryo-EM dataset.
* One starting conformation, used as the initial structure for the MDSPACE analysis.

The target conformations are therefore known, but they are not used as the initial MDSPACE model. It is used to create the dataset and later serves as a reference to evaluate whether the MDSPACE workflow recovered the expected structural state.

This setup provides a controlled benchmark: because we know which conformations generated the data, we can directly assess whether the recovered conformational changes are consistent with the expected TmrAB structure.

---

## Prerequisites

No programming is required. The practical will be performed using the MDSPACE graphical interface that will already be installed with all its external dependencies.

Before starting, participants should be comfortable with:

* Basic concepts of single-particle cryo-EM.
* Molecular structures in PDB format.
* The idea of conformational variability.
* Navigating files, folders, and a graphical scientific software interface.

---

## Software and data

The practical will use the following material:

* MDSPACE software and its external dependencies.
* A TmrAB target conformation used to generate the synthetic cryo-EM dataset.
* A different TmrAB starting conformation used to initialize the MDSPACE analysis.
* A complete, already-generated dataset and analysis folder will be provided with the MDSPACE software.

The required files will be provided in the workshop environment.

| Resource   | Role                                             |
| ---------- | ------------------------------------------------ |
| `6RAF.pdb` | Structure used to generate the synthetic dataset |
| `6RAH.pdb` | Initial structure used by MDSPACE                |

---

## Structural data

The practical uses two TmrAB conformations with distinct roles:

| Structure | Used for dataset generation | Used as initial MDSPACE model | Used for final comparison |
| --------- | --------------------------: | ----------------------------: | ------------------------: |
| `6RAF`    |                         Yes |                            No |                       Yes |
| `6RAH`    |                          No |                           Yes |                       Yes |

This distinction is central to the practical.

The synthetic dataset is generated from the target 6RAF conformation. MDSPACE is then initialized from the starting 6RAH  conformation. The goal is to test whether the information contained in the synthetic cryo-EM images can guide the analysis back toward the target conformation.

---

## Practical plan

The practical is organized as a guided 2 h 30 session, during which every participant should be able to perform all the steps.

| Time      | Step                                              | Goal                                                      |
| --------- | ------------------------------------------------- | --------------------------------------------------------- |
| 0:00–0:10 | Introduction and environment check                | Open MDSPACE and verify access to the data                |
| 0:10–0:25 | Load the target `6RAF` conformation               | Inspect the structure used to generate the dataset        |
| 0:25–0:40 | Generate the synthetic dataset                    | Create cryo-EM data from the target structure        |
| 0:40–0:55 | Inspect the generated dataset                     | Check images, metadata, and expected outputs              |
| 0:55–1:10 | Initialize MDSPACE from the starting conformation | Load the synthetic dataset                                |
| 1:10–1:35 | Run the MDSPACE workflow                          | Process the dataset and launch the analysis               |
| 1:35–2:05 | Post-processing and visualization                 | Inspect recovered structures and conformational space     |
| 2:05–2:25 | Structural comparison                             | Compare recovered structures with the target conformation |
| 2:25–2:30 | Wrap-up                                           | Discuss interpretation, limitations, and next steps       |

---

## While MDSPACE is processing

Some steps may take several minutes. During these periods, participants should not just wait. We will use this time to inspect parameters, discuss expected results, and compare with reference material.

During dataset generation, focus on:

* Which conformation is used to generate the data?
* How many images are generated?
* Which parameters control the synthetic dataset?
* What should the generated projections look like?

During the MDSPACE workflow, focus on:

* Which conformation was used as the starting model?
* What does each workflow step compute?
* What kind of conformational change do we expect to recover?
* How will we decide whether the recovery was successful?

---

## Reference outputs and fallback material

Reference outputs are provided for all workflow stages.

These files serve two purposes:

1. They allow participants to continue if their own run fails or takes too long.
2. They provide expected results for comparison and browsing during processing time.

The reference material includes:

* A precomputed synthetic TmrAB dataset.
* A completed MDSPACE project.
* Post-processing results.
* Example visualizations.
* Final structural comparison with the target conformation.

---

## Expected outcome

At the end of the practical, we will compare the MDSPACE results with the target 6RAH conformations used to generate the synthetic dataset.

The key question is:

> Did we recover target conformations from the starting conformation?

A successful result does not necessarily mean a perfect reconstruction of the target structures. The important point is whether the recovered conformational change is structurally consistent with the target state and with the information present in the synthetic cryo-EM data.
