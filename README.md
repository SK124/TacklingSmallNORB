# Tackling SMALL NORB :
- The smallNORB dataset is a datset for 3D object recognition from shape. It contains images of 50 toys belonging to 5 generic categories: four-legged animals, human figures, airplanes, trucks, and cars. The objects were imaged by two cameras under 6 lighting conditions, 9 elevations (30 to 70 degrees every 5 degrees), and 18 azimuths (0 to 340 every 20 degrees). The training set is composed of 5 instances of each category (instances 4, 6, 7, 8 and 9), and the test set of the remaining 5 instances (instances 0, 1, 2, 3, and 5).
##So, what makes this Dataset challenging?

-Do not use full dataset for training, use 25%,50%,75%, data effiecny is paramount. 

-The Test set and Train set are made up of different toys, so your model can win if and only if it learns what is common between toys in train and test set e.g Humans -> have limbs and legs, what they wear doesnt add much to their classfication.

-The images are limited in features, they are binocular images. two images taken with slight devaition theta, simmilar to human vision (stereovision),and the images have small spatial resolution(96 x 96). so model can win only if it is able to leverage from this scarce features

-Positional metadata is a nominal cateroigal variable, it has positional information, but how will model learn from it and correlate with image data and this combination of nominal categorical variables?

##Lets Find out

##Salient Features Applied

-We have only 2 channels of grayscale images, to utilize Transfer Learning out of the box we need 3 channels or need to go arounf changing netwro architectures, can we do better?

How about hndpicking features that might be helful? The images have no textures in them which is another hurdle, to tackle both of this, we can make a 3rd channel but of what?
Image Gradients a.k.a Sobel Edges will act as a 3rd Channel because it creates an image emphasising edges which will mimic texture here and also help us to use Pretrained models without tweaking theri architecture.

-Since we are dealing with only a subset of data, we need to take maximum advantage of limited features available (have small Bias) and at the same time not overfit (have less Variance). This reduces our search space to models with lesser parameters.
so potential candidates would be MobileNets,InceptionNets,ResNets,DenseNets

-InceptionNets dont work with images of resolution 96x96, what's the go around?
Upscale the spatial resolution.

-MobileNets worked out of the box and gave test accuracies in 95% Zone

-ResNets worked similarly and gave good performance across different data regimes(25%,50%,75%)

-DenseNets performed the best and gave accuracies close to SOTA by just using (75% of training : 75% of 75% for training and 25% of 75% for validation),it makes sense because DenseNets oncontary to resnets which have aditive cnnectons, densenets concatenate features directly from previuos layers which works here because we have information scarcity so densenets here alleviate vanishing gradients problems
& strengthen feature propagation, in this experiment DenseNet121 was experimened thouroughly and it was the most data efficent model but it also involves few more tricks discussed below.

-Positonal Metadata : Thinking beyond images is important to push the last 1-2 of accuracy%, because the positional metadata has information about lightining, azimuth and camera angle which supplements image data because it indireclty tells about shadow position and shape and is an important feature in this task.

To use Positonal Metadata, the final arcitecture has to be modified, after tha images are convoliuted and reduced to a mutilimensional vector, conctenate the image vector with this positional metadata vector and retrain the network with pretrained weigths loaded in the layers prior to this modification.

