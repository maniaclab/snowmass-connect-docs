# Job Submissions to the OSG

This section provides a short introduction to submitting jobs to the OSG from the Snowmass Connect login node using the snowmass21.energy subgroup 
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
