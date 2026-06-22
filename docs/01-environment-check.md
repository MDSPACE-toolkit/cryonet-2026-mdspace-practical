# Introduction and environment check

The goal of this first step is to ensure that everyone can access a working Linux environment, start MDSPACE Desktop, and locate the files required for the practical.

At the end of this section, you should have:

- MDSPACE Desktop open and ready to use.
- Access to the workshop data folder.
- Access to the reference outputs, in case they are needed later.

---

## Workshop environment

MDSPACE Desktop is a standalone graphical application that implements the MDSPACE method and provides the preprocessing steps required to prepare input data. Some operations rely on external programs that must be available at runtime.

For this practical, participants can use any working Linux environment. This can be:

- a native Linux laptop;
- a Linux virtual machine on Windows or macOS;
- a Linux environment prepared in advance following the workshop instructions.

If you need to create a Linux virtual machine from scratch, AlmaLinux 9 is recommended because it closely matches the environment used to prepare and test the practical.

The detailed instructions for preparing a Linux environment are provided in the installation document.

During the practical, the instructor will provide the link to download the MDSPACE Desktop software bundle.

---

## MDSPACE Desktop installation options

On Linux, MDSPACE Desktop can be installed in two main ways.
Check the download page [here](https://gallois.cc/assets/cryonet.html).

### Native RPM installation

On Enterprise Linux 9 or 10 systems, including AlmaLinux, Rocky Linux, Red Hat Enterprise Linux, and compatible distributions, MDSPACE Desktop and its external dependencies can be installed directly with dnf using the provided RPM packages.

Separate packages are available for:

- EL9 and EL10;
- x86_64 and aarch64.

### Portable MDSPACE bundle

For the practical session, the recommended method is to use the portable MDSPACE bundle provided by the instructor.

The bundle includes MDSPACE Desktop together with the required external programs and libraries. Once started, it automatically extracts and configures its contents, so no system-wide installation of external dependencies is required.

Bundles are provided for:

- x86_64 systems;
- aarch64 / ARM64 systems.

The appropriate bundle must be selected according to the system architecture.

---

## Windows and macOS users

Native Windows and macOS packages are not currently provided.

Participants using Windows or macOS should run MDSPACE Desktop inside a Linux virtual machine. The virtual machine should be prepared before the practical, following the installation document.

The virtual-machine architecture must match the host architecture:

- x86_64 for Intel and AMD Windows computers and Intel-based Macs;
- aarch64 / ARM64 for Apple Silicon Macs.

During the practical, the instructor will provide the MDSPACE Desktop bundle to run inside the Linux virtual machine.

---

## Open MDSPACE Desktop

Open a terminal in the workshop environment and move to the folder containing the MDSPACE Desktop bundle.

For an x86_64 system, start MDSPACE Desktop with:

`QT_QPA_PLATFORM=xcb ./MdSpace-Desktop-bundle-x86_64.run`

For an aarch64 / ARM64 system, use the corresponding aarch64 bundle:

`QT_QPA_PLATFORM=xcb ./MdSpace-Desktop-bundle-aarch64.run`

If the command fails, check that you are in the correct directory and that the file is executable.

For x86_64:

`chmod +x ./MdSpace-Desktop-bundle-x86_64.run`

For aarch64 / ARM64:

`chmod +x ./MdSpace-Desktop-bundle-aarch64.run`

Then run the bundle again.

If MDSPACE Desktop still does not start, notify the instructor.

After opening the software, check that the main window appears correctly and that the graphical interface is responsive.

MDSPACE Desktop follows a Multiple Document Interface (MDI) design. Several internal windows can be opened simultaneously and arranged according to the user’s preferences.

The layout can be adjusted using the View menu. The font size can be increased using the Accessibility menu.

Dock widgets can also be rearranged freely. They can be kept inside the main window, moved to another docking area, or floated as independent panels.

---

## Locate the workshop data

The workshop data folder contains the files required for the practical. It should be located alongside the MDSPACE bundle or in the folder indicated by the instructor.

The main resources are:

| Resource          | Purpose                                                                                                     |
| ----------------- | ----------------------------------------------------------------------------------------------------------- |
| Data              | Input structures used to generate the synthetic cryo-EM-like dataset and to initialize the MDSPACE workflow |
| Reference dataset | Precomputed dataset of 500 images                                                                           |
| Reference outputs | Precomputed MDSPACE results used for comparison and as a fallback if a live run fails or takes too long     |

---

## Reference material

Reference material is provided so that all participants can continue the practical even if a live run fails or takes longer than expected.

The reference material can be downloaded [here](https://gallois.cc/assets/cryonet/cryonet-2026.tar.gz).

It includes:

- a precomputed synthetic dataset;
- a completed MDSPACE analysis;
- post-processing Python code.

The structures used in the practical can also be downloaded from the RCSB PDB:

- [6RAF](https://gallois.cc/assets/6RAF.pdb)
- [6RAH](https://files.rcsb.org/download/6RAH.pdb)

The completed MDSPACE analysis can be opened by dragging the corresponding analysis folder into the MDSPACE Desktop interface.

If a live run fails or takes longer than expected, use the provided reference outputs to continue the practical. Open the relevant reference analysis by dragging its folder into MDSPACE Desktop, then follow the remaining steps using the reference analysis as you would use your own results.
