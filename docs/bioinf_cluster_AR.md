
# Bioinformatics on the cluster

Authors: Adam Reid, Head of Bioinformatics, Gurdon Institute, University of Cambridge; Sina 

Shallaby, Senior Bioinformatician, Gurdon Institute, University of Cambridge

Email: ajr236@cam.ac.uk

Some of this material has been remixed from training materials developed by University of Cambridge Bioinformatics Training Facility [course on High Performance Computing](https://cambiotraining.github.io/hpc-intro/), Licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)

### Aim of the course

- Teach users the basics of how to use the Gurdon Institute compute cluster (skynet)

### Learning outcomes
- You will be able to access the cluster, move files to and from your own computer and the internet
- You will be able to submit jobs using the Slurm submission system
- You will be able to access Rstudio server
- You will be able to use nextflow on the cluster
- You will be able to connect VScode to the cluster to run Jupyter Notebooks or other code
- You will be aware of suitable places for storing files

### Pre-requisites
- Some Linux command-line knowledge
- A cluster account (contact [Charles](mailto:crb55@cam.ac.uk))

## Contents

- [Part 1 Introduction to the cluster](#part-1-introduction-to-the-cluster)
  - [What is a cluster and what are its uses?](#what-is-a-cluster-and-what-are-its-uses?)
  - [The Gurdon compute cluster](#the-gurdon-compute-cluster)
  - [Getting on the cluster](#getting-on-the-cluster)
  - [Moving files](#moving-files)
  - [Practical 1](#practical-1)
- [Part 2 Submitting jobs](#part-2-submitting-jobs)
  - [Slurm job scheduler](#slurm_job_scheduler)
  - [Practical 2](#practical-2)
- [Part 3 Rstudio, Jupyter lab and Nextflow](#part-3-rstudio-jupyter-lab-and-nextflow)
  - [RStudio server](#rstudio-server)
  - [Jupyter lab](#jupyter-lab)
  - [Running Nextflow pipelines](#nextflow)
- [Part 4 Useful tools](#part-4-useful-tools)
  - [Installing software](#installing-software)
  - [Filezilla](#filezilla)
  - [VScode](#vscode)
  - [AI agents](#ai-agents)
- [Appendix](#appendix)

## Part 1 Introduction to the cluster
<a id="#part-1-introduction-to-the-cluster"></a>

### What is a cluster and what are its uses?
<a id="#what-is-a-cluster-and-what-are-its-uses?"></a>

The terms __cluster__, __compute cluster__, __HPC (high performance computing)__ and __farm__ are often used interchangeably to mean the same thing - several computers connected together in a network. Each computer is referred to as a **node** in the network. 

The main usage of HPC clusters is to run resource-intensive and/or parallel tasks.

For example: running thousands of simulations, each one taking several hours; assembling a genome from sequencing data, which requires computations on large volumes of data in memory; or mapping hundreds of RNA-seq samples to a reference.

These tasks would be extremely challenging to complete on a regular computer. However, they are just the kind of task that a HPC cluster would excel at. 

When working on a cluster it is important to understand what kinds of _resources_ are available to us. 

These are the main resources we need to consider:

- **CPU** (central processing units) is the "brain" of the computer, performing a wide range of operations and calculations. 
CPUs can have several "cores", which means they can run tasks in parallel, increasing the throughput of calculations per second. 

A typical personal computer may have a CPU with 4-8 cores. 

A single compute node on an HPC may have 24-128 cores.

- **RAM** (random access memory) is a quick access storage where data is temporarily held while being processed by the CPU. 
A typical personal computer may have 8-32GB of RAM. 

A single compute node on an HPC may often have 256GB-1TB RAM.

- **GPUs** (graphical processing units) are similar to CPUs, but are more specialised in the type of operations they can do. While less flexible than CPUs, each GPU can do thousands of calculations in parallel. 
This makes them extremely well suited for graphical tasks, but also more generally for matrix computations and so are often used in machine learning applications. 


Usually, HPC clusters are available to members of large institutions (such as a Universities or research institutes) or sometimes from cloud providers. 
This means that:

- There are many users, who may simultaneously be using the cluster. 
- Each user may want to run several jobs concurrently. 
- Often large volumes of data are being processed and there is a need for high-performance storage (allowing fast read-writting of files).

So, at any one time, across all the users, there might be many thousands of processes running on the cluster!
There has to be a way to manage all this workload, and this is why HPC clusters are typically organised somewhat differently from what we might be used to when we work on our own computers. 

Here is a schematic of a cluster, we go into its details in the following sections. 

![Organisation of a typical HPC.](../images/hpc_overview.svg)

### Nodes

There are two types of nodes on a cluster: 

- _login_ nodes (also known as _head_ or _submit_ nodes).
- _compute_ nodes (also known as _worker_ nodes).

The **login nodes** are the computers that the user connects to and from where they interact with the cluster. 
Depending on the size of the cluster, there is often only one login node, but larger clusters may have several of them. 
Login nodes are used to interact with the filesystem (move around the directories), download and move files, edit and/or view text files and doing other small routine tasks. 

The **compute nodes** are the machines that will actually do the hard work of running jobs.
These are often high-spec computers with many CPUs and high RAM (or powerful GPU cards), suitable for computationally demanding tasks.
Often, there are several "flavours" of compute nodes on the same cluster. 
For example some compute nodes may have fewer CPUs but higher memory (suitable for memory-intensive tasks), while others may have the opposite (suitable for highly-parallelisable tasks). 

Users do not have direct access to the _compute nodes_ and instead submitting jobs via a _job scheduler_.

### The Gurdon compute cluster
<a id="#the-gurdon-compute-cluster"></a>


<img src="../images/gurdon_cluster_outline.png" alt="Gurdon cluster architecture" width="800"/>

#### Filesystem

The filesystem on a HPC cluster often consists of storage partitions that are shared across all the nodes, including both the login and compute nodes. This means that data can be accessed from all the computers that compose the HPC cluster.

Although the filesystem organisation may differ depending on the institution, typical HPC servers often have two types of storage:

The user's home directory (e.g. `/mnt/home3/user`) is the default directory that one lands on when logging in to the HPC. This is often quite small and possibly backed up. The home directory can be used for storing things like configuration files or locally installed software.

You have a storage quota in your home directory that you can check by running the `quota` command, which will tell you the total storage available to you and how much has been used already.

A scratch space (e.g. `/mnt/scratch/user`), which is high-performance, large-scale storage. This type of storage may be private to the user or shared with a group. It is usually not backed up, so the user needs to ensure that important data are stored elsewhere. This is the main partition were data is processed from. You would usually move data there for processing and then make sure both raw data and analysis results get stored in a location with backup.

At the Gurdon Institute we have:
   
- __home2__ (`/mnt/home2/group/user`), __home3__ (`/mnt/home3/group/user`), __home5__ (External users: `/mnt/home5/group/user`)

  Features: limited total space (1 TB), backed up locally, but important data should also be backed up elsewhere e.g. RFS/RDS

  Useful for: Installing software, storage of data and results
  
- __scratch__ (`/mnt/scratch/group/user`)

  Features: no quotas (~1 Petabyte total), not backed up

  Useful for: Running compute jobs, generating lots of intermediate files

- **Our resources**

The 2204 partition (queue) has access to nodes 5-10 each with 384GB RAM and 64 cores (128 threads) and node 11 (1TB) with 128 cores (256 threads).
This is the set default partition to use.

The 1804 partition has access to nodes 17-24 each with 24 cores (48 threads) and 256GB RAM (all Intel Haswell CPUs).

We have several GPUs available on the cluster. For access please contact Charles.

#### How do I move data between RFS and the Gurdon cluster or between my computer and the cluster?

Although sequencing data, stored on RFS, is made available on the cluster, you cannot mount your RFS project folders on the cluster.

Instead you can mount your RFS folder on e.g. your laptop and then use [FileZilla](#filezilla) to copy across to the Gurdon cluster. We will talk more about FileZilla later in this course.

#### Where is my sequencing data?

Sequencing data is now stored in the Sequencing folder of your group's RFS project and mirrored on the cluster in: ```/mnt/beegfs/Sequencing```

For samples sequenced at CI, data is automatically downloaded to:

```/mnt/beegfs/Sequencing/(GL Folder)/CI FASTQ/```

For samples sequenced at the Gurdon Institute, FASTQ files are manually copied to:

```/mnt/beegfs/Sequencing/(GL Folder)/NovaSeq FASTQ/```

#### Bioinformatics share

Team workspaces are not visible by members of other teams and so to allow people to share data between the Bioinformatics group and other groups, we have set up a shared space with unrestricted access. This means that anything can be deleted by anyone so please be careful.

This can be accessed on the cluster here: `/mnt/bioinfo_sharing/sharing/`

or from your Mac by using *Finder*. Under the *Go* menu, select *Connect to server*, type `smb://cb-share1.gurdon.private.cam.ac.uk` in the box and choose to connect as Guest.

### Getting on the cluster
<a id="#getting-on-the-cluster"></a>

If you are using a Mac:

Open __terminal__ from *Spotlight Search* or this icon:

<img src="../images/terminal_icon.png" alt="Mac terminal" width="80"/>

If you are on a PC:

Open __Command Prompt__ with this icon:

<img src="../images/cmd_prompt_icon.png" alt="PC terminal" width="80"/>

Then type:

```
ssh <user>@cb-milan2.gurdon.private.cam.ac.uk
```
  
e.g.
```
ssh ajr236@cb-milan2.gurdon.private.cam.ac.uk
```

### Moving files
<a id="#moving-files"></a>

As previously mentioned, you can use [FileZilla](#filezilla) to move files between your computer and the cluster. Additionally there are command line options which also allow you to retrieve files from other machines or servers.

#### scp - move files between your computer and the cluster

Copy a file from cluster to my computer (from my computer)

```
scp ajr236@cb-milan2.gurdon.private.cam.ac.uk:/mnt/home3/reid/ajr236/graph.png .
```

Copy a file from my computer to the cluster (from my computer)

```
scp graph.png ajr236@cb-milan2.gurdon.private.cam.ac.uk:/mnt/home3/reid/ajr236/
```

#### wget – get files from the internet

```
wget https://s3ftp.flybase.org/genomes/Drosophila_melanogaster/dmel_r6.43_FB2021_06/gtf/dmel-all-r6.43.gtf.gz
```

N.b. To run visualisations you will need to copy your files to your local computer

### Practical 1
<a id="#practical-1"></a>

1. ssh to the cluster

2. Check your storage quota

3. Make a new directory in your homespace called `tutorial` and move into that directory

4. Get the `automated_gene_summaries.tsv.gz` file from [flybase](http://s3ftp.flybase.org/releases/FB2021_06/precomputed_files/genes/) using wget 

5. Copy the file from the cluster to your local machine using `scp`

  - Open a new terminal

  - scp `automated_gene_summaries.tsv.gz` from <username>@cb-milan2.gurdon.private.cam.ac.uk:<path>

6. Unzip the file on the command line using `gunzip`

7. Check the file contents

8. Get just the first ten lines and make a new file called `head.txt`

9. Copy `head.txt` to the cluster with `scp`


## Part 2 Submitting jobs
<a id="#submitting-jobs"></a>
  
HPC servers usually have job scheduling software that manages all the jobs that the users submit to be run on the compute nodes. This allows efficient usage of the compute resources (CPUs and RAM), and the user does not have to worry about affecting other people's jobs.

We discussed __nodes__ and __CPUs__ above. When submitting your jobs to a cluster there are several other useful terms to know. Not least:

- **Job** : a unit of work, with resource requests and steps to be run
- **Thread** : single process running on a core
- **Queue/Partition** : a group of nodes with particular characteristics e.g. resources, software 
  
The job scheduler uses an algorithm to prioritise the jobs, weighing aspects such as:

- how much time did you request to run your job?
- how many resources (CPUs and RAM) do you need?
- how many other jobs have you got running at the moment?

  Based on these, the algorithm will rank each of the jobs in the queue to decide on a "fair" way to prioritise them. Note that this priority dynamically changes all the time, as jobs are submitted or cancelled by the users, and depending on how long they have been in the queue. For example, a job requesting many resources may start with a low priority, but the longer it waits in the queue, the more its priority increases.

This can be thought of like managing tables at a restaurant
  
![restaurant_queue_manager](../images/restaurant_queue_manager.svg)
  
Why not just run jobs on the head node?
- It doesn't allow fair distribution of resources
- You know that your job will get the resources you need
- Helps with record-keeping and troubleshooting
- Job gets run in the background (go for a coffee!)
- More power – faster runs
- Parallelisation!!!

When should I use the head nodes?
- Editing text files
- Basic UNIX commands – mv, cp, rm etc.
- Managing data
- Short, low-memory processes
- Testing – with simple example data for fast turn around
- For submitting jobs
- Monitoring jobs
- Post processing

### Slurm job scheduler
<a id="#slurm-job-scheduler"></a>

On a cluster, the computational resources have to be managed efficently to allow all users equal and plannable access to compute time and resources.
To make this possible, a cluster uses a job scheduler to assign resources and run tasks efficiently and fairly without conflicts.

On the Gurdon cluster (and on the [University of Cambridge HPC cluster](https://docs.hpc.cam.ac.uk/hpc/)) the job scheduler is __Slurm__

There are several Slurm programs which allow submission and management of jobs
  
#### Examining the available queues and node status

`sinfo` will tell you about the partitions and what resources are available
  
<img src="../images/sinfo_screenshot.png" alt="sinfo screenshot" width="600"/>
  
down = not in use
  
drng/drain = removed by administrator

mix = some CPUs allocated some idle

idle = available 

alloc = node in use
  
#### Examining pending and running jobs

```
squeue
squeue -u <username>
```

To get detailed parameters about a job:
  
`sacct --format jobname,account,state,AllocCPUs,reqmem,maxrss,averss,elapsed -j <jobid>`

To get parameters about a finished job, such as how long it took, how much memory it used and whether it failed or not
  
`seff <jobid>`

This will give you output such as (as well as some warnings from Perl which you can ignore):

```
Job ID: 822393
Cluster: skynet
User/Group: /reid
State: FAILED (exit code 2)
Nodes: 1
Cores per node: 2
CPU Utilized: 00:00:00
CPU Efficiency: 0.00% of 00:00:00 core-walltime
Job Wall-clock time: 00:00:00
Memory Utilized: 1.14 MB
Memory Efficiency: 0.01% of 7.81 GB
```

*Memory Utilized* is particularly useful for determining how much memory to ask for in the future when running similar jobs.
  
To kill a specific job:

```
scancel <jobid>
```

To kill all your running jobs:

```
scancel -u <username>
```
  
#### Submitting a job
  
- srun

   Interactive, typically for MPI jobs– “proper” parallel computing

- salloc
  
  Interactive shell with job allocation – bit like logging into a virtual machine

- sbatch

  “embarrassingly” parallel computing – i.e. most bioinformatics

  Submit jobs for non-interactive execution

  Requires writing a bash script

- slurm_sub.py
  
  Custom script which makes running sbatch easy and has added reporting

### Batch scripts (sbatch)

- Allow multiple steps to be run in a single job
- Ideal for small bioinformatics workflows
- Submitted in the background and allows for good reporting
- Set up scheduling parameter templates rather than typing them at execution

```
#!/bin/bash
#SBATCH --job-name=parallel_job # Job name 
#SBATCH --mail-type=END,FAIL # Mail events (NONE, BEGIN, END, FAIL, ALL) 
#SBATCH --mail-user=email@ufl.edu # Where to send mail 
#SBATCH --nodes=1 # Run all processes on a single node 
#SBATCH --ntasks=1 # Run a single task 
#SBATCH --cpus-per-task=4 # Number of CPU cores per task 
#SBATCH --mem=1gb # Job memory request 
#SBATCH --time=00:05:00 # Time limit hrs:min:sec
#SBATCH --output=parallel_%j.log # Standard output and error log 
pwd; hostname; date

echo "Running sleep program on $SLURM_CPUS_ON_NODE CPU cores" 

sleep 10 

date
```
  
### slurm_sub.py

This is a custom script which makes submitting sbatch jobs easier
  
It is located at `/mnt/shared/slurm/slurm_sub.py`. Simple things can be run very simply e.g. `slurm_sub.py sleep 10`

- STDOUT goes to job.o
- STERR goes to job.e
- The batch script is job.sbatch.sh
- -j to change job name from ‘job’
- -p, -n, -N, -m parameters just like sbatch

### Job submission parameters

| sbatch parameter | Flag | Default value |
|-----------|------|---------------|
| Job name  | `-j` | job |
| Partition/queue | `-p` | 2204 |
| Number of tasks | `-n` | 1 |
| Number of nodes | `-N` | 1 |
| Standard output | `-o`  | `slurm-<jobid>.out` |
| Standard error  | `-e` |  `slurm-<jobid>.out` |
| Jobe events mailing  |` --mt [NONE, BEGIN, END, FAIL, ALL]` | END |
| Email address  |` --mu` | NONE |
| RAM | `-m` | 4000 |
  
  
### Choosing your parameters
  
If you don't specify resource requirements you will get the following resources by default:

- 7 days of running time (equivalent to -t 7-00:00:00)
- 2204 partition, with Ubuntu 22.04 operating system
- 1 CPU (equivalent to -c 1)
- 4GB RAM (equivalent to --mem=4000)
  
Choosing your parameters can be tricky, because you probably don't know how much you need. It is always sensible to do a test run to see how much RAM, time and how many CPUs are required. You can run `seff` once it has finished to see what was used and then extrapolate to your full dataset.
 
### Partitions

 Often, HPC servers have different types of compute node setups (e.g. queues for fast jobs, or long jobs, or high-memory jobs, etc.). SLURM calls these "partitions" and you can use the `-p` option to choose which partition your job runs on. Usually, which partitions are available on your HPC should be provided by the admins.

It's worth keeping in mind that partitions have separate queues, and you should always try to choose the partition that is most suited to your job.

The current default partitions is __2204__ which runs Ubuntu 22.04. This is standard for job submitions if you don't specifically choose a partition and should be used for all current work as it uses the most recent hardware and software and has most resources available.

We have a legacy partitions: __1804__ which runs Ubuntu 18.04.  

There is also a partition specifically for running Nextflow jobs called __NXFL__. We will talk a bit more about setting up and using Nextflow on the cluster later on.

### Job submission examples

If we run the program *hostname* on the head node we get this result:

<img src="../images/hostname.png" alt="hostname screenshot" width="600"/>
  
If we submit *hostname* to the cluster with srun we get:

<img src="../images/hostname_srun.png" alt="hostname srun screenshot" width="600"/>

If we submit *sleep 100* to the cluster as a batch job using *slurm_sub.py* we get:

<img src="../images/slurm_sub_sleep.png" alt="sleep screenshot" width="600"/>
  
  
### Practical 2
<a id="#practical-2"></a>
  
1. Use srun to submit the command `hostname` – which node did the job run on?

2. Adjust the parameters of srun to run the same command multiple times (-n) – did they run on the same or different nodes?

3. Use slurm_sub.py to submit the command `sleep 100`. Use `squeue –u <user>` to find out which node it is running on. What were the output status and error messages from the job? What time did the job start and when did it finish?

4. Run the `sleep 100` command again, but kill it before it finishes. What was the output/error status of the job this time?

5. Copy files: `sample1_1.fastq`, `sample1_2.fastq` and `dm6_chrM.fa` from `/mnt/bioinfo_sharing/sharing/course_material/cluster/` to your tutorial directory. Map the data to reference fasta `dm6_chrM.fa` with the following commands, running each job using slurm_sub.py.  __Do not copy and paste the commands – they may fail due to hidden characters.__

```
slurm_sub.py –j index1 bwa index dm6_chrM.fa
slurm_sub.py –j mem1 bwa mem –o sample1.sam dm6_chrM.fa sample1_1.fastq sample1_2.fastq
slurm_sub.py –j view1 samtools view –b –o sample1.bam sample1.sam 
Slurm_sub.py –j sort1 samtools sort –o sample1_sort.bam sample1.bam
slurm_sub.py –j index1 samtools index sample1_sort.bam 
```
  
6. Try viewing the mapped reads in [IGV](https://software.broadinstitute.org/software/igv/download) on your laptop or using `samtools view sample1_sort.bam | less` on the cluster. Always check your output!

BONUS EXERCISE: Write a shell script which captures the steps and runs the whole thing. Then run on several sets of fastq files.
  
## Part 3 RStudio, Jupyter lab and Nextflow
<a id="#part-3-rstudio-jupyter-lab-and-nextflow"></a> 

### RStudio server
<a id="#rstudio-server"></a>

RStudio is a development environment specifically for R. It allows you to write and run R code interactively instead of having to rely on submitting jobs on the cluster. RStudio would usually run on your computer and thus has limited memory available. We offer a browser-based version called RStudio where you can run larger projects directly on the Gurdon cluster, meaning your file storage will be on the cluster and there is more memory available to you than on your laptops.

Different head nodes have different versions of R installed and these can each be accessed on your laptop from different URLs. Older R versions will be maintained by virtualisation.

R v4.4.2 on cb-milan2   http://cb-milan2.gurdon.private.cam.ac.uk:8787/

R v4.2.0 on cb-milan1   http://cb-milan1.gurdon.private.cam.ac.uk:8787/
  
When you install an R package on cb-milan2 it will not be available from R on other nodes, each is kept separate. Your own R package installs are kept in e.g. `~/R/x86_64-pc-linux-gnu-library/4.4/` with a different folder for each R version.
  
If you install a more up-to-date version than is available centrally (in `/usr/local/lib/R/site-library`), then both will be available in the “packages” menu.

Default loading e.g. “library(Seurat)” will use your local version.

### Jupyter lab
<a id="#jupyter-lab"></a>

Jupyter Lab is a developmental environment for Project Jupyter - an environment that allows you to create interactive notebooks for Python, Julia and R (amongst other additional languages). These notebooks provide reproducible pipelines for research that can be run on a computer or cluster. For larger projects it is particularly helpful to have all the resources of the cluster.

We have an easier way to run Jupyter Notebooks on the cluster through using [Visual Studio Code](#vscode), so this information is mostly here for reference.

You may want to run Jupyter lab (or notebook) on the cluster so you can, for instance, analyse scRNA-seq data using python and scanpy. You can do this through software like [Visual Studio Code](#vscode), or directly by setting up a jupyterlab instance, as here:

Login to the cluster:

`ssh <user>@cb-milan2.gurdon.private.cam.ac.uk`

Start a screen:

`screen -S jupyterlab`

Allocate resources on slurm:

`salloc -p 2204 -c 4 --mem=64G`

Submit an interactive job to slurm

`srun --pty bash`

Activate a conda/mamba environment if needed, e.g.

`conda activate scanpy`

Note which node the job is running on. Then start jupyter lab/notebook on a specific port, e.g. port 8181. N.b. when running Jupyter notebook you may need to do this first `export XDG_RUNTIME_DIR=""`

`jupyter lab --no-browser --ip 0.0.0.0 --port 8181`

Copy the link that shows up in your screen - something like:

`http://127.0.0.1:8181/lab?token=53da29195682403ce73074170f3a260b80c36004571a1b89`

Start a new terminal on your laptop / desktop and ssh again with port forwarding this time, substituting <node> for the actual node your job is running on. Port 8181 on the local machine will be connected to 8181 on the cluster node (which we specified above) by a tunnel.

`ssh <user>@cb-milan2.gurdon.private.cam.ac.uk -L 8181:<node>:8181`

Then paste the Jupyter lab URL you copied into your browser - tada!

Kill screen - when all is done the screen will hang around unless you kill it (n.b. this kills all your screens)

`pkill screen`

Alternatively you can check which screen is the newest running one by listing all your running screens
`screen -ls`

Identify the PID of the Jupyter lab screen and kill it with

`screen -XS <PID> quit`

### Nextflow
<a id="#nextflow"></a>

Nextflow is a framework to write and run reproducible computational pipelines. It comes with it's own developmental language to write new pipelines and also with an extensive database of (bioinformatics) pipelines already available through [Nextflow](https://www.nextflow.io/) and [nf-core](https://nf-co.re/).

If you want to run Nextflow pipelines on the cluster you should write your work directory and results to a folder in your scratch directory. Lots of intermediate files are produced and this is much more efficient on scratch rather than in the slower home directory with more limited space.

You should instruct Nextflow to run jobs on the NXFL partition. You can do this using the Gurdon config file ```/mnt/shared/nextflow/gurdon.config```.

You can submit the main Nextflow process as a job, rather than using screen or tmux, because of the long time limits on the Gurdon cluster.

You should set your Nextflow cache directory to our shared one, e.g. ```export NXF_SINGULARITY_CACHEDIR="/mnt/shared/nextflow/"```

## Part 4 Useful Tools
<a id="#part-4-useful-tools"></a>

### Installing software
<a id="#installing-software"></a>
  
- You can install software and run it from your home directory (you may want to add paths to your .bashrc file)
- You can use [Mamba](https://mamba.readthedocs.io/en/latest/installation/mamba-installation.html) for R/python packages (local but allows multiple environments and “easy” installation). We strongly recommend mamba over conda due to [licensing issues with some conda repositories](https://www.datacamp.com/blog/navigating-anaconda-licensing)
- If something might be generally useful and/or it is hard (impossible without root access?) to install you can ask [Charles](mailto:crb55@cam.ac.uk) to install it centrally.

### FileZilla
<a id="#filezilla"></a>

### FileZilla
<a id="#filezilla"></a>

FileZilla is a free file transfer program that allows you to upload and download files between your computer and the cluster using a secure SFTP connection.

You can download the FileZilla Client for your operating system from:
https://filezilla-project.org/

To connect to the cluster regularly it is easiest to set up a preset connection for it. You can do that by opening the Site Manager (under Files).

![FileZilla Site Manager](../images/filezilla1.png)

There you need to fill in some parameters:

| Filezilla parameter | Value |
|-----------|---------------|
| Protocol  | SFTP - SSH File Transfer Protocol |
| Host | cb-milan2.gurdon.private.cam.ac.uk |
| Port | 22 |
| Logon Type | Normal |
| User | <cluster username>  |
| Password  | <cluster password> |

After saving this you can click "Connect" to connect to the cluster.

![FileZilla Connection](../images/filezilla2.png)

After connecting, the left panel shows the directory structure and files on your local computer, the right one shows the cluster. You can now drag files from one panel to the other which will copy them to the respective location.
You can also create new folders on the cluster by right clicking into the right panel and selecting "Create Directory".

At the bottom of the Filezilla window you have another panel showing you the connection status and state/progress of transfers.

Once you are done you can click "Disconnect" or simply exit Filezilla.

Filezilla only moves files, you cannot edit files, run commands or submit jobs.

### VScode
<a id="#vscode"></a>

Visual Studio Code is an IDE (Integrated Development Environment) allowing you to code in many languages including Python, R, Java, C, Julia. 
You can load a variety of free extentions to extend its functionality.

You can connect VSCode to a cluster in multiple ways. One way is loading the extention "Remote SSH" which allows you to connect to a cluster through a SSH tunnel. This extention installs a "Open Remote Window" option you can find all the way in the bottom left corner or when starting a new workspace under the selection "Connect to...".
Selecting this options asks you to "Connect to host". Here you can either fill in the address of the cluster or - if you have added it to your known hosts on your computer - it is already selectable by the alias you saved that host under.

Once you are connected to the cluster, code will be executed in the directory on the cluster you save your code to. You can also execute it in any conda environments you have created on the cluster (or directly create new ones from the VS Code terminal) by running `conda activate <name>` in the VS Code terminal or by using the shortcut  `Ctrl+Shift+P` and choose "Python: select Interpreter". This will list existing conda environments and python versions.

This is particularly helpful for executing Jupyter Notebooks. To enable this you need to install the "Jupyter" extension. If you connect your workspace to the cluster and start a new Jupyter Notebook file (.ipynb) it will be executed on the cluster.

Additionally you should install the "slurm" extention to submit jobs to the cluster through slurm, which works with the same slurm commands used directly on the cluster run from the VS Code Terminal.


### AI agents
<a id="#ai-agents"></a>

You can use AI like the university Copilot or Claude on the cluster. However, there are potential safety concerns with running AI agents directly on the cluster and giving them execution permissions. An AI agent usually has the same permissions on the cluster as the user who installed it, so your agent will only be able to access, edit and delete files you have permission to do so and will not affect other user's data. However it is advisable to set up the agent so it will always require your explicit review and permission to execute any command to avoid data loss and other problems.

You can use agents like Claude Code or Copilot through [Visual Studio Code](#vscode) which help you with coding and documentation as well as setting up slurm submissions.

To set up Copilot in VSCode, the easiest solution is through installing and setting up the Github Copilot extention which will give you access to the Copilot account connected to your GitHub account.

To set up Claude in VSCode, you will need the [Claude Code app](https://claude.com/product/claude-code) installed on the cluster. You can then run it in every VSCode project by running `claude` from the shell in VSCode. Alternatively there exist several Claude extentions for VSCode like "Claude Code for VS Code" (the official Anthropic extention) or "Claude Code Assistant for VSCode" as well as many others specific to Claude Chat or managing Claude config files.


## Appendix
<a id="#appendix"></a>
  
### Cheatsheets
    
Linux Unix - [https://www.maths.ox.ac.uk/system/files/legacy/2356/basic-unix.pdf](https://www.maths.ox.ac.uk/system/files/legacy/2356/basic-unix.pdf)

Slurm - [https://www.chpc.utah.edu/presentations/SlurmCheatsheet-Summer2019.pdf](https://www.chpc.utah.edu/presentations/SlurmCheatsheet-Summer2019.pdf)

#### Gurdon cluster

|            |             |
|------------|-------------|
| Logging in | `ssh <user>@cb-milan2.gurdon.private.cam.ac.uk` |
| Filesystem | __Scratch__ `/mnt/scratch` – no quotas (~1 Petabyte total) |
|            | (Running compute jobs) |
|            | __home2__ `/mnt/home2` – limited space (1 TB) |
|            | __home3__ `/mnt/home3` – limited space (1 TB) |
|            | __home5__ `/mnt/home5` – limited space (1 TB) |
|            | (Installing software, backing up results, Conda environments) |
| Sequencing | `/mnt/beegfs/Sequencing` (from head node only) |
| RStudio server | v4.2.0   [http://cb-milan1.gurdon.private.cam.ac.uk:8787/](http://cb-milan1.gurdon.private.cam.ac.uk:8787/) |
|            |v4.4.2  [http://cb-milan2.gurdon.private.cam.ac.uk:8787/](http://cb-milan2.gurdon.private.cam.ac.uk:8787/) |


### Practical 1 answers

```
# SSH to the cluster
ssh ajr236@cb-milan2.gurdon.private.cam.ac.uk

# Check the storage quota
quota

# Make a new working directory
mkdir tutorial

# Change to that directory
cd tutorial

# Get interesting file from the internet
wget http://s3ftp.flybase.org/releases/FB2021_06/precomputed_files/genes/automated_gene_summaries.tsv.gz 

# From local machine, copy the file from the cluster to that machine
scp ajr236@cb-milan2.gurdon.private.cam.ac.uk:/mnt/home3/reid/ajr236/tutorial/automated_gene_summaries.tsv.gz .

# Unzip the file
gunzip automated_gene_summaries.tsv.gz

# Make a new file with just the first 10 lines in it
head -10 automated_gene_summaries.tsv > head.txt

# From local machine, copy the new file to the working directory on the cluster
scp head.txt ajr236@cb-milan2.gurdon.private.cam.ac.uk:~/tutorial/
```

### Practical 2 answers

1. Use srun to submit the command `hostname` – which node did the job run on?

```
srun hostname
```

2. Adjust the parameters of srun to run the same command multiple times (-n) – did they run on the same or different nodes?

```
srun --ntasks 2 hostname
```

3. Use slurm_sub.py to submit the command `sleep 100`. Use `squeue –u <user>` to find out which node it is running on. What were the output status and error messages from the job? What time did the job start and when did it finish?
  
```
# submit the sleep command using slurm_sub.py
slurm_sub.py sleep 100

# Check that the job is running and on which node
squeue -u <user>

# When the job is finished, look at the start and finish times to work out how long it took
# Also look to see if the job finished properly e.g. “Job finished successfully, return value: 0”
cat job.o

# Error file should be empty
cat job.e
```

4. Run the `sleep 100` command again, but kill it before it finishes. What was the output/error status of the job this time?

```
# submit the sleep command using slurm_sub.py
slurm_sub.py sleep 100

# Kill the job (get the job id from the message upon submission or from squeue
scancel <jobid> 

# There is no status message in stdout
cat job.o

# Error file should say something like “slurmstepd: *** JOB 5010440 ON node20 CANCELLED AT 2022-01-31T15:49:14 ***”
cat job.e
```
  
5. Copy files: `sample1_1.fastq`, `sample1_2.fastq` and `dm6_chrM.fa` from `/mnt/bioinfo_sharing/sharing/course_material/cluster/` to your tutorial directory. Map the data to reference fasta `dm6_chrM.fa` with the following commands, running each job using slurm_sub.py

```
# Copy files   
cp /mnt/bioinfo_sharing/sharing/course_material/cluster/sample1_*.fastq .
cp /mnt/bioinfo_sharing/sharing/course_material/cluster/dm6_chrM.fa .
  
# Make an index of the reference sequence 
bwa index dm6_chrM.fa 

# Run the mapping step
slurm_sub.py –j bwamem1 bwa mem dm6_chrM.fa sample1_1.fastq sample1_2.fastq -o sample1.sam
  
# Convert sam to bam
slurm_sub.py –j sam2bam1 samtools view -b sample1.sam -o sample1.bam

# sort the bam file
slurm_sub.py –j sort1 samtools sort -o sample1_sorted.bam sample1.bam

# Index the bam file
slurm_sub.py –j index1 samtools index sample1_sorted.bam
```

#### Practical 2 bonus exercise

Map_batch.sh –

```
#!/bin/bash
sample_name='sample2'

bwa mem ../dm6_chrM.fa $sample_name\_1.fastq  $sample_name\_2.fastq -o $sample_name\.sam
samtools view -b $sample_name\.sam -o $sample_name\.bam
samtools sort -o $sample_name_sorted\.bam $sample_name\.bam
samtools index $sample_name_sorted\.bam
```

```
slurm_sub.py -j sample2_batch bash map_batch.sh 
```
  
Or

```
sbatch -J sample2_batch map_batch.sh 
```
  
### Online courses

#### Linux command line
  
[https://ubuntu.com/tutorials/command-line-for-beginners#1-overview](https://ubuntu.com/tutorials/command-line-for-beginners#1-overview)
  
[https://www.futurelearn.com/courses/linux-for-bioinformatics](https://www.futurelearn.com/courses/linux-for-bioinformatics)

[https://info-ee.surrey.ac.uk/Teaching/Unix/](https://info-ee.surrey.ac.uk/Teaching/Unix/)

[https://swcarpentry.github.io/shell-novice/](https://swcarpentry.github.io/shell-novice/)
  
[An actual command line interface on the web for practicing](https://www.tutorialspoint.com/linux_terminal_online.php)

#### Cluster computing
  
[http://bioinformatics-core-shared-training.github.io/shell-novice/](http://bioinformatics-core-shared-training.github.io/shell-novice/)

[https://bioinformatics-core-shared-training.github.io/Managing-your-research-data/](https://bioinformatics-core-shared-training.github.io/Managing-your-research-data/)

[https://github.com/bioinformatics-core-shared-training/nextflow_september_2021](https://github.com/bioinformatics-core-shared-training/nextflow_september_2021)

[http://bioinformatics-core-shared-training.github.io/hpc/](http://bioinformatics-core-shared-training.github.io/hpc/)

<hr>  

![gurdon](../images/gurdon.png)
<img src="../images/cambridge.png" alt="cambridge" width="600"/>

Licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)
