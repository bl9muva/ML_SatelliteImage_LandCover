# ML_SatelliteImage_LandCover
## Abstract
Land classification is an important tool to distinguish between different kinds of areas e.g. residential communities,
forests, or the ocean. In order to make assumptions about a certain territory, like a county, one first needs to know
what it is composed of. On a satellite image, the different kinds of terrain have different colors and hence can be
automatically classified. In this project, I implement transfer learning on the Xception convolutional neural network
(CNN) which was trained and tested on labeled land-use data of York County, Virginia with an accuracy of 86%.

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

In this work, I implement CNNs for land cover and land use classification. Land cover usually can be determined from
satellite and aerial images since they document how much each of a region is covered with forest, wetlands, buildings,
agriculture, and water types. Land use however is not traditionally determined from satellite imagery since it shows
how the landscape is used by people. It is a socio-economic function of a piece of land. Nonetheless, these two terms
are connected, and recent advances in machine learning have shown that both can be reasonably predicted from satellite
and imagery data with other additional spatial, geographic and social datasets.

## Motivation and Proposal
Most recently in 2020, Governor Ralph S. Northam, presented the Virginia Coastal Master Planning framework which
aims to improve the Commonwealth’s ability to respond and adapt to climate change threats to coastal communities.
Although there is abundant research on the location of where climate changes will occur, there is a lack of knowledge
on where people will be in relation to these high-risk geographic locations. Understanding population density in
these locations would aid decision-making and prioritize resource allocation. To aid these efforts, a machine learning
algorithm could be developed to identify areas in the coast with high levels of population, like residential communities
or commercial properties.

I propose that satellite images are true reflections of land use, which in turn are closely related to habitation and
population changes in the area. In order to aid the effort of the Virginia Coastal Resilience Planning, land use
classifications from satellite images can offer direct evidence for appropriate direction of coastal flood protection and
adaptation. In addition, in a dire scenario of disaster rescue mission, the land use model and its relation to population
distribution can offer proper allocation of rescue resources for humans and properties.

## Data
The U.S. Geological Survey (USGS) collects and provides water, earth, biological, and civilian mapping data. Currently,
the organization maintains many datasets for U.S. geological locations, including Virgnia coastal areas. These datasets
can be downloaded using the Google Earth Engine API https://developers.google.com/earth-engine as well
as USGS EarchExplorer https://earthexplorer.usgs.gov/.

Although landsat8 contains time-series images, I decided to only work on images that were taken on a particular time
point since the shear size of all satellite images is overwhelming. There are also additional considerations of image
qualities, for example, the presence of cloud as well as seasonal changes can effect the land use classification. The
dataset used in this project was acquired on 07/20/2019 by landset 8 OLI/TIRS (OperationalLand Imager / Thermal
Infrared Sensor) C2 L1, path 014 and row 034. This dataset has a total of 11 bands, in which bands 2, 3, 4 are blue,
green, and red channels at 30m resolutions, respectively. To aid the model training, I used a third-party program
‘Spectral Discovery for Landsat-8’ to merge these three RGB bands together and obtained a 15m colored satellite
images of Virginia coast.

This satellite image was further sliced into two separate images, one for model training and validation, and the other for
testing and demo purposes.

Since CNN is a supervised learning, I decided to provide labels for our training and validation data by my own
visual inspections. Due to limitations imposed by manual labor as well as the resolution of the satellite image, I
split our training/validation data into a 30x50 rectangle grids, and labeled all 1500 rectangles into one of 10 categories:
airport, commercial, farmland, forest, lake, marsh, ocean, residential, seashore, and sound. These labeled figures were
subsequently employed for the CNN model building.

## Results
To train and test the model, a 80-20 split was employed. For a baselne model, a simple 3 convolution layer CNN was 
able to achieve an accuracy of 99% on training data, but only 68% on the validation data. Clearly, I encountered an 
overfitting problem.

To improve the accuracy on the validation data, I decided to use a pre-trained Imagenet model which are included
in the ‘keras.application‘ package since the project aims to classify images. The following pre-trained models were
explored, Xception and DenseNet169. Although Xception had a larger size of 88MB, it contained a top-5 accuracy of
0.945. DenseNet169 was explored due to its lower size of 57MB and top-5 accuracy of 0.932. Overall, the transfer
learning model that performed most optimally was Xception. The neural network was supplemented with a pooling layer
and a two dense layers. The dense layers contain 200 and 170 units. Overall, I found this network configuration was a
good compromise between effectiveness and calculation time.

I started out with a learning rate of 0.01 and observed the accuracy while training the model. It turned out to be
overfitting after only a few epochs since test accuracy stalled at about 60%. The next step was to lower the learning
rate to 0.0001 and introduced a dropout layer between the two dense layers with an aggressive rate of 0.4. This improved
the results to a test accuracy of around 80%.

An additional optimization was achieved by augmenting the image data. Using keras’ preprocessing layers I added random
flips, zooms and rotations in order enrich the amount of information the model can learn from the training data. This
improved results further to around 86% test accuracy and also decreased the amount of overfitting.

To test the trained model, I applied the final model on the test image. I first chose to split the test image in the same
resolution as our training image, and the result is very encouraging. By my visual inspection, the labeled image showed
patterns that mimic different features on the test satellite image. Interestingly, when I doubled the resolution of the
test image both on rows and columns, the resulted classification clearly has more detailed features, i.e., an apparent
improvement. Additionally, I have explored an even higher resolution (3x in both dimensions), the resulted land use
appeared to be too much fragmented. Clearly, image resolution is a tunable feature in our model, and can be explored
advantageously with some caution.

## Discussion
This classification problem is unique in that each satellite fragment may be composed of several objects. For example,
a satellite image of an ocean front community may contain two labels, residential and water. For these observations,
the label with the highest surface area of the image was chosen. I noted an increase of 4% in test accuracy when
additional data augmentation were included. This technique may have been especially beneficial by segmenting
the observations into more precise labels for some observations. I chose to use a smaller learning rate due to the
significant gains in accuracy, and to overcome the overfitting.

Additionally, for this problem, the dataset contained approximately 1500 satellite images of Virginia Coast. I would
like to acknowledge that additional training data would be beneficial to generate a more optimal algorithm, but due to
the labor-intensive task of generating labels, this would be out of scope for this project. Overall, the algorithm developed
provides promising results into the use of transfer learning into land cover classification.

I also recognize that there are a number of shortcomings in these labels: 1. these categories may not conform to
conventions by domain experts; 2. many of images contain multiple features and their labels can be ambivalent, 3. I
may make some clerical errors. Although these manually labeled sub-figures afford us a way to train our CNN models
on satellite images, they may also have hampered us to achieve a higher accuracy of the final model.

## Acknowledgement
This project is accomplished using the Colab, a product from Google Research. 

If anyone is interested in the original satellite image as well as my manually labeled 1500 images for running this
notebook, or if you have any questions about this project, please drop me a message: binyong@gmal.com. 
