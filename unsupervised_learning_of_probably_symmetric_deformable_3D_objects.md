# Unsupervised Learning of Probably Symmetric Deformable 3D Objects from Images in the Wild

Paper: https://arxiv.org/pdf/1911.11130.pdf (CVPR 2020 best paper award)

## Goal
Given a single image of an object (the object needs to be vertically symmetrical), the NN produces a 3D model of the object. The salient contributions are a) the model
is self-supervised (which is a theme of many papers in CVPR this year), b) the training data needs only a single view of any object (previously, it usually requires
paired images of the same image from different angles).

## Method
The idea of self-supervision is really neat. On a high level, given an image, the network predicts 4 things: 
* lighting: a 6D vector indicating the position and angle of the lighting (use feedforward ConvNet)
* canonical image: the object in frontal view (which is vertically symmetrical mostly). (use encoder-decoder)
* depth: 3D vector indicating depth of each pixel of the canonical view (use encoder-decoder)
* confidence of symmetry: used in the loss function calculation. predicts which pixel in the object is more likely to be symmetrical 
(e.g. hair bangs / moles are usually not symmetrical, vs. eyes / noses / mouthes are). (use feedforward NN).

Then the network reconstructs the original image by rendering the canonical image with the given lighting and depth model (using some linear algebra transformation).
An important thing here is the rendering needs to be differentiable for back-propagation to work properly.
It is self-supervised because the loss function is between the original image and the reconstructed image. 

The symmetry in the object is a really important constraint. In reconstructing, it reconstructs both from the canonical image and the canonical image flipped vertically. The loss
needs to be low on both reconstructed images. Without this imposed constraint, you can imagine there can be some degenerative case. For example, the model just produces identity mapping
instead of truly learning the canonical view. 

Loss function: the loss function is based on the L1 distance between the corresponding pixels of the original and reconstructed image, scaled by the predicted confidence of symmetry.
The loss then is the negative log-likelihood of a factorized Laplacian distribution (~exp(-x)) of the this residual. In addition, instead of only computing the loss on the final image, 
the author also computes a "perceptual" loss on the mid-layer VGG net output to mitigate the issue of blurry output. 
(I need to think more to understand this loss function. The relation wrt to the confidence is not linear. Somehow it constrians the confidence prediction to be not become pathologically low in confidence?)

## Highlight
* frames the problem as an unsupervised one by turning into a reconstruction problem. It's made possible by incorporated the non-ML based rendering part (which circumvents
the need to design separate loss function for the depth, view and lighting model).
* cleverly imposed the summetry constraint
* cleverly incorporates the confidence prediction into the loss function
