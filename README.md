# TYD_ForceField_Parameters_FF19SB
## 1. Background
* In the pKa analysis conducted using [H++](http://newbiophysics.cs.vt.edu/H++/index.php), it was noted that the tyrosine residue, positioned identically in chain A and chain B of the dimeric protein - which share the same sequence - exhibits distinct pKa values across the two chains.
* Two figures below show the titration curve of the TYR240 on Chain A (left) and Chain B(right).

<p align="left">
  <img align="left" src="./img/TYR_A_pKa.svg" alt="pKa=11.7" width="50%" class="center">
</p>

<p align="right">
  <img align="right" src="./img/TYR_B_pKa.svg" alt="pKa=6.9" width="50%" class="center">
</p>

* The pKa of TYR240 in chain A is 11.9 which looks normal. However, TYR240 in chain B as much lower pKa of 6.9. After a thorough analysis of the local environment and particularly the interactions with ARG, I am persuaded that the TYR240B is likely to be deprotonated. Furthermore, attempts to simulate a reasonable conformation of TYR240B using molecular dynamics in Amber have not been successful, lending further conficence to this hypothesis.

## 2. Build the force field
* While updating force field parameters for a modified amino acid is not a common occurrence, the available [tutorials](https://ambermd.org/tutorials/basic/tutorial5/index.php) on the Amber website, including the one for the protein force field of ff14SB, are somewhat outdated. I've found an [Amber mail archive](http://archive.ambermd.org/202203/0174.html) that provides parameters for the deprotonated tyrosine (TYD) specifically for ff14SB. However, my current need is for a compatible set of force field parameters for TYD that would work seamlessly with ff19SB.
* After downloading the TYD.lib and TYD.frcmod files for the deprotonated tyrosine, I have referred to the TYR force field parameters within the [amino19.lib file](https://github.com/csimmerling/ff19SB_201907/tree/master/forcefield_files) and made the necessary modifications to TYD.lib, considering the removal of the hydroxyl hydrogen (HO) to represent the deprotonated state to ensure compatibility with ff19SB. 

    ``````
    tleap -s -f tleap_19.in
    ``````

* After implementing modifications to the `./TYD_ff14SB/TYD.lib` file for integration with the ff19SB force field, running the tleap script resulted in significantly fewer errors compared to the initial attempt using the ff14SB parameters. However, I've encountered a couple of persistent errors related to missing angle parameters between the deprotonated oxygen and the two adjacent aromatic carbons in the tyrosine structure. The modified TYD.lib is here: `./TYD_ff19SB/TYD.lib`.

    ```
    ~/Amber22/amber22/bin/teLeap: Error!
    Could not find angle parameter for atom types: O - C - CA
            for atom OH at position 8.301000, -84.438000, -55.844000,
                atom CZ at position 8.074000, -85.029000, -54.653000,
            and atom CE2 at position 8.305000, -84.351000, -53.441000.

    ~/Amber22/amber22/bin/teLeap: Error!
    Could not find angle parameter for atom types: CA - C - O
            for atom CE1 at position 7.626000, -86.356000, -54.649000,
                atom CZ at position 8.074000, -85.029000, -54.653000,
            and atom OH at position 8.301000, -84.438000, -55.844000.
    ```
* To resolve the remaining errors, I added the angle parameters for the O - C - CA and CA - C - O interactions in the `./ff19SB/TYD.frcmod` file, specific to the deprotonated tyrosine's aromatic ring. Given the structural rigidity of the aromatic ring, these angles (120.210) are expected to be relatively consistent and can be derived from established parameters for similar phenolic structures. 
* After careful consideration, the force constant for the angle involving the deprotonated oxygen and adjacent carbon atoms on the aromatic ring (O - C - CA and CA - C - O) was set at 80.000 kcal/mol.rad^2. This value is consistent with the characteristic rigidity of aromatic rings and is in line with common practices for similar atom types in the ff19SB force field. This setup aims to balance the structural integrity of the tyrosine residue's phenolic ring with the dynamic nature of molecular simulations. Further validation of these parameters will be conducted through energy minimization and molecular dynamics simulation to ensure the stability and realism of the modeled systems. 

    ```
    ANGLE
    CA-C -O    80.000     120.210   
    O -C -CA   80.000     120.210
    ```
* Successfully resolved the force field parameter errors, and no further issues were identified in the tleap output. This indicates that the modified force field parameters for the deprotonated tyrosine (TYD) are now correctly formatted and recognized by the Amber software.