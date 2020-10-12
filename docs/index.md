# Snowmass Connect

The purpose of this documentation is to facilate the onboarding of researchers with the Snowmass21 collaboration in accessing
the Open Science Grid (OSG) via the Snowmass Connect service hosted at the University of Chicago.
  
## Getting Started

Snowmass Connect is a job submission service providing access to the distributed cyberinfrastructure of the Open Science Grid. To sign up, visit [https://connect.snowmass21.io](https://connect.snowmass21.io). You will be notified by email once your membership request has been approved. It is important that you upload your ssh-keys following the instructions on the portal. The process will create your home directory on the Snowmass submit node and grant you access via passwordless ssh.

In order to be able to submit jobs to the OSG you should follow up with a request to be added to a particular subgroup: snowmass21.energy or snowmass21.cosmic. An email back will confirm your addition to your requested subgroup project. 

## Login to the Submit Node

Once you upload your ssh-keys in the Snowmass Connect portal you can connect to the login node via passwordless ssh as:

`ssh <user_id>@login.snowmass21.io` 

You can find your `<user_id>` from your profile on the Snowmass Connect portal. 
The login node is also a submission node for jobs to the Open Science Grid. Upon login you will land in your home directory `/home/user_id`. Your home 
directory has 50GB of quota. Use your home directory to store job submission files, scripts and code. Do **not** store large files (larger than 300 MB) in your home directory for the purpose of submissions to the OSG. 

The Snowmass login node provides a service to the users of the collaboration in the following ways:

1. Provide users a gateway to the Open Science Grid in order to run their production computational workflows via job submissions to the OSG HTCondor pool
2. Provide access to the OSG Storage at the University of Chicago where users can stage input files for grid jobs and collect
output from their jobs
3. Provide an environment for the development of OSG appropriate workflows that will leverage distributed High ThroughPut 
computing. To facilitate such development a list of scientific software is accessible from the login node using `modules`. You can list availablle 
modules using the `module avail` command. You can load a module with the `module load <module_name>` command. Users can request remote worker
nodes where the module environment is available, discussed in the [Job submission](#Job-submissions-to-the-OSG) section.


## Storage Access

This section describes the storage locations accessible by the users when connected to Snowmass login node. It also describes the means by which
users can move data in from their home institutions.

1. Home directory. As mentioned above, your home directory will have 50GB of storage  available and can
be used for scripts, submission files and small size data. Large input files for jobs on the grid should not be stored here.
2. OSG storage accebible from the login node at `/collab`. You will receive a 1TB quota for their personal user space, and there is a shared 50TB quota for the project space. Larger space allocations can be accommodated on a case-by-case basis. You should store their data in either of two subdirectories:  
    * For private user data: `/collab/user/<user_id>`  
    * For shared data among the members of the Snowmass21 collaboration:`/collab/project/snowmass21`
3. Local storage in`/scratch`. You can access that storage location for storing workflows, data and job submission scripts.

For details on Data Management on the Snowmass21 login refer to [this](data_management.md) section.

 
## Job Submissions to the OSG

This section provides a short introduction to submitting jobs to the OSG from the Snowmass Connect login node using the snowmass21.energy subgroup as the project name. A minimal HTCondor submission script, `myjob.submit`, to the OSG is inlined below:

    Universe = Vanilla
    Executable     = run.sh
    Error   = output.err.$(Cluster)-$(Process)
    Output  = output.out.$(Cluster)-$(Process)
    Log     = output.log.$(Cluster)
    should_transfer_files = YES
    WhenToTransferOutput = ON_EXIT
    request_cpus = 1
    request_memory = 1 GB
    +ProjectName="snowmass21.energy"
    Queue 1

Refer to the [HTCondor manual](https://htcondor.readthedocs.io/en/stable/users-manual/index.html) for more information on the declared parameters and on customizing your submission scripts.

When the HTCondor script above is submitted, you request a remote worker node with 1 core and 1 GB to run the `run.sh` executable. In this case, `run.sh` is a shell script that contains a list of commands that executes your workload on the worker node.  For example: 

    #/bin/bash
    ./code_executable <input_file> <output_file>
    <additional commands>

The parameter `should_transfer_files = YES` instructs HTCondor to use the HTCondor file transfer 
method to transfer the `Executable` to the remote host and the job files `Error` (stderr) , `Output` (stdout) and `Log` 
back to your directory on the submit host. You will have a number of options to transfer code executables and input/output files to the remote worker node, described in the next section.

You can submit the job script to the OSG via the HTCondor command on the Snowmass login node as: 
`condor_submit myjob.submit`, which will return a unique `<JobID>` number. 
You can use the `<JobID>` to query the status of your job with `condor_q <JobID>`

For an introduction on managing your jobs with HTCondor we refer to [this](https://opensciencegrid.org/user-school-2019/#materials/day1/files/osgus19-day1-part1-intro-to-htc.pdf) presentation by the OSG. You can use also the tutorial command on the Snowmass Login node to access and submit examples to the OSG. A summary of the tutorial command is given [here](tutorial.md).

###  Job Submission Guidelines

1. If your application/code was built or depends on modules used on the snowmass21 login node and it dynamically links against libraries of the module environment you would need to ensure that these modules are also availablle and loaded on the remote worker node. To do so:
    * Insert the following parameter in your submission script: `Requirements = (HAS_MODULES =?= TRUE)`. This will request a worker node on a site where the OSG modules are available
    * Before you invoke your executable inside the `run.sh` script load the modules as: `module load module1 module2`
    * We discuss modules with a little bit more detail in [here](software.md).
  
2. You must always populate the project name field, e.g. `+ProjectName="snowmass21.energy"`, in your HTCondor submission script to:
    * Ensure your job is validated for HTCondor to run it on the OSG grid
    * Job statistics are properly collected and displayed on the [OSG monitoring dashboard](https://bit.ly/3j36bAc) for the snowmass project.


## Snowmass Software on the Connect Node

A list of supported software and submission scripts are included [here](software.md)

## Support and Consultation

Snowmass21 Connect is supported by the University of Chicago and Open Science Grid staff. To report issues with the service or to request a consultation on submitting and running jobs on the OpenScienceG Grid please submit a ticket to <help@opensciencegrid.org>. Additional support is available in the **#snowmass-connect** channel at http://snowmass2021.slack.com.
