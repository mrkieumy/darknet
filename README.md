# Yolo-v4 and Yolo-v3/v2 for Windows and Linux

Paper Yolo v4: https://arxiv.org/abs/2004.10934

This repository forked from a great work of https://github.com/AlexeyAB/darknet

[![Darknet Continuous Integration](https://github.com/AlexeyAB/darknet/workflows/Darknet%20Continuous%20Integration/badge.svg)](https://github.com/AlexeyAB/darknet/actions?query=workflow%3A%22Darknet+Continuous+Integration%22)
[![CircleCI](https://circleci.com/gh/AlexeyAB/darknet.svg?style=svg)](https://circleci.com/gh/AlexeyAB/darknet)
[![TravisCI](https://travis-ci.org/AlexeyAB/darknet.svg?branch=master)](https://travis-ci.org/AlexeyAB/darknet)
[![Contributors](https://img.shields.io/github/contributors/AlexeyAB/Darknet.svg)](https://github.com/AlexeyAB/darknet/graphs/contributors)
[![License: Unlicense](https://img.shields.io/badge/license-Unlicense-blue.svg)](https://github.com/AlexeyAB/darknet/blob/master/LICENSE)
[![DOI](https://zenodo.org/badge/75388965.svg)](https://zenodo.org/badge/latestdoi/75388965)
[![arxiv.org](http://img.shields.io/badge/cs.CV-arXiv%3A2004.10934-B31B1B.svg)](https://arxiv.org/abs/2004.10934)

* [Requirements (and how to install dependecies)](#requirements)

1.  [How to use](#how-to-use-on-the-command-line)
2.  How to compile on Linux
    * [Using cmake](#how-to-compile-on-linux-using-cmake)
    * [Using make](#how-to-compile-on-linux-using-make)
3.  How to compile on Windows
    * [Using CMake-GUI](#how-to-compile-on-windows-using-cmake)
    * [Using vcpkg](#how-to-compile-on-windows-using-vcpkg)
    * [Legacy way](#how-to-compile-on-windows-legacy-way)
4.  [Training and Evaluation of speed and accuracy on MS COCO](https://github.com/AlexeyAB/darknet/wiki#training-and-evaluation-of-speed-and-accuracy-on-ms-coco)
5.  [How to train with multi-GPU:](#how-to-train-with-multi-gpu)
6.  [How to train (to detect your custom objects), KAIST dataset for pedestrian detection with Yolov3](#how-to-train-to-detect-your-custom-objects)
7.  [How to train tiny-yolo (to detect your custom objects)](#how-to-train-tiny-yolo-to-detect-your-custom-objects)
8.  [When should I stop training](#when-should-i-stop-training)
9.  [How to improve object detection](#how-to-improve-object-detection)
10.  [How to mark bounded boxes of objects and create annotation files](#how-to-mark-bounded-boxes-of-objects-and-create-annotation-files)
11. [How to use Yolo as DLL and SO libraries](#how-to-use-yolo-as-dll-and-so-libraries)


#### Youtube video of results

[![Yolo v4](http://img.youtube.com/vi/1_SiUOYUoOI/0.jpg)](https://youtu.be/1_SiUOYUoOI "Yolo v4")

Others: https://www.youtube.com/user/pjreddie/videos


### Requirements

* Windows or Linux
* **CMake >= 3.12**: https://cmake.org/download/
* **CUDA > 8.0**: https://developer.nvidia.com/cuda-toolkit-archive (on Linux do [Post-installation Actions](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#post-installation-actions)). Checking supported **GPU with CC >= 3.0**: https://en.wikipedia.org/wiki/CUDA#GPUs_supported
* **OpenCV >= 2.4**: use your preferred package manager (brew, apt), build from source using [vcpkg](https://github.com/Microsoft/vcpkg) or download from [OpenCV official site](https://opencv.org/releases.html) (on Windows set system variable `OpenCV_DIR` = `C:\opencv\build` - where are the `include` and `x64` folders [image](https://user-images.githubusercontent.com/4096485/53249516-5130f480-36c9-11e9-8238-a6e82e48c6f2.png))
* **cuDNN >= 7.0 for CUDA 10.0** https://developer.nvidia.com/rdp/cudnn-archive (on **Linux** copy `cudnn.h`,`libcudnn.so`... as desribed here https://docs.nvidia.com/deeplearning/sdk/cudnn-install/index.html#installlinux-tar , on **Windows** copy `cudnn.h`,`cudnn64_7.dll`, `cudnn64_7.lib` as desribed here https://docs.nvidia.com/deeplearning/sdk/cudnn-install/index.html#installwindows )
* on Linux **GCC or Clang**, on Windows **MSVC 2015/2017/2019** https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=Community


#### Yolo v3 in PyTorch: https://github.com/mrkieumy/YOLOv3_PyTorch

#### How to use on the command line

On Linux use `./darknet`, on Windows use `darknet.exe`
example: `./darknet detector test ./cfg/coco.data ./cfg/yolov4.cfg ./yolov4.weights`

On Linux find executable file `./darknet` in the root directory, while on Windows find it in the directory `\build\darknet\x64` 

Some example test on Yolo v4 with COCO weight:
* Test **image**: `./darknet detector test cfg/coco.data cfg/yolov4.cfg yolov4.weights`
* **Output coordinates** of objects: `./darknet detector test cfg/coco.data yolov4.cfg yolov4.weights -ext_output dog.jpg`
* Test **video**: `./darknet detector demo cfg/coco.data cfg/yolov4.cfg yolov4.weights -ext_output test.mp4`
* Test on **WebCam 0**: `./darknet detector demo cfg/coco.data cfg/yolov4.cfg yolov4.weights -c 0`
* Smart WebCam **net-videocam**: `./darknet detector demo cfg/coco.data cfg/yolov4.cfg yolov4.weights http://192.168.0.80:8080/video?dummy=param.mjpg`
* Yolo v4 - **save result videofile res.avi**: `./darknet detector demo cfg/coco.data cfg/yolov4.cfg yolov4.weights test.mp4 -out_filename res.avi`
* Yolo v3 **KAIST** video: `./darknet detector demo data/kaist.data cfg/yolov3-kaist.cfg yolov3-kaist.weights test_video.mp4`
* **JSON and MJPEG server** that allows multiple connections from your soft or Web-browser `ip-address:8070` and 8090: `./darknet detector demo ./cfg/coco.data ./cfg/yolov3.cfg ./yolov3.weights test50.mp4 -json_port 8070 -mjpeg_port 8090 -ext_output`
* Yolo v3 Tiny **on GPU #1**: `./darknet detector demo cfg/coco.data cfg/yolov3-tiny.cfg yolov3-tiny.weights -i 1 test.mp4`
* Alternative method Yolo v3 COCO - image: `./darknet detect cfg/yolov4.cfg yolov4.weights -i 0 -thresh 0.25`
* Train on **KAIST dataset** with pretrained yolov3-kaist-detector: 
    `./darknet detector train data/kaist.data cfg/yolov3-kaist.cfg weights/kaist-visible-detector.weights -dont_show`
* To process a list of images `data/train.txt` and save results of detection to `result.json` file use: 
    `darknet.exe detector test cfg/coco.data cfg/yolov4.cfg yolov4.weights -ext_output -dont_show -out result.json < data/train.txt`
* To process a list of images `data/train.txt` and save results of detection to `result.txt` use:                             
    `darknet.exe detector test cfg/coco.data cfg/yolov4.cfg yolov4.weights -dont_show -ext_output < data/train.txt > result.txt`
* Pseudo-lableing - to process a list of images `data/new_train.txt` and save results of detection in Yolo training format for each image as label `<image_name>.txt` (in this way you can increase the amount of training data) use:
    `darknet.exe detector test cfg/coco.data cfg/yolov4.cfg yolov4.weights -thresh 0.25 -dont_show -save_labels < data/new_train.txt`
* To calculate anchors: `darknet.exe detector calc_anchors data/obj.data -num_of_clusters 9 -width 416 -height 416`
* To check accuracy mAP@IoU=50: `darknet.exe detector map data/obj.data yolo-obj.cfg backup\yolo-obj_7000.weights`
* To check accuracy mAP@IoU=75: `darknet.exe detector map data/obj.data yolo-obj.cfg backup\yolo-obj_7000.weights -iou_thresh 0.75`

##### For using network video-camera mjpeg-stream with any Android smartphone

1. Download for Android phone mjpeg-stream soft: IP Webcam / Smart WebCam

    * Smart WebCam - preferably: https://play.google.com/store/apps/details?id=com.acontech.android.SmartWebCam2
    * IP Webcam: https://play.google.com/store/apps/details?id=com.pas.webcam

2. Connect your Android phone to computer by WiFi (through a WiFi-router) or USB
3. Start Smart WebCam on your phone
4. Replace the address below, on shown in the phone application (Smart WebCam) and launch:

* Yolo v4 COCO-model: `darknet.exe detector demo data/coco.data yolov4.cfg yolov4.weights http://192.168.0.80:8080/video?dummy=param.mjpg -i 0`

### How to compile on Linux (using `cmake`)

The `CMakeLists.txt` will attempt to find installed optional dependencies like
CUDA, cudnn, ZED and build against those. It will also create a shared object
library file to use `darknet` for code development.

Open a bash terminal inside the cloned repository and launch:

```bash
./build.sh
```

### How to compile on Linux (using `make`)

Just do `make` in the darknet directory. (You can try to compile and run it on Google Colab in cloud [link](https://colab.research.google.com/drive/12QusaaRj_lUwCGDvQNfICpa7kA7_a2dE) (press «Open in Playground» button at the top-left corner) and watch the video [link](https://www.youtube.com/watch?v=mKAEGSxwOAY) )
Before make, you can set such options in the `Makefile`: [link](https://github.com/AlexeyAB/darknet/blob/9c1b9a2cf6363546c152251be578a21f3c3caec6/Makefile#L1)

* `GPU=1` to build with CUDA to accelerate by using GPU (CUDA should be in `/usr/local/cuda`)
* `CUDNN=1` to build with cuDNN v5-v7 to accelerate training by using GPU (cuDNN should be in `/usr/local/cudnn`)
* `CUDNN_HALF=1` to build for Tensor Cores (on Titan V / Tesla V100 / DGX-2 and later) speedup Detection 3x, Training 2x
* `OPENCV=1` to build with OpenCV 4.x/3.x/2.4.x - allows to detect on video files and video streams from network cameras or web-cams
* `DEBUG=1` to bould debug version of Yolo
* `OPENMP=1` to build with OpenMP support to accelerate Yolo by using multi-core CPU
* `LIBSO=1` to build a library `darknet.so` and binary runable file `uselib` that uses this library. Or you can try to run so `LD_LIBRARY_PATH=./:$LD_LIBRARY_PATH ./uselib test.mp4` How to use this SO-library from your own code - you can look at C++ example: https://github.com/AlexeyAB/darknet/blob/master/src/yolo_console_dll.cpp
    or use in such a way: `LD_LIBRARY_PATH=./:$LD_LIBRARY_PATH ./uselib data/coco.names cfg/yolov4.cfg yolov4.weights test.mp4`
* `ZED_CAMERA=1` to build a library with ZED-3D-camera support (should be ZED SDK installed), then run
    `LD_LIBRARY_PATH=./:$LD_LIBRARY_PATH ./uselib data/coco.names cfg/yolov4.cfg yolov4.weights zed_camera`

To run Darknet on Linux use examples from this article, just use `./darknet` instead of `darknet.exe`, i.e. use this command: `./darknet detector test ./cfg/coco.data ./cfg/yolov4.cfg ./yolov4.weights`

### How to compile on Windows (using `CMake`)

This is the recommended approach to build Darknet on Windows if you have already
installed Visual Studio 2015/2017/2019, CUDA >= 10.0, cuDNN >= 7.0, and
OpenCV >= 2.4.

Open a Powershell terminal inside the cloned repository and launch:

```PowerShell
.\build.ps1
```

### How to compile on Windows (using `vcpkg`)

1. Install or update Visual Studio to at least version 2017, making sure to have it fully patched (run again the installer if not sure to automatically update to latest version). If you need to install from scratch, download VS from here: [Visual Studio Community](http://visualstudio.com)

2. Install CUDA

3. Install [vcpkg](https://github.com/Microsoft/vcpkg) and try to install a test library to make sure everything is working, for example `vcpkg install opengl`

4. Open Powershell and type these commands:

```PowerShell
PS \>                  cd vcpkg
PS Code\vcpkg>         .\vcpkg install darknet[full]:x64-windows #replace with darknet[opencv-base,weights]:x64-windows for a quicker install; use --head if you want to build latest commit on master branch and not latest release
```

5. You will find darknet inside the vcpkg\installed\x64-windows\tools\darknet folder, together with all the necessary weight and cfg files

### How to compile on Windows (legacy way)

1. If you have **CUDA 10.0, cuDNN 7.4 and OpenCV 3.x** (with paths: `C:\opencv_3.0\opencv\build\include` & `C:\opencv_3.0\opencv\build\x64\vc14\lib`), then open `build\darknet\darknet.sln`, set **x64** and **Release** https://hsto.org/webt/uh/fk/-e/uhfk-eb0q-hwd9hsxhrikbokd6u.jpeg and do the: Build -> Build darknet. Also add Windows system variable `CUDNN` with path to CUDNN: https://user-images.githubusercontent.com/4096485/53249764-019ef880-36ca-11e9-8ffe-d9cf47e7e462.jpg

    1.1. Find files `opencv_world320.dll` and `opencv_ffmpeg320_64.dll` (or `opencv_world340.dll` and `opencv_ffmpeg340_64.dll`) in `C:\opencv_3.0\opencv\build\x64\vc14\bin` and put it near with `darknet.exe`
    
    1.2 Check that there are `bin` and `include` folders in the `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.0` if aren't, then copy them to this folder from the path where is CUDA installed
    
    1.3. To install CUDNN (speedup neural network), do the following:
      
    * download and install **cuDNN v7.4.1 for CUDA 10.0**: https://developer.nvidia.com/rdp/cudnn-archive
      
    * add Windows system variable `CUDNN` with path to CUDNN: https://user-images.githubusercontent.com/4096485/53249764-019ef880-36ca-11e9-8ffe-d9cf47e7e462.jpg
    
    * copy file `cudnn64_7.dll` to the folder `\build\darknet\x64` near with `darknet.exe`
    
    1.4. If you want to build **without CUDNN** then: open `\darknet.sln` -> (right click on project) -> properties  -> C/C++ -> Preprocessor -> Preprocessor Definitions, and remove this: `CUDNN;`

2. If you have other version of **CUDA (not 10.0)** then open `build\darknet\darknet.vcxproj` by using Notepad, find 2 places with "CUDA 10.0" and change it to your CUDA-version. Then open `\darknet.sln` -> (right click on project) -> properties  -> CUDA C/C++ -> Device and remove there `;compute_75,sm_75`. Then do step 1

3. If you **don't have GPU**, but have **OpenCV 3.0** (with paths: `C:\opencv_3.0\opencv\build\include` & `C:\opencv_3.0\opencv\build\x64\vc14\lib`), then open `build\darknet\darknet_no_gpu.sln`, set **x64** and **Release**, and do the: Build -> Build darknet_no_gpu

4. If you have **OpenCV 2.4.13** instead of 3.0 then you should change paths after `\darknet.sln` is opened

    4.1 (right click on project) -> properties  -> C/C++ -> General -> Additional Include Directories:  `C:\opencv_2.4.13\opencv\build\include`
  
    4.2 (right click on project) -> properties  -> Linker -> General -> Additional Library Directories: `C:\opencv_2.4.13\opencv\build\x64\vc14\lib`
    
5. If you have GPU with Tensor Cores (nVidia Titan V / Tesla V100 / DGX-2 and later) speedup Detection 3x, Training 2x:
    `\darknet.sln` -> (right click on project) -> properties -> C/C++ -> Preprocessor -> Preprocessor Definitions, and add here: `CUDNN_HALF;`
    
    **Note:** CUDA must be installed only after Visual Studio has been installed.

### How to compile (custom):

Also, you can to create your own `darknet.sln` & `darknet.vcxproj`, this example for CUDA 9.1 and OpenCV 3.0

Then add to your created project:
- (right click on project) -> properties  -> C/C++ -> General -> Additional Include Directories, put here: 

`C:\opencv_3.0\opencv\build\include;..\..\3rdparty\include;%(AdditionalIncludeDirectories);$(CudaToolkitIncludeDir);$(CUDNN)\include`
- (right click on project) -> Build dependecies -> Build Customizations -> set check on CUDA 9.1 or what version you have - for example as here: http://devblogs.nvidia.com/parallelforall/wp-content/uploads/2015/01/VS2013-R-5.jpg
- add to project:
    * all `.c` files
    * all `.cu` files 
    * file `http_stream.cpp` from `\src` directory
    * file `darknet.h` from `\include` directory
- (right click on project) -> properties  -> Linker -> General -> Additional Library Directories, put here: 

`C:\opencv_3.0\opencv\build\x64\vc14\lib;$(CUDA_PATH)\lib\$(PlatformName);$(CUDNN)\lib\x64;%(AdditionalLibraryDirectories)`

-  (right click on project) -> properties  -> Linker -> Input -> Additional dependecies, put here: 

`..\..\3rdparty\lib\x64\pthreadVC2.lib;cublas.lib;curand.lib;cudart.lib;cudnn.lib;%(AdditionalDependencies)`
- (right click on project) -> properties -> C/C++ -> Preprocessor -> Preprocessor Definitions

`OPENCV;_TIMESPEC_DEFINED;_CRT_SECURE_NO_WARNINGS;_CRT_RAND_S;WIN32;NDEBUG;_CONSOLE;_LIB;%(PreprocessorDefinitions)`

- compile to .exe (X64 & Release) and put .dll-s near with .exe: https://hsto.org/webt/uh/fk/-e/uhfk-eb0q-hwd9hsxhrikbokd6u.jpeg

    * `pthreadVC2.dll, pthreadGC2.dll` from \3rdparty\dll\x64

    * `cusolver64_91.dll, curand64_91.dll, cudart64_91.dll, cublas64_91.dll` - 91 for CUDA 9.1 or your version, from C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v9.1\bin

    * For OpenCV 3.2: `opencv_world320.dll` and `opencv_ffmpeg320_64.dll` from `C:\opencv_3.0\opencv\build\x64\vc14\bin` 
    * For OpenCV 2.4.13: `opencv_core2413.dll`, `opencv_highgui2413.dll` and `opencv_ffmpeg2413_64.dll` from  `C:\opencv_2.4.13\opencv\build\x64\vc14\bin`

## How to train with multi-GPU:

1. Train it first on 1 GPU for like 1000 iterations: `darknet.exe detector train cfg/coco.data cfg/yolov4.cfg yolov4.conv.137`

2. Then stop and by using partially-trained model `/backup/yolov4_1000.weights` run training with multigpu (up to 4 GPUs): `darknet.exe detector train cfg/coco.data cfg/yolov4.cfg /backup/yolov4_1000.weights -gpus 0,1,2,3`

If you get a Nan, then for some datasets better to decrease learning rate, for 4 GPUs set `learning_rate = 0,00065` (i.e. learning_rate = 0.00261 / GPUs). In this case also increase 4x times `burn_in =` in your cfg-file. I.e. use `burn_in = 4000` instead of `1000`.

https://groups.google.com/d/msg/darknet/NbJqonJBTSY/Te5PfIpuCAAJ

## How to train (to detect your custom objects):
To train with Yolo v3 on KAIST dataset, clone this github and customize as following:

0. Data file `data/kaist.data`, the number of class, train on visible or thermal)

1. Cfg file `cfg/yolov3-kaist.cfg` with some rows:

  * Recommend [`batch=64`] and  subdivisions to [`subdivisions=16`], increase subdivisions to 64 based on you GPU (when CUDA error appear)
  * change line max_batches is the maximum of batch to train: number_of_batch (for 1 epoch) = size_of_dataset/batch_size. 
  If you start training from Yolov4.weights, the previous max_batch is 500500, if you want to train with batch_size=64 for 50 epochs (KAIST dataset size 7601).
  We have 7601/64=119 batch for 1 epoch, 119 * 50 = 5950 batch, so you have to set max_batches = 506450 because (500500+5950).
  * change line steps: where at that batch, learning rate will be change, f.e. [`steps=503500,504500`], this go together with line scales = .1, .1,
  this means the lr will decrease 10 time (lr = lr*0.1) at every step.
  * set network size `width=416 height=416` or any value multiple of 32: https://github.com/AlexeyAB/darknet/blob/0039fd26786ab5f71d5af725fc18b3f521e7acfd/cfg/yolov3.cfg#L8-L9
  * change line `classes=1` to your number of objects in each of 3 `[yolo]`-layers (the last 3 detection layers)
      * https://github.com/mrkieumy/darknet/blob/0039fd26786ab5f71d5af725fc18b3f521e7acfd/cfg/yolov3.cfg#L610
      * https://github.com/mrkieumy/darknet/blob/0039fd26786ab5f71d5af725fc18b3f521e7acfd/cfg/yolov3.cfg#L696
      * https://github.com/mrkieumy/darknet/blob/0039fd26786ab5f71d5af725fc18b3f521e7acfd/cfg/yolov3.cfg#L783
  * change [`filters=18`] to filters=(classes + 5)x3 in the 3 `[convolutional]` before each `[yolo]` layer, keep in mind that it only has to be the last `[convolutional]` before each of the `[yolo]` layers.
      * https://github.com/mrkieumy/darknet/blob/0039fd26786ab5f71d5af725fc18b3f521e7acfd/cfg/yolov3.cfg#L603
      * https://github.com/mrkieumy/darknet/blob/0039fd26786ab5f71d5af725fc18b3f521e7acfd/cfg/yolov3.cfg#L689
      * https://github.com/mrkieumy/darknet/blob/0039fd26786ab5f71d5af725fc18b3f521e7acfd/cfg/yolov3.cfg#L776
  
  So if `classes=3` then should be `filters=24`. If `classes=2` then write `filters=21`.
  
  **(Do not write in the cfg-file: filters=(classes + 5)x3)**
  
  (Generally `filters` depends on the `classes`, `coords` and number of `mask`s, i.e. filters=`(classes + coords + 1)*<number of mask>`, where `mask` is indices of anchors. If `mask` is absence, then filters=`(classes + coords + 1)*num`)

  So for example, for 1 object (person), your file `yolov3-kaist.cfg` should differ from `yolov3.cfg` in such lines in each of **3** [yolo]-layers:

  ```
  [convolutional]
  filters=18

  [region]
  classes=1
  ```
3. Check the data file `data/kaist.data` containing (where **classes = number of objects**):

  ```
  classes= 1
  train  = data/train_thermal.txt
  valid  = data/test_thermal.txt
  names = data/kaist_person.names
  backup = backup/
  ```

4. Put kaist dataset including both image-files (.jpg) and annotation file (.txt) in the same directory and edit this directory in file `test_thermal.txt` and `train_thermal.txt`


Each `.jpg`-image-file has each `.txt`-annotation-file - in the same directory and with the same name, but with `.txt`-extension, 
and put to file: object number and object coordinates on this image, for each object in new line with format: 

`<object-class> <x_center> <y_center> <width> <height>`

  Where: 
  * `<object-class>` - integer object number from `0` to `(classes-1)`
  * `<x_center> <y_center> <width> <height>` - float values **relative** to width and height of image, it can be equal from `(0.0 to 1.0]`
  * for example: `<x> = <absolute_x> / <image_width>` or `<height> = <absolute_height> / <image_height>`
  * atention: `<x_center> <y_center>` - are center of rectangle (are not top-left corner)

  For example for `img1.jpg` you will be created `img1.txt` containing:

  ```
  1 0.716797 0.395833 0.216406 0.147222
  0 0.687109 0.379167 0.255469 0.158333
  1 0.420312 0.395833 0.140625 0.166667
  ```

6. Create file `train_thermal.txt` in directory `data/`, with filenames of your images, each filename in new line, with path relative to `darknet.exe`, for example containing:

  ```
  data/obj/img1.jpg
  data/obj/img2.jpg
  data/obj/img3.jpg
  ```

7. Download pre-trained weights for the convolutional layers and put to the directory `weights/`
    * <a href="https://drive.google.com/file/d/14A3K2IPPPC8-BwPh-YjeHARaZqjnR655/view?usp=sharing">KAIST_dataset </a>
    * <a href="https://drive.google.com/file/d/1Kyoyira0liRRr_FOY8DDSeATLQAwXtu-/view?usp=sharing">kaist_thermal_detector.weights</a> for test thermal video and images.
    * <a href="https://drive.google.com/file/d/1xiSKTNEB5ng0T5kgyjUKytlpn3q84uK6/view?usp=sharing">kaist_visible_detector.weights</a> for pre-trained weight for KAIST dataset.

8. Start training by using the command line: `./darknet detector train data/kaist.data cfg/yolov3-kaist.cfg weights/kaist_thermal_detector.weights`
     
   To train on Windows use command: `darknet.exe detector train data/kaist.data cfg/yolov3-kaist.cfg weights/kaist_thermal_detector.weights` (just use `darknet.ext` instead of `./darknet`)
     
   * (file `yolo-kaist_last.weights` will be saved to the `build\darknet\x64\backup\` for each 100 iterations)
   * (file `yolo-kaist_xxxx.weights` will be saved to the `build\darknet\x64\backup\` for each 1000 iterations)
   * (to disable Loss-Window use `darknet.exe detector train data/obj.data yolo-obj.cfg yolov4.conv.137 -dont_show`, if you train on computer without monitor like a cloud Amazon EC2)
   * (to see the mAP & Loss-chart during training on remote server without GUI, use command `darknet.exe detector train data/obj.data yolo-obj.cfg yolov4.conv.137 -dont_show -mjpeg_port 8090 -map` then open URL `http://ip-address:8090` in Chrome/Firefox browser)

8.1. For training with mAP (mean average precisions) calculation for each 4 Epochs (set `valid=valid.txt` or `train.txt` in `obj.data` file) and run: `darknet.exe detector train data/obj.data yolo-obj.cfg yolov4.conv.137 -map`

9. After training is complete - get result `yolo-obj_final.weights` from path `build\darknet\x64\backup\`

 * After each 100 iterations you can stop and later start training from this point. For example, after 2000 iterations you can stop training, and later just start training using: `darknet.exe detector train data/obj.data yolo-obj.cfg backup\yolo-obj_2000.weights`

    (in the original repository https://github.com/pjreddie/darknet the weights-file is saved only once every 10 000 iterations `if(iterations > 1000)`)

 * Also you can get result earlier than all 45000 iterations.
 
 **Note:** If during training you see `nan` values for `avg` (loss) field - then training goes wrong, but if `nan` is in some other lines - then training goes well.
 
 **Note:** If you changed width= or height= in your cfg-file, then new width and height must be divisible by 32.
 
 **Note:** After training use such command for detection: `darknet.exe detector test data/obj.data yolo-obj.cfg yolo-obj_8000.weights`
 
  **Note:** if error `Out of memory` occurs then in `.cfg`-file you should increase `subdivisions=16`, 32 or 64: [link](https://github.com/AlexeyAB/darknet/blob/0039fd26786ab5f71d5af725fc18b3f521e7acfd/cfg/yolov3.cfg#L4)
 
### How to train tiny-yolo (to detect your custom objects):

Do all the same steps as for the full yolo model as described above. With the exception of:
* Download default weights file for yolov3-tiny: https://pjreddie.com/media/files/yolov3-tiny.weights
* Get pre-trained weights `yolov3-tiny.conv.15` using command: `darknet.exe partial cfg/yolov3-tiny.cfg yolov3-tiny.weights yolov3-tiny.conv.15 15`
* Make your custom model `yolov3-tiny-obj.cfg` based on `cfg/yolov3-tiny_obj.cfg` instead of `yolov3.cfg`
* Start training: `darknet.exe detector train data/obj.data yolov3-tiny-obj.cfg yolov3-tiny.conv.15`

For training Yolo based on other models ([DenseNet201-Yolo](https://github.com/AlexeyAB/darknet/blob/master/build/darknet/x64/densenet201_yolo.cfg) or [ResNet50-Yolo](https://github.com/AlexeyAB/darknet/blob/master/build/darknet/x64/resnet50_yolo.cfg)), you can download and get pre-trained weights as showed in this file: https://github.com/AlexeyAB/darknet/blob/master/build/darknet/x64/partial.cmd
If you made you custom model that isn't based on other models, then you can train it without pre-trained weights, then will be used random initial weights.
 
## When should I stop training:

Usually sufficient 2000 iterations for each class(object), but not less than number of training images and not less than 6000 iterations in total. But for a more precise definition when you should stop training, use the following manual:

1. During training, you will see varying indicators of error, and you should stop when no longer decreases **0.XXXXXXX avg**:

  > Region Avg IOU: 0.798363, Class: 0.893232, Obj: 0.700808, No Obj: 0.004567, Avg Recall: 1.000000,  count: 8
  > Region Avg IOU: 0.800677, Class: 0.892181, Obj: 0.701590, No Obj: 0.004574, Avg Recall: 1.000000,  count: 8
  >
  > **9002**: 0.211667, **0.60730 avg**, 0.001000 rate, 3.868000 seconds, 576128 images
  > Loaded: 0.000000 seconds

  * **9002** - iteration number (number of batch)
  * **0.60730 avg** - average loss (error) - **the lower, the better**

  When you see that average loss **0.xxxxxx avg** no longer decreases at many iterations then you should stop training. The final avgerage loss can be from `0.05` (for a small model and easy dataset) to `3.0` (for a big model and a difficult dataset).
  
  Or if you train with flag `-map` then you will see mAP indicator `Last accuracy mAP@0.5 = 18.50%` in the console - this indicator is better than Loss, so train while mAP increases. 

2. Once training is stopped, you should take some of last `.weights`-files from `darknet\build\darknet\x64\backup` and choose the best of them:

For example, you stopped training after 9000 iterations, but the best result can give one of previous weights (7000, 8000, 9000). It can happen due to overfitting. **Overfitting** - is case when you can detect objects on images from training-dataset, but can't detect objects on any others images. You should get weights from **Early Stopping Point**:

![Overfitting](https://hsto.org/files/5dc/7ae/7fa/5dc7ae7fad9d4e3eb3a484c58bfc1ff5.png) 

To get weights from Early Stopping Point:

  2.1. At first, in your file `obj.data` you must specify the path to the validation dataset `valid = valid.txt` (format of `valid.txt` as in `train.txt`), and if you haven't validation images, just copy `data\train.txt` to `data\valid.txt`.

  2.2 If training is stopped after 9000 iterations, to validate some of previous weights use this commands:

(If you use another GitHub repository, then use `darknet.exe detector recall`... instead of `darknet.exe detector map`...)

* `darknet.exe detector map data/obj.data yolo-obj.cfg backup\yolo-obj_7000.weights`
* `darknet.exe detector map data/obj.data yolo-obj.cfg backup\yolo-obj_8000.weights`
* `darknet.exe detector map data/obj.data yolo-obj.cfg backup\yolo-obj_9000.weights`

And comapre last output lines for each weights (7000, 8000, 9000):

Choose weights-file **with the highest mAP (mean average precision)** or IoU (intersect over union)

For example, **bigger mAP** gives weights `yolo-obj_8000.weights` - then **use this weights for detection**.

Or just train with `-map` flag: 

`darknet.exe detector train data/obj.data yolo-obj.cfg yolov4.conv.137 -map` 

So you will see mAP-chart (red-line) in the Loss-chart Window. mAP will be calculated for each 4 Epochs using `valid=valid.txt` file that is specified in `obj.data` file (`1 Epoch = images_in_train_txt / batch` iterations)

(to change the max x-axis value - change [`max_batches=`](https://github.com/AlexeyAB/darknet/blob/0039fd26786ab5f71d5af725fc18b3f521e7acfd/cfg/yolov3.cfg#L20) parameter to `2000*classes`, f.e. `max_batches=6000` for 3 classes)

![loss_chart_map_chart](https://hsto.org/webt/yd/vl/ag/ydvlagutof2zcnjodstgroen8ac.jpeg)

Example of custom object detection: `darknet.exe detector test data/obj.data yolo-obj.cfg yolo-obj_8000.weights`

* **IoU** (intersect over union) - average instersect over union of objects and detections for a certain threshold = 0.24

* **mAP** (mean average precision) - mean value of `average precisions` for each class, where `average precision` is average value of 11 points on PR-curve for each possible threshold (each probability of detection) for the same class (Precision-Recall in terms of PascalVOC, where Precision=TP/(TP+FP) and Recall=TP/(TP+FN) ), page-11: http://homepages.inf.ed.ac.uk/ckiw/postscript/ijcv_voc09.pdf

**mAP** is default metric of precision in the PascalVOC competition, **this is the same as AP50** metric in the MS COCO competition.
In terms of Wiki, indicators Precision and Recall have a slightly different meaning than in the PascalVOC competition, but **IoU always has the same meaning**.

![precision_recall_iou](https://hsto.org/files/ca8/866/d76/ca8866d76fb840228940dbf442a7f06a.jpg)


### Custom object detection:

Example of custom object detection: `darknet.exe detector test data/obj.data yolo-obj.cfg yolo-obj_8000.weights`

| ![Yolo_v2_training](https://hsto.org/files/d12/1e7/515/d121e7515f6a4eb694913f10de5f2b61.jpg) | ![Yolo_v2_training](https://hsto.org/files/727/c7e/5e9/727c7e5e99bf4d4aa34027bb6a5e4bab.jpg) |
|---|---|

## How to improve object detection:

1. Before training:
  * set flag `random=1` in your `.cfg`-file - it will increase precision by training Yolo for different resolutions: [link](https://github.com/AlexeyAB/darknet/blob/0039fd26786ab5f71d5af725fc18b3f521e7acfd/cfg/yolov3.cfg#L788)

  * increase network resolution in your `.cfg`-file (`height=608`, `width=608` or any value multiple of 32) - it will increase precision


  * check that each object that you want to detect is mandatory labeled in your dataset - no one object in your data set should not be without label. In the most training issues - there are wrong labels in your dataset (got labels by using some conversion script, marked with a third-party tool, ...). Always check your dataset by using: https://github.com/AlexeyAB/Yolo_mark

  * my Loss is very high and mAP is very low, is training wrong? Run training with ` -show_imgs` flag at the end of training command, do you see correct bounded boxes of objects (in windows or in files `aug_...jpg`)? If no - your training dataset is wrong.

  * for each object which you want to detect - there must be at least 1 similar object in the Training dataset with about the same: shape, side of object, relative size, angle of rotation, tilt, illumination. So desirable that your training dataset include images with objects at diffrent: scales, rotations, lightings, from different sides, on different backgrounds - you should preferably have 2000 different images for each class or more, and you should train `2000*classes` iterations or more

  * desirable that your training dataset include images with non-labeled objects that you do not want to detect - negative samples without bounded box (empty `.txt` files) - use as many images of negative samples as there are images with objects

  * What is the best way to mark objects: label only the visible part of the object, or label the visible and overlapped part of the object, or label a little more than the entire object (with a little gap)? Mark as you like - how would you like it to be detected.

  * for training with a large number of objects in each image, add the parameter `max=200` or higher value in the last `[yolo]`-layer or `[region]`-layer in your cfg-file (the global maximum number of objects that can be detected by YoloV3 is `0,0615234375*(width*height)` where are width and height are parameters from `[net]` section in cfg-file) 
  
  * for training for small objects (smaller than 16x16 after the image is resized to 416x416) - set `layers = 23` instead of https://github.com/AlexeyAB/darknet/blob/6f718c257815a984253346bba8fb7aa756c55090/cfg/yolov4.cfg#L895
      set `stride=4` instead of https://github.com/AlexeyAB/darknet/blob/6f718c257815a984253346bba8fb7aa756c55090/cfg/yolov4.cfg#L892
      and set `stride=4` instead of https://github.com/AlexeyAB/darknet/blob/6f718c257815a984253346bba8fb7aa756c55090/cfg/yolov4.cfg#L989
  
  * for training for both small and large objects use modified models:
      * Full-model: 5 yolo layers: https://raw.githubusercontent.com/AlexeyAB/darknet/master/cfg/yolov3_5l.cfg
      * Tiny-model: 3 yolo layers: https://raw.githubusercontent.com/AlexeyAB/darknet/master/cfg/yolov3-tiny_3l.cfg
      * YOLOv4: 3 yolo layers: https://raw.githubusercontent.com/AlexeyAB/darknet/master/cfg/yolov4-custom.cfg
  
  * If you train the model to distinguish Left and Right objects as separate classes (left/right hand, left/right-turn on road signs, ...) then for disabling flip data augmentation - add `flip=0` here: https://github.com/AlexeyAB/darknet/blob/3d2d0a7c98dbc8923d9ff705b81ff4f7940ea6ff/cfg/yolov3.cfg#L17
  
  * General rule - your training dataset should include such a set of relative sizes of objects that you want to detect: 

    * `train_network_width * train_obj_width / train_image_width ~= detection_network_width * detection_obj_width / detection_image_width`
    * `train_network_height * train_obj_height / train_image_height ~= detection_network_height * detection_obj_height / detection_image_height`
    
    I.e. for each object from Test dataset there must be at least 1 object in the Training dataset with the same class_id and about the same relative size:

    `object width in percent from Training dataset` ~= `object width in percent from Test dataset` 
   
    That is, if only objects that occupied 80-90% of the image were present in the training set, then the trained network will not be able to detect objects that occupy 1-10% of the image.
    
  * to speedup training (with decreasing detection accuracy) set param `stopbackward=1` for layer-136 in cfg-file

  * each: `model of object, side, illimination, scale, each 30 grad` of the turn and inclination angles - these are *different objects* from an internal perspective of the neural network. So the more *different objects* you want to detect, the more complex network model should be used.

  * to make the detected bounded boxes more accurate, you can add 3 parameters `ignore_thresh = .9 iou_normalizer=0.5 iou_loss=giou` to each `[yolo]` layer and train, it will increase mAP@0.9, but decrease mAP@0.5.

  * Only if you are an **expert** in neural detection networks - recalculate anchors for your dataset for `width` and `height` from cfg-file:
  `darknet.exe detector calc_anchors data/obj.data -num_of_clusters 9 -width 416 -height 416`
   then set the same 9 `anchors` in each of 3 `[yolo]`-layers in your cfg-file. But you should change indexes of anchors `masks=` for each [yolo]-layer, so for YOLOv4 the 1st-[yolo]-layer has anchors smaller than 30x30, 2nd smaller than 60x60, 3rd remaining, and vice versa for YOLOv3. Also you should change the `filters=(classes + 5)*<number of mask>` before each [yolo]-layer. If many of the calculated anchors do not fit under the appropriate layers - then just try using all the default anchors.


2. After training - for detection:

  * Increase network-resolution by set in your `.cfg`-file (`height=608` and `width=608`) or (`height=832` and `width=832`) or (any value multiple of 32) - this increases the precision and makes it possible to detect small objects: [link](https://github.com/AlexeyAB/darknet/blob/0039fd26786ab5f71d5af725fc18b3f521e7acfd/cfg/yolov3.cfg#L8-L9)
  
    * it is not necessary to train the network again, just use `.weights`-file already trained for 416x416 resolution
    * but to get even greater accuracy you should train with higher resolution 608x608 or 832x832, note: if error `Out of memory` occurs then in `.cfg`-file you should increase `subdivisions=16`, 32 or 64: [link](https://github.com/AlexeyAB/darknet/blob/0039fd26786ab5f71d5af725fc18b3f521e7acfd/cfg/yolov3.cfg#L4)

## How to mark bounded boxes of objects and create annotation files:

Here you can find repository with GUI-software for marking bounded boxes of objects and generating annotation files for Yolo v2 - v4: https://github.com/AlexeyAB/Yolo_mark

With example of: `train.txt`, `obj.names`, `obj.data`, `yolo-obj.cfg`, `air`1-6`.txt`, `bird`1-4`.txt` for 2 classes of objects (air, bird) and `train_obj.cmd` with example how to train this image-set with Yolo v2 - v4

Different tools for marking objects in images:
1. in C++: https://github.com/AlexeyAB/Yolo_mark 
2. in Python: https://github.com/tzutalin/labelImg
3. in Python: https://github.com/Cartucho/OpenLabeling
4. in C++: https://www.ccoderun.ca/darkmark/
5. in JavaScript: https://github.com/opencv/cvat


## How to use Yolo as DLL and SO libraries

* on Linux
    * using `build.sh` or
    * build `darknet` using `cmake` or
    * set `LIBSO=1` in the `Makefile` and do `make`
* on Windows
    * using `build.ps1` or
    * build `darknet` using `cmake` or
    * compile `build\darknet\yolo_cpp_dll.sln` solution or `build\darknet\yolo_cpp_dll_no_gpu.sln` solution

There are 2 APIs:
* C API: https://github.com/AlexeyAB/darknet/blob/master/include/darknet.h
    * Python examples using the C API::     
         * https://github.com/AlexeyAB/darknet/blob/master/darknet.py	
         * https://github.com/AlexeyAB/darknet/blob/master/darknet_video.py
    
* C++ API: https://github.com/AlexeyAB/darknet/blob/master/include/yolo_v2_class.hpp
    * C++ example that uses C++ API: https://github.com/AlexeyAB/darknet/blob/master/src/yolo_console_dll.cpp
    
----

1. To compile Yolo as C++ DLL-file `yolo_cpp_dll.dll` - open the solution `build\darknet\yolo_cpp_dll.sln`, set **x64** and **Release**, and do the: Build -> Build yolo_cpp_dll
    * You should have installed **CUDA 10.0**
    * To use cuDNN do: (right click on project) -> properties -> C/C++ -> Preprocessor -> Preprocessor Definitions, and add at the beginning of line: `CUDNN;`

2. To use Yolo as DLL-file in your C++ console application - open the solution `build\darknet\yolo_console_dll.sln`, set **x64** and **Release**, and do the: Build -> Build yolo_console_dll

    * you can run your console application from Windows Explorer `build\darknet\x64\yolo_console_dll.exe`
    **use this command**: `yolo_console_dll.exe data/coco.names yolov4.cfg yolov4.weights test.mp4`
    
    * after launching your console application and entering the image file name - you will see info for each object: 
    `<obj_id> <left_x> <top_y> <width> <height> <probability>`
    * to use simple OpenCV-GUI you should uncomment line `//#define OPENCV` in `yolo_console_dll.cpp`-file: [link](https://github.com/AlexeyAB/darknet/blob/a6cbaeecde40f91ddc3ea09aa26a03ab5bbf8ba8/src/yolo_console_dll.cpp#L5)
    * you can see source code of simple example for detection on the video file: [link](https://github.com/AlexeyAB/darknet/blob/ab1c5f9e57b4175f29a6ef39e7e68987d3e98704/src/yolo_console_dll.cpp#L75)
   
`yolo_cpp_dll.dll`-API: [link](https://github.com/AlexeyAB/darknet/blob/master/src/yolo_v2_class.hpp#L42)
```
struct bbox_t {
    unsigned int x, y, w, h;    // (x,y) - top-left corner, (w, h) - width & height of bounded box
    float prob;                    // confidence - probability that the object was found correctly
    unsigned int obj_id;        // class of object - from range [0, classes-1]
    unsigned int track_id;        // tracking id for video (0 - untracked, 1 - inf - tracked object)
    unsigned int frames_counter;// counter of frames on which the object was detected
};

class Detector {
public:
        Detector(std::string cfg_filename, std::string weight_filename, int gpu_id = 0);
        ~Detector();

        std::vector<bbox_t> detect(std::string image_filename, float thresh = 0.2, bool use_mean = false);
        std::vector<bbox_t> detect(image_t img, float thresh = 0.2, bool use_mean = false);
        static image_t load_image(std::string image_filename);
        static void free_image(image_t m);

#ifdef OPENCV
        std::vector<bbox_t> detect(cv::Mat mat, float thresh = 0.2, bool use_mean = false);
	std::shared_ptr<image_t> mat_to_image_resize(cv::Mat mat) const;
#endif
};
```
