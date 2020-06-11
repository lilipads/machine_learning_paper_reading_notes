# Why Momentum Really Works

Paper: https://distill.pub/2017/momentum/

## Overall impression
This is a rather mathy paper that I had to sit down with pen and paper to work it through. It offers some unique mathematical insights into how gradient
descent works, and why momentum descent makes it better (spoiler: the convergence rate has a square root improvement over gradient descent).

The paper has some small abuse of notation (superscript sometimes means power,
and sometimes means at step k) -- worth watching out if reading. Another potential gotcha: unlike what the name "convergence rate" might suggest,
convergence is faster when convergence rate is low; so don't interpret convergence rate as "the speed of convergence" or "the rate of convergence".


Also, distill.pub is great. 

## TL;DR
0. Introducing eigenspace: Normally, the dimensions in gradient descent is coupled. i.e. you can't optimize one parameter, and then freeze it, and move on to 
optimize the next parameter. However, we can decouple in the eigenspace (Let's say the quadratic approximation of the function is w^TAw + b^Tw. The eigenspace
is formed by the eigenvectors of A.) 

1. *How gradient descent works*: In the eignespace, every gradient step shrinks the error in the i-th eigen-dimension by (1−αλ_i). alpha is the learning rate. lambda is the eigenvalue in that dimension.

2. *The optimal learning rate for gradient descent*: With the above insight, one can prove that gradient descent converges the fastest at a learning rate that makes the dimension with the largest eigenvalue
and the one with the smallest eigenvalue converge at the same rate. (This is just to shed light on how it works; it isn't useful for choosing the learning rate in practice, 
because you wouldn't know the eigenspace of this unknown function if you are doing gradient descent on it.)

3. *Momentum speeds up convergence by a square root factor*: After working out a bunch of math, the paper shows that in momentum descent, the optimal momentum, beta, should be set at (1 - sqrt(αλ_i)).
At this value, the error in each gradient step shrinks by (1 - sqrt(αλ_i))
instead of (1−αλ_i). This can be good or bad, depending on the value of alpha and lambda. Intuitively it makes sense. When tuned inappropriately, 
momentum descent can explode.

4. *Momentum already achieved the optimal speed of convergence*: Adam, RMSProp, Momentum all fall into the family of “Linear First Order Methods". There is a mathematical upper bound of how fast these methods 
can converge. And Momentum already reaches this upper bound. This doesn't mean there is no more room for improvement (clearly, Adam and RMSProp usually
work better). It just means in the most pathological case in a local region, they all have reached the limit.

5. *Complication in stochastic gradient descent*: In stochastic gradient descent, momentum descent has to balance another tradeoff: there is the deterministic component and a zero-meaned stochastic error term.
Lowering the step-size decreases the stochastic error but also slows down convergence. Increasing momentum
causes the errors to compound. Despite these undesirable properties, momentum descent is still bettter than vanilla gradient descent.


