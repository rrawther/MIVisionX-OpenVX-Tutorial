## 1. Tutorial Exercise4 (Object detection with video decoding) Overview
*This project shows how to run video decoding and object detection using simple pretrained Caffe model, yolov2*

It is best to start doing these exercises after going through the presentation slides and MIVisionX github pages discussed earlier during this tutorial session. This exercise makes use for mv_compile utility which is built and installed with MIVisionX github repository https://github.com/GPUOpen-ProfessionalCompute-Libraries/MIVisionX.git. The tutorial exercise has two .cpp files, ``mvobj_detect.cpp and visualize.cpp``. But it needs extra header files, .cpp files, and inference deployment library which is generated by mv_compile utility to completely build and execute the application.

For doing this exercise, follow the steps in order.
For the second part of exercise, I will be showing how to run the object detection using 4 video streams by compiling model for batch of 4 and specifying 4 video files for the input to exercise 4.

## Prerequisites

* Ubuntu `16.04`/`18.04` or CentOS `7.5`/`7.6`
* [ROCm supported hardware](https://rocm.github.io/ROCmInstall.html#hardware-support) 
	* AMD Radeon GPU or APU required
* [ROCm](https://github.com/RadeonOpenCompute/ROCm#installing-from-amd-rocm-repositories)
* Build & Install [MIVisionX](https://github.com/GPUOpen-ProfessionalCompute-Libraries/MIVisionX#linux-1)
	* MIVisionX installs model compiler at `/opt/rocm/mivisionx`
  * mv_compile installs at at `/opt/rocm/mivisionx/bin` and mvdeploy_api.h installs at `/opt/rocm/mivisionx/include` 

### Step 1. Clone this repository into local system

### Step 2. download pretrained Caffe model from the following link
[yoloV2Tiny20.caffemodel](https://github.com/kiritigowda/YoloV2NCS/raw/master/models/caffemodels/yoloV2Tiny20.caffemodel)

### Step 3. compile model for OPENCL-ROCm-OpenVX backend using mv_compile utility
The mv_compile utility generates deployment library, header files, and .cpp files required to run inference for the specified model.
```
mv_compile --model yoloV2Tiny20.caffemodel --install_folder example4 --input_dims 1,3,416,416
```
There will be a file libmv_deploy.so (under ./lib), weights.bin and mvtestdeploy sample app (under ./bin).
Also there will be mv_extras folder for extra post-processing helper functions.
Open mvdeploy_api.h to go through API functions supported for inference deployment. 

### Step 4. Make sure mvtestdeploy utility runs
mvtestdeploy is a pregenerated application built during Step 2 which show how to deploy inference for an input image file
```	
cd exercise4
./bin/mvtestdeploy <inputdatafile> <output.bin> --install_folder . -t N
This runs inference for an input file and generate output for N number of iterations and generates output
```
### Step 5. Build mv_objdetect example
mv_objectdetect is supposed to build on top of all the files generated in step 3. Basically it shows how to add preprocessing OpenVX nodes for video decoding and image_to_tensor conversion. 
Go through mv_objdetect.cpp file. This exercise uses a single or multiple video streams for input. 
The second part of the sample will show how to run it through multiple video files.

```
copy all files in clones sample folder (mvobjdetect.spp, visualize.cpp, visualize.h and CMakeLists.txt) into exercise4 folder everything is there to build and run the sample
cp mvobjdetect.cpp visualize.cpp .
cp visualize.h .
cp CMakeLists.txt .
```

### Step 6. cmake and make mvobjdetect
```
mkdir build && cd build && cmake -DUSE_POSTPROC=ON ../
make -j
```
Note: if build directory exists from previous build, please remove it before creating again

### Step 7. Run object detection with video/image

```
./build/mvobjdetect ../data/img.jpg - --install_folder . --bb 20 0.2 0.4 --v
./build/mvobjdetect ../data/test.mp4 - --install_folder . --frames 5000 --bb 20 0.2 0.4 --v
```
### Step 8. Run object detection with multiple video streams
Go thorugh steps 2 to 5, this time compiing the model for a batch of 4

```
mv_compile.exe --model yoloV2Tiny20.caffemodel --install_folder example4_batch4 --input_dims 1,3,416,416
cd example4_batch4
cp ../*.cpp .
cp ../*.h .
cp ../CMakeLists.txt .
mkdir build && cd build && cmake -DUSE_POSTPROC=ON ../
make -j

./build/mv_objdetect ../data/Videos_4.txt - --install_folder . --frames 5000 --bb 20 0.2 0.4 --v
```
### Step 9. Sample output for multiple video object detection
<p align="center"><img width="80%" src="images/Video_4_screenshot.png" /></p>

# License
This project is licensed under the MIT License - see the LICENSE.md file for details

# Author
rrawther@amd.com
