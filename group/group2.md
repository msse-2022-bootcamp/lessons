---
layout: page
title: "Group Assignment Day 2"
---


<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.7/MathJax.js?config=TeX-MML-AM_CHTML">
</script>


Complete the background reading for this assignment. You can find this on the course website.

This homework focuses on computational "tricks" that are used to more effectively model a bulk system. We employ a distance cut-off to greatly reduce the number of calculations, while periodic boundaries allows us to simulate an "infinite" system.

There are three tasks for this homework. Each group member should complete one task and review the task of a teammate.

Each group member should submit a file with their problem solved. Put your homework solutions in the subdirectory "homework" under "day1" on your group repository. Name your file `lastName_firstName_taskX.py` where `X` is your task number. If necessary answer any questions in a markdown file called `lastName_firstName_taskX.md`.

Remember that you must also review a teammate's code. 

> ## Minimizing git conflicts
> The best way to minimize the likelihood of of conflicts appearing is to have each group member work on different files, even if this results in repeated code. 
> There are better strategies to minimize conflict that have to do  with project structure or management. We don't know those strategies yet, so for now, each group member should submit a different file for their task.
{: .callout}

For this homework, you must complete one of three coding tasks.

These tasks are of varying difficulty, but remember that you can work together as a team. 

## Task 1 - Exploring the LJ Potential and Cutoffs
Use your `calculate_LJ` function to calculate the Lennard Jones potential energy for a range of distances, `r`, from `r = 0.1` to `r = 5` in increments of `0.1`. Create a plot of this data (you will need to set the y limit to see anything meaningful. Remember from class that the minimum of this function will occur at `r=1` and will be equal to -1).

Most often when we are running a simulation with pairwise potentials, we will use something called a `cutoff distance`. This is a distance after which the interaction energy between two particles is not calculated and is assumed to be zero. 
This can lessen computational time significantly, since the energy calculation has to be performed for fewer particles.

A common cutoff distance is $$3 \sigma$$.

Create a Jupyter notebook named `lastname_firstname_task1` and write your answer to the questions in a markdown cell. You will have to write code to answer
the question:

1. Do you agree with this choice of cutoff? Why or why not? Justify your answer with numbers.

## Task 2 - Tail Correction
Truncating interactions using a cutoff removes contribution to the potential energy that might be non-negligible. 
 The tail correction for our system makes a correction for use of the cutoff. We only have to calculate this once at the start of our simulation. The formula is:

$$ U_{tail} = \frac{8 \pi N^2}{3 V} \epsilon \sigma^3
	\left[\frac{1}{3} \left(\frac{\sigma}{r_c} \right)^9 
	- \left(\frac{\sigma}{r_c} \right)^3 \right]
$$

In our reduced units:

$$ U_{tail} = \frac{8 \pi N^2}{3 V}
	\left[\frac{1}{3} \left(\frac{1}{r_c} \right)^9 
	- \left(\frac{1}{r_c} \right)^3 \right]
$$

where $$V$$ is the volume of the simulation box, and $$N$$ is the number of particles. 

Create a Jupyter notebook called `lastName_firstName_task2` and do the following using a combination of markdown and code cells.

1. Write a function `calculate_tail_correction` which calculates the tail correction. Your function should have `box_length`, `n_particles` (number of particles) and `cut-off` as function parameters. You can calculate the volume of the box from the `box_length`. Assume a cubic box, so volume is equal to `box_length**3`.
2. Write an `assert` statement to check against the reported value from [NIST](https://www.nist.gov/mml/csd/chemical-informatics-research-group/lennard-jones-fluid-reference-calculations). The value you are looking for is $$U_{LRC}$$. We were using `Configuration 1` in class.
3. Write two other test cases for your function.
4. Write an explanation of your test cases in a mark down cell. 

## Task 3 - Periodic boundaries

1. Modify the calculate distance function to account for periodic boundaries. Your function should take an additional keyword argument, `box_length`, which has a default value of `None`.  If `box_length` is specified, the `box_length` value should be used to calculate the minimum image distance (in other words, the distance considering periodic boundaries). 
When calculating the minimum image distance, follow this procedure:
    - Calculate the distance in each dimension as we did previously (ie $$x_2 - x_1$$ )
    - From your group discussion you should have arrived at the conclusion that the maximum distance in any direction (`x`, `y`, or `z`) was  $$\frac {l_B}{2}$$ (where $$l_B$$ is the box length). Therefore, if our calculated distance in any direction is greater than $$\frac {l_B}{2}$$, we will want to subtract $$l_B$$ from the value. If there is more than one box length between the two points, you will want to subtract the appropriate number. 

2. Make sure to test behavior of your function using a few (at least 2) test cases and `assert` statements. Write explanations of the test cases you chose.
    
## Reviewing your teammate's change
You may need too pull the changes from your teammate's branch when you are reviewing their pull request. You can pull their branch to your local computer by doing

~~~bash
$ git switch main
$ git fetch 
$ git switch -c TEAMMATE_BRANCH --track origin/TEAMMATE_BRANCH
~~~

You should then be able to see your teammates files. You should open their Jupyter notebook and review their code. If any changes are needed, leave this in your review on GitHub.

An app called [ReviewNB](https://www.reviewnb.com/) has been added to your team repositories as well. 
This should facilitate reviewing Jupyter notebooks. 

## Turning this assignment in
In order to complete this assignment, you must:
1. Complete your programming task. Save your programming task along with any answers to questions in a file called `lastName_firstName_taskX.ipynb`. Make sure that your tasks and answers are labeled clearly.
3. Open a pull request containing your files.
4. Review a teammate's submission and leave a review/comment. As you review your team member's pull request, check that they completed the homework assignment, have test cases, and have answered the questions.
5. Edit your personal group project assignment repo to contain links to your pull request the the pull request you have reviewed.

## Grading
This assignment is worth 5% of your total grade. See [the rubric](/group/rubric2) for details on grading.

