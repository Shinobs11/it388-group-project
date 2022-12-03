## Makefile
### Simply run `make` to compile `omp_grayscale.c` and `mpi_grayscale.c`

Note: If running on expanse, you may need to load the following modules first: `module load cpu/0.15.4 gcc/10.2.0 openmpi/4.0.4`


&nbsp;  

## OMP Grayscale
### omp_grayscale.c
`./grayscale <threads> <image_file> <compressedFileName> <greyscaleFileName>`
Loads an Image using the single-file public domain (stb) library. 
A 2x2 block compression algorithm is applied to compute the average of each 2x2 block of pixels and reduce them to a 1x1 block of pixels, effectively halving the image height/width. There is an OMP parallel for directive on this for loop allowing for multiple threads to compute the average of each 2x2 block simultaneously. 

After the compressed image is computed a grayscale algorithm is applied using the luminosity method with the weights of 0.3 for red, 0.59 for green, and 0.11 for blue.
The grayscale algorithm works by reducing the values of the three RGB channels to a single channel that indicates the lightness-brightness of the pixel.
This algorithm was implemented with a for loop that iterates through the compressed image and computes the luminosity for each pixel.
To parallelize this, an OMP parallel for directive was placed on top of the loop which allows for multiple threads to compute the luminosity value for each pixel simultaneously. 
This lowers needing to store data for 3 values of each pixel to only 1 value for each pixel, further reducing the image size. 


### omp_jobscript.sb
`sbatch omp_jobscript.sb`
Runs the omp implementation with 1, 2, 5, 10, & 20 threads to compress the same image.

&nbsp;  

## MPI Grayscale
### mpi_grayscale.c
`mpiexec -n <# of procs> ./mpi_grayscale <image_file> <compressedFileName> <greyscaleFileName>`
Loads an Image using the single-file public domain (stb) library. 
This image is then shared across the processors by using MPI's shared memory function: MPI_Win_allocate_shared() to allocate the shared memory, and MPI_Win_shared_query() to access the shared region.

The workers calculate their work using the formula: work_height_start = (rank >= hmod) ? ((hmod) & (hdiv +1) + (rank -hmod) * hdiv) : rank * (hdiv+1) which will assign the processor the correct row to start at even if there's an uneven distrubution of work.

A 2x2 block compression algorithm is applied to compute the average of each 2x2 block of pixels and reduce them to a 1x1 block of pixels using the work_height_start value, effectively halving the image height/width.

The work_height_start value is then divided by two since the compressed image will have half the height as the original image. This new updated work_height_start value is then used for the workers to calculate the grayscale value using the average method.  

With these processors all working on a shared memory object, the manager core is able to simply write the file stored in this shared region and there is no need to retrieve the work from all the workers. 

### mpi_jobscript.sb
`sbatch mpi_jobscript.sb`
Runs the mpi implementation with 1, 2, 5, 10, & 20 threads to compress the same image.

### exec.sh
Executes `mpi_grayscale.c` on all images located within `./images/base/` directory
> You may need to create the following additional directories: `mkdir ./images/comp` and `mkdir ./images/gray`

`./exec.sh <# of procs>`


