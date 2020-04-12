https://arxiv.org/pdf/1909.08792.pdf

4/12

* published by Waymo
* background: don't want to use fancy models for all cars. this algo ranks the importance of all the agents (cars, pedestrians, bycycles, etc), and prioritizes the ones that should be later looked at by fancy models.
* overall impression: novel in the sense that it repurposes existing algorithms (e.g. CNN, GBDT) and uses them in a different setting.

highlights:
1. data generation: self-supervised learning, does not need human labelling. It's sort of an ablation test using the simulator. 
The goal is to minimize a cost function subject to a few constraints imposed by each neighboring car. If you remove a constraint caused by a particular car, the minimal cost that would satisfy the remaining constriants would go down (smaller the better). The bigger
that delta is, the more relevant (or important) that car is. => discretize this into 3 categories (very relevant, relevant, not relevant).
This data generation depends on the planner implementation. 

2. loss function: pairwise logistical loss. If a is correctly predicted to be more important than b, then the loss is 0. If a should be
more important than b but is predicted otherwise, then it's log(1 + exp(score of b -  score of a)). [question: why +1? why not just score of b - score of a?]
This pairwise loss turns out to be better than pointwise loss as measured by NDCG. Skip saming pairs that are of the same relevance (because relevance is discretized).

3. method:
a) CNN: a stack of 10 "images". The "image" is handcrafted, for example, lanes in the scene, snapshot of locations / velocities of all agents, etc.
The images are stacked together to be fed into the CNN like the RGB channels of a typical image. The CNN is fully convolutional. The output is of the same dimension of an input image. Each output pixel indicates the importance score of
that pixel. Only the pixels that is associated with an agent in the ground truth is relevant. The rest can be discarded. (so in this sense, 
a FCN is a bit computationally wasteful?)

b) GBDT with handcrafted features. The above CNN can be used end-to-end, or can be used as a feature extraction method for the GBDT.
The feature is the neighborhood of the agent in the FCN output. Advantage over end-to-end CNN method: instead of relying on a single pixel,
it takes advantage of the neighborhood information. [question: though in theory, with a few more convolutions, this relation can be learned
by an end-to-end NN architecture too? but maybe GBDT is just for efficient for this final step than more convolutions?]

concepts to read up more later:
* learning to rank
