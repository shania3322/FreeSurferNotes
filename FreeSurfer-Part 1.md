---
Author: "Shiya"
Title: "Introduction/Single Subject/Analysis_ROI/Analysis"
Status: Ongoing
Date: 06-02-2023
Tags: "freesurfer"
---

Note: 90% of content are from the materials provided on FreeSurfer workshop page. A small part of complementary materials are from other online resources and are provided with links.


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

##### mgz file format: compressed mgh file
- can store 4D data (like NIFTI)
- cols, rows, slices, frames; typical anatomical volume: 256x256x128x1
- "Volume-encoded" surface files: {nvertices} x 1 x 1 x 40

##### Other volume-style format
mgz, nii, nii.gz


### Directories


```
subjects/../
	- label
		- {lh,rh}.aparc.annot (automatic cortical parcellation)
	- mri
		- transforms/talairach.xfm ( text file with matrix)
		- `mgz` files
			- orig.mgz
			- rawavg.mgz
			- orig.mgz
			- wm.mgz (white matter segmentation)
			- 1. T1.mgz (intensity normalization or bias correction from nu.mgz to T1.mgz)
			- 2. brainmask.mgz (skill strip)
			- 3. aseg.mgz (automatic volume labeling)
			- 4. filled.mgz (subcortical mass)
	- scripts (logs go here)
		- recon-all.log
		- recon-all.done
		- ...
	- stats
		- aseg.stats (volume summaries)
		- {lh,rh}.aparc.stats (desikan/killiany surface summaries)
		- {lh,rh}.aparc.a2009s.stats (destrieux surface summaries)
		- wmparc.stats (white matter parcellation)
	- surf
		- 5.1. lh.orig.nofix,rh.orig.nofix (surface extraction)
		- 5.2. lh.orig,rh.orig (surface extraction)
		- 6. lh.white, rh.white (white matter surface)
		- 7. lh.pial, rh.pial (Pial Surface)
		- 8. lh.thickness, rh.thickness (distance between white and pial surfaces)
		- 9. lh.curv, rh.curv (curvature of a tangent circle at each vertec)
		- 10. lh.area, lh.volume
		- 11.1 lh.sphere.reg, rh.sphere.reg (spherical registration)
		- 11.2 lh.shere, rh.sphere
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

FreeSurferColorLUT.txt
	- color lookup table with segment index
```


### Commands

`recon-all -i file1 -i file2 -subject bert -all`
- file1,file1: `file.dcm` or `file.nii`
- `dcm` file to `mgz` file
- `-all` flag means do everything, may take 10-13 hours

manually
```
mkdir -p $SUBJECTS_DIR/subjectname/mri/orig
mri_convert invol1 $SUBJECTS_DIR/subjectname/mri/orig/001.mgz
mri_convert invol2 $SUBJECTS_DIR/subjectname/mri/orig/002.mgz
recon-all -subject subjectname -all
```

`recon-all -s <subjectName> -qcache` 
adding the qcache option, which will smooth the data at different levels and store them in the subject’s output directory

`dcm2niix`  combine all dcm files of one subject in the folder to one nifti file

`dcmunpack -src /path/to/dicoms`
- generate a `dcm` file from raw MRI output

`setevn SUBJECTS_DIR path`
- set environment variable such as SUBJECTS_DIR

`asegstats2table --help`
`aparcstats2table --help`
`recon-all --help`
`mri_convert --help`


`freeview`
	`-v` volume image
	`-f` surface image 
coronal view, sagittal view, axial view and 3D view

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

Volume Model vs Surface Model
| Volume Model                           | Surface Model                                                        |
| -------------------------------------- | -------------------------------------------------------------------- |
| voxel is an intersection of grid lines | vertex is an intersection of triangles                               |
| voxel assigned a value                 | vetex assigned a value                                               |
| colums,rows, slices                    | -                                                                    |
|                                        | surface-based is more accurate than volume-based                     |
|                                        | each vertex has an area (mean of triagles), a thickness and a volume |
|                                        |                                                                      |

#### Volumes
- xxx.mgz files (mri folder)
- All conformed 256x256x256, 1mm3
- `tkmedit` - volume viewer
#### Surfaces
- surf folder
- Number of vertices stays the same (expect for patches), xyz location changes
- `tksurfer` - surface viewer (obsolete?)

#### Surface Based Analysis
 1. Intersubject registration and smoothing 
	 1. what are smoothing and registration?
		- Spatial smoothing: 
			 - might increase CNR/SNR
			 -  improve intersubject registration
		-  How much smoothing?
			- blob-size
			- typically 5-20mm FWHM
			- surface smoothing more forgiving than volume-based ?
			- volume-based smoothing is averating of "nearby" voxels
			- Typically, spatially convolve image with Gaussian kernel
			- surface-based smoothing is averaging of nearby vertices
		- registration: subject's brains are aligned to each other on a voxelwise basis ?
		- smoothing and registration can be perform in the volume or surface?
2. Why surface-based anaysis?
	- 2D
	- inter-subject registration: anatomy, not intensity
	- smoothing and clustering

*Please rewrite this section, and compare the pros and cons of volume-based analysis and surface-based analysis.*


#### Region of Interest (ROI) Analysis
- subcortical segmetation (volume)
	- aseg.mgz, aseg.stats
	- eTIV is "estimated" total intracranial volume, it does not determine where the skull is and count voxels inside the skull
	- mri/transform/talarach.xmf is the linear transform from ICV (?) to MNI305 space [paper](https://surfer.nmr.mgh.harvard.edu/pub/articles/buckner2004.pdf)
	- Volumetic segmentation atlas (averaging among 39 subjects)
	- eTIV changes if talairach.xfm changes
- cortical parcellation/annotation (surface)
	- ?.aparc.annot (Desikan/Killiany atlas), ?.aparc.a2009.annot (Destrieux atlas), ?.aparc.stats, ?.aparc.a2009.stats
	- thickness(what does thisckness mean?) and area ROI studies
	- Desikan/Killiany atlas (surface parcellation, averaging among 40 subjects) [paper](https://surfer.nmr.mgh.harvard.edu/ftp/articles/desikan06-parcellation.pdf) 
	- Destrieux atlas (surface parcellation, averaging among 12 subjects) [paper](https://surfer.nmr.mgh.harvard.edu/ftp/articles/fischl04-parcellation.pdf)
- Cortical + Subcortical 
	- aparc+aseg.mgz
- white matter (wm) segmentation
	- wmparc.mgz, wmparc.stats
- preparation/analysis of stats
`asegstats2table --subjects xx --meas xx --stats wmparc.stats --tablefile aseg.table.txt`

`aparcstats2table`

```
-- meas 
	volume
	thickness
--parc 
	aparc
	aparc.a2009
-- hemi
	lh
	rh
```

- GLM analysis on stats files (output `sig.table.dat`file)
	- `mri_glmfit --table xx.table.txt`

##### parcellation, segmentation, and altas
https://carpentries-incubator.github.io/SDC-BIDS-sMRI/04-Image_Quantification/index.html
- A parcellation can be defined as the splitting of an object into non-overlapping components. Since an atlas divide the brain into regions, atlasing can be considered a kind of parcellation. Reciprocally, any kind of brain parcellation can be considered an “atlas”.

- A difference is that an atlas is often expected to be defined on an entire brain, while a parcellation can be limited to a specific region (e.g. thalamus parcellation). A parcellation can also be applied for example to the cortical GM (the outer layer of the brain) or the subcortical GM for deep GM (GM structures below the cortex).

- Note that Freesurfer call cortical parcellation “parcellation” (denoting `parc` the associated files) and subcortical parcellation “segmentation” (denoting `seg` the associated files).

#### Structural and Functional Data Integration
- Important questions:
	- How big are your voxels?
	- How much smoothing?
	- Physiological contamination?
	- How big is your effect?
	- ...
- Difficulties of integrating anatomical and functional data
	- various levels of neuroanatomy
	- difficult to create accurate models of the brain
	- distortions and different geomotries across imaging domains
		- eg. T1-weighted imaging, T2 weighted imaging, diffusion imaging
	- anatomy variability across individuals
		- automated spatial normalization
			- affine/linear averaging
			- spherical(surface) vs volumetric normalization
			- 3D spatial smoothing vs surface smoothing
		- individual labeling of regions/structures
		- functional localizer
- Using structure information, which includes folds and connectivity, to predict function
- Structural data: surface boundaties, folded, semi-inflated, inflated models, etc.
- A prior hypothesized region in ROI analysis (what does it mean?)
- Using diffusion to define structure
	- diffusion tractography - 'structural connectivity'
	- structural connectivity and functional connectivity (what's the connection?)
	- seed area or seeds (what is it?)
	- mean connectivity maps

Quiz:
What does it mean to conform the volume?                                        
Are the FS results in Talairach/mni305 or native space?                         
What is the subcortical?                                                        
What is the medial wall?                                                        
Will the surface be accurate in the medial wall?                                
What is the difference between a surface and a surface overlay?                 

