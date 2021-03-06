---
layout: post
title:  "Finding signal with CUDA"
date:   2015-09-09 13:19:51
categories: neural_nets
---

GPUs are really cool devices.

GPU interface libraries have come a long way since the days of old, and general purpose GPU's have become [extremely][torch] [common][caffe] in the world of data science and machine learning specifically because of the capabilities of a GPU to compute huge amounts of complex math, like an algorithm that can separate signal from noisy input data.

When I first became interested to the concept of machine learning and forecasting, the very first algorithm that I taught myself was the [artificial neural network][ann]. artificial neural networks are amazingly simple tools to describe, but quite challenging to implement in practice. 

an artificial neural network defines a structure that takes your projects data as input, subjects the data to a vast series of internal objects, and spits out your selected outputs. Initially the output values would be close to meaningless as your initial weights would be randomly generated, but the magic happens when you pipe the output's to a learning algorithm that is able to score how well a set of weights did for that interval, which allows you to select better weights which brings your output closer to what you want.

an example of a scoring function is below:
{% highlight c++ %}
__host__ __device__ double scoreFunc(double whenMinGuess, double whenMaxGuess, int whenAns, double latGuess, double lonGuess, double latAns, double lonAns){
    if(whenMinGuess <= (whenAns/MaxTrialSize)
            && whenMaxGuess >= (whenAns/MaxTrialSize))
        return exp(-(1*distCalc(latGuess, lonGuess, latAns, lonAns) + 2*fabs(whenMaxGuess-whenMinGuess))/3);
    else
        return 0;
}
{% endhighlight %}

where:
{% highlight c++ %}
whenMinGuess = LB on where the network thinks the earthquake event occurs.
whenMaxGuess = UB on where the network thinks the earthquake event occurs.
whenAns = hour on which the main earthquake event occurs (from the training set).
latGuess = latitude of the seismological site that the network believes will be closest.
lonGuess = longitude of the seismological site that the network believes will be closest.
latAns = latitude of where the epicentre of the main earthquake event.
lonAns = longitude of where the epicentre of the main earthquake event.
{% endhighlight %}

The main problem with neural networks and their associated learning algorithms is that they require a significant amount of math calculations for even the most rudimentary models, and much more so for complex natural systems. GPUs are perfect devices to run the complex math of an machine learning network on, as network algorithms are inherently parallelizable; they do not require information on neighbouring sets of weights to correctly compute their outputs and store state. 

An example of my artificial neural network GPU based algorithm can be seen [here][netKern], it utilizes the CUDA run-time library and follows a command queue pattern utilizing an attached [orders.json][orders] file that tells the program the structure of the artificial neural network.


[torch]: 	http://torch.ch/
[caffe]:	http://caffe.berkeleyvision.org/
[ann]:		https://en.wikipedia.org/wiki/Artificial_neural_network
[orders]: 	https://bitbucket.org/zeryx/earthquake_forecaster/src/725ec7a5100acc0a0db86239d025d00ec0c916b8/orders.json
[netKern]: https://bitbucket.org/zeryx/earthquake_forecaster/src/725ec7a5100acc0a0db86239d025d00ec0c916b8/kernels/networkKernel.cu
