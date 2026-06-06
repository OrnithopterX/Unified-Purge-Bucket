# Unified-Purge-Bucket
An all in one nozzle cleaning system for the Voron 2.4 

This project was inspired by the Decontaminator Purge Bucket & Nozzle Scrubber (Which can be found here: https://github.com/VoronDesign/VoronUsers/tree/main/orphaned_mods/edwardyeeks/Decontaminator_Purge_Bucket_&_Nozzle_Scrubber). This project attemps to pick up where the Decontaminator Purge Bucket & Nozzle Scrubber left off. 


The Unified Purge Bucket is comprised of two parts, the cleaning station and the nozzle cleaning macro. Here are the updates for both: 

The cleaning station
 - The bucket now contains the nozzle brush. This makes cleaning the brush much easier.
 - The cleaning station is located right next to the z-probe, making nozzle cleaning before a print faster.
 - The purge bucket covers the bed wires, which both keeps the printer cleaning, but also looks much neater too!
 - Sheet stops are intergrated into the bucket mount. 

Macro 
 - The clean_nozzle macro can now run diring a print, and resume printing after the cleaning is done. 
 - Fixed error that is thrown when clean_nozzle is run before homing. Now the macro automatically homes all (G28) before cleaning, if needed.

Required Hardware: 
- 4x M3 t-nuts
- 4x M3x6mm bolts
- 4x M2x10 self-tapping screws
- 8x 6x3mm neodymium magnets

We are currently in open beta! I have tested this system for many weeks, but I want to make it even better. Please let me know how it works for you! 
