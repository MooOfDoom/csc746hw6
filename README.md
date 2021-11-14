#  mpi-2dmesh-harness-instructional

This project contains the code harness for doing an MPI-based scatter-process-gather
motif.

What the code provides:  
* Loads input files, parses command line arguments, writes output  
* Performs domain decomposition using one of three different strategies: row-slab, column-slab, or tiled,   sets up tiles on every rank that contain metadata like tile size, extents, etc.  , assigns tiles to ranks  
* Executes a scattering step by iterating over all tiles and invoking a method to send
data from rank 0 to other ranks, and from non-zero ranks to receive data from rank 0.
* Executes a processing step by iterating over all tiles and sets the stage for user code to be executed on a tile  
* Executes a tile gather step, where data from all tiles not owned by rank 0 is sent back to rank 0  
* Writes output to a disk file  

# Build instructions, general

This distribution uses cmake and MPI.

After downloading, cd into the mpi-2dmesh-harness directory, then:  

% mkdir build  
% cd build  
% cmake ../
% make

# Platforms

In principle, this code will build and run on both Cori@NERSC and the VM. It's
fine to use the VM for initial development and testing, but please run your
performance tests on Cori.

# Setting up your NERSC environment

% module swap PrgEnv-intel PrgEnv-gnu  
% module load openmpi  
% module load cmake

# Information about data files

Zebra file dimensions 
* Original: 3556 2573
* 4x Augmented: 7112 5146

* zebra-gray-int8.dat - raw 8-bit grayscale pixel values from the Zebra_July_2008-1.jpg image
* zebra-gray-int8-4x.dat - raw 8-bit grayscale pixel values from the Zebra_July_2008-1.jpg image but 
augmented 2x in each direction

Source file:  Zebra_July_2008-1.jpg, obtained from Wikimedia commons, https://commons.wikimedia.org/wiki/File:Zebra_July_2008-1.jpg

# python display script

imshow.py - a python script to display the raw 8-bit pixel values in grayscale. 

Usage:  
    python imshow.py filename-of-raw-8bit-bytes int-cols-width int-rows-height

To use this script from Cori, first please enable X11-tunneling through ssh before you
log in. E.g., "ssh -Y username@cori.nersc.gov"

On Cori, before using this script, please load the Python module:  
% module load python

Some people on Mac systems have reported problems with the remote image display
not working from Cori. This is likely the result of either (1) forgetting to enable
X tunneling through ssh by forgetting to add the -Y option to ssh, or (2) a
Mac-side Quartz configuration issue. 

If this issue affects you, you might want to consider trying NX to remote desktop
to Cori. See https://docs.nersc.gov/connect/nx/ for more information.

# Running the program on Cori

To allocate N nodes on Cori, use the following command:

% salloc --nodes N --qos interactive --time 00:30:00 --constraint knl --account m3930

Then to run the program with R ranks, use:

% srun -n R ./mpi_2dmesh

In order to set the decomposition strategy, supply the -g command line flag with argument 1 for row-slab, 2 for column-slab, and 3 for tiled decomposition, like so:

% srun -n R ./mpi_2dmesh -g 3
