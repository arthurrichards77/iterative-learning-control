# iterative-learning-control

* Tested in Matlab R2016a on Linux: everything but the drone example
* Tested in R2018a on Windows: everything

## Simulink implementation of iterative learning control (ILC)

![Simulink model ilc.mdl](https://raw.githubusercontent.com/arthurrichards77/iterative-learning-control/master/ilc_mdl.png)

The 'Transport Delay' and 'Transfer Fcn' represent the dynamics of the system we wish to control.  The ILC block attempts to learn a set of inputs to achieve a desired output sequence, by repeating the input sequence multiple times with feedback corrections due to error each time.

The fundamental update equation is u(k+N-1) = u(k-1) + gamma*e(k) where u is the input, k is the time step, N is the length of the repeating sequence, gamma is the learning rate and e(k) = yDes(k mod N) - y(k) is the error between the desired output yDes and the achieved output y.  In short, the previous input in the sequence, next time round, is corrected using the current measured error.  The initial settings are equal to the desired sequence, u(k)=yDes(k) for k=1..N, assuming that the system, perhaps with a simple controller, will already attempt to follow the input.

## Simple example

The file ilc.mdl (shown in image above) implements ILC for an example system with a delay, some dynamics, and a gain other that unity.  The desired sequence is smooth but a bit weird, yDes(k) = sin(2*pi*k/180)^3 for k=1..180 and time step dt=0.5s.  With learning rate gamma=0.12, the output converges to the desired output as shown.

![Results scope from ilc.mdl](https://raw.githubusercontent.com/arthurrichards77/iterative-learning-control/master/basic_ilc.png)

## Ramp example

The file ilc_ramp.mdl changes the desired sequence to be a sequence of ramped steps, which is continuous but non-smooth due to the changes in gradient.  Under ILC, the system makes an effort to follow the sequence, shown by the decreasing error at the step levels.  However, serious ringing is seen at the corners as the simulation progresses.  This needs some work: the signal is possibly too fast for the system to follow, suggesting the need to increase the time step, and perhaps smooth the corners somehow.

![Scope from ilc_ramp.mdl](https://raw.githubusercontent.com/arthurrichards77/iterative-learning-control/master/ramp.png)

## Effect of learning rate

The file ilc_rate_demo.mdl replicates the basic example three times with different learning rates, giving the results shown below, each scope comparing the output with the desired output.  With low gamma, the output doesn't change.  With medium gamma, close inspection shows that the gap does decrease, but very slowly, and the two signals still don't match after 5 cycles of the whole sequence.  With high gamma, the sequence match almost exactly after just one cycle, but by the fifth, we see signs of potential instability appearing.  Hence, the learning rate must be tuned according to the familiar trade-off for any feedback gain: too low and we get sluggish performance, but too high and we get instability.

![Low gamma](https://raw.githubusercontent.com/arthurrichards77/iterative-learning-control/master/rate_slow.png)
Low gamma

![Medium gamma](https://raw.githubusercontent.com/arthurrichards77/iterative-learning-control/master/rate_med.png)
Medium gamma

![High gamma](https://raw.githubusercontent.com/arthurrichards77/iterative-learning-control/master/rate_fast.png)
High gamma
