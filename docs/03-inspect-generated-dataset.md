# Inspect the generated dataset

## Goal

In this section, we will inspect the synthetic dataset generated from the `6RAF` conformation.

At the end of this section, you should understand:

- Which files are produced during dataset generation.
- How to inspect the generated particle images.
- What information is stored in the dataset directory.

---

## Open the generated dataset

<video width="800" controls loop muted autoplay playsinline>
  <source src="../assets/data_inspect.webm" type="video/webm">
  <a href="https://mdspace-toolkit.github.io/cryonet-2026-mdspace-practical/assets/data_inspect.webm">
    Open the video in the interactive online version
  </a>
</video>

/// caption
Fig. 5. Dataset inspection using MDSPACE Desktop.
///

In MDSPACE Desktop, the dataset will open automatically once generation is complete. To reopen a dataset later, you have two options:

- Drag and drop the entire dataset folder into the MDSPACE main window.
- Alternatively, open the synthetic-dataset simulator from the main menu (**Tools > Simulate cryo-EM/cryo-ET data**), choose **Open existing synthetic dataset**, then select `generator_params.txt` inside the dataset folder. You can also drop either that file or the folder that contains it into the main window.

After loading the dataset, use the **Generated data** result tab to inspect the particle images or subtomograms record by record, display their metadata, and plot metadata distributions. For standalone inspection, an XMD, STAR, or CS metadata file can also be dropped into the main window or opened using **Tools > Viewers > Cryo-EM data viewer**.

---

## Generated folder structure

After dataset generation, the output directory should contain several files and subfolders.

A typical generated folder may look like this:

| File or folder        | Meaning                                                                                 |
| --------------------- | --------------------------------------------------------------------------------------- |
| reference_centered.pdb| The reference structure translated so that its center of mass is at the origin          |
| generator_params.txt  | Parameters used for dataset generation                                                  |
| ctf.param             | CTF parameters used for microscope simulation                                           |
| generate_data.log     | Main generation log                                                                     |
| nma.log               | Normal mode analysis log                                                                |
| eigenvalues.txt       | Eigenvalues associated with the computed normal modes                                   |
| modes/                | Normal mode vectors used for deformation                                                |
| ground_truth_pdb/     | Optional deformed PDB structures: `raw_<index>.pdb` is centered and deformed; `rotated_<index>.pdb` also has the generated pose |
| data_spi/             | Individual SPIDER images and associated metadata for the EM variant                     |
| data_stack/           | Final MRCS image stack and metadata for the EM variant                                  |
| data_volumes/         | Final subtomogram stack and metadata for the ET variant                                |
| tilt_series/          | Optional retained tilt images and selection files when **Tilt-series output** was set to `Save tilt series` |
| generated_data.h5     | The coordinate-level ground truth                                                       |

---

## Inspect the generated images

When browsing the generated images, check the following points:

- Particles should be clearly visible, not clipped by the image boundaries, and surrounded by about 25% of their diameter by an empty margin. If the particle is too close to the border, adjust size and sampling.
- Record the pixel size for later MDSPACE processing. For this practical it remains 2 Å/pixel.
- Particles should appear with different orientations if rotation sampling is enabled.
- Particles should be reasonably centered. Small shifts are expected if shift simulation is enabled, but particles should remain well inside the image box.
- The noise level should be compatible with the selected SNR and microscope-simulation parameters.
- There should be no obvious empty images, corrupted particles, or images where the particle is mostly outside the box.

> You can restart the generation process at any time by clicking **Run current stage**. To obtain a good dataset, use an iterative approach: start with a small number of noise-free images (for example, 5), tune the pixel size and box size, then try different noise levels. Finally, select the target number of images and generate the full dataset.

---

## Inspect the metadata

The metadata files describe the simulated particles. Depending on the selected variant, inspect:

=== "Single-particle EM"

    - `data_spi/particles_spi.xmd`: individual SPIDER images and metadata.
    - `data_stack/particles.xmd`: final MRCS stack and metadata.
    - The metadata can include per-particle CTF defocus values. Defocus distributions are available for inspection when a distributed CTF defocus was selected during generation; this practical does not vary defocus.

=== "Tomography ET"

    - `data_volumes/subtomograms.xmd`: generated subtomograms and their metadata.

The selected metadata should be displayed in the software when you browse the generated dataset.

---

## Inspect the HDF5 ground-truth archive

The file `generated_data.h5` contains the coordinate-level ground truth associated with the synthetic dataset.

This file is useful for validation and later analysis. It stores the molecular coordinates used during generation, together with the projection-pose metadata used to create each particle image.

It is organized as follows:

| HDF5 dataset            | Meaning                                                                                                                                          |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| /frames/raw             | Generated molecular coordinates (Å) before projection rotation and shift. If deformation was enabled, these coordinates include the deformation. |
| /frames/rotated         | Same coordinates (Å) after applying the projection rotation and shift used to generate the particle image.                                       |
| /transforms/euler       | Euler angles (°) and image shifts (pixels) associated with each generated particle.                                                              |
| /transforms/composed    | Transform matrix associated with the projection pose (translation expressed in pixel units).                                                     |
| /metadata/reference_pdb | Reference PDB stored in the archive as a string.                                                                                                 |
| /metadata/pixel_size    | Final pixel size of the generated images, in Å/pixel.                                                                                            |

Coordinate frames are stored in Å. Image shifts in the transform metadata are stored in pixels. The stored pixel size gives the conversion between pixel shifts and physical shifts.

For most users, this file does not need to be parsed manually. It is mainly useful for post-processing, debugging, and checking that the recovered structures can be compared with the known generated conformations.

We will later introduce the [mdspace-analysis](https://github.com/MDSPACE-toolkit/mdspace-analysis) Python library, which can load and parse these HDF5 files to simplify the analysis.

## Inspect generated conformations

The `ground_truth_pdb/` folder contains generated PDB structures when `HDF5 + PDBs` was selected. You can drag and drop a `raw_<index>.pdb` or `rotated_<index>.pdb` file directly onto the main window to open a Structure & map viewer. The raw file contains centered, deformed coordinates; the rotated file also has the generated pose. It can be useful to run the software in tiled mode to make side-by-side comparisons easier.

---

## Inspect normal mode files

If normal mode deformation was enabled, the output folder also contains NMA-related files.

These files contain the normal-mode vectors used to deform the structure. After generation or reload, inspect them directly in the simulator's **Normal modes** result tab.

For standalone inspection, open **Tools > Viewers > Normal mode viewer** and select `reference_centered.pdb` together with a mode file such as `modes/vec.7`. The viewer loads the other `vec.N` files from the same directory. You can also drop the PDB and the `modes/` directory together into the main window or into an open Normal mode viewer.
