# Software Access

You can access software from the Snomwass21 host installed in local directories or
repositories in cvmfs.

## Installed Software 

1. The following packages are installed in `/software`:

        cepcenv
        Delphes-3.4.2    
        ee_gen    
        FCCAnalyses    
        LCIO    
        LCIO_snowmass   
        MG5    
        MG5_aMC_v3_0_3   
        miniconda3   
        pythia8303
        ilc
        rivet

These can run on the local host or, if the executables are static,
in HTCCondor jobs submitted to the OSPool.

2. The following packages are installed in stash, `/collab/project/snowmass21/software/`:

        cepcenv    
        Delphes-3.4.2
        julia
        LCIO
        miniDST

Those are also available via cvmfs in ` /cvmfs/stash.osgstorage.org/osgconnect/collab/project/snowmass21/software`
and intended to run primarily as part HTCondor jobs to the OSPool.

## Software in cvmfs repositories

You can run software installed in commonly used cvmfs repos for the High Energy Physics community. 
For example you can set up your environment to run Rivet with LHAPDF on the local host as:

        source /cvmfs/sft.cern.ch/lcg/views/LCG_101/x86_64-centos7-gcc8-opt/setup.sh
        export PATH=/cvmfs/sft.cern.ch/lcg/external/texlive/2020/bin/x86_64-linux/:$PATH
        export LHAPDF_DATA_PATH=/cvmfs/sft.cern.ch/lcg/external/lhapdfsets/current/:/cvmfs/sft.cern.ch/lcg/views/LCG_101/x86_64-centos7-gcc8-opt/share/LHAPDF/
        /cvmfs/sft.cern.ch/lcg/views/LCG_101/x86_64-centos7-gcc8-opt/bin/rivet <arguments>

You can also include these lines in your execution script for
a job submitted to the OSPool.

## Software in Singularity containers

Software stacks in singularity containers deployed in cvmfs 
repos can run on the Snowmass21 host or reference the location of 
the images in the submit script for jobs to the OSPool.
We present two examples to demonstrate the two cases.

### Running a Singularity container on the login host

Singularity is installed on the Snowmass21 host. Below is an example that runs a container located in cvmfs and binds a directory located in the distributed filesystem to /data:

        singularity run -B /collab/project/snowmass21/data/ilc:/data /cvmfs/unpacked.cern.ch/registry.hub.docker.com/infnpd/mucoll-ilc-framework\:1.0-centos8

Then source the environment setup inside the container: 

        Singularity> source /opt/ilcsoft/init_ilcsoft.sh 


### Running a Singularity container in the OSPool

To run a singularity container in the OSPool, you will need to specify a set of requirements and the /cvmfs location of the singularity image. Below is the same example from above but written as a job submission. Note, that if the singularity image is hosted in `/cvmfs/singularity.opensciencegrid.org` you only need to specify `HAS_SINGULARITY == TRUE`.

        Universe = Vanilla
        Executable   = run.sh
        Requirements = ( ( HAS_SINGULARITY == TRUE ) && ( HAS_CVMFS_unpacked_cern_ch ) )
        +SingularityImage = "/cvmfs/unpacked.cern.ch/registry.hub.docker.com/infnpd/mucoll-ilc-framework:1.6-centos8"
        Error   = output.err.$(Cluster)-$(Process)
        Output  = output.out.$(Cluster)-$(Process)
        Log     = output.log.$(Cluster)
        should_transfer_files = YES
        WhenToTransferOutput = ON_EXIT
        request_cpus = 1
        request_memory = 5 GB
        +ProjectName="snowmass21.energy"
        Queue 1

File `run.sh` is a bash-shell script that contains a list of commands to be executed on the worker node. A generic example is as follows:

        #!/bin/bash 
        #set up the environment
        source /opt/ilcsoft/init_ilcsoft.sh
        #create a data directory
        mkdir data
        cd data
        #download some data
        wget  https://stash.osgconnect.net/collab/project/snowmass21/data/ilc/<some_data> 
        #do some work
        
Additional examples are presented [here](extra_examples.md)
