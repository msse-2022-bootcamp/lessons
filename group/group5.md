---
layout: page
title: "Group Assignment Day 5"
---

<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.7/MathJax.js?config=TeX-MML-AM_CHTML">
</script>

# Tasks

The tasks "Adding NumPy MC to Package" and "Unit Tests" are required. 
For the third task, pick one of the remaining options "Adding an `__init__.py` file, or one of the tasks 
from `Adding an Analysis Module`.

### Adding NumPy MC to Package

Add the NumPy version of your code to your package. 
What is the best way to reduce the amount of repeated code when adding your NumPy functions?
Add NumPy to your package and update `__init__.py`

### Unit Tests
Make sure each of your tests (excluding `run_simulation` and `read_xyz`) has at least one unit test. 

### Adding an Analysis Module

You should add another file, `analysis.py` which has functions for analyzing your simulation results.
You must pick one to complete the assignment, or you can do both for an extra challenge!

#### Calculation of the pressure

On the [NIST page](https://mmlapps.nist.gov/srs/LJ_PURE/mc.htm) showing benchmarks for the MC of LJ fluids, 
a calculated pressure is also shown. 
Pressure is also a quantity we can calculate from our generated configurations.
For this assignment, you must add the ability to calculate pressure in an analysis module.

The pressure calculation is similar to the energy calcuation.
You will need a tail correction to be calculated based on the number of particles and the box size.
Then the pressure can be calculated according to the equations in reduced units:

$$ \mathbf{f^*(r_{ij})} = \frac{48}{r^2} [(\frac{1}{r_{ij}})^{12} - \frac{1}{2}(\frac{1}{r_{ij}})^{6}] \mathbf{r}^*_{ij}$$

$$ 
P^* = \frac{1}{3V^*} \left< 3 N T^* + \sum_{i < j} \textbf{f*}_i \cdot \textbf{r*}_i  \right>
$$

The angle brackets in this equation represent the time average (this would mean that the pressure of the system is the average of the pressure for many configurations).

The equation for $$ f $$, above, represents the the vector of force between two particles).  To write a function for the pressure, it's helpful to simply the equations above mathematically first. You will notice that $$ f^* $$ is a function of the distance between two particles ( $$ r_{ij} $$ ) multiplied by the vector $$ r $$. If you fill this into the equation for $$ P^* $$, you will have the dot product of some scalar (based on the distance between particles) times the dot product of vector $$ r $$ with itself (ie r dot r). The dot product of $$ r $$ with itself will give you $$ r ^ 2 $$ in your numerator, allowing you to cancel out $$ r ^ 2 $$ in the denominator of $$ f^* $$. 

You will be left with the following equation for the pressure

$$
P^* = \frac{1}{3V^*} \left( 3 N T^* + \sum_{i < j} f^*(r) \right)
$$

where 

$$
f^*(r_{ij}) = 48 [(\frac{1}{r_{ij}})^{12} - \frac{1}{2}(\frac{1}{r_{ij}})^{6}] 
$$

which is a function of the particle particle distance, $$ r_{ij} $$ . This is called the pair virial. Cutoffs and the minimum image convention (periodic boundaries) should be used for this calculation.
You can loop over all of your particles and calculate this and compare to the [reported values from NIST](https://www.nist.gov/mml/csd/chemical-informatics-group/lennard-jones-fluid-reference-calculations-cuboid-cell) for our starting configurations (see $$ W_{pair} $$ in the table)

The tail correction is

$$
P_{tail} = \frac{16 \pi N^2}{3 V^{*2}} \left[\frac{2}{3} (\frac{1}{r_c^*})^9 - (\frac{1}{r_c^*})^3\right] 
$$

where $$r_c$$ is the cut-off distance.

Should this be added to your MC loop, or used as a post-processing analysis? (meaning that coordinates would be saved during similation and you perform calculate these values after)


#### Time Averaged RDF
On Day 2, part of your homework was to use the radial distribution function (RDF) to analyze molecular configurations.
Usually, when a radial distribution function is used for analysis, it is taken as a time-average.
Create a function, `time_averaged_rdf` which takes a list of molecular configurations and returns the time-averaged radial distribution function. To try it out, you should save some of the molecular configurations you generate as a list.

The steps will be:
1. Generate molecular configurations using your simulation package.
2. Analyze the configurations by:
  - Calculating all of the particle pairwise distances for each configuration.
  - Using the rdf function on the calculated distances.
  - Taking the average of calculted RDFs. The RDF is like a normalized histogram. This means you should take the average of each bin to get the time average.

Here is an RDF function using NumPy you can use. This function takes in pairwise particle distances for one configuration.

~~~
def rdf(values, n_bins, max_value, num_particles, box_length):
    """
    Compute the RDF for a set of values
    Parameters
    -----------
    values : np.ndarray
        The distance values to compute the RDF for.
    n_bins : int
        the number of bins to use for the histogram
    max_value : float
        The maximum value for which to compute the RDF.
    num_particles : int
        The number of particles in the system.
    box_length : float
        The box length
    Returns
    -------
    bin_centers : np.ndarray
        An array of the bin centers
    rdf : np.ndarray
        An array of the rdf values.
    """
    histogram, bins = np.histogram(values, bins=n_bins, range=(0, max_value))

    bin_size = bins[1] - bins[0]
    bin_centers = bins + bin_size/2
    bin_centers = bin_centers[:-1]

    rdf = []
    rdf = histogram / (4 * math. pi * bin_centers**2 * bin_size * num_particles ** 2 / box_length ** 3)

    return bin_centers, rdf
~~~
{: .language-python}
