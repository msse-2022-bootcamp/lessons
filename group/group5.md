---
layout: page
title: "Group Assignment Day 3"
---

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

$$ f^*(r) = \frac{48}{r^2} [(\frac{1}{r_{ij}})^{12} - \frac{1}{2}(\frac{1}{r_{ij}})^{6}] \mathbf{r}^*_{ij}$$

$$ 
P^* = \frac{1}{3V^*} \left< 3 N T^* + \sum_{i < j} \textbf{f*}_i \cdot \textbf{r*}_i  \right>
$$



$$
P_{tail} = \frac{16 \pi N^2}{3 V^{*2}} \left[\frac{2}{3} (\frac{1}{r^*})^9 - (\frac{1}{r^*})^3\right] 
$$

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

```
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
```