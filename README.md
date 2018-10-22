# MaCRUISE: Consistent cortical reconstruction and multi-atlas brain segmentation 
### [[project page]](https://github.com/MASILab/MaCRUISE)   [[paper]](https://www.ncbi.nlm.nih.gov/pubmed/27184203)

A T1 MRI scan can be segmented to 133 labels with consistent cortical surface reconstruction.
<img src="https://ars.els-cdn.com/content/image/1-s2.0-S1053811916301501-fx1_lrg.jpg" width="600px"/>

It has been implemented as a single Docker.
```diff
- Please cite the following papers, if you used the MaCRUISE.
```
The papers can be found [MaCRUISE journal](https://www.ncbi.nlm.nih.gov/pubmed/27184203) and [MaCRUISE conference](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4845967/), whole full citation is

Yuankai Huo, Andrew J. Plassard, Aaron Carass, Susan M. Resnick, Dzung L. Pham, Jerry L. Prince, and Bennett A. Landman. "Consistent cortical reconstruction and multi-atlas brain segmentation." NeuroImage 138 (2016): 197-210.

Yuankai Huo, Aaron Carass, Susan M. Resnick, Dzung L. Pham, Jerry L. Prince, and Bennett A. Landman. "Combining multi-atlas segmentation with brain surface estimation." In Medical Imaging 2016: Image Processing, vol. 9784, p. 97840E. International Society for Optics and Photonics, 2016.

```diff
+ The code and docker are free for noncommercial purposes.
+ The licence.md shows the terms for commercial and for-profit purposes.
```

## Quick Start
#### Get our docker images, one is for [SLANT](https://github.com/MASILab/SLANTbrainSeg) whole brain segmentaiton, another one is for MaCRUISE
```
# Get SLANT docker image (~5G disk space)
sudo docker pull vuiiscci/slant:deep_brain_seg_v1_0_0
# Get MaCRUISE docker image (~10G disk space)
sudo docker pull masidocker/spiders:MaCRUISE_v3_1_0
```
#### Step 1, Run [SLANT](https://github.com/MASILab/SLANTbrainSeg)  brain segmentation
You can run the following command or change the "input_dir", then you will have the final segmentation results in output_dir
```
# you need to specify the input and output directory
export input_dir=/home/input_dir   
export output_dir=/home/output_dir
# make that directory
export SLANT_dir=$output_dir/SLANT
sudo mkdir $input_dir
# set output directory
sudo mkdir $output_dir
sudo mkdir $SLANT_dir
# download the test volume file, you can even put multiple input files here, no worries.
sudo wget -O  $input_dir/test_volume.nii.gz  https://www.nitrc.org/frs/download.php/10666/test_volume.nii.gz
# run the docker
sudo nvidia-docker run -it --rm -v $input_dir:/INPUTS/ -v $SLANT_dir:/OUTPUTS masidocker/spiders:deep_brain_seg_v1_0_0 /extra/run_deep_brain_seg.sh
```
- You will see the final a segmentation file in "FinalResult"
- You will see the final a overlay pdf in "FinalPDF"
- You will see the final a txt file contains all label names and volume in "FinalVolTxt".

## Source Code
The SLANT is a whole brain segmentation pipeline that contains (1) pre-processing, (2) deep learning, (3) post-processing, which have all been contained in the Docker. The main scratch in Docker is the "run_deep_brain_seg.sh". The related source code and binary files have been included in the Docker. They can also be found in the "matlab" and "python".

- Pre- and Post-processing code can be found in "matlab"
- Train and testing code for deep learning part can be found in "python"

## Detailed envrioment setting  

#### Testing platform
- Ubuntu 16.04
- cuda 8.0
- Pytorch 0.2
- Docker version 1.13.1-cs9
- Nvidia-docker version 1.0.1 to 2.0.3


#### install Docker
```
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install docker-ce
```

#### install Nvidia-Docker
```
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update
sudo apt-get install -y nvidia-docker2
```


