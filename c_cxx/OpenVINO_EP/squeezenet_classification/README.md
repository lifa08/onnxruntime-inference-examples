# Image classification with Squeezenet in CPP using OpenVINO Execution Provider:

1. The image classification uses a Squeezenet Deep Learning ONNX Model from the ONNX Model Zoo. Currently this sample is only supported on Linux platforms.

2. The sample involves presenting an image to the ONNX Runtime (RT), which uses the OpenVINO Execution Provider for ONNX RT to run inference on various Intel hardware devices like Intel CPU, GPU, VPU and more. The sample uses OpenCV for image processing and ONNX Runtime OpenVINO EP for inference. After the sample image is inferred, the terminal will output the predicted label classes in order of their confidence.

The source code for this sample is available [here](https://github.com/microsoft/onnxruntime-inference-examples/tree/main/c_cxx/OpenVINO_EP/squeezenet_classification).

# How to build

## Prerequisites
1. [The Intel<sup>®</sup> Distribution of OpenVINO toolkit](https://docs.openvinotoolkit.org/latest/index.html)

### Install OpenVINO Runtime

```bash
$ sudo mkdir /opt/intel
$ curl -L https://storage.openvinotoolkit.org/repositories/openvino/packages/2022.2/linux/l_openvino_toolkit_ubuntu20_2022.2.0.7713.af16ea1d79a_x86_64.tgz --output openvino_2022.2.0.7713.tgz
$ tar -xf openvino_2022.2.0.7713.tgz
$ sudo mv l_openvino_toolkit_ubuntu20_2022.2.0.7713.af16ea1d79a_x86_64 /opt/intel/openvino_2022.2.0.7713
$ cd /opt/intel/
$ sudo ln -s openvino_2022.2.0.7713 openvino_2022
$ cd /opt/intel/openvino_2022.2.0.7713/
$ source setupvars.sh
[setupvars.sh] ERROR: Unsupported Python version. Please install one of Python 3.6 - 3.9 (64-bit) from https://www.python.org/downloads/
```

To solve python version problem, install python3.9, and let python3 point to python3.9

```bash
$ sudo -E add-apt-repository ppa:deadsnakes/ppa
$ sudo apt install python3.9
$ which python3.9
/usr/bin/python3.9
$ python3 --version
Python 3.10.6
$ sudo ln -sf /usr/bin/python3.9 /usr/bin/python3
```

### Compile Onnxruntime with OpenVINO

```bash
$ source /opt/intel/openvino_2022/setupvars.sh
[setupvars.sh] OpenVINO environment initialized

$ ./build.sh --config RelWithDebInfo --use_openvino CPU_FP32 --build_shared_lib
$ tools/ci_build/github/linux/copy_strip_binary.sh -r build/Linux/ -a onnxruntime -l libonnxruntime.so.1.14.0 -c RelWithDebInfo -s . -t 8b0669bf63d18cf6a90416d68af29cd306bcbbf7
$ cp -r build/Linux/onnxruntime/ ~/install/
$ cp libonnxruntime_providers_openvino.so ~/install/onnxruntime/lib/
$ cp libonnxruntime_providers_shared.so ~/install/onnxruntime/lib/
```

2. Use opencv (use the same opencv package that comes builtin with Intel<sup>®</sup> Distribution of OpenVINO toolkit)
3. Use any sample image as input to the sample.
4. Download the latest Squeezenet model from the ONNX Model Zoo.
   This example was adapted from [ONNX Model Zoo](https://github.com/onnx/models).Download the latest version of the [Squeezenet](https://github.com/onnx/models/tree/master/vision/classification/squeezenet) model from here.


## Install ONNX Runtime for OpenVINO Execution Provider

## Build steps
[build instructions](https://onnxruntime.ai/docs/build/eps.html#openvino)

## Reference Documentation
[Documentation](https://onnxruntime.ai/docs/execution-providers/OpenVINO-ExecutionProvider.html)

If you build it by yourself, you must append the "--build_shared_lib" flag to your build command.

```
./build.sh --config Release --use_openvino CPU_FP32 --build_shared_lib
```

## Build the sample C++ Application
1. Navigate to the directory /onnxruntime/build/Linux/Release/

2. Now copy all the files required to run this sample at this same location (/onnxruntime/build/Linux/Release/)

3. compile the sample
```
g++ -o run_squeezenet squeezenet_cpp_app.cpp -I ../../../include/onnxruntime/core/session/ -I /opt/intel/openvino_2021.4.689/opencv/include/ -I /opt/intel/openvino_2021.4.689/opencv/lib/ -L ./ -lonnxruntime_providers_openvino -lonnxruntime_providers_shared -lonnxruntime -L /opt/intel/openvino_2021.4.689/opencv/lib/ -lopencv_imgcodecs -lopencv_dnn -lopencv_core -lopencv_imgproc
```
Note: This build command is using the opencv location from OpenVINO 2021.3 Release Installation. You can use any version of OpenVINO and change the location path accordingly.

4. Run the sample

To Run
(using Intel OpenVINO-EP)
```
./run_squeezenet --use_openvino <path_to_onnx_model> <path_to_sample_image> <path_to_labels_file>
```
Example:
```
./run_squeezenet --use_openvino squeezenet1.1-7.onnx demo.jpeg synset.txt  (using Intel OpenVINO-EP)
```
(using Default CPU)
```
./run_squeezenet --use_cpu <path_to_onnx_model> <path_to_sample_image> <path_to_labels_file>
```

## References:

[OpenVINO Execution Provider](https://www.intel.com/content/www/us/en/artificial-intelligence/posts/faster-inferencing-with-one-line-of-code.html)

[Other ONNXRT Reference Samples](https://github.com/microsoft/onnxruntime-inference-examples/tree/main/c_cxx)
