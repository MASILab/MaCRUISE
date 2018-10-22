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
#### Step 1, Run [SLANT](https://github.com/MASILab/SLANTbrainSeg)  brain segmentation (will take ~ 15 mins)
You can run the following command or change the "input_dir", then you will have the final segmentation results in output_dir
```
# you need to specify the input and output directory
export input_dir=/home/input_dir   
export output_dir=/home/output_dir
# make SLANT directory
export SLANT_input_dir=$input_dir/SLANT
export SLANT_output_dir=$output_dir/SLANT
sudo mkdir $input_dir
sudo mkdir $SLANT_input_dir
sudo mkdir $output_dir
sudo mkdir $SLANT_output_dir
# download the test volume file, you can even put multiple input files here, no worries.
sudo wget -O  $SLANT_input_dir/test_volume.nii.gz  https://www.nitrc.org/frs/download.php/10666/test_volume.nii.gz
# run the docker
sudo nvidia-docker run -it --rm -v $SLANT_input_dir:/INPUTS/ -v $SLANT_output_dir:/OUTPUTS masidocker/spiders:deep_brain_seg_v1_0_0 /extra/run_deep_brain_seg.sh
```
#### Step 2, Run MaCRUISE surface reconstruction (will take several hours)
You can run the following command or change the "input_dir", then you will have the final segmentation results in output_dir
```
# make MaCRUISE directory
export MaCRUISE_input_dir=$input_dir/MaCRUISE
export MaCRUISE_output_dir=$output_dir/MaCRUISE
sudo mkdir $MaCRUISE_input_dir
sudo mkdir $MaCRUISE_output_dir
# prepare files
cp $SLANT_input_dir/test_volume.nii.gz $MaCRUISE_input_dir/T1.nii.gz
cp $SLANT_output_dir/FinalResult/test_volume_seg.nii.gz $MaCRUISE_input_dir/orig_target_seg.nii.gz
# run the docker
sudo docker run --rm -v $MaCRUISE_input_dir:/INPUTS/ -v $MaCRUISE_output_dir:/OUTPUTS/ masidocker/spiders:MaCRUISE_v3_1_0 xvfb-run -a --server-args="-screen 0 1920x1200x24 -ac +extension GLX" /extra/MaCRUISE_v3_1_0

```

- You will see the final egmentation file in "../MaCRUISE/MaCRUISE/Output/SegRefine"
- You will see the final surface file in "../MaCRUISE/MaCRUISE/Output/Surfaces" and "../MaCRUISE/MaCRUISE/Output/Surfaces_FreeView"
- You will see the final a overlay pdf in "../MaCRUISE/MaCRUISE/Output/PDF"

## Source Code
The source code have all been contained in the Docker

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


