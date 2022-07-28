---
title: "Install denseflow for optical flow extraction"
parent: Posts
---
<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

# Introduction
[denseflow](https://github.com/open-mmlab/denseflow) is the recommended tool by mmaction2 for extracting frames from videos, especially for the optical flows. This post record errors and solutions I have encountered.

If you have root access of your device, i.e,. `sudo`, solution of most problems can be easily found from Google. This post assume **NO root access**.

# Commands
We follow the [install.md](https://github.com/open-mmlab/denseflow/blob/master/INSTALL.md) in `denseflow` of current version (2022-07-28).
Before running the below commands, make sure you have installed a **CUDA** in the **main** environment (it's NOT the `cudatoolkit` installed by `conda`)
```bash
# ZZROOT is the root dir of all the installation
# you may put these lines into your `.bashrc` or `.zshrc`.
export ZZROOT=$HOME/app
export PATH=$ZZROOT/bin:$PATH
export LD_LIBRARY_PATH=$ZZROOT/lib:$ZZROOT/lib64:$LD_LIBRARY_PATH

# fetch install scripts
git clone https://github.com/innerlee/setup.git
cd setup

# opencv depends on ffmpeg for video decoding
# ffmpeg depends on nasm, yasm, libx264, libx265, libvpx
./zznasm.sh
./zzyasm.sh
./zzlibx264.sh
./zzlibx265.sh
./zzlibvpx.sh
./zzffmpeg.sh
./zzopencv.sh

# you may put this line into your .bashrc
export OpenCV_DIR=$ZZROOT

# install boost
./zzboost.sh
# you may put this line into your .bashrc
export BOOST_ROOT=$ZZROOT

# install hdf5
./zzhdf5.sh

# finally, install denseflow
./zzdenseflow.sh
```

# Bebugging Record
## Hints
- All files brought by the `setup` are put in `~/app` by default. In case your want to restart/give up, just delete this folder.
- After every installation, e.g., `./zzopencv.sh`, remember to read the print log (last few rows should be enough) for checking if the installation succeed.


## Error 1: cmake failed
The below error happends when running the commands:

- `cmake -DCMAKE_INSTALL_PREFIX=$HOME/app -DUSE_HDF5=no -DUSE_NVFLOW=no ..`, which comes from the `readme.md` of `denseflow`;
- `./zzdenseflow.sh`
```shell
CMake Error at CMakeLists.txt:19 (find_package):
  By not providing "FindOpenCV.cmake" in CMAKE_MODULE_PATH this project has
  asked CMake to find a package configuration file provided by "OpenCV", but
  CMake did not find one.

  Could not find a package configuration file provided by "OpenCV" with any
  of the following names:

    OpenCVConfig.cmake
    opencv-config.cmake

  Add the installation prefix of "OpenCV" to CMAKE_PREFIX_PATH or set
  "OpenCV_DIR" to a directory containing one of the above files.  If "OpenCV"
  provides a separate development package or SDK, be sure it has been
  installed.


-- Configuring incomplete, errors occurred!
See also "/home/s1141196/PolyU/denseflow/build/CMakeFiles/CMakeOutput.log".
See also "/home/s1141196/PolyU/denseflow/build/CMakeFiles/CMakeError.log".
```

**Solution**
This simply means that the installation of `opencv` was failed, can be verified by excuting the `./zzopencv.sh` again. There seems to be some solutions requiring root access, you may google it by yourself.

## Error 2: x265 not found using pkg-config
The error `x265 not found using pk-config` happens when running the `./zzffmpeg.sh`.

**Solution**
I found two issues[(1)](https://github.com/innerlee/setup/issues/12#issuecomment-686974585)[(2)](https://github.com/innerlee/setup/issues/42) mention this error and they both state this error can be solved by:
```
conda install pkg-config
```
By the way, my conda install stucks and this is solved by `conda update --all --yes`.

## Error 3: unsupported GNU version! gcc versions later than 8 are not supported!
This error happens when running `./zzopencv.sh`. Interestingly, it may not be printed in the first try of installation (but actually was failed).

```bash
In file included from /usr/include/cuda_runtime.h:83,
                 from <command-line>:
/usr/include/crt/host_config.h:138:2: error: #error -- unsupported GNU version! gcc versions later than 8 are not supported!
  138 | #error -- unsupported GNU version! gcc versions later than 8 are not supported!
      |  ^~~~~
[ 35%] Linking CXX static library ../lib/liblibtiff.a
[ 37%] Built target libtiff
CMake Error at cuda_compile_1_generated_gpu_mat.cu.o.RELEASE.cmake:222 (message):
  Error generating
  /home/s1141196/app/src/opencv/build/modules/core/CMakeFiles/cuda_compile_1.dir/src/cuda/./cuda_compile_1_generated_gpu_mat.cu.o


make[2]: *** [modules/core/CMakeFiles/opencv_core.dir/build.make:65: modules/core/CMakeFiles/cuda_compile_1.dir/src/cuda/cuda_compile_1_generated_gpu_mat.cu.o] Error 1
make[1]: *** [CMakeFiles/Makefile2:3724: modules/core/CMakeFiles/opencv_core.dir/all] Error 2
```

**Solution**

I found it's because the imcompability betweent the versions of **CUDA** and **GCC**. Check [CUDA-GCC](https://stackoverflow.com/questions/6622454/cuda-incompatible-with-my-gcc-version/46380601#46380601) and found that my CUDA is 10.2 but GCC is 9.3.0.

Downgrading the GCC version to lower than 8.0 can work with CUDA 10.2, which can be easily solved with **root** access:
- [link1](https://github.com/espressomd/espresso/issues/3654)
- [link2](https://stackoverflow.com/questions/65605972/cmake-unsupported-gnu-version-gcc-versions-later-than-8-are-not-supported)

Otherwise, excuting the `./zzgcc.sh` to install `gcc==7.5.0`:
```bash
./zzm4.sh
./zzgmp.sh
./zzmpfr.sh
./zzmpc.sh
./zzgcc.sh
gcc --version # check installation
```
If `gcc` installation failed, you may refer to this [issue](https://github.com/innerlee/setup/issues/44) to see if you can find a solution.
