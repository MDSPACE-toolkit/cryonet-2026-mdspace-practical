# Introduction and environment check

The goal of this first step is to ensure everyone can access the workshop environment, open MDSPACE Desktop, and locate the required files for the practical.

At the end of this section, you should have:

- MDSPACE Desktop open and ready to use.
- Access to the workshop data folder.
- Access to the reference outputs, in case they are needed later.

---

## MDSPACE Desktop

MDSPACE Desktop is a standalone graphical application that implements the MDSPACE method and provides all the preprocessing steps required to prepare the input data. Several of these steps rely on external programs, which must be available to MDSPACE Desktop at runtime.

On Linux, the distributed MDSPACE bundle includes all required external dependencies. They are automatically extracted and configured at runtime, so no additional installation is required.

On other operating systems, these external dependencies are not currently bundled and must be compiled and installed manually. The required dependencies are:

- [XMIPP](https://github.com/I2PC/xmipp)
- [The MDSPACE-modified version of ELNEMO](https://github.com/MDSPACE-toolkit/nma)
- [MDSPACE-GENESIS](https://github.com/MDSPACE-toolkit/mdspace-genesis)
- Optionally, a topology generator such as [SMOG 2](https://smog-server.org/smog2/) or [Martinize2](https://github.com/marrink-lab/vermouth-martinize). Alternatively, topology files can be generated separately and imported into MDSPACE Desktop.

---

## Open MDSPACE

Start MDSPACE from the workshop environment, opening a terminal and typing ./MdSpace-Desktop-bundle-x86_64.run.

If the command fails or MDSPACE does not start, check that you are in the correct directory and that the file is executable. You can make the file executable by running chmod +x ./MdSpace-Desktop-bundle-x86_64.run. If issues persist, notify the instructor for assistance.

After opening the software, check that the main window appears correctly and that the graphical interface is responsive. You should be able to identify the main areas of the interface.

MDSPACE follows a Multiple Document Interface (MDI) design: several internal windows can be opened at the same time and arranged according to the user’s preferences.

The layout can be adjusted using the View menu, and the font can be increased in the Accessibility menu.

Dock widgets can also be rearranged freely. They can be kept inside the main window, moved to another docking area, or floated as independent panels.

---

## Locate the workshop data

The workshop data folder contains the files required for the practical.

You should be able to locate this folder alongside the MDSPACE bundle.

| Resource          | Purpose                                                                                                                     |
| ----------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Data              | The structure used to generate the synthetic cryo-EM-like dataset and the structure used to initialize the MDSPACE workflow |
| Reference dataset | Precomputed dataset of 500 images                                                                                           |
| Reference outputs | Precomputed results used for comparison and fallback (optimized parameters)                                                 |

---

## Check access to reference outputs

Reference outputs are provided so that all participants can continue the practical even if a live run fails or takes longer than expected.

The reference material can be downloaded [here](https://gallois.cc/assets/cryonet/archive.tar.gz) and includes:

- A precomputed synthetic dataset.
- A completed MDSPACE analysis.
- Post-processing Python code.

The completed MDSPACE analysis can be opened by dragging the folder inside the MDSPACE interface. If a live run fails or takes longer than expected, use the provided reference outputs to continue the practical. Simply open the relevant reference analysis by dragging its folder into the MDSPACE interface, then follow the remaining steps in the practical using the data from the reference analysis as you would with your own results.
