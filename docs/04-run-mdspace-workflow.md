# Run the MDSPACE workflow

In this section, we will run the complete MDSPACE workflow using the synthetic dataset generated in the previous steps.

The goal is to start from another structure, the 6RAH conformation, which differs from the 6RAF conformation used to generate the dataset, and to test whether MDSPACE can recover structural deformations using molecular dynamics simulation and information from the cryo-EM image.

## Principle of MDSPACE

MDSPACE, for Molecular Dynamics simulation for Single Particle Analysis of Continuous Conformational hEterogeneity, is an iterative method designed to extract continuous conformational landscapes from cryo-EM single-particle images.

For each particle image, MDSPACE starts from the same initial atomic structure and performs 3D-to-2D flexible fitting. During this fitting, the molecular dynamics simulation is guided by a 2D image-based biasing potential, which compares the experimental particle image with a simulated projection of the current atomic model.

After each iteration, the fitted structures obtained from the particle images form an ensemble of conformations. This ensemble is rigidly aligned and analyzed by principal component analysis. The dominant principal component directions are then used to guide the next round of MD-based fitting. This makes the fitting more robust, especially for particle views where the conformational change is weak, ambiguous, or difficult to observe in projection.

MDSPACE also refines the initial rigid-body alignment of the particles over the iterations. Therefore, the workflow progressively improves both the molecular conformations and the particle orientation and translation parameters.

The workflow contains seven main steps:

1. Import the starting structure and image dataset.
2. Reconstruct a 3D volume from the particles.
3. Rigidly register the starting structure into the reconstructed volume.
4. Generate or import the molecular topology.
5. Compute normal modes.
6. Minimize the molecular system.
7. Run the MDSPACE analysis.

---

## 1. Import the PDB and XMD files

### Goal

The PDB file provides the molecular structure that will be used as the starting model. The XMD file describes the synthetic cryo-EM particle dataset. It contains the information needed to locate the particle images and read the associated metadata shift and orientation.

### In this practical

We use the 6RAH.pdb starting conformation as input, and the generated stack/particles.xmd file from the synthetic dataset as the image input.

After creating a new workflow window using File > New, import the PDB and XMD files. Check that both inputs are correctly listed in the project and that the particle dataset can be previewed. A successful load should unlock the next step tab in the workflow window.

---

## 2. Reconstruct a volume from the XMD dataset

### Goal

This step reconstructs a 3D density volume from the synthetic particle images. The reconstructed volume gives a first 3D representation of the image dataset.

This volume is primarily used to align the initial molecular structure with the particle dataset frame of reference.

### In this practical

We reconstruct a volume from the synthetic dataset. The volume must be reconstructed in the same unit as the PDB structure (Ångströms). Enter the sampling parameter used during dataset generation, then click Start Step.

After reconstruction, visually inspect the volume. The purpose of this step is to obtain a reasonable density for rigid registration, not a perfect high-resolution reconstruction.

---

## 3. Rigidly register the PDB onto the reconstructed volume

### Goal

The goal is to place the starting PDB structure and the reconstructed volume in the same frame of reference. This step applies only a global rotation and translation to the molecular structure.

Rigid registration does not change the internal conformation of the PDB structure. It does not bend, deform, or relax the molecule. It only changes its position and orientation.

### In this practical

We register the structure onto the reconstructed density. This gives MDSPACE a properly positioned initial model before molecular dynamics and image fitting begin.

Use the left mouse button to approximately align the structure and the volume. If the reconstruction was performed correctly, the two should match in scale. When the two are roughly aligned, click Start Step to perform the fine automatic registration.

---

## 4. Generate or import the topology

### Goal

The topology describes the molecular system in a format that the molecular dynamics engine can use. It defines the atoms, atom types, bonds, angles, and force-field parameters needed to compute molecular forces.

Without a valid topology, the molecular dynamics part of MDSPACE cannot run.

### In this practical

We will use a coarse-grained C-alpha Go model to generate the structure and the corresponding topology file. In this representation, the system is simplified relative to an all-atom model, greatly reducing the computational cost of molecular dynamics simulations.

This simplification is important for MDSPACE because the workflow performs many short MD simulations, one simulation per particle image and per MDSPACE iteration. A C-alpha Go model makes the practical feasible within a reasonable time while still allowing the recovery of meaningful large-scale conformational changes.

Select CAGO as the force field, then click Start Step.

---

## 5. Compute normal modes

### Goal

Normal modes can be used in the NMMD part of the MDSPACE workflow. In NMMD, normal-mode directions are combined with MD-based atomic displacements to encourage collective motions and accelerate large-scale conformational changes.

### In this practical

Because the synthetic dataset was itself generated using normal modes, we do not use the original generation modes during the recovery workflow. However, normal modes can still be computed and visualized for inspection by clicking Start Step.

---

## 6. Minimize the system

### Goal

The input structure may contain local strain, unfavorable contacts, or small inconsistencies introduced during preparation. Minimization relaxes the structure before the MDSPACE run.

This step prepares a stable molecular system for the later simulation.

### In this practical

We minimize the registered C-alpha 6RAH structure. After minimization, the structure should remain close to the registered input model while improving its local geometry.

A small structural adjustment is expected. A large, unexpected displacement may indicate a problem with the input structure, topology, or minimization settings.

---

## 7. Run the MDSPACE analysis

### Goal

In MDSPACE, 3D-to-2D flexible fitting uses a biasing potential based on the correlation coefficient between the experimental particle image and a 2D projection simulated from the atomic model. This image-projection agreement guides the MD simulation.

The result is an ensemble of fitted structures, one per particle image, together with refined metadata and PCA-based conformational coordinates.

### In this practical

We run MDSPACE starting from the registered and minimized 6RAH C-alpha structure.

We use four MDSPACE iterations. The first iteration uses standard MD-based 3D-to-2D flexible fitting without normal modes. This avoids injecting the normal-mode information that was used to generate the synthetic dataset directly into the recovery process. For that, select the MD THEN NMMD option. The full list of parameters can mostly be left at their defaults.

After the first iteration, MDSPACE analyzes the ensemble of fitted structures using principal component analysis. The following iterations use PCA-based refinement. In these iterations, the principal component vectors from the previous ensemble are used to guide MD-based flexible fitting in the next iteration that will use NMMD.

This iterative process incorporates ensemble conformational information into the MD simulation, making the 3D-to-2D fitting of individual particle images more robust to noise and to views where conformational changes are less detectable or ambiguous in the projection plane.

### While MDSPACE is running

The run may take several minutes, depending on the selected settings and available computing resources. During this time, we will prepare the next analysis steps.

MDSPACE provides tools to inspect the simulation outputs directly from the interface. In particular, we can display simulation logs and visualize the conformational landscape produced during the run, for example, using PCA or UMAP projections. These views are useful to check whether the workflow completed correctly and whether the generated structures explore a meaningful conformational space.

To continue the analysis further, we will also use Python and the provided mdspace-analysis library. This will allow us to compare the reference structures with the set of PDB files produced at each MDSPACE iteration.

In the next section, while the processing is still running or just after it completes, we will prepare the Python environment and load the generated conformations. We will then use these tools to evaluate whether the MDSPACE iterations moved the starting structure toward the target conformation used to generate the synthetic dataset.
