# Tackling SMALL NORB : Data Effiencent Modelling

- The smallNORB dataset is a datset for 3D object recognition from shape. It contains images of 50 toys belonging to 5 generic categories: four-legged animals, human figures, airplanes, trucks, and cars. The objects were imaged by two cameras under 6 lighting conditions, 9 elevations (30 to 70 degrees every 5 degrees), and 18 azimuths (0 to 340 every 20 degrees). The training set is composed of 5 instances of each category (instances 4, 6, 7, 8 and 9), and the test set of the remaining 5 instances (instances 0, 1, 2, 3, and 5).
- Curated and Open Sourced by [Yann LeCun](http://yann.lecun.com/) & [Fu Jie Huang](http://www.cs.nyu.edu/jhuangfu/). [Source](https://link-url-here.org)

 ![alt text for screen readers](https://miro.medium.com/max/2400/1*vsI-D7bo5ou8h2-CQaUdUQ.png) 
## What makes this Dataset challenging?

- Do not use full dataset for experiments, work with 25%, 50%, 75%. Data efficency is paramount. 

- The Test set and Train set are made up of different toys, so your model can win if and only if it learns what is common between toys in train and test set e.g Humans -> have limbs and legs, what they wear doesnt add much to their classfication.

- The images are limited in features, they are binocular images. two images taken with slight devaition theta, simmilar to human vision (stereovision),and the images have small spatial resolution(96 x 96). so model can win only if it is able to leverage from this scarce features

- Positional metadata is a nominal cateroigal variable, it has positional information, but how will model learn from it and correlate with image data and this combination of nominal categorical variables? Lets find out

## Salient Features Applied

- We have 2 channels of grayscale images, to utilize Transfer Learning out of the box we need 3 channels or modify network architectures, can we do better?

- How about handpicking features that might be helpful? The images have no textures in them which is another hurdle, to tackle both of this, we can make a 3rd channel but of what?
- Image Gradients a.k.a Sobel Edges will be an ideal 3rd Channel because it creates an image emphasising edges which will mimic textures here and help us to use Pretrained models without tweaking theri architecture as well.

- Since we are dealing with only a subset of data, we need to take maximum advantage of limited features available so that our moded (has small bias) and at the same time not overfit (have small variance). This is a Tradeoff Game however this reduces our search space to models with lesser parameters and better feature propagation.
so potential candidates would be MobileNets,InceptionNets,ResNets,DenseNets

- InceptionNets dont work with images of resolution 96x96, what's the go around?
Upscale the spatial resolution.

- MobileNets worked out of the box and gave test accuracies in 95% Zone

- ResNets worked similarly and gave good performance across different data regimes(25%,50%,75%)

- DenseNets performed the best and gave accuracies close to SOTA by using (75% of training : 75% of 75% for training and 25% of 75% for validation), it makes sense because DenseNets oncontary to resnets which have additive connections, densenets concatenate features directly from previous layers similar to Feature Pyramid Networks, which works here because we have information scarcity so densenets here alleviate vanishing gradients problems
& strengthen feature propagation.
- ![image](https://miro.medium.com/max/474/1*GeK21UAbk4lEnNHhW_dgQA.png)
- In these experiments, DenseNet121 was experimened thouroughly and it was the most data efficent model but it also involves few more tricks discussed below.
- ![image](https://user-images.githubusercontent.com/47039231/136275232-617987e8-5828-42a1-9069-7c587bafd89d.png)

- Positonal Metadata : Thinking beyond images is important to push the last 1-2 of accuracy%, because the positional metadata has information about lightining, azimuth and camera angle which supplements image data because it indireclty tells about shadow position, its shape and is an important feature in this task.

- To use Positonal Metadata, the final architecture has to be modified, after tha images are convoliuted and reduced to a mutilimensional vector, conctenate the image vector with this positional metadata vector and retrain the network with pretrained weigths loaded in the layers prior to this modification.

![Picture1](https://user-images.githubusercontent.com/47039231/136276209-5062d728-b576-45c8-a4bd-0ca2a77aa2c9.png)

