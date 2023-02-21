### Quick access
[FreeSurfer Tutorial and workshop](https://surfer.nmr.mgh.harvard.edu/fswiki/FsTutorial)
[glossary of FreeSurfer Tutorial](https://surfer.nmr.mgh.harvard.edu/fswiki/FreeSurferTutorial)
[FreeSurfer short course](https://andysbrainbook.readthedocs.io/en/latest/FreeSurfer/FreeSurfer_Introduction.html)
[MRI questions](https://mriquestions.com/complete-list-of-questions.html)
[Structural MRI (Pre)processing adn Neuroimaging Analysis](https://carpentries-incubator.github.io/SDC-BIDS-sMRI/)

### Contents
- Part 1 - Introduction / Single Subject Analysis / ROI Analysis]]
- Part 2 - Group Analysis / Longitudinal Analysis / Troubleshooting
- Part 3- Multimodal Analysis / Diffusion Analysis / fMRI Analysis / MR Acquisition
- Part 4- fMRI Analysis / Multimodal Analysis / Future Directions

```dataview
table Title, Status, Date
from #freesurfer 
sort Status Desc
```

### Practice
Current version **FreeSurfer 7.3.2**

#### 1. segmentation of thalamic nuclei [link](https://freesurfer.net/fswiki/ThalamicNuclei)
Input here is T1 data.
FreeSurfer 7.3.2
After running command below, we get new files.

```
old command:
segmentThalamicNuclei.sh  bert  [SUBJECTS_DIR]

new command: 
segment_subregions hippo-amygdala --cross bert

files:
subjects/bert/mri
	- ThalamicNuclei.v13.T1.FSvoxelSpace.mgz 
	- ThalamicNuclei.v13.T1.mgz
	- ThalamicNuclei.v13.T1.volumes.txt

subjects/bert/stats
	- thalamic-nuclei.lh.v13.T1.stats
	- thalamic-nuclei.rh.v13.T1.stats

subjects/bert/scripts
	- thalamic-nuclei-mainFreeSurferT1.log
```

- ThalamicNuclei.v13.T1.volumes.txt:
	stores the estimated volumes of the thalamic nuclei

- ThalamicNuclei.v13.T1.mgz:
	stores the discrete segmentation volumes at subvoxel resolution (0.5 mm)

- ThalamicNuclei.v13.T1.FSvoxelSpace.mgz:
	stores the discrete segmentation volume in the FreeSurfer voxel space (normally 1mm isotropic, unless higher resolution data was used in recon-all with the flag -cm)
	
Check segmentation in freeview:
note: It is possible to preset the subject folder. Instead of indicating subjects/bert/xxx.mgz, we can use xxx.mgz with freeview directly. ( need to check how to do it)
```
freeview -v subjects/bert/mri/nu.mgz -v subjects/bert/mri/thalamic-nuclei.lh.v12.T1.mgz:colormap=lut
```
Group data into table:
```
asegstats2table --statsfile=thalamic-nuclei.lh.v12.T1.stats --tablefile=thalamic-nuclei.lh.v12.T1.dat --subjectsfile=subjectlist
```

Question:
How do I examine if the segmentation is correct? By observing in freeview? Or look at the stats?
What are these segments used for? In what kind of analysis? 
What should I do if the segmentation fails?


#### 2. Hippocampal subfields freesurfer [old in FreeSurfer 6.0](https://surfer.nmr.mgh.harvard.edu/fswiki/HippocampalSubfields) [new in FreeSurfer 7.3+](https://surfer.nmr.mgh.harvard.edu/fswiki/SubregionSegmentation)
current version:FreeSurfer 7.3.2

- Using a probalilistic atlas built with ultra-high resolution ex vivo MRI data (~0.1mm isotropic).
- Low RAM memory
- It simultaneously segments hippocampal subregions and the nuclei of the amygdala. 
- It subdivides the hippocampal substructures into head and body
	- suffix "HBT" - HP Parcellation, the hippocampus is subdivided into head, body and tail
	- suffix "FS60" - HP Parcellation, no head/body subdivision for the hippocampal subregions
	- suffix "CA" -  Molecular subregion added to nearest neighbor

Run following commands:
```
segment_subregions [structure] --cross [subject]

[structure]
	- thalamus
	- hippo-amygdala
	- brainstem

--cross [subject] 
	subject to segment for cross-sectional analysis
--long-base [subject]
	base subject for longitudinal analysis. Time-points are inferred automatically
```

