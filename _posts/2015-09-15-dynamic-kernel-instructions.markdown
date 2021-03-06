---
layout: post
title:  "Dynamic Kernel Instruction Queuing"
date:   2015-09-15 22:19:51
categories: CUDA
---

Creating long and complex can be tricky, especially if the work envelope is changes alot. One way to organize such a structure is with many smaller functions, but that would still require re-compiling every time you want to adjust your kernel’s structure.

If you want to be able to alter the algorithms used within a kernel at run-time, you can use a pattern I call a `dynamic kernel order queue`. This queue is a structure containing `nouns` and `verbs` that enable you to describe a particular process for a kernel to run in sequential order.
{% highlight C++ %}
/*I use enums because string compare on a GPU takes too many cycles,
 where as an integer compare is extremely cheap.*/
enum neuroNouns{.
    nounInput =1,
    nounHidden =2,
    nounMemory = 3,
    nounMemGateIn = 4,
    nounMemGateOut = 5,
    nounMemGateForget = 6,
    nounOutput = 7,
    nounBias =10
};

enum neuroVerbs{
    verbZero =0,
    verbSquash =1,
    verbSum = 2,
    verbMemGate = 3,
    verbMemForget =4
};

struct Noun{
    neuroNouns def;
    int id;
};

struct Verb{
    neuroVerbs def;
};

class Order{ // order contains any number of nouns between 1-4, and a single verb or action
    
public:
    Order(Noun first, Verb verb);
    Order(Noun first, Noun second, Verb verb);
    Order(Noun first, Noun second, Noun third, Verb verb);
    Order(Noun first, Noun second, Noun third, Noun fourth, Verb verb);
    void assign(Noun first, Noun second, Noun third, Noun fourth, Verb verb);
    Noun first();
    Noun second();
    Noun third();
    Noun fourth();
    Verb verb();
    
private:
    Noun _first;
    Noun _second;
    Noun _third;
    Noun _fourth;
    Verb _verb;
};
{% endhighlight %}

A single object of order contains all of the knowledge necessary for an optimized CUDA kernel to execute a variety of intricate commands based on the values associated with a particular order object. An example of reading orders from within a kernel is below, however it could still use some optimization:

{% highlight C++ %}
__global__ void NetKern(kernelArray<double> Vec, kernelArray<int> params, Order* commandQueue, 
	int hour, kernelArray<double> meanCh, kernelArray<double> stdCh, size_t device_offset){

	const int idx = blockIdx.x * blockDim.x + threadIdx.x; // for each thread is one individual
	const int ind = params.array[10];

	const int weightsOffset = params.array[11] + idx + device_offset;
	const int inputOffset = params.array[12] + idx + device_offset;
	const int hiddenOffset = params.array[13] + idx + device_offset;
	const int memOffset = params.array[14] + idx + device_offset;
	const int memGateInOffset = params.array[15] + idx + device_offset;
	const int memGateOutOffset = params.array[16] + idx + device_offset;
	const int memGateForgetOffset = params.array[17] + idx + device_offset;
	const int outputOffset = params.array[18] + idx + device_offset;
	const int fitnessOffset = params.array[19] + idx + device_offset;
	const int communityMagOffset = params.array[20] +idx +device_offset;
	const int whenOffset = params.array[21] + idx + device_offset;
	const int howCertainOffset = params.array[22] + idx + device_offset;
	...



	...
	for(int j=0; j<params.array[23]; j++){

	const double latSite = siteData[j*2];
	const double lonSite = siteData[j*2+1];
	const double GQuakeAvgdist = distCalc(latSite, lonSite, avgLatGQuake, avgLonGQuake);
	const double GQuakeAvgBearing = bearingCalc(latSite, lonSite, avgLatGQuake, avgLonGQuake);
	const double CommunityDist = distCalc(latSite, lonSite, CommunityLat, CommunityLon);
	const double CommunityBearing = bearingCalc(latSite, lonSite, CommunityLat, CommunityLon);
	...



	...
	for(int itr=0; itr< params.array[26]; itr++){
	/*every order is sequential and run after the previous order 
	to massively simplify the workload in this kernel. */


	if(commandQueue[itr].first().def== typeHidden 
		&& commandQueue[itr].verb().def == typeZero){
	    neuroZero(Vec.array[hiddenOffset+commandQueue[itr].first().id*ind]);
	    
	}

	else if(commandQueue[itr].first().def == typeMemGateIn 
		&& sharecommandQueuedQueue[itr].verb().def == typeZero){
	    neuroZero(Vec.array[memGateInOffset+commandQueue[itr].first().id*ind]);
	    
	}

	else if(commandQueue[itr].first().def == typeMemGateOut 
		&& commandQueue[itr].verb().def == typeZero){
	    neuroZero(Vec.array[memGateOutOffset+commandQueue[itr].first().id*ind]);
	    
	}

	else if(commandQueue[itr].first().def == typeMemGateForget 
		&& commandQueue[itr].verb().def == typeZero){
	    neuroZero(Vec.array[memGateForgetOffset+commandQueue[itr].first().id*ind]);
	    
	}

	else if(commandQueue[itr].first().def == typeMemory 
		&& commandQueue[itr].verb().def == typeZero){
	    neuroZero(Vec.array[memOffset+commandQueue[itr].first().id*ind]);
	    
	}

	else if(commandQueue[itr].first().def == typeOutput 
		&& commandQueue[itr].verb().def == typeZero){
	    neuroZero(Vec.array[outputOffset+commandQueue[itr].first().id*ind]);
	    
	}
	...

	...
}

{% endhighlight %}

Instead of having a variety of for loops for each component of the kernel you want repeated, a single for loop can be used. Using this format makes the entire kernel isolated from the algorithm, allowing the complete redesign of the strategies and concepts being used from even outside of the program itself.
