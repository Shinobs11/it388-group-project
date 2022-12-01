## Makefile
### Simply run `make` to compile `omp_grayscale.c` and `mpi_grayscale.c`

> Note: If running on expanse, you may need to load the following modules first: `module load cpu/0.15.4 gcc/10.2.0 openmpi/4.0.4`


&nbsp;  

## OMP Grayscale
### omp_grayscale.c
> `./grayscale <threads> <image_file> <compressedFileName> <greyscaleFileName>`
  
  
### omp_jobscript.sb
> `sbatch omp_jobscript.sb`

&nbsp;  

## MPI Grayscale
### mpi_grayscale.c
> `mpiexec -n <# of procs> ./mpi_grayscale <image_file> <compressedFileName> <greyscaleFileName>`
  
  
### mpi_jobscript.sb
> `sbatch mpi_jobscript.sb`

### exec.sh
Executes `mpi_grayscale.c` on all images located within `./images/base/` directory

>`./exec.sh <# of procs>`


