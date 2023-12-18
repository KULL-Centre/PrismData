Changelog
=========


<!--
Unreleased
----------
### Added
### Fixed
### Changed
### Deprecated
### Removed
-->

Version 2.0.0 - Dec 18 2023
-------------------
Version 2 depends on BioPython 1.8 or greater 

### Added
- Outputs coverage and identity in the header of merged files

### Fixed
- Several minor bugs were fixed

### Removed
- Function `__align_path_convert`  is removed and parsing of alignment return objects now rely on the `__getitem__` method

Version 1.2
-----------

Internal test version renamed to 2.0 because the change in BioPython dependency is not backwards compatible 

Version 1.1
-----------

Version used in the Rosetta pipeline up to version 0.2.6 and possible newer. It depends on BioPython=1.76 with `Bio.Align` and a particular version of string formatting of alignment return objects (`format()` function) used in `__align_path_convert`

WARNING: This version is faulty if used with anything but BioPython version 1.76 because the string formatting method changed. Symptome is that any attempt on alignment will alway remove the 6 first residues and nothing else. 

### Changed
- The central aligner was changed from `pairwise2` to the `Bio.Align` because the substitutions matrices from `Bio.SubsMat` were no longer available


Version 1.002
-------------

Version available in depreciated repository on binf system. Depends on BioPython 1.76 with `pairwise2` and `Bio.SubsMat`
