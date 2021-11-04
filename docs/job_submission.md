# Job Submission to the OSG

This section provides a short introduction on how to submit jobs to the OSG from the Snowmass Connect login node using the snowmass21.energy subgroup 
as the project name. A minimal HTCondor submission script, `myjob.submit`, to the OSG is inlined below:

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

Refer to the [HTCondor manual](https://htcondor.readthedocs.io/en/stable/users-manual/index.html) and the [OSG Helpdesk](https://support.opensciencegrid.org/support/home) for more information on the declared parameters and on customizing your submission scripts.

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

For an introduction on managing your jobs with HTCondor we refer to [this](https://opensciencegrid.org/user-school-2019/#materials/day1/files/osgus19-day1-part1-intro-to-htc.pdf) presentation by the OSG.

###  Job Submission Guidelines

1. If your application/code was built or depends on modules used on the snowmass21 login node and it dynamically links against libraries of the module environment you would need to ensure that these modules are also availablle and loaded on the remote worker node. To do so:
    * Insert the following parameter in your submission script: `Requirements = (HAS_MODULES =?= TRUE)`. This will request a worker node on a site where the OSG modules are available
    * Before you invoke your executable inside the `run.sh` script load the modules as: `module load module1 module2`
    * We discuss modules with a little bit more detail in [here](software.md).
  
2. You must always populate the project name field, e.g. `+ProjectName="snowmass21.energy"`, in your HTCondor submission script to:
    * Ensure your job is validated for HTCondor to run it on the OSG grid
    * Job statistics are properly collected and displayed on the [OSG monitoring dashboard](https://bit.ly/3j36bAc) for the snowmass project.
   
   
## The Tutorial Command

OSG provides a list of tutorials available as repositories on Github. These tutorials are designed for OSG submit nodes, are tested regularly and should work as is, but if you experience any issues please contact us. Users on the Snowmass login node should be able to run most of those. 

## List of options for the tutorial command

From the Snowmass21 Connect login node, the following tutorial commands are available:

    $ tutorial
    $ tutorial list
    $ tutorial info <tutorial-name>
    $ tutorial <tutorial-name>

## Available Tutorials

The following tutorials pre-installed. Some additional tutorials specific to Snowmass21 will be deployed there as well. Tutorials that will not work on the Snowmass login node at present are struckthrough. To see what is currently available: 

    $ tutorial list
    Currently available tutorials:
    R ...................... Estimate Pi using the R programming language
    R-addlibSNA ............ Shows how to add R external libraries for the R jobs on the OSG
    ScalingUp-Python ....... Scaling up compute resources - Python example to optimize a function on grid points
    annex .................. None
    blast-split ............ How to run BLAST on the OSG by splitting a large input file
    connect-client ......... Demonstrates how to use the connect client for remote job submission
    dagman-wordfreq ........ DAGMan based wordfreq example
    dagman-wordfreq-stash .. DAGMan based wordfreq - data from stash
    error101 ............... Use condor_q -better-analyze to analyze stuck jobs
    exitcode ............... Use HTCondor's periodic_release to retry failed jobs
    htcondor-transfer ...... Transfer data via HTCondor's own mechanisms
    matlab-HelloWorld ...... Creating standalone MATLAB application - Hello World 
    nelle-nemo ............. Running Nelle Nemo's goostats on the grid
    oasis-parrot ........... Software access with OASIS and Parrot
    octave ................. Matrix manipulation via the Octave programming language
    osg-locations .......... Tutorial based on OSG location exercise from the User School
    pegasus ................ An introduction to the Pegasus job workflow manager
    photodemo .............. A complete analysis workflow using HTTP transfer
    quickstart ............. How to run your first OSG job
    root ................... Inspect ntuples using the ROOT analysis framework
    scaling ................ Learn to steer jobs to particular resources
    scaling-up-resources ... A simple multi-job demonstration
    software ............... Software access tutorial
    tensorflow-matmul ...... Tensorflow math operations as a singularity container job on the OSG - matrix multiplication
   
Tutorials `stash-cvmfs` and `stash-http` do not work on the Snowmass Connect Login node at present. We are working to replace with working variants.


## Install and Setup a Tutorial

On the Snowmass Connect login node, create a directory, cd to it, and invoke the command:

    $ tutorial <tutorial-name>

This command will clone the tutorial repository to your current working directory. cd to the repository directory 
and follow the steps described in the readme.md file. Alternatively, you can view the readme.md 
file at the tutorial's corresponding GitHub page.
