
## Connect to Gricad clusters

More details about Griccad infrastructure are available here:

**https://gricad-doc.univ-grenoble-alpes.fr/hpc/**

  ```mermaid
graph TD;
A[f-dahu] --> B(dahu33);
A --> C(........);
A --> D(dahu192);
A --> E(dahu-fat1);
A --> F(............);
A --> G(dahu-fat4);
A --> H(dahu-visu);
```
  
1. Create an ssh key  
```  
ssh-keygen -t rsa
```  
 
Type **Enter** twice WITHOUT ENTERING ANY PASSWORD and you should have two keys in $HOME/.ssh  
  
a private key: id_rsa  
a public key: id_rsa.pub  

2. Create config file


Then create the $HOME/.ssh/config file on your computer , in which you must enter the bastion and server information for the connection.

replace login_gricad with yours 
  
For the Dahu server:  
  
  ```
Host dahu  
ProxyCommand ssh -qX login_gricad@trinity.u-ga.fr nc 129.88.197.54 22  
User login_gricad  
GatewayPorts yes
``` 
  
  
Next, you need to set the correct rights:  
  
**chmod ugo-rwx .ssh/config**  
**chmod u+rw .ssh/config**  
  
keep read/write rights only for the user and the private key: id_rsa  
  
Then, copy the ssh keys  
 ```
ssh-copy-id login_gricad@trinity.u-ga.fr 
  ```
 
Enter the passwod  
then  
 ```
ssh-copy-id dahu
```
  
Enter the password  
  
and you should be good to go for future sessions.  
  
Now, to connect, just type  
 ``` 
ssh dahu
```

Once you are connected to dahu, you will be able to run jobs with the scheduler named OAR 
An introduction to OAR is described here  
[https://gricad-doc.univ-grenoble-alpes.fr/hpc/joblaunch/job_management/](https://gricad-doc.univ-grenoble-alpes.fr/hpc/joblaunch/job_management/)  

Basically, to perform computations, you don't have to do them on the server you're connected to (front-end), but on other servers (compute nodes) accessible from the front-end.
  
Hence the use of a job scheduler (OAR) to launch calculations on the nodes.  
Here's an example of a job header:
Change the name of project to your project  
```
$ cat job_exemple.oar
  
#!/bin/bash  
#OAR -n Test_job  
#OAR -O Test_job.%jobid%.o  
#OAR -E Test_job.%jobid%.e  
#OAR --project sno-elmerice
#OAR -l nodes=1/core=1,walltime=01:30:00  
  
cd YOUR_WORKING_DIRECTORY  
./EXECUTABLE  

```
  
**Description:**  
```
#OAR -l nodes=1/core=1,walltime=01:30:00
```  
Launch of a job, on a single computing core for 1h30 (possibility of increasing core=1 a plus when using MPI)  
The project on which the hours will be used is sno-elmerice

The results will be written to two files  
```
#OAR -O Test_job.NUMERO_JOB.o (standart output)  
#OAR -E Test_job.NUMERO_JOB.o (standart error)
```
If you want to use the fat nodes, just add this to your script:
```
#OAR -t fat
```
If you need to use immediatly the node  without waiting add this  (yo need to asked for a walltime less than 30 minutes
```
#OAR -t devel
```
**Memory**
You have different type of nodes with different features (use recap.py  to see all of them). The memory you are using in a job is equal to:
```
n_cores_asked x ( total_mem/n_cores)
```
If  you want the whole memory, you should asked for all available cores (n_cores)
```
[f-dahu /home/chekkim ]$ recap.py  
========================================================================================  
| node | cpumodel |n_cpus n_cores| scratch1_type | hasgpu |  
| | | total_mem| scratch2_type | |  
========================================================================================  
| dahu33 | Gold 6130 | 2 | 16 | 192 |dedicated_ssd | dedicated_hdd | NO |  
| dahu34 | Gold 6130 | 2 | 32 | 192 |dedicated_ssd | dedicated_hdd | NO |  
| [ + 37 more node(s) ] |  
| dahu72 | Gold 6130 | 2 | 32 | 192 |dedicated_ssd | dedicated_hdd | NO |  
| dahu73 | Gold 6126 | 2 | 24 | 192 |system_ssd | dedicated_hdd | NO |  
| [ + 2 more node(s) ] |  
| dahu76 | Gold 6126 | 2 | 24 | 192 |system_ssd | dedicated_hdd | NO |  
| dahu77 | Gold 6126 | 2 | 24 | 192 |dedicated_ssd | dedicated_hdd | NO |  
| [ + 3 more node(s) ] |  
| dahu81 | Gold 6126 | 2 | 24 | 192 |dedicated_ssd | dedicated_hdd | NO |  
| dahu82 | Gold 6130 | 2 | 32 | 192 |dedicated_ssd | dedicated_hdd | NO |  
| [ + 23 more node(s) ] |  
| dahu106 | Gold 6130 | 2 | 32 | 192 |dedicated_ssd | dedicated_hdd | NO |  
| dahu107 | Gold 6130 | 2 | 32 | 192 |dedicated_ssd | none | NO |  
| dahu108 | Gold 5218 | 2 | 32 | 192 |dedicated_ssd | dedicated_hdd | NO |  
| dahu109 | Gold 5218 | 2 | 32 | 192 |dedicated_ssd | dedicated_hdd | NO |  
| dahu110 | Gold 6244 | 2 | 16 | 192 |dedicated_ssd | none | NO |  
| dahu111 | Gold 6244 | 2 | 16 | 192 |dedicated_ssd | none | NO |  
  
| dahu142 | Gold 5218 | 2 | 32 | 192 |dedicated_hdd | none | NO |  
| dahu143 | Gold 5218 | 2 | 32 | 192 |dedicated_hdd | none | NO |  
| dahu144 | Gold 5318Y| 2 | 48 | 256 |system_ssd | none | NO |  
| [ + 47 more node(s) ] |  
| dahu192 | Gold 5318Y| 2 | 48 | 256 |system_ssd | none | NO |  
| dahu-fat1| Gold 6244 | 2 | 16 |1500 |dedicated_raid0_ssd | dedicated_raid5_hdd| NO |  
| dahu-fat2| Gold 6244 | 2 | 16 |1500 |dedicated_ssd | dedicated_hdd | NO |  
| [ + 1 more node(s) ] |  
| dahu-fat4| Gold 6244 | 2 | 16 |1500 |dedicated_ssd | dedicated_hdd | NO |  
| dahu-visu| Silver 4216| 2 | 32 | 192 |system_hdd | none | NO |  
========================================================================================
```
To run the job in 
```
chmod 750 job_exemple.oar  
oarsub -S ./job_exemple.oar
```
**Use the interactive mode**

If you are developping a code, it is better to have acces to a node for debugging issues. In the passive mode , if you make a small mistake , then you  have to submit your job again and wait in the queue.
Using the interactive mode allows to correct your mistake and run your code until you make sure that everything is working fine.

To do that use **oarsub** with **-I** command:

```
oarsub -I -l nodes=1/core=1,walltime=01:30:00 --project sno-elmerice 
```
for devel mode
```
oarsub -I -l nodes=1/core=1,walltime=00:30:00 --project sno-elmerice -t devel
```
for fat nodes
```
oarsub -I -l nodes=1/core=1,walltime=04:30:00 --project sno-elmerice -t fat
```

**Workdir/Datastorage on dahu** 

 ```mermaid
graph TD;
A[dahu node] --> B(/bettik);
A --> C(/silenus);
A --> D(/mantis);
```

You have access to the $HOME(/home/your_login), which is a limited space under quota (50GB max).  

You must work on /bettik/login_gricad,  /bettik/PROJECTS/PROJECT_NAME/login_gricad or /bettik/PROJECTS/PROJECT_NAME/COMMON if you want to share you data/code between the members of the project 

/bettik is a parallel filesystem so it is very fast when it comes to write/read data by the same program with different tasks

This space is accessible to all nodes. 

Mre info on Gricad Website
  
[https://gricad-doc.univ-grenoble-alpes.fr/hpc/data_management/bettik/](https://gricad-doc.univ-grenoble-alpes.fr/hpc/data_management/bettik/)  
  
  
To copy data from your PC to /bettik, use the rsync command.  
  
Let's imagine we have a folder: Data_Model  
  
We'd like to copy this folder to /bettik/login_gricad to be able to work on it or write data to it. 

  
The copy is made with the command:  
  ```
rsync -rav Data_Model dahu:/bettik/login_gricad/  
  ```
To copy data from dahu to your PC:  
- To copy ( becareful there's the dot . which means copy here)  
 ```
rsync -rav dahu:/bettik/login_gricad/Dossier_bettik .  
```
- If you have a local folder called Local_folder to which you want to copy data from /bettik  
 ```
rsync -rav dahu:/bettik/login_gricad/Dossier_bettik/ Local_folder/
```
**WARNING**: if you want to transfer huge data or a lot of files use mantis-cargo instead 

https://gricad-doc.univ-grenoble-alpes.fr/hpc/data_management/data_transfer/

Replace dahu per mantis-cargo.u-ga.fr

```
rsync -rav Data_Model login_gricad@mantis-cargo.u-ga.fr:/bettik/login_gricad/
```
**Other Datastorage available  on dahu /silenus /mantis** 
Silenus is a parallel filesystem composed of SSD so it is very fast but consider this as a scratch as the files will be automatically deleted when they reach 60 days old.
So If you need to save them, you have  to copy them to /bettik workdir for short term preservation, or to the Mantis2 cloud storage for long term preservation.
```
     WARNING: if your files are only accessed in read mode, and you still use them, you
     should do a `touch <file>` or `touch -r <directory>` to update the change time as
     HPC scratches do not update the access time.
     Otherwise, they may be deleted 60 days after their creation.
```
Mantis is a cloud storage used for long term preservation
https://gricad-doc.univ-grenoble-alpes.fr/hpc/data_management/mantis/


