# Wrap-up

In this practical, we used a synthetic dataset to test whether MDSPACE can recover conformational variability from cryo-EM particle images.

We started from an initial conformation different from the generated target ensemble and ran the MDSPACE workflow over several iterations. The post-processing analysis showed two complementary results:

- In the PCA projection, the recovered MDSPACE structures progressively moved toward the generated ground-truth conformational space.
- In the RMSD analysis, the per-image structural error decreased across iterations.

These results show that the iterative MDSPACE procedure can recover part of the conformational landscape encoded in the synthetic images. The recovery is not exact, and the fitted structures remain more dispersed than the ground-truth conformations, but the global trend is clear: the ensemble evolves toward the expected conformational region.

This practical also illustrates an important point about flexible fitting methods: the results depend on both the image signal and the simulation parameters. Parameters such as the force constant, simulation length, number of iterations, image size, and noise level can influence the strength and stability of the recovery. In practice, these parameters were adjusted to balance computation time and recovery quality, but better results can be achieved with more refined parameter settings.

## Current implementation status

This practical uses the current standalone C++ implementation of MDSPACE. Development of the new standalone implementation began in October 2025 with the goal of making the MDSPACE workflow easier to run outside the original Scipion/ContinuousFlex environment.

The current version already supports the main steps required for this practical:

- Generation of synthetic cryo-EM projection images from a known conformational ensemble.
- Initialization of an MDSPACE project from a starting atomic structure.
- Iterative flexible fitting using molecular dynamics.
- Storage of recovered structures and metadata in HDF5 archives.
- A companion post-processing Python library.

The software is currently used through two complementary execution modes. For small-scale tests and teaching examples, the workflow can be run locally using the standalone graphical interface. For larger runs, an HPC workflow is also available: the project and preprocessing steps are prepared with the graphical interface, while the computationally expensive MDSPACE fitting step is submitted and executed on an HPC system.

The implementation remains under active development and is being stabilized in preparation for public release. At the moment, the software is not publicly available. If you would like to try it, please contact us.

Future improvements will focus on robustness, reduced computation time, improved HPC integration, and an easier-to-use workflow for larger or more realistic datasets.
