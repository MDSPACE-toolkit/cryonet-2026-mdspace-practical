# Post-processing and visualization

In this section, we will inspect the results produced by the MDSPACE workflow and compare them with the known structures from the synthetic experiment.

We will start with a quick inspection inside the MDSPACE interface, then move to a Python analysis using the mdspace-analysis library. The Python part will allow us to load the generated conformations, load the structures produced at each MDSPACE iteration, and visualize everything in the same PCA space.

---

## Quick inspection in MDSPACE

<video width="800" height="600" controls>   <source src="../assets/mdspace_inspect.webm" type="video/webm"> </video>

Once the MDSPACE run has completed, first inspect the results directly in the software.

Open the simulation logs and check that the MD jobs finished correctly. The goal is simply to make sure that the workflow produces usable outputs before starting the quantitative analysis.

Then open the conformational landscape tools. MDSPACE can display quick PCA and UMAP projections of the generated structures. At this stage, these plots are mainly used as diagnostic views: they help check whether the results look reasonable, whether some structures behave as outliers, and whether the analysis parameters are appropriate.

This software-based inspection is useful for a first look, but we will continue the analysis in Python to compare the recovered structures with the synthetic ground truth and gain greater flexibility in the analysis.

---

## Python analysis

We will now use the mdspace-analysis Python package to inspect the HDF5 files produced by MDSPACE.

The analysis will use two sources of structures:

- The synthetic conformations stored in generated_data.h5.
- The registered structures are stored in each MDSPACE iteration folder, for example genesis/000/coords.h5, genesis/001/coords.h5, and so on.

The synthetic structures correspond to the conformations used to generate the particle images. They are deformed, but they are not shifted or rotated according to the projection metadata. The MDSPACE structures will be loaded from the registered frames, because these have already been placed in a common coordinate frame.

We will then project all these structures into the same PCA space.

---

## Install the analysis tools

Install the analysis package and the plotting dependencies:

pip install mdspace-analysis scikit-learn matplotlib

Then import the required modules:

from pathlib import Path

import matplotlib.pyplot as plt

import numpy as np

from sklearn.decomposition import PCA

from mdspace_analysis import MdspaceHdf5

Set the paths to the generated dataset and to the MDSPACE project folder:

generated_h5 = Path("out/generated_data.h5")

project_dir = Path("workflow_id/")

Adapt these paths to match the folder names used during the practical.

---

## Load the synthetically generated conformations

The generated dataset contains an HDF5 file named generated_data.h5.

This file stores the coordinate-level ground truth of the synthetic dataset. For the PCA comparison, we use /frames/raw, because these are the generated conformations before projection, rotation, and shift. As we performed the MDSPACE analysis using c-alpha only, we now load the dataset conformation using the ca selection so the structures can be compared.

with MdspaceHdf5(generated_h5) as archive:

    generated_structures = [

        archive.raw(frame, selection="ca")

        for frame in range(archive.n_frames)

    ]

print("Generated structures:", len(generated_structures))

print("C-alpha atoms:", generated_structures[0].shape[0])

---

## Load the MDSPACE iterations

Each MDSPACE iteration writes an HDF5 archive in its corresponding genesis folder.

For example, if four iterations were run, the project may contain:

genesis/000/coords.h5

genesis/001/coords.h5

genesis/002/coords.h5

genesis/003/coords.h5

Load the registered C-alpha structures from each iteration:

n_iterations = 4

iteration_archives = [

    project_dir / "genesis" / f"{iteration:03d}" / "coords.h5"

    for iteration in range(n_iterations)

]

mdspace_iterations = []

for h5_path in iteration_archives:

    with MdspaceHdf5(h5_path) as archive:

        frames = [

            archive.registered(frame, selection="ca")

            for frame in range(archive.n_frames)

        ]

        mdspace_iterations.append(frames)

for i, frames in enumerate(mdspace_iterations):

    print(f"Iteration {i}: {len(frames)} structures")

The registered frames are used because they are already aligned into the same reference frame. This makes them appropriate for PCA.

---

## Build one common PCA space

To compare all structures visually, we need to project them onto a common PCA space.

We first combine the generated conformations and all MDSPACE iteration structures into one array. Each structure is flattened into a one-dimensional vector.

all_structures = []

labels = []

for structure in generated_structures:

    all_structures.append(structure.reshape(-1))

    labels.append("Generated")

for iteration, frames in enumerate(mdspace_iterations):

    for structure in frames:

        all_structures.append(structure.reshape(-1))

        labels.append(f"Iteration {iteration}")

X = np.asarray(all_structures)

labels = np.asarray(labels)

print("PCA input matrix:", X.shape)

Now compute the PCA projection:

pca = PCA(n_components=2)

projection = pca.fit_transform(X)

print("Explained variance:", pca.explained_variance_ratio_)

All structures are now represented in the same two-dimensional PCA coordinate system.

---

## Plot the MDSPACE iterations

We can now plot each MDSPACE iteration in the same PCA space.

Each subplot shows the synthetic generated conformations in the background and one MDSPACE iteration in the foreground.

fig, axes = plt.subplots(1, 4, figsize=(16, 4), sharex=True, sharey=True)

for iteration, ax in enumerate(axes):

    generated_mask = labels == "Generated"

    iteration_mask = labels == f"Iteration {iteration}"

    ax.scatter(

        projection[generated_mask, 0],

        projection[generated_mask, 1],

        s=12,

        alpha=0.35,

        label="Generated dataset",

    )

    ax.scatter(

        projection[iteration_mask, 0],

        projection[iteration_mask, 1],

        s=22,

        label=f"Iteration {iteration}",

    )

    ax.set_title(f"Iteration {iteration}")

    ax.set_xlabel("PC1")

    ax.set_ylabel("PC2")

axes[0].legend()

plt.tight_layout()

plt.show()

The important point is that all four subplots use the same PCA axes. This allows direct comparison between iterations.

If the recovery is progressing, later iterations should move toward the region occupied by the generated synthetic conformations.

---

## Interpret the result

The PCA plot gives a first visual summary of the recovery.

In this example, the generated synthetic conformations are distributed mainly along a line in PCA space. This is expected because the synthetic dataset was generated using a limited number of normal modes. The main structural variability is therefore constrained to a low-dimensional conformational path.

We can then compare the MDSPACE iterations with this reference distribution. From the first to the fourth iteration, the recovered structures progressively move closer to the region occupied by the generated conformations. This indicates that MDSPACE is recovering a conformational trend consistent with the synthetic dataset.

In other words, the PCA plot shows that the generated dataset spans the target conformational space, and that the MDSPACE iterations gradually approach it during the workflow.

This visualization should still be interpreted alongside direct structural inspection and RMSD measurements. PCA is useful for understanding the global trend, but it is not by itself a complete validation of the recovery.
