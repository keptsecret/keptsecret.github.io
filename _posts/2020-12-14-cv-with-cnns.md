---
title:      "Basic Computer Vision with CNNs"
date:       2020-12-14 10:00:00 +0700
categories: programming ML CV CNN
---
___Author's note__: So this is a rather new topic, even though I have played around with dense (fully connected) networks before - convolutional networks are a new concept for me and I was surprised with its performance._

There's certainly more I should be finding out about CNNs, but it's gonna take a while so here's a quick rundown of the simple - also just so I can quickly come back and review through this stuff later on. A content summary of stuff from chapter 14 of [Hands-on Machine Learning](https://github.com/ageron/handson-ml2) (great practical book, I'm enjoying learning from it).

## The Layers

### Convolutional layer

In a traditional dense layer, every neuron in a layer is connected to the outputs of every neuron in the layer before it.
This generally means, if we pass in an image to a dense layer, it tries to find a relationship between every pixel in the image, which as may be apparent is not how images work.

In an image, only some related pixels form a line or an edge, which in turn makes up a shape that our eyes and brain interprets as an object. It can be seen why a dense layer may not be appropriate for learning to identify images. That is where convolutional layers come in.

A convolutional layer is made up of stacked layers of filters. Each filter usually looks for different relationships in an image. In the example notebook below, there is a horizontal filter and a vertical filter, but a convolutional layer are made up of many more than just that.

<script src="https://gist.github.com/keptsecret/077e37e5e79ada29cc109ef48e14db23.js"></script>

Each filter is a matrix, generally described by a __kernel size__ (how many pixels the filter processes at once), and is shifted through the image matrix performing matrix multiplication (dot product) between the filter kernel and the region of the image the kernel is over. The step size between each shift is determined by the __stride__.

In the above example, the two filters created have a 3x3 kernel size and the convolutional layer the filters are given to therefore have 2 filters (or __feature maps__) and a stride of 1.
A convolutional layer is preferred over a fully connected layer in computer vision for its ability to reduce the image down into various features with spatial dependencies.

<img src="https://miro.medium.com/max/1356/1*-OM6jQTMNACDX2vAh_lvMQ.png">

As can be seen then, the larger the kernel size, the more the image will be reduced. In larger images, the model may start with the largest kernel size (7x7) to reduce the input information down. Though in general, a 3x3 kernel size is most common.
Meanwhile, the output size can be reduced using the size of the stride as well. A stride of 2 will essentially halve the output dimensions, as the filter is being shifted by 2 spots each time across the input.

To note, there is a "special" convolutional layer, which has a kernel size of 1x1. At first glance, it may seem that this layer wouldn't do anything as it maps the output layer 1-to-1 directly.
However, what a 1x1 convolutional layer can do is increase or decrease the number of feature maps in the input. This could be useful for reducing the dimensionality of the input and remove redundant filters or lower computational load. As such, a 1x1 kernel is usually placed before another convolutional layer.

A problem arises with each layer the image passes through though. For one, the outputs are shrinking, which may be undesirable if you want to maintain information across layers. The information on the outer edge of the image is processed less than that in the center and is being lost. The solution to this is to use zero padding, which is essentially adding a rim of 0s around the input (in keras, use `padding='same'`). This helps maintain the output size, which will only decrease when we specify it to, such as using a 2-stride.

I've largely been using _Keras_ to create neural networks and that won't change here, so creating a convolutional layer in keras can be done with `keras.layers.Conv2D`.

{% highlight python linenos %}
tf.keras.layers.Conv2D(
    filters,
    kernel_size,
    strides=(1, 1),
    padding="valid",
    data_format=None,
    dilation_rate=(1, 1),
    groups=1,
    activation=None,
    use_bias=True,
    kernel_initializer="glorot_uniform",
    bias_initializer="zeros",
    kernel_regularizer=None,
    bias_regularizer=None,
    activity_regularizer=None,
    kernel_constraint=None,
    bias_constraint=None,
    **kwargs
)
{% endhighlight %}
