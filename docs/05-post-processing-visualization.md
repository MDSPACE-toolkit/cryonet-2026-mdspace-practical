# Post-processing and visualization

In this section, we will inspect the results produced by the MDSPACE workflow and compare them with the known structures from the synthetic experiment.

We will start with a quick inspection inside the MDSPACE desktop, then move to a Python analysis using the mdspace-analysis library. The Python part will allow us to load the generated conformations and the structures produced at each MDSPACE iteration, with more flexibility than in MDSPACE desktop. The mdspace-analysis is kept in synch with the MDSPACE C++ library and MDSPACE desktop and should be installed and used using the same major version used to produce the data.

The goal is to check whether the conformations recovered by MDSPACE progressively move toward the synthetic conformational space used to generate the particle images.

---

## Quick inspection in MDSPACE

<video width="800" height="600" controls>

 <source src="../assets/mdspace_inspect.webm" type="video/webm">

</video>

Once the MDSPACE run has completed, first inspect the results directly in the software. If the provided machines are too slow to run the analysis in a reasonable time, it is time to load the completed MDSPACE analysis provided in the practical data folder.

Open the simulation tab and check that the MD jobs finished correctly. MDSPACE desktop can display all molecular dynamics simulation outputs, averaged across all simulations, including the image correlation coefficient (REST_CV1) and other useful quantities.

Then open the conformational landscape tab. MDSPACE desktop can display quick PCA and UMAP projections of the recovered structures. At this stage, these plots are mainly used as diagnostic views: they help check whether the results look reasonable, whether some structures behave as outliers, and whether the analysis parameters are appropriate. Clicking on the map can select a structure and display the deformation during the simulation, the associated image, and the data for this given simulation. Selecting several points can reconstruct a volume and see the averaged MD simulation data.

This software-based inspection is useful for a first look, but we will continue the analysis in Python to compare the recovered structures with the synthetic ground truth and gain greater flexibility.

---

## Python analysis

We will now use the mdspace-analysis Python package to inspect the HDF5 files produced by MDSPACE.

The analysis will use two sources of structures:

- The synthetic conformations stored in generated_data.h5;
- The registered structures stored in each MDSPACE iteration folder, for example, `genesis/000/coords.h5`, `genesis/001/coords.h5`, and so on.

The synthetic structures correspond to the conformations used to generate the particle images. They are deformed structures before projection, rotation, and image shifts. The MDSPACE structures will be loaded from the registered frames, as these have already been placed in a common coordinate frame (the image one) that matches the synthetic structure.

Because the 6RAF and 6RAH can have different atom orderings and atom counts, we will first compute a paired C-alpha selection between the two archives. This ensures that all structures are compared using the same atoms in the same order.

We will then project all structures into the same PCA space. We will also compute RMSD-based summaries to compare the recovered structures with the generated synthetic conformations.

---

## Install the analysis tools

Install the plotting dependencies and the mdspace-analysis package:

```bash
python -m venv venv
source venv/bin/activate

pip install scikit-learn matplotlib

pip install https://github.com/MDSPACE-toolkit/mdspace-analysis/releases/download/v0.1.0/mdspace_analysis-0.1.0-py3-none-any.whl
```

Then import the required modules:

```python
from pathlib import Path
import numpy as np
import matplotlib.pyplot as plt
from sklearn.decomposition import PCA
from mdspace_analysis import MdspaceHdf5
from mdspace_analysis.geometry import align_coordinates, rmsd
```

---

## Set the input paths

Set the path to the generated synthetic dataset and to the MDSPACE project folder:

```python
# Path to the generated synthetic dataset.
generated_h5 = Path("/home/guest/Public/out/generated_data.h5")

# Path to the MDSPACE project directory.
project_dir = Path("/home/guest/Public/0eaf7db35391a5d2/")
```

Adapt these paths to match the folder names used during the practical.

Then define the HDF5 archive produced at each MDSPACE iteration:

```python
iteration_archives = [
    project_dir / "genesis" / f"{iteration:03d}" / "coords.h5"
    for iteration in range(4)
]
```

---

## Compute the common C-alpha selection

Before comparing the generated structures with the MDSPACE structures, we need to make sure that both datasets use the same atoms in the same order.

This step is important because the generated structure and the MDSPACE structure may not use exactly the same residue numbering. For example, one PDB may keep the original residue numbers, while another one may have been renumbered during preprocessing. In addition, the generated dataset files contain full atoms, whereas the MDSPACE outputs contain only carbon alpha atoms.

We therefore compute a paired C-alpha selection between the generated archive and the first MDSPACE archive:

```python
with MdspaceHdf5(generated_h5) as generated_archive, MdspaceHdf5(iteration_archives[0]) as mdspace_archive:
    common_selection = generated_archive.selection_from_archive(
        mdspace_archive,
        selection="ca",
    )

print("Common C-alpha atoms:", common_selection.size)
```

The resulting selection contains the C-alpha atoms that are present in both archives and can be safely compared.

---

## Load the synthetically generated conformations

The generated dataset contains an HDF5 file named generated_data.h5.

This file stores the coordinate-level ground truth of the synthetic dataset. For the PCA comparison, we use the raw generated conformations because they represent the synthetic structures before projection, rotation, and image shifts.

```python
with MdspaceHdf5(generated_h5) as archive:
    generated_structures = [
        archive.raw(frame, selection=common_selection.left)
        for frame in range(archive.n_frames)
    ]

generated_structures = np.asarray(generated_structures)

print("Generated structures:", generated_structures.shape[0])

print("Common C-alpha atoms:", generated_structures.shape[1])
```

---

## Load the MDSPACE iterations

Each MDSPACE iteration writes an HDF5 archive in its corresponding genesis folder.

For example, if four iterations were run, the project may contain:

```bash
genesis/000/coords.h5

genesis/001/coords.h5

genesis/002/coords.h5

genesis/003/coords.h5
```

We load the registered C-alpha structures from each iteration:

```python
mdspace_iterations = []
for h5_path in iteration_archives:
    with MdspaceHdf5(h5_path) as archive:
        frames_by_image = {}
        for frame in range(archive.n_frames):
            image = archive.image_index(frame)
            structure = archive.registered(
                frame,
                selection=common_selection.right,
            )
            frames_by_image[image] = structure
        mdspace_iterations.append(frames_by_image)

for iteration, frames in enumerate(mdspace_iterations):
    print(f"Iteration {iteration}: {len(frames)} structures")
```

The registered frames are used because they have already been aligned and share the same frame of reference as the generated data structures, making them appropriate for PCA.

Using image_index(frame) to return a dictionary {image index, structure} is safer than assuming that frame 0 always corresponds to image 0, because some images may be skipped or dropped during processing if MD simulations fail.

---

## Build one common PCA space

To visually compare all structures, we project them onto a common PCA space.

Here, the PCA space is computed using only the generated ground-truth conformations. The MDSPACE structures recovered at each iteration are then projected onto the same PCA basis.

This choice makes the interpretation more direct: PC1 and PC2 describe the main conformational variability present in the generated dataset. Therefore, the position of the MDSPACE structures shows how close each iteration is to the known ground-truth conformational trajectory.

As a result, the subplots can be compared in a single shared coordinate system, while keeping the generated conformational space as the reference.

```python
generated_X = np.asarray([
    structure.reshape(-1)
    for structure in generated_structures
])
print("Generated PCA input matrix:", generated_X.shape)

pca = PCA(n_components=2)
generated_projection = pca.fit_transform(generated_X)
print("Generated-only explained variance:", pca.explained_variance_ratio_)
```

Now compute the PCA projection:

```python
mdspace_projections = []
for iteration, frames in enumerate(mdspace_iterations):
    iteration_X = np.asarray([
        structure.reshape(-1)
        for _, structure in sorted(frames.items())
    ])
    iteration_projection = pca.transform(iteration_X)
    mdspace_projections.append(iteration_projection)
    print(
        f"Iteration {iteration} projected matrix:",
        iteration_X.shape,
    )
```

---

## Plot the MDSPACE iterations

We can now plot each MDSPACE iteration in the same PCA space.

Each subplot shows the synthetic generated conformations in the background and one MDSPACE iteration in the foreground.

```python
fig, axes = plt.subplots(1, 4, figsize=(16, 4), sharex=True, sharey=True)
generated_mask = labels == "Generated"
for iteration, ax in enumerate(axes):
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
        alpha=0.8,
        label="Iteration",
    )

    ax.set_title(f"Iteration {iteration}")
    ax.set_xlabel("PC1")

axes[0].set_ylabel("PC2")
axes[0].legend()
plt.tight_layout()
plt.show()
```

The important point is that all four subplots use the same PCA axes. This allows direct comparison between iterations.

---

## RMSD analysis

The PCA plot gives a visual summary of the conformational recovery. We can complement it with a coordinate-based measurement using RMSD.

A strict per-image RMSD compares each recovered structure to the generated conformation associated with the same image index. This is useful if the goal is to check one-to-one particle-level recovery.

---

## Plot per-image RMSD by iteration

In this dataset, the generated archive is guaranteed to contain all synthetic frames. Therefore, the generated frame index can be used directly as the image index.

The MDSPACE archives may contain only a subset of recovered structures. Therefore, for each MDSPACE archive, we use frame_by_image_index() to map:

`image index -> MDSPACE frame index`

Then we only compare recovered structures whose image indices are present in the generated archive.

```python
rmsd_by_iteration = []
n_matched_by_iteration = []
with MdspaceHdf5(generated_h5) as generated_archive:
    n_generated_frames = generated_archive.n_frames
    for iteration, h5_path in enumerate(iteration_archives):
        rmsd_values = []
        with MdspaceHdf5(h5_path) as mdspace_archive:
            mdspace_frame_by_image = mdspace_archive.frame_by_image_index()
            common_images = sorted(
                image
                for image in mdspace_frame_by_image
                if 0 <= image < n_generated_frames
            )

            print(
                f"Iteration {iteration}: "
                f"{len(common_images)} matched images"

            )

            for image in common_images:
                mdspace_frame = mdspace_frame_by_image[image]
                target = generated_archive.raw(
                    image,
                    selection=common_selection.left,
                )

                structure = mdspace_archive.registered(
                    mdspace_frame,
                    selection=common_selection.right,
                )

                aligned_structure = align_coordinates(structure, target)
                value = rmsd(aligned_structure, target)
                rmsd_values.append(value)

        rmsd_values = np.asarray(rmsd_values, dtype=float)
        rmsd_by_iteration.append(rmsd_values)
        n_matched_by_iteration.append(rmsd_values.size)
        if rmsd_values.size == 0:
            print(f"Iteration {iteration}: no matched structures")
            continue

        print(
            f"Iteration {iteration}: "
            f"median RMSD = {np.median(rmsd_values):.3f} Å, "
            f"mean RMSD = {rmsd_values.mean():.3f} Å, "
            f"std = {rmsd_values.std():.3f} Å"
        )

iterations = np.arange(len(rmsd_by_iteration))
valid_positions = []
valid_rmsd_values = []
for iteration, values in enumerate(rmsd_by_iteration):
    if values.size == 0:
        continue
    valid_positions.append(iteration)
    valid_rmsd_values.append(values)

plt.figure(figsize=(6, 4))
plt.boxplot(
    valid_rmsd_values,
    positions=valid_positions,
    widths=0.6,
    showmeans=True,
)

plt.xlabel("MDSPACE iteration")
plt.ylabel("RMSD to matching generated structure (Å)")
plt.title("Per-image recovery error across MDSPACE iterations")
plt.xticks(iterations)
plt.tight_layout()
plt.show()
```

---

## Interpretation of the results
