# jetson-base-images
Dockerfiles for creating new base images. The images are available on the [resinplayground Docker Hub](https://hub.docker.com/u/resinplayground) and do not require any files from the Nvidia SDK Manager. If building using these Dockerfiles, you will need the SDK Manager files.

Base images base: balenalib/jetson-nano-ubuntu:bionic

### 1. resinplayground/jetson-nano-cuda
CUDA version: 10.0.326

Image size: 2.53 GB

Compressed size on Docker Hub: 1.34 GB

### 2. resinplayground/jetson-nano-cuda-cudnn-opencv (v0.2:slim)
CUDA version: 10.0.326

cuDNN version: 7.6.3

OpenCV version 4.1.1

Image size: 4.59 GB

Compressed size on Docker Hub: 2.5GB

Excerpt from OpenCV config:

```
  OpenCV modules:
       To be built:                 aruco bgsegm bioinspired calib3d ccalib core cudaarithm cudabgsegm cudacodec cudafeatures2d cudafilters cudaimgproc cudalegacy cudaobjdetect cudaoptflow cudastereo cudawarping cudev datasets dnn dnn_objdetect dpm face features2d flann freetype fuzzy gapi hfs highgui img_hash imgcodecs imgproc line_descriptor ml objdetect optflow phase_unwrapping photo plot quality reg rgbd saliency shape stereo stitching structured_light superres surface_matching text tracking ts video videoio videostab xfeatures2d ximgproc xobjdetect xphoto
     Disabled:                    world
     Disabled by dependency:      -
     Unavailable:                 cnn_3dobj cvv hdf java js matlab ovis python2 python3 sfm viz
     Applications:                tests perf_tests apps
     Documentation:               NO
     Non-free algorithms:         NO
 
   GUI: 
     GTK+:                        YES (ver 2.24.32)
       GThread :                  YES (ver 2.56.4)
       GtkGlExt:                  NO
     VTK support:                 NO
 
   Media I/O: 
     ZLib:                        /usr/lib/aarch64-linux-gnu/libz.so (ver 1.2.11)
     JPEG:                        /usr/lib/aarch64-linux-gnu/libjpeg.so (ver 80)
     WEBP:                        /usr/lib/aarch64-linux-gnu/libwebp.so (ver encoder: 0x020e)
     PNG:                         /usr/lib/aarch64-linux-gnu/libpng.so (ver 1.6.34)
     TIFF:                        /usr/lib/aarch64-linux-gnu/libtiff.so (ver 42 / 4.0.9)
-     JPEG 2000:                   build (ver 1.900.1)
     OpenEXR:                     build (ver 2.3.0)
     HDR:                         YES
     SUNRASTER:                   YES
     PXM:                         YES
     PFM:                         YES
   Video I/O:
     DC1394:                      NO
     FFMPEG:                      YES
       avcodec:                   YES (57.107.100)
       avformat:                  YES (57.83.100)
       avutil:                    YES (55.78.100)
       swscale:                   YES (4.8.100)
       avresample:                NO
     GStreamer:                   YES (1.14.5)
     v4l/v4l2:                    YES (linux/videodev2.h)
 
   Parallel framework:            TBB (ver 2017.0 interface 9107)
 
   Trace:                         YES (with Intel ITT)
 
   Other third-party libraries:
     Lapack:                      NO
     Eigen:                       NO
     Custom HAL:                  YES (carotene (ver 0.0.1))
     Protobuf:                    build (3.5.1)
 
   NVIDIA CUDA:                   YES (ver 10.0, CUFFT CUBLAS)
     NVIDIA GPU arch:             53
     NVIDIA PTX archs:
 
   cuDNN:                         YES (ver 7.6.3)
 
   OpenCL:                        YES (no extra features)
     Include path:                /usr/src/app/opencv-4.1.1/3rdparty/include/opencl/1.2
     Link libraries:              Dynamic load
 
   Python (for build):            /usr/bin/python2.7
 
   Java:                          
     ant:                         NO
     JNI:                         NO
     Java wrappers:               NO
     Java tests:                  NO
 
   Install to:                    /usr/local
- -----------------------------------------------------------------
```


### 3. resinplayground/jetson-nano-cuda-opencv (v0.2:full)
CUDA version: 10.0.326

Image size: 5.13 GB

OpenCV version: 4.3.0

Excerpt from OpenCV config:
```
 OpenCV modules:
    To be built:                 alphamat aruco bgsegm bioinspired calib3d ccalib core cudaarithm cudabgsegm cudacodec cudafeatures2d cudafilters cudaimgproc cudalegacy cudaobjdetect cudaoptflow cudastereo cudawarping cudev datasets dnn dnn_objdetect dnn_superres dpm face features2d flann freetype fuzzy gapi hfs highgui img_hash imgcodecs imgproc intensity_transform line_descriptor ml objdetect optflow phase_unwrapping photo plot python3 quality rapid reg rgbd saliency shape stereo stitching structured_light superres surface_matching text tracking ts video videoio videostab xfeatures2d ximgproc xobjdetect xphoto
    Disabled:                    world
    Disabled by dependency:      -
    Unavailable:                 cnn_3dobj cvv hdf java js matlab ovis python2 sfm viz
    Applications:                tests perf_tests apps
    Documentation:               NO
    Non-free algorithms:         NO

  GUI: 
    GTK+:                        YES (ver 3.22.30)
      GThread :                  YES (ver 2.56.4)
      GtkGlExt:                  NO
    VTK support:                 NO

  Media I/O: 
    ZLib:                        /usr/lib/aarch64-linux-gnu/libz.so (ver 1.2.11)
    JPEG:                        /usr/lib/aarch64-linux-gnu/libjpeg.so (ver 80)
    WEBP:                        build (ver encoder: 0x020f)
    PNG:                         /usr/lib/aarch64-linux-gnu/libpng.so (ver 1.6.34)
    TIFF:                        /usr/lib/aarch64-linux-gnu/libtiff.so (ver 42 / 4.0.9)
    JPEG 2000:                   build Jasper (ver 1.900.1)
    OpenEXR:                     build (ver 2.3.0)
    HDR:                         YES
    SUNRASTER:                   YES
    PXM:                         YES
    PFM:                         YES

  Video I/O:
    DC1394:                      YES (2.2.5)
    FFMPEG:                      YES
      avcodec:                   YES (57.107.100)
      avformat:                  YES (57.83.100)
      avutil:                    YES (55.78.100)
      swscale:                   YES (4.8.100)
      avresample:                NO
    GStreamer:                   YES (1.14.5)
    v4l/v4l2:                    YES (linux/videodev2.h)

  Parallel framework:            TBB (ver 2017.0 interface 9107)

  Trace:                         YES (with Intel ITT)

  Other third-party libraries:
    Lapack:                      YES (/usr/lib/aarch64-linux-gnu/liblapack.so /usr/lib/aarch64-linux-gnu/libcblas.so /usr/lib/aarch64-linux-gnu/libatlas.so)
    Eigen:                       YES (ver 3.3.4)
    Custom HAL:                  YES (carotene (ver 0.0.1))
    Protobuf:                    build (3.5.1)

  NVIDIA CUDA:                   YES (ver 10.0, CUFFT CUBLAS)
    NVIDIA GPU arch:             53
    NVIDIA PTX archs:

  cuDNN:                         YES (ver 7.6.3)

  OpenCL:                        YES (no extra features)
    Include path:                /usr/src/app/opencv-4.3.0/3rdparty/include/opencl/1.2
    Link libraries:              Dynamic load

  Python 3:
    Interpreter:                 /usr/local/bin/python3.6 (ver 3.6.9)
    Libraries:                   /usr/lib/python3.6/config-3.6m-aarch64-linux-gnu/libpython3.6.so (ver 3.6.9)
    numpy:                       /usr/local/lib/python3.6/site-packages/numpy/core/include (ver 1.18.4)
    install path:                lib/python3.6/site-packages/cv2/python-3.6

  Python (for build):            /usr/local/bin/python3.6

  Java:                          
    ant:                         NO
    JNI:                         NO
    Java wrappers:               NO
    Java tests:                  NO

  Install to:                    /usr/local
-----------------------------------------------------------------

```




