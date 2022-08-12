---
title: "Creating a Simple Python Package"
teaching: 0
exercises: 45
start: true
questions:
- "How should I organize my code?"
objectives:
- "Put Python code into a module (.py file)"
- "Import functions from our module."
- "Create a simple Python package."
keypoints:
- "When working on a large project, you should organize your code into modules."
---

# Moving Code into a module

For the next section, we will move our code from the Jupyter notebook to a module. 
Our ultimate goal will be to make a small python package which contains our Monte Carlo code. 
We will be able to import that package into the Jupyter notebook to more cleanly run our simulations. 

Create a folder called `mcsim` in the top level of your project directory.
We are going to work on our package in this directory.

~~~
$ mkdir mc-package
~~~
{: .language-bash}

Within that folder, we have to create another directory which will contain our Python source code.
Sometimes you will see this folder called `src` for "source".
However, it is also sometimes given the name of your Python package.
We will name our Python package "mcsim".

~~~
$ cd mc-package
$ mkdir mcsim
$ cd mcsim
~~~
{: .language-bash}

In that folder, open a file called `monte_carlo.py`. 
Gather all of your functions from your Jupyter notebook and paste them into the python module you created (`monte_carlo.py`). 
In a Python module, most import statements are typically at the top of the module, followed by function definitions. 
When you are done, your file might look something like the following:

~~~
"""
Functions for running a Monte Carlo Simulation
"""

import math
import random
import os

def calculate_LJ(r_ij):
    """
    The LJ interaction energy between two particles.
    
    Computes the pairwise Lennard Jones interaction energy based on the separation distance in reduced units.
    
    Parameters
    ----------
    r_ij : float
        The distance between the particles in reduced units.
        
    Returns
    -------
    pairwise_energy : float
        The pairwise Lennard Jones interaction energy in reduced units.

    """
    
    r6_term = math.pow(1/r_ij, 6)
    r12_term = math.pow(r6_term, 2)
    
    pairwise_energy = 4 * (r12_term - r6_term)
    
    return pairwise_energy

def calculate_distance(coord1, coord2, box_length=None):
    """
    Calculate the distance between two 3D coordinates.
    
    Parameters
    ----------
    coord1, coord2: list
        The atomic coordinates
    
    Returns
    -------
    distance: float
        The distance between the two points.
    """
    
    distance = 0
    for i in range(3):
        dim_dist = (coord1[i] - coord2[i]) 
        
        if box_length:
            dim_dist = dim_dist - box_length * round(dim_dist / box_length)
        
        dim_dist = dim_dist**2
        distance += dim_dist
    
    distance = math.sqrt(distance)
    return distance

def calculate_tail_correction(num_particles, box_length, cutoff):
    """
    Calculate the long range tail correction
    """
    
    const1 = (8 * math.pi * num_particles ** 2) / (3 * box_length ** 3)
    const2 = (1/3) * (1 / cutoff)**9 - (1 / cutoff) **3
    
    return const1 * const2

def calculate_total_energy(coordinates, box_length, cutoff):
    """
    Calculate the total Lennard Jones energy of a system of particles.
    
    Parameters
    ----------
    coordinates : list
        Nested list containing particle coordinates.
    
    Returns
    -------
    total_energy : float
        The total pairwise Lennard Jones energy of the system of particles.
    """
    
    total_energy = 0
    
    num_atoms = len(coordinates)
    
    for i in range(num_atoms):
        for j in range(i+1, num_atoms):
            
            #print(f'Comparings atom number {i} with atom number {j}')
            
            dist_ij = calculate_distance(coordinates[i], coordinates[j], box_length=box_length)
            
            if dist_ij < cutoff:
                interaction_energy = calculate_LJ(dist_ij)
                total_energy += interaction_energy
            
    return total_energy
    
def read_xyz(filepath):
    """
    Reads coordinates from an xyz file.
    
    Parameters
    ----------
    filepath : str
       The path to the xyz file to be processed.
       
    Returns
    -------
    atomic_coordinates : list
        A two dimensional list containing atomic coordinates
    """
    
    with open(filepath) as f:
        box_length = float(f.readline().split()[0])
        num_atoms = float(f.readline())
        coordinates = f.readlines()
    
    atomic_coordinates = []
    
    for atom in coordinates:
        split_atoms = atom.split()
        
        float_coords = []
        
        # We split this way to get rid of the atom label.
        for coord in split_atoms[1:]:
            float_coords.append(float(coord))
            
        atomic_coordinates.append(float_coords)
        
    
    return atomic_coordinates, box_length

def accept_or_reject(delta_e, beta):
    """
    Accept or reject based on change in energy and temperature.
    """
    
    if delta_e <= 0:
        accept = True
    else:
        random_number = random.random()
        p_acc = math.exp(-beta*delta_e)
        
        if random_number < p_acc:
            accept = True
        else:
            accept = False
    
    return accept

def calculate_pair_energy(coordinates, i_particle, box_length, cutoff):
    """
    Calculate the interaction energy of a particle with its environment (all other particles in the system)
    
    
    Parameters
    ----------
    coordinates : list
        The coordinates for all the particles in the system.
        
    i_particle : int
        The particle index for which to calculate the energy.
    
    box_length : float
        The length of the simulation box.
    
    cutoff : float
        The simulation cutoff. Beyond this distance, interactions are not calculated
    
    Returns
    -------
    e_total : float
        The pairwise interaction energy of the i-th particle with all other particles in the system.
    
    """
    
    e_total = 0
    
    i_position = coordinates[i_particle]
    
    num_atoms = len(coordinates)
    
    for j_particle in range(num_atoms):
        if i_particle != j_particle:
            j_position = coordinates[j_particle]
            rij = calculate_distance(i_position, j_position, box_length)

            if rij < cutoff:
                e_pair = calculate_LJ(rij)
                e_total += e_pair
    
    return e_total

# Set simulation parameters
reduced_temperature = 1.5
num_steps = 50000
max_displacement = 0.1 
cutoff = 3

# Reporting information
freq = 1000
steps = []
energies = []
all_coordinates = []

# Calculated quantities
beta = 1/reduced_temperature

# Read initial coordinates
file_path = os.path.join('..','lj_sample_configurations', 'lj_sample_config_periodic1.txt')
coordinates, box_length = read_xyz(file_path)
num_particles = len(coordinates)

# Calculated based on simulation inputs
total_energy = calculate_total_energy(coordinates, box_length, cutoff)
total_energy += calculate_tail_correction(num_particles, box_length, cutoff)

for step in range(num_steps):
    
    # 1. Randomly pick one of num_particles particles
    random_particle = random.randrange(num_particles)
    
    # 2. Calculate the interaction energy of the selected particle with the system. Store this value.
    current_energy = calculate_pair_energy(coordinates, random_particle, box_length, cutoff)
    
    # 3. Generate a random x, y, z displacement range (-max_displacement, max_displacement) - uniform distribution
    x_rand = random.uniform(-max_displacement, max_displacement)
    y_rand = random.uniform(-max_displacement, max_displacement)
    z_rand = random.uniform(-max_displacement, max_displacement)
    
    # 4. Modify the coordinate of selected particle by generated displacements.
    coordinates[random_particle][0] += x_rand
    coordinates[random_particle][1] += y_rand
    coordinates[random_particle][2] += z_rand
    
    # 5. Calculate the new interaction energy of moved particle, store this value.
    proposed_energy = calculate_pair_energy(coordinates, random_particle, box_length, cutoff)
    
    # 6. Calculate energy change and decide if we accept the move.
    delta_energy = proposed_energy - current_energy
    
    accept = accept_or_reject(delta_energy, beta)
    
    # 7. If accept, keep movement. If not revert to old position.
    if accept:
        total_energy += delta_energy
    else:
        # Move is not accepted, roll back coordinates
        coordinates[random_particle][0] -= x_rand
        coordinates[random_particle][1] -= y_rand
        coordinates[random_particle][2] -= z_rand
    
    # 8. Print the energy and store the coordinates at certain intervals
    if step % freq == 0:
        print(step, total_energy/num_particles)
        steps.append(step)
        energies.append(total_energy/num_particles)
        all_coordinates.append(coordinates)
~~~
{: .language-python}

Note that we have moved all the imports to the top and also changed the location of our file path. 
If you have moved everything without problems, you should be able to run the simulation by typing the following command from your `mcsim` folder.

~~~
$ python monte_carlo.py
~~~
{: .language-bash}

This seems great! 
But, we want to make our simulation code importable from another file like a Jupyter notebook. 
Let's make our simulation run into a function. 
For this function, we want to consider what are input simulation parameters. 
In other words, if we want to run several simulations, what would we change? We will want to make these our function arguments. 

~~~
def run_simulation(coordinates, box_length, cutoff, reduced_temperature, num_steps, max_displacement, freq=1000):
    """
    Run a Monte Carlo simulation with the specified parameters. 
    """

    # Reporting information
    steps = []
    energies = []
    all_coordinates = []

    # Calculated quantities
    beta = 1/reduced_temperature
    num_particles = len(coordinates)

    # Calculated based on simulation inputs
    total_energy = calculate_total_energy(coordinates, box_length, cutoff)
    total_energy += calculate_tail_correction(num_particles, box_length, cutoff)


    for step in range(num_steps):
        
        # 1. Randomly pick one of num_particles particles
        random_particle = random.randrange(num_particles)
        
        # 2. Calculate the interaction energy of the selected particle with the system. Store this value.
        current_energy = calculate_pair_energy(coordinates, random_particle, box_length, cutoff)
        
        # 3. Generate a random x, y, z displacement range (-max_displacement, max_displacement) - uniform distribution
        x_rand = random.uniform(-max_displacement, max_displacement)
        y_rand = random.uniform(-max_displacement, max_displacement)
        z_rand = random.uniform(-max_displacement, max_displacement)
        
        # 4. Modify the coordinate of selected particle by generated displacements.
        coordinates[random_particle][0] += x_rand
        coordinates[random_particle][1] += y_rand
        coordinates[random_particle][2] += z_rand
        
        # 5. Calculate the new interaction energy of moved particle, store this value.
        proposed_energy = calculate_pair_energy(coordinates, random_particle, box_length, cutoff)
        
        # 6. Calculate energy change and decide if we accept the move.
        delta_energy = proposed_energy - current_energy
        
        accept = accept_or_reject(delta_energy, beta)
        
        # 7. If accept, keep movement. If not revert to old position.
        if accept:
            total_energy += delta_energy
        else:
            # Move is not accepted, roll back coordinates
            coordinates[random_particle][0] -= x_rand
            coordinates[random_particle][1] -= y_rand
            coordinates[random_particle][2] -= z_rand
        
        # 8. Print the energy and store the coordinates at certain intervals
        if step % freq == 0:
            print(step, total_energy/num_particles)
            steps.append(step)
            energies.append(total_energy/num_particles)
            all_coordinates.append(coordinates)
~~~
{: .language-python}

Now we can try this out in a Jupyter notebook. 
The code in our `mcsim` folder now should only be functions, we will write our script in a Jupyter notebook in the top level of our directory.

In a Jupyter notebook, you can now run your code

~~~
import os

import mcsim.monte_carlo

help(mcsim.monte_carlo.run_simulation)
~~~
{: .language-python}

~~~
# Set simulation parameters
reduced_temperature = 1.5
num_steps = 50000
max_displacement = 0.1 
cutoff = 3
freq = 1000

# Read initial coordinates
file_path = os.path.join('lj_sample_configurations', 'lj_sample_config_periodic1.txt')
coordinates, box_length = mcsim.monte_carlo.read_xyz(file_path)
~~~
{: .language-python}

~~~
mcsim.monte_carlo.run_simulation(coordinates, box_length, reduced_temperature, cutoff, num_steps, max_displacement)
~~~
{: .language-python}

## Creating and installing a package

This works as long as we are in a the folder where we can "see" our file. 
This has to do with the location where Python is looking for imported modules. 
When you import something, Python will check your current directory as well as your installed packages.

### Examining your PYTHONPATH

You can see the PATH where Python looks for imports by using some functionality in the `sys` module. 
Open an interactive Python interpreter and execute the following:

~~~
>>> import sys
>>> sys.path
~~~
{: .language-python}

This will return your Python PATH to you. 
You will see your current directory, as well as a PATH containing "site-packages".
This folder is where all of your packages are installed.

Python's built-in packaging system is called [distutils](https://docs.python.org/3/library/distutils.html). However, this tool is largely unused today in favor of [setuptools](https://setuptools.readthedocs.io/en/latest/).
`Setuptools` enhances distutils, and is also bundled with `pip`.
There are [a number of packages related to Python packaging](https://packaging.python.org/key_projects/).

For our exercise, we will do a local, or development install.
If you develop and distribute packages in the future, you will need to follow slightly different procedures.

## Creating a package

To create a package and add it to your PATH, you just need to create a `setup.py` file and run a `pip install`.
Note that the package we show here is **very** minimal.
Usually packages are much more complicated.

Create a `setup.py` file in the top level of your folder and add the following text, modified with your name. 
This is a modified version of the setup.py file in the [Python packaging tutorial](https://packaging.python.org/tutorials/packaging-projects/).

You can find a more in-depth sample [setup.py](https://github.com/pypa/sampleproject/blob/main/setup.py) in the Python Packaging Authority's sample project.

~~~
import setuptools

setuptools.setup(
    name="mcsim-yourname",
    version="0.0.1",
    author="Your Name",
    author_email="author@example.com",
    description="A small example package",
    long_description="A sample Python package which performs MC simulation.",
    long_description_content_type="text/markdown",
    url="https://github.com/USERNAME/REPONAME",
    project_urls={
        "Bug Tracker": "https://github.com/pypa/sampleproject/issues",
    },
    classifiers=[
        "Programming Language :: Python :: 3",
    ],
    package_dir={"": "."},
    packages=setuptools.find_packages(where="."),
    python_requires=">=3.9",
)
~~~
{: .language-python}

This is a file which tells Python setuptools how to install your package.
Note that if you plan to actually ever upload this package to the [Python Package Index (PyPI)](https://pypi.org/),
this name would need to be unique.
Since we want to be able to impor

Next, do a development install of your project:

~~~
$ pip install -e .
~~~
{: .language-bash}

The `-e` means "editable" mode. 
We are using it because we want the changes we make in our package to be reflected.
If you check your site packages folder, you will see your package

You will now be able to import the package from anywhere on your computer. 