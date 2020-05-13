# GROMACS
HPCCM recipes for GROMACS build and installation

## Generating Container Specification File

    $ ./gromacs_docker_builds.py -h/--help

    gromacs_docker_builds.py [-h] [--format {docker,singularity}]
                            [--gromacs {2020.1,2020.2}]
                            [--fftw {3.3.7,3.3.8}]
                            [--cmake {3.14.7,3.15.7,3.16.6,3.17.1}]
                            [--gcc {5,6,7,8,9}] [--cuda {9.1,10.0,10.1}]
                            [--double] [--regtest]
                            [--openmpi {3.0.0,4.0.0} | --impi {!!!Not Implemented Yet!!!}]
                            [--ubuntu {16.04,18.04,19.10,20.4} | --centos {5,6,7,8}]
                            [--engines simd=avx_512f|avx2|avx|sse2:rdtscp=on|off [simd=avx_512f|avx2|avx|sse2:rdtscp=on|off ...]]

##### Sample command to Generate Container Specification File for Docker:
    ./gromacs_docker_builds.py --format docker --gromacs 2020.1 --ubuntu 18.04 --gcc 9 --cmake 3.17.1 --engines simd=sse2:rdtscp=off simd=sse2:rdtscp=on  --openmpi 3.0.0 --regtest --fftw 3.3.7> Dockerfile

##### Choosing `SIMD` and `RDTSCP` instruction for `GROMACS` build using the option `--engines` :
###### Value format:
     simd=avx_512f|avx2|avx|sse2:rdtscp=on|off
###### Example (Warning: There should be no space in `--engines` option value)
     simd=avx2:rdtscp=on

It is possible to choose multiple value for `--engines` to have multiple `GROMACS` installation within the same container as follows:

    --engines simd=sse2:rdtscp=on simd=avx2:rdtscp=on

## Generating Docker Image
    docker build -t <image_name> .

The above commands assumes that it is run in the directory where `Dockerfile` is located.

## Running Image
The Available GROMACS wrapper binaries will be the followings based on `mpi` enabled or disabled (enabling double precision not tested yet):

* `gmx`
* `gmx_mpi`

#### With Singularity
Build Singularity image from Docker:

    singularity build <name of image to be built> docker://<docker_image>

Example of running `mdrun` with `gmx_mpi` wrapper:

    mpirun -np <no of processes> singulairty exec -B <host directory to bind> <singularity image> mdrun_mpi -s <.tpr file> -deffnm <ouput_file_name>

#### Without Singularity

Bind the directory that you want Docker to get access to. Below is an example of running `mdrun` module using `gmx` wrapper:

    mkdir $HOME/data
    docker run -v $HOME/data:/data -w /data -it <image_name> gmx mdrun -s <.tpr file> -deffnm <ouput_file_name>


## Dependencies

* `python3`
* `libgomp1`
* `openmpi`
* `fftw`

