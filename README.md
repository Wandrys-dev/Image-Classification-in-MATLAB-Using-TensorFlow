# Image Classification in MATLAB Using TensorFlow

### This example shows how to call a TensorFlow&trade; model from MATLAB&reg; using co-execution with Python&reg;.

![](Images/mainImage.png)

There are different options for accessing deep learning models within MATLAB.  These include:

1. Using models created in MATLAB using the [Deep Learning Toolbox](https://www.mathworks.com/products/deep-learning.html)
2. [Converting](https://www.mathworks.com/help/deeplearning/deep-learning-import-and-export.html) models from other frameworks into MATLAB
3. Co-executing models from other frameworks with MATLAB

This example provides an overview of how to perform 3. Co-execution. For a further example on co-execution see [Hyperparameter Tuning in MATLAB using Experiment Manager & TensorFlow](https://github.com/matlab-deep-learning/Hyperparameter-Tuning-in-MATLAB-using-Experiment-Manager-and-TensorFlow).

For reference, the following [table](#comparison-table) provides a comparison for all options.

## What is co-execution?

Co-execution between MATLAB and TensorFlow is when both frameworks are used together, in executing a single application.  Co-execution can achieve this by passing data to and from each framework. It requires MATLAB and Tensorflow to be installed on the same machine.

### Requirements

The example requires the following software:

* [MATLAB R2021b or later](https://www.mathworks.com/products/matlab.html)
* [Tensorflow 2.8](https://www.tensorflow.org/install)
* [Python 3.8+](https://www.python.org/downloads/)

For more information about installing Python, see [Install Supported Python Implementation](https://www.mathworks.com/help/matlab/matlab_external/install-supported-python-implementation.html). 

## Why perform co-execution?

There are many benefits to co-execution. These include:

* You can leverage the features and capabilities of both frameworks together in a single application.  For example, MATLAB performs the pre-and post-processing of data when training a model in TensorFlow.
* Teams working in multiple frameworks can build applications together.  This reduces the need to:
	* Rely on a single language
	* Manually re-code code from one language to another.
	* Force some team members to work in their non-preferred environment.

## What are the limitations of co-execution?

* Performance - Due to data passed between frameworks, there is a latency introduced. In most situations, the latency overhead is negligible, e.g., when performing model training. In other situations, e.g., performing inference at high throughputs, the latency impact is higher.
* Automatic code generation to embedded-devices. MATLAB can automatically generate code for the following: [C/C++ using MATLAB Coder](https://www.mathworks.com/products/matlab-coder.html), [CUDA using GPU Coder](https://www.mathworks.com/products/gpu-coder.html) and, [VHDL using Deep Learning HDL Toolbox](https://www.mathworks.com/products/deep-learning-hdl.html), including the deep learning model and pre and post-processing steps.  This deployment option is not available when co-execution with TensorFlow.  
	* Note: For versions R2022a or newer, MATLAB supports integration with TensorFlow Lite (TFLite) pretrained models. This enables the simulation of TensorFlow Lite models from both MATLAB and Simulink.  For code generation, MATLAB generates code for pre and/or post-processing and generates a call to the TensorFlow Lite interpreter on a supported target.
* Datatype conversion and data reformatting - Only select data types in both frameworks are supported for co-execution. This is because not all data types from one framework can be mapped and translated into an equivalent type in the other framework. For a list of data types that can be used, see [MATLAB to Python Data Type Mapping](https://www.mathworks.com/help/matlab/matlab_external/passing-data-to-python.html),

## How can co-execution be performed?

The [example](https://github.com/matlab-deep-learning/Image-Classification-in-MATLAB-Using-TensorFlow/blob/main/ImageClassificationinMATLABusingTensorFlow.m) shows how MATLAB can co-execute with TensorFlow to classify images using a model from [tf.Keras.applications](https://www.tensorflow.org/api_docs/python/tf/keras/applications).  The following steps outline what is covered in the example:

* **Configuring python setup:**

The script [checkPythonSetup](https://github.com/matlab-deep-learning/Image-Classification-in-MATLAB-Using-TensorFlow/blob/main/checkPythonSetup.m) contains commands to help set up the python environment. You don't need to run these commands, unless the default Python configuration causes errors.

For more information on setting up or troubleshooting the Python Environment in MATLAB see [Calling Python from MATLAB](https://www.mathworks.com/help/matlab/call-python-libraries.html)

* **Loading an image:**
```matlab:Code(Display)
imgOriginal = imread("./Images/banana.png");
imshow(imgOriginal)
```
![](Images/banana.png)
* **Importing model directly into MATLAB:**
```matlab:Code(Display)
model = py.tensorflow.keras.applications.efficientnet_v2.EfficientNetV2L();  
```
* **Calling a TensorFlow pretrained model for image classification:**

```matlab:Code(Display)
% converting input from MATLAB array into Python array.
X = py.numpy.asarray(imgforTF);

% call preprocessing function that is required for the image input in Keras.
X = py.tensorflow.keras.applications.efficientnet_v2.preprocess_input(X); 

% classify image 
Y = model.predict(X, batch_size); 

% label of classification output
label = py.tensorflow.keras.applications.efficientnet_v2.decode_predictions(Y); 
```
Note that many [pretrained models](https://www.mathworks.com/help/deeplearning/ug/pretrained-convolutional-neural-networks.html) are available for use directly in MATLAB without the need for co-execution. 

* **Gathering and displaying the classification result in MATLAB:**
```matlab:Code(Display)
labelStr = string(label); 
imshow(imgOriginal);
title(labelStr,Interpreter="none");
```
![](Images/bananaClassified.png)


## Comparison of Models accessible in MATLAB <a name="comparison-table"/>
| Capability  | Models created using the [Deep Learning Toolbox](https://www.mathworks.com/products/deep-learning.html) | Models [Converted from other Frameworks](https://www.mathworks.com/help/deeplearning/deep-learning-import-and-export.html)| Co-execution |
| ------------- | ------------- | ------------- | ------------- |
| Integrates with pre and post processing with MATLAB | ![](Images/000000.png) | ![](Images/000000.png)  | ![](Images/000000.png)    |
| Requires installation of MATLAB products only | ![](Images/000000.png)  | ![](Images/000000.png) | ![](Images/000002.png)  |
| Supports debugging from MATLAB| ![](Images/000000.png)   | ![](Images/000000.png)  | ![](Images/000002.png)  |
| Offers best inference performance in MATLAB and Simulink| ![](Images/000000.png)   | ![](Images/000000.png)  | ![](Images/000002.png)  |
| Comes with many MATLAB application examples  | ![](Images/000000.png)  | ![](Images/000000.png) | ![](Images/000002.png)  |
| Requires no datatype conversion and data reformatting | ![](Images/000000.png)   | ![](Images/000001.png) | ![](Images/000002.png)  |
| Provides largest coverage for embedded code generation with [MATLAB Coder](https://www.mathworks.com/products/matlab-coder.html), [GPU Coder](https://www.mathworks.com/products/gpu-coder.html) & [Deep Learning HDL Toolbox](https://www.mathworks.com/products/deep-learning-hdl.html) | ![](Images/000000.png)   | ![](Images/000001.png) | ![](Images/000002.png)  |
| Requires no additional libraries for standalone deployment with [MATLAB Compiler](https://www.mathworks.com/products/compiler.html)  | ![](Images/000000.png)   | ![](Images/000001.png) | ![](Images/000002.png)  |
| Accesses popular models in a single line of code  | ![](Images/000000.png)   | ![](Images/000002.png)  | ![](Images/000002.png)  |
| Access to models from TensorFlow and PyTorch | ![](Images/000002.png) | ![](Images/000001.png) | ![](Images/000000.png)    |


Key:

![](Images/000000.png) Most support and / or low effort
![](Images/000001.png) Some support and / or some effort
![](Images/000002.png) Little to no support  and / or high effort

Copyright 2022, The MathWorks, Inc.

