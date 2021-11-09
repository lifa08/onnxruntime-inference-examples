# FNS Candy
FNS Candy is a style transfer model. In this sample application, we use the ONNX
Runtime C API to process an image using the FNS Candy model in ONNX format.

# Build Instructions
See [../README.md](../README.md)

# Prepare data
First, download the FNS Candy ONNX model from [here][1].

Then, prepare an image:
1. PNG format
2. Dimension of 720x720

__Note__ the image must be of dimension 720x720, otherwise, it would fail:
```
Warning: Checker does not support models with experimental ops: ImageScaler
Warning: Checker does not support models with experimental ops: Crop
Warning: Checker does not support models with experimental ops: Crop
Warning: Checker does not support models with experimental ops: Crop
Warning: Checker does not support models with experimental ops: Crop
Warning: Checker does not support models with experimental ops: Crop
Warning: Checker does not support models with experimental ops: Crop
Warning: Checker does not support models with experimental ops: Crop
Warning: Checker does not support models with experimental ops: Affine
fail
input_data_length:1440000
```

# Run
Command to run the application:

```
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

__Note__: cpu must be specified even it is the default, otherwise following errors occur:

```bash
$ ./fns_candy_style_transfer.exe candy.onnx demonstration.png output.png
Try to enable CUDA first
... [E:onnxruntime:test, provider_bridge_ort.cc:889 onnxruntime::ProviderSharedLibrary::Ensure] LoadLibrary failed with error 126 "The specified module could not be found."
OrtSessionOptionsAppendExecutionProvider_Cuda: Failed to load shared library
CUDA is not available
.
.
.
fail
```

[1]: https://raw.githubusercontent.com/microsoft/Windows-Machine-Learning/master/Samples/FNSCandyStyleTransfer/UWP/cs/Assets/candy.onnx
