## Slurm

Slurm is an open-source workload manager/scheduler for the Discovery cluster. Slurm is basically the intermediary between the Login nodes and compute nodes. Hence, the Slurm scheduler is the gateway for the users on the login nodes to submit work/jobs to the compute nodes for processing.


__https://slurm.schedmd.com/quickstart.html__


## Commands


| Command  | Syntax | Description |
| ------------- |:-------------:|:-------------:|
|    sbatch   |sbatch JOBSCRIPT      |Submit a batch script to Slurm for processing.      |
|    squeue  | squeue -u      |Show information about your job(s) in the queue. The command when run without the -u flag, shows a list of your job(s) and all other jobs in the queue.      |
| srun       | srun  --mpi=pmix -n $NBTASKS $EXE    |  Run jobs on the cluster    |
| scancel       |  scancel JOBID    | End or cancel a queued job.     |
| sacct       |  sacct -j JOBID     | Show information about current and previous jobs.`      |
| sinfo      |  sinfo    | Get information about the resources on available nodes that make up the HPC cluster      |


## Job submission example 

Consider you have a script in one of the programming languages such as Python, MatLab, C, Fortran , or Java. How would you execute it using Slurm?

The below section explains a step by step process to creating and submitting a simple job. Also, the SBATCH script is created and used for the execution of a python script or fortran code.

1.  Prepare your code/script

Write your python script or compile your fortran code 

**Example of Hello World in MPI _hello_mpi.f90_**

PROGRAM hello_world_mpi
include 'mpif.h'

integer process_Rank, size_Of_Cluster, ierror, tag
CHARACTER(256) PNAME
INTEGER RESULTLEN

call MPI_INIT(ierror)
call MPI_COMM_SIZE(MPI_COMM_WORLD, size_Of_Cluster, ierror)
call MPI_COMM_RANK(MPI_COMM_WORLD, process_Rank, ierror)
call MPI_GET_PROCESSOR_NAME( PNAME, RESULTLEN, ierror)

print *, 'Hello World from process: ', process_Rank, 'of ', size_Of_Cluster
print *, 'Hello World from process: ', PNAME(1:RESULTLEN)

call MPI_FINALIZE(ierror)
END PROGRAM

`
mpif90 -o hello_mpi  hello_mpi.f90`

1. Create your submission job

A job consists in two parts: resource requests and job steps.

Resource requests consist in a number of CPUs, computing expected duration, amounts of RAM or disk space, etc. Job steps describe tasks that must be done, software which must be run.

The typical way of creating a job is to write a submission script. A submission script is a shell script. If they are prefixed with SBATCH, are understood by Slurm as parameters describing resource requests and other submissions options. You can get the complete list of parameters from the sbatch manpage man sbatch.

in this exemple job.sh contains ressources request (lines starting with #SBATCH) and a sleep unix command.
