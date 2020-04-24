Self-Supervised Viewpoint Learning From Image Collections


https://research.nvidia.com/sites/default/files/pubs/2020-03_Self-Supervised-Viewpoint-Learning/SSV-CVPR2020.pdf

paper: from nvidia, CVPR2020

need to re-read this in future to fully understand

Gist: goal is to predict viewpoint of an object (car, human, bus, etc). Uses GAN to achieve self-supervision. Result is better than other 
self-supervised methods but worse than supervised methods.

Highlight: 
1. symmetry loss - horizontally flipped images must have opposite viewpoints (in 2 of the 3 axis). We can take advantage of this
property to encode a loss to train the network.

2. uses GAN to self-learn 

Architecture:
1. analysis network (V): "the judger". This network will predict the viewpoint and style of a real image. The syntehsis network will
 synthesize an artificial image based on the predicted viewpoint and style. This analysis network needs to:
 
 a) minimize the differences between the real image and synthesized image (cosine distance between the convnet features)
 
 b) minimize the differences between the predicted viewpoints / styles and the ground truth input viewpoints and styles of the 
 synthetic image
 
 c) maximally satisfy the symmetry constraint
 
 d) maximize the chance of correctly predicting which image is real vs synthesized
 
 The goal of these losses is to train the network to predict the style and viewpoint better. (question: is d relevant to this goal?)
 

2. synthesis network (S): goal is to synthesize realistic images. Input is a view point feature and a style feature. The view point feature
gets rotatated along the 3 axes in a 3D convnet. Losses:

a) fool the discriminator as much as it can (I think?)

b) pairs of images with same viewpoint input (or style input) should have similar predicted viewpoint (or style)

c) symmetry constraint

b and c are analogous to loss b and c in the analysis network. The difference is that they backprop into different parameters: the loss
here updates the parameters of the synthesis network, and the loss in 1. is to update the parameters of the analysis network. 

Freeze V when training S, and vice versa.

Question: why can the model learn to predict the viewpoint, not just some random latent representation? Is this purely credited to the symmetry
constraint? but there are other latent representations that can satisfy the symmetry constraint?

Inference: since the model doesn't actually know how to parametrize the view point (3 axes) in our way, we build another 
linear regression model to regress the model output to the GT. 



Question: unlike cars where the position uniquely determines the viewpoint, 
for face viewpoint, after fixing the head position, eyes can still move around. How does the network handle this? Or does it only
discrinimate between different head poses, not the actual direction of the gaze?


Further reading: 
InfoGAN [7], StyleGAN [27] and HoloGAN [41]
