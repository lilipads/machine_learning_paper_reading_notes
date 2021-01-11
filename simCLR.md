# A Simple Framework for Contrastive Learning of Visual Representations

1/11/2021
paper: https://arxiv.org/pdf/2002.05709.pdf

## TLDR
Instead of supervised learning for image classifcation, this paper uses unsupervised learning -- specifically contrastive learning. 

The core idea is pretty simple:
the data augmentation defines the prediction task itself. Each image goes through a series of transformations (cropping, blurring, color distortion, etc). The learned representations of augmented
images from the same base image should have high predicted similarity. Then one can apply simple linear models on this learned representation
on a small dataset to achieve supervised tasks.

The key that enables the good results shown in this paper is in the details.

## Key Takeaways

* It is the composition of multiple image transformation that helps. No single transformation can achieve this result. In addition, some of these transformations
are not helpful in supervised learning but instrumental in contrastive learning.

* Color distortion is particularly important. Otherwise, the color distributions of different patches of the same image are still very similar and the model can overfit to that.

* The model first uses ResNet (or other architecture) to get the latent vector h. Then it applies non-linear transformation to get representation z. The loss function is 
applied on z. But the result of the model is vector h. The paper claims that h contains more information than z, since z
is learned to be as invariant to the data transformations as possible. This is super not intuitive to me at first. [I suppose by virtue of h being a slightly earlier layer than z,
it contains somewhat richer information, and the choice of h is somewhat random: it could be h, or the layer before h, or the layer before that?]

* They use cosine similarity to measure the similarity, and prove that the normalization (as in not just directly taking the dot product) is important, so as the temperature used in the 
contrastive cross entropy loss function. [Question: This is shown in Table 5, but it doesn't seem to cleanly isolate the effect of normalizaion vs. temperature. It is 
not clear to me that if normalization or temperature, when isolated, helps alone.]

## Other Interesting Details
* The **contrastive cross entropy** loss function is worth taking note, and very intuitive to understand. 

* **Large batch size** plays a more important role in contrastive learning than supervised learning, because the larger the batch size is, the more negative examples there are.

* Training with large batch
size may be unstable when using standard SGD/Momentum
with linear learning rate scaling. To
stabilize the training, they use the **LARS optimizer**.

* "In our contrastive learning, as positive pairs are computed in the same device, the model can exploit the local information leakage to improve pre- diction accuracy without improving representations. 
We address this issue by **aggregating Batch Normalization mean and variance over all devices** during the training." 

* To verify h contains more information about the transformations themselves than z, they did an experiment to use the latent vector to predict the transformation.

* In their own words: "We note that almost all individual components of our frame- work have appeared in previous work, 
although the specific instantiations may be different. The superiority of our frame- work relative to previous work is 
not explained by any single design choice, but by their composition."
