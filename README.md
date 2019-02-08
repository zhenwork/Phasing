# Phasing

This project aims to provide convenient commandline toolkit for scientists to solve phase 
retrieval problems with alternating projection algorithms such as ER, HIO, HPR, RAAR and GIF-RAAR.


At present, this algorithm support the following algorithms

- Error Reduction (ER)
- Hybrid Input-Output [(HIO)](https://www.osapublishing.org/ol/abstract.cfm?uri=ol-3-1-27)
- Hybrid Projection Reflection [(HPR)](https://www.ncbi.nlm.nih.gov/pubmed/12801170)
- Relaxed Averaged Alternating Reflections [(RAAR)](https://iopscience.iop.org/article/10.1088/0266-5611/21/1/004)
- Relaxed Averaged Alternating Reflections with Generalized Interior Feedback [(GIF-RAAR)](https://www.ncbi.nlm.nih.gov/pubmed/23187243) 
- [Shrink-Wrap](https://link.aps.org/doi/10.1103/PhysRevB.68.140101) 

In this package, sections begin with '[Developer]' means that this section is written for the next developer, though user with some python experience are also encouraged to read that section to better understand how this package works.

#### Reference 
In the previous section, links are connected to papers for the corresponding algorithm. However, 
I have not been able to produce a complete references because I am not familiar with markdown. 
I'll come back and add a complete reference in about 2 months (i.e. 2019.3).

This package grew from the following repo: [https://github.com/cwg45/Image-Reconstruction](https://github.com/cwg45/Image-Reconstruction)
It only implements the HIO algorithm. I begin this project when I was playing with the script in 
this repo. 

I think I have completely rewritten the code, especially in this second version. However, 
I am not sure if there are still some remnant codes coming from that repo. Therefore I'll still 
keep the link here for record. 

## [Developer] Project Structure

This package is divided in to three layers. 

The lowest layer is in CpuUtil.py. At present, it contains all the alternating projections operators. After studying these algorithms, one can realize that many of the existing alternating projections algorithms can be written in a similar format. The only difference is the relation between the coefficients. Therefore, the functions in this module is rather general. It does the general alternating projection operations.

The reason to design it in this way, is that, in practice, I have never applied the operator for more than 10^4 times. Therefore, I think it will not slow down the calculation to put the function in another module.

The second layer is in AlterProj.py. In this function, there is an object controlling the actual calculation process. In practice, I imagine the user will create one such object, then use the object's methods to hold the data, derive support information, tune shrinkwrap behavior and tune the algorithm behavior. 

The reason to design such an object is multiple. Because the majority operations in the function is linear or simple elementwise operations, it's very straightforward to implement them with numpy array and numba functions. However, there will be quite a lot of auxiliary variables to finish all the calculation. To reduce the time consumed by memory allocation, I will create all the auxiliary variables required in this AlterPorj object. Then send them to the operation functions through dictionary to reduce the memory allocation. Also, in the operation functions, I have pay attention to reduce the memory allocation as much as I can.

ToolChain.py is of an even higher level. However, it is not a superclass of objects in the AlerPorj.py file. The objects in the AlterPorj.py file are totally independent and are designed to work alone. The reason that I have created this object is that in practice, I rarely use only one algorithm to solve the phase. Instead, I would usually apply several algorithms insequence to obtain better result. That what this object is intended to do. 

Basically, it has a property called the algorithm_sequence which is a list containing several dictionaries. Each dictionary can be used to initialize an AlterPorj object. The user can use a pre-designed algorithm-sequence to process the image. The user may also modify existing sequences and fine tune the behavior. 

Before I create this object, I thought it would be useful. However, not so much anymore, because I just find it a little bit clumsy. 


## Package Dependence
This repo depends on the following packages:

    1.numpy
    2.numba
    3.cudatoolkit
    4.pyculib
    5.scipy
    6.scikit-image
 
To run the examples, one needs additionally the following
packages:

    1. matplotlib
    2. jupyter notebook
    
## Detailed Record
Honestly, I have not idea how to name this section. There is quite a lot I would like to 
elaborate here.

### What has been done
First, the user can use CPU for simulation with the available algorithms now. However, there are 
still a lot to be done.

### What to be done
- Fix the default behavior of the CpuAlterProj object. I think there is some problem with the 
initialization when one changed the algorithm and some other parameters
- Add a comprehensive test.
- Write a demo
- Furnish the doc

### What can be done for the next developer if there is any
- Add GPU support. Therefore, if the user needs to analysis a large object, then the GPU can 
speed up the calculation a lot. 
- However, I have no plan for multiple GPU reconstruction in parallel. Because it's just a 
little bit too tricky at present.
- Add metrics to measure the convergence progress and the convergence property.

### What's the working flow with this package

#### Simple initialization

1. Create a AlterProj object. This object is the interface for all the later on calculation.
2. Initialize the object with the magnitude and the mask for the magnitude. Notice that the 
magnitude should have been shifted with numpy.fft.ifftshift
3. Set the behavior of the object to be the default behavior
4. Execute the algorithm

#### Complex initialization

1. Create a AlterProj object. This object is the interface for all the later on calculation.
2. Initialize the object with the magnitude and the mask for the magnitude. Notice that the 
magnitude should have been shifted with numpy.fft.ifftshift
3. Initialize the support with whatever you think is proper.
3. Initialize the algorithm 
4. Set the behavior of the shrink-wrap algorithm.



