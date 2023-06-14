## Slurm

Slurm is an open-source workload manager/scheduler for the Discovery cluster. Slurm is basically the intermediary between the Login nodes and compute nodes. Hence, the Slurm scheduler is the gateway for the users on the login nodes to submit work/jobs to the compute nodes for processing.


** https://slurm.schedmd.com/quickstart.html **


## How to use it




## Commands


| Command  | Syntax | Description |
| ------------- |:-------------:|:-------------:|
|    sbatch   |sbatch <jobscript>      |Submit a batch script to Slurm for processing.      |
|    squeue  | squeue -u      |Show information about your job(s) in the queue. The command when run without the -u flag, shows a list of your job(s) and all other jobs in the queue.      |
| srun       | srun <resources>     |  Run jobs interactively on the cluster    |
| scancel       |  scancel <job-id>    | End or cancel a queued job.     |
| sacct       |  sacct -j <job-id>     | Show information about current and previous jobs.`      |
| sinfo      |  sinfo    | Get information about the resources on available nodes that make up the HPC cluster      |
