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

&nbsp;  

## MPI Grayscale
### mpi_grayscale.c
`mpiexec -n <# of procs> ./mpi_grayscale <image_file> <compressedFileName> <greyscaleFileName>`
  
  
### mpi_jobscript.sb
`sbatch mpi_jobscript.sb`

### exec.sh
Executes `mpi_grayscale.c` on all images located within `./images/base/` directory
> You may need to create the following additional directories: `mkdir ./images/comp` and `mkdir ./images/gray`

`./exec.sh <# of procs>`


