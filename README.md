# Parallel Gaussian Processes for Optimal Sensor Placement
Master's thesis of Tolga H. Dur that was carried out in the summer of 2019. Academic work cited by this thesis can be found in the documentation branch.

## Abstract
Optimal sensor placement is of vital importance to many complex systems. However, despite its importance, existing algorithms that optimize sensor placements have scalability issues and therefore do not work with big data generated by computational simulations. Instead, they use small-scale data collected by existing sensors, which is expensive and imprecise for large domains. This thesis aims to solve these scalability issues by parallelizing computations of a Gaussian process (GP) based sensor placement algorithm. By doing so, it provides unprecedented use of GP's with big data to solve this important problem in spatial statistics. Exemplary, the sensor placements are optimized for part of the test-side around the London South Bank University.

\Instead of running the algorithm for the whole domain, our approach only considers relevant sub-domains of the test-side that were obtained by Fluidity's native domain decomposition. For computations on these, a mixture of multi-processing and multi-threading is used to fully utilize all available resources. The results are validated by comparing the real pollution levels to the ones predicted by a posterior GP, and further measuring performance in data assimilation. According to both measures, we find that our algorithm leads to near perfect results. For instance, for one sub-domain our mean estimation is off by only 6.15e-03, compared to 1.93 for random placements.

## User Guide
For sensor placement outside the MAGIC project, the API of the SensorPlacement class can simply be called with the necessary parameters, such as the prior covariance matrix and the number of sensors to be placed. For sensor placement within the MAGIC project, however, an API was written to further ease this process. Furthermore, with a few alternations in the MagicProject class, this can also be used for sensor placement outside the MAGIC project. The process of using this API is outlined in the following.  

The simulation data has to be converted into CSV-files and placed correctly in the file structure that is described in chapter 5. For VTU-files, the conversion is taken care of by the *data_preparation.py* script, which merely needs to be called with the file path. Optionally, this script also normalizes and standardizes observations. After conversion, the CSV-Files have to be placed into the relevant folder specifying the sub-domain that is of interest. For work outside the LSBU32 test-side, this can be placed into the folder called *subdomain_None*. 

After these preparations, the MagicProject API can be called for description, validation and sensor placements. For sensor placement, for example, the API only needs a few parameters specifying, for example, the sub-domains of interest. If the placement is outside the LSBU32 test-side, this can be substituted with a *None*. Furthermore, there is the option of specifying already placed sensors with an array of indices. An example of how the API can be used can be seen in the following: 

``` python
""" Description """
MagicProject.plotHistogram(subdomain=8, number_bins=5)
MagicProject.describeData(subdomain=8)
MagicProject.plotResiuals(subdomain=8, number_bins=800)

""" Validation """
print(MagicProject.validation_random(subdomain=6, k=1))

indLoc = np.loadtxt('./solutions/subdomain_8/validation_format/num_sens/7sens.txt', dtype=int)
print(MagicProject.validation(subdomain=8, A=indLoc))

""" Calling functions to optimize sensor placement """
t0 = time()
A = MagicProject.parallelPlacement(subdomains=[1, 2], k=4, algorithm=4)
t1 = time()
print('The parallel placement algorithm takes ', (t1-t0), 'seconds')

t0 = time()
A = MagicProject.simplePlacement(subdomain=6, k=4, algorithm=3)
t1 = time()
print('The non-parallel placement algorithm takes ', (t1-t0), 'seconds')
```

## Data
Simulation data that models air pollution around the London South Bank University. Due to the large size, the data is not included in this repository. 

## Dependencies
NumPy, Matplotlib, Multiprocessing, Vtk, Vtktools, GP, Pandas, Heapq, Time
