---
layout: page
title: "Group Assignment Day 3"
---


<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.7/MathJax.js?config=TeX-MML-AM_CHTML">
</script>


Complete the background and reading discussion for today. Then, start the programming tasks.

## Task 1 - Exploring the Acceptance Criteria
Write code to calculate the probability of accepting a Monte Carlo move for energies ranging from -2 to 2 for T = 0.9, T = 0.4, and T = 1.4. What is the effect of temperature on the probability of a MC move being accepted? Does this make sense from what you know about thermodynamics? Why or why not? Create a plot showing your results. Note that you aren't going to be able to use your function `accept_or_reject` for this. You will have to take `p_acc` out of it to make the plot.

## Task 2 Alternate Initial Configuration
So far we have only used an initial configuration from a file. Write two functions which can generate initial system configurations from a number of particles and box size (you could choose to have the user specify density instead of box size if you wished). Make sure your functions includesdocstrings! 

For the first function, the initial configuration should be generated from randomly placing particles in a box. For the second function, place the particles on a cubic lattice. The functions should return `coordinates, box_length` the same way our `read_xyz` function did so we can switch out the two without changing our code.
       
## Task 3 Radial Distribution Function
For this task, you will be using and analyzing data from Monte Carlo Simulation code. You will look at the effect of varying the number of frames to be analyzed for your RDF under the same conditions. Create a plot of RDF using 1 frame, 100 frames, and 1000 frames. What is the effect of increasing the number of frames on the RDF?

A function for computing the RDF can be provided to you upon request, but if you would like a challenge, you can write your own. You can see some instructions for writing calculating the RDF [here](http://www.physics.emory.edu/faculty/weeks/idl/gofr2.html). If you prefer to use the provided function, you can find it [here](https://gist.github.com/janash/1c0a80f176a13dc764a15b7ab2612b29). You will have to add calls to this function to your simulation loop and average the results.


## Grading
This assignment is worth 5% of your total grade. See [the rubric](rubric3) for details on grading.