## Writeup Template
### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Model Predicitve Control**

The goals / steps of this project are the following:

* Clone/fork the project's template files from the project repository. (Note: Please do not submit your project as a pull request against our repo!)
* Choose your state, input(s), dynamics, constraints and implement MPC.
* Test your solution on basic examples.
* Test your solution on the simulator!
* When the vehicle is able to drive successfully around the track, submit! Remember to include a separate file in .txt, .md, .html, or .pdf format addressing the questions in the rubric.
* Try to see how fast you get the vehicle to SAFELY go!

[//]: # (Image References)
[image1]: ./equations.png


### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  


### Your code should compile.

Done! Difficult due to ipopt!

### The Model

The model consists of the vehicles x and y position (x & y) , the angle of the vehicle (psi), the velocity (v) and the cross track error (cte) and the psi error. The model then calculates the optimal actuator outputs which conists of an steering angle and throttle. The equations which can be found in row 102 to row 107 in the MPC.cpp are the ones which use the current state and current actuations to calculate the actuation of the next timestep.

### Timestep Length and Elapsed Duration (N & dt)

N is the length of the control inputs. Thus N also determines the computational cost of the model.
In my previous trials which can be seen in the table below it can be seen definetely that a higher N and a lower dt performed miserably, which brings us to the dt.
A larger dt leads to less frequenct actautions, which at many points in the simulation lead to a sudden steering of the vehicle and to a fatal crash.

| dt/N	| 30 				| 20 	|10 	|
| :------------: |:---------------:| :-----:|:-----:|
| 0.1    | Very bad steering, very unstable, not an option| Reaction to slow and unstable |Too fast, doesn’t think ahead |
| 0.3    | A lot smoother than 0.1 but reaction to slow when going around curves at higher speeds        |   very stable on the road but still sometimes in unsafe situations|   Very good but silly leaves the road|
| 0.5 | Weird behavior at some point; steering weird   |   Quite ok at first, better than (30,0.5) however weird at the bridge |   Too slow and not too central |

The above table shows my previous erros tried.
In general N was a lot better the close to 10 and also perfomred the best between 0.1 and 0.3. Thus in my final model I choose a NT value of 15 and a dt value of 0.2

#### Polynomial Fitting and MPC Preprocessing

As done in the QA from Eric and Dominic we first transform the waypoints in order to make the vehicle the origin. This is done in row 101 to row 124 in the first part the preprocessing. And in the second part we do the polynomial fitting which then gives us the cte and epsi which is later used to calculate steering and throttle actuations.


#### Model Predictive Control with Latency

The latency for this project was 100 ms. Thus the actuations are performed 100 ms after the calculations have be done. As so for example: 
* t(0ms) - perform caluclations;
* t(0 + 100ms) - perform actuations;
* t(200ms) - perform caluclations;
* t(200 + 100 ms) - perform actuations;

Thus the actuations are still performed every 200 ms as wished.
In order to include the latency when most important - going around tight curves when going fast before - I included a high multiplicator in row 66 (which lead to a drastic increase of performance around tight curves). The car then knows when to break and goes smoothly around the curves instead of noticing to late that it has to steer and thus this abruptly and crashes.


### The vehicle must successfully drive a lap around the track.

Done!


