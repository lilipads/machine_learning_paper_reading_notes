# ChauffeurNet: Learning to Drive by Imitating the Best and Synthesizing the Worst

paper: https://arxiv.org/pdf/1812.03079.pdf, by Waymo

4/29 (written before joining Waymo; there is no insider knowledge here)

## goal
use imitation learning to do mid-to-mid-level (instead of end-to-end) *planning* for autonomous driving -- i.e.
what the ego car should do next. The model also handles some prediction tasks and perception tasks to reach this goal.

## key ideas:
0. mid-to-mid level: end-to-end would be to take the input from the raw sensor data (e.g. raw camera input, etc). Here, it takes
processed inputs. The advantage of mid-to-mid level is a) with imposed heuristics, it's more tractable, b) it's easier to generate
more samples from simulations (would be harder to synthesize camera images of a parked car in a slightly different position, but easy to
shift a bounding box). 

1. The hand engineered image-format inputs include: roadmap, traffic lights, speed limits, history of other cars, etc. are all encoded
in the format of images. They are stacked together and passed into the neural net.

2. architecture (fig 2): the above inputs are fed into a feature net, that extracts a few features that will be shared in three
separate downstream models, including a NN to plan the next step for the ego car (agent RNN, which is the meat of the model), 
a NN to predict on-road vs. off-road pixels (road mask net), and
a NN to predict the future state of other cars (perception RNN). (question: how are the other two networks, road mask net and percetion RNN,
used in the planning other than to compute loss?)

3. Even though this is imitation learning, loss function is not just about imitating the expert driver, but also abouts some imposed
human rules. So in this sense it is not pure "imitation learning".

3.1. imitation loss: basically, the (x, y) position, bounding box, orientation, future waypoint prediction, and speed of the ego car should match the ground
truth as much as possible. (Question: why cross entropy loss instead of IoU loss for bounding box and L1 / L2 loss for position? maybe it wants to take advantage of the raw
softmax input over a distribution instead of a discrete output after argmax.)

3.2. enviromental loss: make sure the car doesn't collide with any other cars, is on-road, etc. and the predictions for other cars and pedestrians are accurate.
In ablation tests, these losses turns out to play a pretty big role (M2, M3, M4 in Table 3 all depends on the existence of this environmental loss).
Particularly, it is essential for doing perturbation (see below) because once it's perturbed, the situation is no longer drawn from the data set and you can't purely imitate.

4. perturbation: nudge the car a little bit off its expert-driven track, and ask the model to learn to drive back to its original trajectory. The reason
this is important is the same reason why we need DAGGER in imitation learning: small errors build up. The car doesn't know how 
to handle a situation that's already off which the expert doesn't get itself into in the first place. It's cool that this is the same idea I used in the
Udacity nanodegree imitation learning project even though at the time I didn't learn about this concept yet. In ablation tests, model trained on
datasets augmented by the perturbed scenarios can handle tricky cases better (e.g. behind a slow car, nudging for a parked car, etc). (comment: the paper didn't mention
but I suspect this makes the model learn faster even for the simple situations too?)

5. past drop out: Since the past motion history during training is from an expert demonstration, the net can
learn to “cheat” by just extrapolating from the past rather than finding the underlying
causes of the behavior. For example, such a trained net may learn to
only stop for a stop sign if it sees a deceleration in the past history, and will therefore never
stop for a stop sign when relying on the past which is actually its own past predictions (closed loop planning). To address this, for 50%
of the sample, the past history of ego car is taken away.

6. imitation drop out: drop out a loss function component (imitation loss) half of the time. This works well based on ablation test results.
This is cool and novel to me cause I originally would have thought lowering the weight of this loss componenet instead of dropping out stocastically would be better.

## Result

"With these ingredients,
we got a model good enough to drive a real car. That said, the model is not yet fully
competitive with motion planning approaches but we feel that this is a good step forward
for machine learned driving models."

Seems like a first step in ML-based planning. 

Relevant read:
1. Drago's very accessible lecture video that talks about this paper: https://www.youtube.com/watch?v=Q0nGo2-y0xY
2. https://patrick-llgc.github.io/Learning-Deep-Learning/paper_notes/chauffeurnet.html



