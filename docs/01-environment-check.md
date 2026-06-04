# Introduction and environment check

The goal of this first step is to ensure everyone can access the workshop environment, open MDSPACE, and locate the required files for the practical.

At the end of this section, you should have:

- MDSPACE open and ready to use.
- Access to the workshop data folder.
- Access to the reference outputs, in case they are needed later.

---

## Workshop environment

The practical will be performed in the computing environment provided for the workshop, which should already include MDSPACE installed as a bundle.

---

## Open MDSPACE

Start MDSPACE from the workshop environment, opening a terminal and typing `./MdSpace-bundle-el9-x86_64.run`.

If the command fails or MDSPACE does not start, check that you are in the correct directory and that the file is executable. You can make the file executable by running `chmod +x ./MdSpace-bundle-el9-x86_64.run`. If issues persist, notify the instructor for assistance.

After opening the software, check that the main window appears correctly and that the graphical interface is responsive. You should be able to identify the main areas of the interface.

MDSPACE follows a Multiple Document Interface (MDI) design: several internal windows can be opened at the same time and arranged according to the user’s preferences. The layout can be adjusted using the **View** menu. The **Accessibility** menu allows users to increase font and contrast.

Dock widgets can also be rearranged freely. They can be kept inside the main window, moved to another docking area, or floated as independent panels.

---

## Locate the workshop data

The workshop data folder contains the files required for the practical.

You should be able to locate this folder alongside the MDSPACE bundle.

| Resource          | Purpose                                                                                                                     |
| ----------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Data              | The structure used to generate the synthetic cryo-EM-like dataset and the structure used to initialize the MDSPACE workflow |
| Reference Dataset | Precomputed dataset                                                                                                         |
| Reference outputs | Precomputed results used for comparison and fallback                                                                        |

---

## Check access to reference outputs

Reference outputs are provided so that all participants can continue the practical even if a live run fails or takes longer than expected.

The reference material includes:

- A precomputed synthetic dataset.
- A completed MDSPACE analysis.
- Post-processing results.
- Example visualizations.
- Final structural-comparison outputs.

The completed MDSPACE analysis can be opened by dragging the folder inside the MDSPACE interface. If a live run fails or takes longer than expected, use the provided reference outputs to keep up with the practical. Simply open the relevant reference analysis by dragging its folder into the MDSPACE interface, then follow the remaining steps in the practical using the data from the reference analysis as you would with your own results.
