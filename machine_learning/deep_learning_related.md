## Deeping Learning Related Topics

In this post I would like to share some of the topics I read about in Deeping Learning(DL). I also use this to keep notes.

Table of Contents:

- [CNN Architectures](#cnn_architectures)



<a name='cnn_architectures'></a>

### CNN Architectures

Reference: [slides](http://cs231n.stanford.edu/slides/2017/cs231n_2017_lecture9.pdf) from lecture 9 Standford CS 231n.

#### LeNet-5 (1998)

Architecture is [CONV-POOL-CONV-POOL-FC-FC]

Conv filters are 5x5, with stride 1.
Pooling layers are 2x2, with stride 2

#### AlexNet (2012)

Architecture: [CONV1-POOL1-NORM1-CONV2-POOL2-NORM2-CONV3-CONV4-CONV5-POOL3-FC6-FC7-FC8]

Input: 227x227x3 images

CONV1: (96 11x11 filters with stride 4 pad 0)  55(55 = (227-11)/4 + 1)x55x96

POOL1: (3x3 filters with stride 2) 27x27x96

#### ZFNet (2013)

AlexNet but:

CONV1: 11x11 stride 4 to 7x7 stride 2
CONV3,4,5: 384, 384, 256 filters to 512, 1024, 512

#### VGGNet (2014)

***Small filters, Deeper networks***

8 layers (AlexNet) -> 16-19 layers

Only use 3x3 CONV stride 1, pad 1 and 2x2 POOL stride 2

Most memory is in early CONV, most params are in late FC

#### GoogLeNet (2014) "Inception"

22 layers, Inception module, No FC

Naive inception module: apply filters with various sizes (1x1, 3x3, 5x5) and pooling operation and then concatenate all results depth wise. ***Problem: expensive to compute and depth must grow at every layer.***

Solution: Bottleneck layer that use 1x1 convolutions to reduce feature depth

#### ResNet (2015) "Residual"
***Very deep networks using residual connections***

more than 150+ layers

- stack residual blocks
- every residual block has two 3x3 conv layers
- periodically double # of filters and downsample spatially stride 2
- no FC layers in the end, except softmax to output classes
- For deeper networks, use "bottleneck" layer to improve efficienty

Suppose input is [28x28x256]

first [1x1 conv, 64] result in [28x28x64]
then [3x3 conv, 64] result in [28x28x64]
finally [1x1 conv, 256] result in [28x28x256]


#### Inception v3 & v4
In v3, 5x5 CONV is replaced as two 3x3 CONV.
In v4, ResNet is embedded.

#### Xception

***Separate channel and spatial filtering***

Apply different 2dCONV (spatial filter) for each input channel, then use N 1x1 (depth filter).


