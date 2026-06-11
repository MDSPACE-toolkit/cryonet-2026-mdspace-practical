# Wrap-up

In this practical, we used a synthetic dataset to test whether MDSPACE can recover conformational variability from cryo-EM-like projection images.

We started from an initial conformation different from the generated target ensemble and ran the MDSPACE workflow over several iterations. The post-processing analysis showed two complementary results:

* In the PCA projection, the recovered MDSPACE structures progressively moved toward the generated ground-truth conformational space.
* In the RMSD analysis, the per-image structural error decreased across iterations.

These results show that the iterative MDSPACE procedure can recover part of the conformational landscape encoded in the synthetic images. The recovery is not exact, and the fitted structures remain more dispersed than the ground-truth conformations, but the global trend is clear: the ensemble evolves toward the expected conformational region.

This practical also illustrates an important point about flexible fitting methods: the results depend on both the image signal and the simulation parameters. Parameters such as force constant, simulation length, number of iterations, image size, and noise level can influence the strength and stability of the recovery. In practice, these parameters were adjusted to balance computation time and recovery quality, but better results can be achieved with more refined parameter settings.

Current implementation status

This practical uses the current standalone C++ implementation of MDSPACE. The new implementation started in October 2025 with the goal of making the MDSPACE workflow easier to run outside the original Scipion/ContinuousFlex environment.

The current version already supports the main steps required for this practical:

* Generation of synthetic cryo-EM-like projection images from a known conformational ensemble.
* Initialization of an MDSPACE project from a starting atomic structure.
* Iterative flexible fitting using molecular dynamics.
* Storage of recovered structures and metadata in HDF5 archives.
* A companion post-processing Python library.

The implementation is still under active development. The implementation should be stabilized and prepared for public release in 2026. Future improvements will focus on robustness, reduced computation time, and an easier-to-use workflow for larger or more realistic datasets.
