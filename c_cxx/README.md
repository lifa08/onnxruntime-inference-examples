This directory contains a few C/C++ sample applications for demoing onnxruntime usage:

1. (Windows and Linux) fns_candy_style_transfer: A C application that uses the FNS-Candy
style transfer model to re-style images. It is written purely in C, no C++.
https://github.com/jcjohnson/fast-neural-style
2. (Windows only) MNIST: A windows GUI application for doing handwriting recognition
3. (Windows only) imagenet: An end-to-end sample for the 
[ImageNet Large Scale Visual Recognition Challenge 2012](http://www.image-net.org/challenges/LSVRC/2012/)
- requires ATL libraries to be installed as a part of the VS Studio installation.
4. model-explorer: A commandline C++ application that generates random data and performs model inference.
A second C++ application demonstrates how to perform batch processing. (TODO: Add CI build for it)
5. OpenVINO_EP: Using OpenVino execution provider on the squeezenet model (TODO: Add CI build for it)
6. opschema_lib_use (TODO: Add CI build for it)

# How to build on Windows

## Prerequisites
1. Visual Studio 2015/2017/2019
2. cmake(version >=3.13)
3. (optional) libpng 1.6

## Step 1 Install libpng
Download precompiled libpng library from https://onnxruntimetestdata.blob.core.windows.net/models/libpng.zip,
unzip and copy to ```C:\Program Files (x86)\libpng```.

__Note__: needed by fns_candy_style_transfer.

## Step 2: Install ONNX Runtime
### Option 1: download a prebuilt package
Download a prebuit onnxruntime from
https://github.com/microsoft/onnxruntime/releases/download/v1.9.0/onnxruntime-win-x64-1.9.0.zip 
unzip and copy to ```C:\Program Files (x86)\libpng```.

### Option 2: build ONNX Runtime from source

#### Open Visual Studio's Developer Command Prompt:

From Visual Studio Tools --> tools --> Command Line --> Developer PowerShell/Developer Command Prompt.
 
This will setup necessary environment for the compiler and other things to be found.


#### Build ONNX Runtime
```
build.bat --config RelWithDebInfo --build_shared_lib --parallel 
```

By default this will build a project with "C:\Program Files (x86)\onnxruntime" install destination.
This is a protected folder on Windows. If you do not want to run installation with elevated priviliges
you will need to override the default installation location by passing extra CMake arguments. For example:

```
build.bat --config RelWithDebInfo --build_shared_lib --parallel --cmake_extra_defines CMAKE_INSTALL_PREFIX=c:\dev\ort_install
```

#### Install ONNX Runtime
By default products of the build on Windows go to ```.\build\Windows\<config>``` folder.
In the case above it would be ```.\build\Windows\RelWithDebInfo```.

__Note__: If you did not specify alternative installation location above you would need to
open an elevated command prompt to install onnxruntime.

```
cd .\Windows\RelWithDebInfo
msbuild INSTALL.vcxproj /p:Configuration=RelWithDebInfo
```

## Step 3: Build the onnxruntime-inference-examples\c_cxx

First open Visual Studio's Developer Command Prompt and change your current folder
to ```onnxruntime-inference-examples\c_cxx```

### Generate Makefiles with cmake

```bat
mkdir build && cd build
cmake .. -A x64 -T host=x64 -DLIBPNG_ROOTDIR="C:\Program Files (x86)\libpng" -DONNXRUNTIME_ROOTDIR="C:\Program Files (x86)\onnxruntime"
```

### Build c_cxx

```bat
msbuild .\onnxruntime_samples.sln /nologo /p:platform="x64" /p:configuration="Release" /p:VisualStudioVersion="16.0" /m
```

### Run the examples

#### fns_candy_style_transfer

See [fns_candy_style_transfer README](fns_candy_style_transfer/README.md) for more detail

In folder ```onnxruntime-inference-examples\c_cxx\build\fns_candy_style_transfer\Release```

In GitBash prompt

```bash
./fns_candy_style_transfer.exe candy.onnx test.png output.png cpu
Warning: Checker does not support models with experimental ops: ImageScaler
Warning: Checker does not support models with experimental ops: Crop
Warning: Checker does not support models with experimental ops: Crop
Warning: Checker does not support models with experimental ops: Crop
Warning: Checker does not support models with experimental ops: Crop
Warning: Checker does not support models with experimental ops: Crop
Warning: Checker does not support models with experimental ops: Crop
Warning: Checker does not support models with experimental ops: Crop
Warning: Checker does not support models with experimental ops: Affine
```

__Note__: To run the samples make sure that your Install Folder Bin is in the path so your
sample executable can find onnxruntime dll and libpng if you used it.

```bash
$ ./fns_candy_style_transfer.exe
... error while loading shared libraries: libpng16.dll: cannot open shared object file: No such file or directory
```

```bash
$ ./fns_candy_style_transfer.exe
... error while loading shared libraries: onnxruntime.dll: cannot open shared object file: No such file or directory
```

If still not found: copy libpng16.dll and onnxruntime.dll from C:\Program Files (x86)\ to this dir.

```bash
$ ls
candy.onnx  fns_candy_style_transfer.exe*  libpng16.dll*  onnxruntime.dll*  output.png  test.png
```


# How to build on Linux

### Install cmake

```bash
sudo snap install cmake --classic
```

### Install libpng

```bash
sudo apt-get install libpng-dev
```

### Install ONNX Runtime
#### Option 1: download precompiled onnxruntime from [here][1]

```bash
aria2c -d ~ https://github.com/microsoft/onnxruntime/releases/download/v1.9.0/onnxruntime-linux-x64-1.9.0.tgz
cd ~/GitHub/
mkdir onnxruntimebin
cd onnxruntimebin/
tar --strip=1 -zxvf ~/onnxruntime-linux-x64-1.9.0.tgz
```

__Note__: aria2c can be installed with ```sudo apt install aria2```

#### Option 2: build it from source code
```bash
git clone https://github.com/microsoft/onnxruntime.git
cd onnxruntime
git checkout v1.9.1
git switch -c v1.9.1
./build.sh --config RelWithDebInfo --parallel --build_shared_lib
tools/ci_build/github/linux/copy_strip_binary.sh -r build/Linux -a onnxruntime -l libonnxruntime.so.1.9.1 -c RelWithDebInfo -s . -t 2a96b73a1afa9aaafb51
cp -r build/Linux/onnxruntime/ ~/install/
```

__Note__: `--build_shared_lib` must be specified, otherwise copy_strip_binary.sh would fail.

__Note__: For this explicit example, the latest onnxruntime is not working:
```error: 'EXHAUSTIVE' undeclared``` since the EXHAUSTIVE has been replaced by
```OrtCudnnConvAlgoSearchExhaustive``` in commit "C API Enum Name Fixes".

### Build the example

```bash
cd ~/GitHub/onnxruntime-inference-examples/c_cxx/build
cmake ../ -DONNXRUNTIME_ROOTDIR=~/GitHub/onnxruntimebin
make -j$(nproc)
```

### Run the example

```bash
cd ~/GitHub/onnxruntime-inference-examples/c_cxx/build/fns_candy_style_transfer
./fns_candy_style_transfer candy.onnx test.png output.png cpu
```

__Note__: cpu must be specified.

[1]: https://github.com/microsoft/onnxruntime/releases/download/v1.9.0/onnxruntime-linux-x64-1.9.0.tgz
