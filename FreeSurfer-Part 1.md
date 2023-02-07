---
Author: "Shiya"
Title: "Introduction/Single Subject/Analysis_ROI/Analysis"
Status: Ongoing
Date: 06-02-2023
Tags: "freesurfer"
---

### File formats

| Format     | Description                                                           |
| ---------- | --------------------------------------------------------------------- |
| file.dcm   | Input: a single DICOM file from the T1 MRI series                     |
| sample.mgz | Output: reconctructed MRI data from FreeSurfer; compressed mgh format |
| file.stats |                                                                       |
| file.label |                                                                       |
| file.touch |                                                                       |
| file.adf   |                                                                       |
| file.dgt   | some gradients                                                        |
| ...        |                                                                       |

#### Files
| File | Description |
| ---- | ----------- |
|      |             |
|      |             |
|      |             |


### Directories

subjects/../
	- label
	- mri
		- transforms/talairach.xfm ( text file with matrix)
		- `mgz` files
			- rawavg.mgz
			- orig.mgz
			- T1.mgz
			- brainmask.mgz
			- aseg.mgz
			- filled.mgz
	- scripts
		- recon-all.log
		- recon-all.done
	- stats
	- surf
		- lh.orig.nofix
		- rh.orig.nofix
	- tmp
	- touch
	- trash
	
models/
	- `*.npy`, `*.h5`
	- topofit  (`*.pt`, ...)

trctrain/hcp/
manually labeled white-matter pathways and Freesurfer segmentaions that are used as training data for TRACULA

fsfast/
matlab/
libraries written in matlab

average/
diffusion/
luts/
tktools/
mni/
mni-1.4/
sessions/

### Commands

`recon-all -i file1 -i file2 -subject bert -all`
- file1,file1: `file.dcm` or `file.nii`
- `dcm` file to `mgz` file
- `-all` flag means do everything, may take 10-13 hours


`dcmunpack -src /path/to/dicoms`
- generate a `dcm` file from raw MRI output

`setevn SUBJECTS_DIR path`
- set environment variable such as SUBJECTS_DIR



### Topics
---
#### 1. Individual processing stream
1. T1 Weighted Input
2. Skull Stripping
3. Intensity Normalization
4. Volumetric Labeling
5. Subcortical Mass
6. Surface Extraction
7. Surface Atlas Registration
8. Gyral Labeling
9. -> stats

[[FreeSurfer-Part2]]