# Inspect the generated dataset

## Goal

In this section, we will inspect the synthetic dataset generated from the 6RAF HER2 conformation.

At the end of this section, you should understand:

- What files were produced during dataset generation.
- How to inspect the generated particle images.
- What information is stored in the dataset folder.

---

## Open the generated dataset

<video width="800" height="600" controls>   <source src="../assets/data_inspect.webm" type="video/webm"> </video>

In MDSPACE, the dataset will automatically open in the software once generation is complete. To reopen a dataset later, you have two options:

- Drag and drop the entire dataset folder into the MDSPACE main window.

- Alternatively, open the datagenerator tool from the main menu (under Tools > Dataset Generator), then click 'Open Folder' and navigate to your dataset folder or generator_params.txt file.

After loading the dataset, you should be able to inspect the generated particle images in the viewer, displaying them image by image with overlaid metadata, and also plot the distribution of the dataset metadata.

---

## Generated folder structure

After dataset generation, the output directory should contain several files and subfolders.

A typical generated folder may look like this:

| File or folder       | Meaning                                                                                 |
| -------------------- | --------------------------------------------------------------------------------------- |
| 6raf.pdb             | Reference input structure used during generation                                        |
| generator_params.txt | Parameters used for dataset generation                                                  |
| ctf.param            | CTF parameters used for microscope simulation                                           |
| generate_data.log    | Main generation log                                                                     |
| nma.log              | Normal mode analysis log                                                                |
| eigenvalues.txt      | Eigenvalues associated with the computed normal modes                                   |
| modes/               | Normal mode vectors used for deformation                                                |
| pdbs/                | Deformed PDB structures generated for individual particles (but not shifted or rotated) |
| volumes/             | Volumes generated from the PDB structures                                               |
| data_spi/            | Individual SPIDER images and associated metadata                                        |
| data_stack/          | Final MRCS image stack and metadata                                                     |
| generated_data.h5    | The coordinate-level ground truth                                                       |

---

## Inspect the generated images

When browsing the generated images, check the following points:

- Particles should be clearly visible, not clipped by the image boundaries, and surrounded by about 25% of their diameter by an empty margin. If the particle is too close to the border, adjust size, sampling, or resize.
- Record the final pixel size for later MDSPACE processing. If a resize factor is applied, the effective pixel size is final pixel size = sampling / resize.
- Particles should appear with different orientations if rotation sampling is enabled.
- Particles should be reasonably centered. Small shifts are expected if shift simulation is enabled, but particles should remain well inside the image box.
- The noise level should be compatible with the selected SNR and microscope-simulation parameters.
- There should be no obvious empty images, corrupted particles, or images where the particle is mostly outside the box.

---

## Inspect the metadata

The metadata files describe the simulated particles. You should find metadata in `data_spi/particles_spi.xmd` and `data_stack/particles.xmd`, and it should be displayed in the software individually when you browse the dataset.

---

## Inspect the HDF5 ground-truth archive

The file generated_data.h5 contains the coordinate-level ground truth associated with the synthetic dataset.

This file is useful for validation and later analysis. It stores the molecular coordinates used during generation, together with the projection-pose metadata used to create each particle image.

It is organized as follows:

| HDF5 dataset              | Meaning                                                                                                                                          |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| `/frames/raw`             | Generated molecular coordinates (Å) before projection rotation and shift. If deformation was enabled, these coordinates include the deformation. |
| `/frames/rotated`         | Same coordinates (Å) after applying the projection rotation and shift used to generate the particle image.                                       |
| `/transforms/euler`       | Euler angles (°) and image shifts (pixel) associated with each generated particle.                                                               |
| `/transforms/composed`    | Transform matrix associated with the projection pose (° and pixel).                                                                              |
| `/metadata/reference_pdb` | Reference PDB stored in the archive.                                                                                                             |
| `/metadata/pixel_size`    | Final pixel size of the generated images, in Å/pixel.                                                                                            |

Coordinate frames are stored in Å. Image shifts in the transform metadata are stored in pixels, following the MDSPACE/XMIPP metadata convention. The stored pixel size gives the conversion between pixel shifts and physical shifts.

For most users, this file does not need to be opened during the practical. It is mainly useful for post-processing, debugging, and checking that the recovered structures can be compared with the known generated conformations.

We will later introduce the [mdspace-analysis](https://github.com/MDSPACE-toolkit/mdspace-analysis) Python library, which can load and parse these HDF5 files to simplify the analysis.

## Inspect generated conformations

The `pdbs/` folder contains the generated PDB structures. You can drag and drop the PDBs directly into the software to display them and view the deformation. It can be useful to run the software in tiled mode to make side-by-side comparisons easier. Note that these structures are not shifted or rotated and are present only when deformation generation is enabled.

---

## Inspect generated volumes

The `volumes/` folder contains the density volumes generated from the PDB structures.

These volumes are intermediate files. They are generated from the atomic structures before being projected into 2D particle images and can be displayed by dragging and dropping them directly into the software.

---

## Inspect normal mode files

If normal mode deformation was enabled, the output folder also contains NMA-related files.

These files contain the normal mode vectors used to deform the structure. They can be inspected by dragging the reference PDB structure into the software, then dragging and dropping the mode file (`vec.n`) inside the newly created viewer window.
