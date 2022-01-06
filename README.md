# ML_SatelliteImage_LandCover
## Abstract
Land classification is an important tool to distinguish between different kinds of areas e.g. residential communities,
forests, or the ocean. In order to make assumptions about a certain territory, like a county, one first needs to know
what it is composed of. On a satellite image, the different kinds of terrain have different colors and hence can be
automatically classified. In this project, we implement transfer learning on the Xception convolutional neural network
(CNN) which was trained and tested on labeled land-use data of York County, Virginia with an accuracy of 88%.
## Background
Convolutional Neural Networks (CNNs) are inspired by the visual cortex architecture, in which many neurons have
a small local receptive field. The name “convolutional” stands for a special kind of linear mathematical operation
called “convolution”. In the convolution operation, the shape of one signal is modified by another signal’s probability
density function. In CNN, we often apply this second function, called kernel, over a finite space, i.e., a local field in a
comparable manner as neurons in visual cortex. This type of local field effect is called “sparse interaction”, a distinct
property rather than a traditional neural network where a matrix multiplication function connects each input unit with
each output unit. Additionally, convolution also leverages two additional important properties, parameter sharing and
equivariant representation, to improve machine learning systems.
In practice, CNN consists of building blocks that combine one or more convolution layers, their corresponding activation
function layers, and a pooling layer that reduces resolution of the signal. By stacking these building blocks on top of
each other, neural network gets deeper and smaller, and multiple features can be extracted in this process. Finally, a
regular feedforward neural network with fully connected layers was added on top of the stack, which accumulates to the
output layer that is usually equipped with a softmax function.
The first CNN called LeNet-5 was introduced by Yann LeCun in 1998. It was used to recognize handwritten check
numbers successfully. Over the years, variants of CNN architectures, such as AlexNet, GoogLeNet, ResNet, have been
developed and were widely used for many different tasks.
