# Software Access

You can access software from the Snomwass21 host installed in local directories or
repositories in cvmfs.

## Installed Software 

1. Installations in `/software` includes the following packages:

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

These can run on the local host or if the executables are static
they can be transferred to remote worker nodes 
for HTCCondor jobs in the OSPool.

2. Installations in stash, `/collab/project/snowmass21/software/` includes the following 
packages:

        cepcenv    
        Delphes-3.4.2
        julia
        LCIO
        miniDST

Those are also available via CVMFS in ` /cvmfs/stash.osgstorage.org/osgconnect/collab/project/snowmass21/software`
and intended to run primarily as part HTCondor jobs to the OSPool.

## Software in cvmfs repositories

You can run software installed in commonly used cvmfs repos for the High Energy Physics community. 
For example you can set up your environment to run Rivet with LHAPDF on the local host as:

        source /cvmfs/sft.cern.ch/lcg/views/LCG_101/x86_64-centos7-gcc8-opt/setup.sh
        export PATH=/cvmfs/sft.cern.ch/lcg/external/texlive/2020/bin/x86_64-linux/:$PATH
        export LHAPDF_DATA_PATH=/cvmfs/sft.cern.ch/lcg/external/lhapdfsets/current/:/cvmfs/sft.cern.ch/lcg/views/LCG_101/x86_64-centos7-gcc8-opt/share/LHAPDF/
        /cvmfs/sft.cern.ch/lcg/views/LCG_101/x86_64-centos7-gcc8-opt/bin/rivet <arguments>

You can also include theese lines in your execution script for
a job submitted to the OSPool.

## Software in Singularity containers

Software stacks in singularity containers deployed in cvmfs 
repos can run on the Snowmass21 host or reference the location of 
the images in the submit script for jobs to the OSPool.
We present two examples to demonstrate the two cases.

### Snowmass21 login host

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
        
## Running Delphes

### Snowmass21 login host

For local calculations on the Snowmass21 host, Delphes is installed in `/software/Delphes-3.4.2`. You must first execute the following setup script: `source /cvmfs/sft.cern.ch/lcg/views/LCG_92/x86_64-slc6-gcc62-opt/setup.sh` before using the executables. 

Export the installation path and adjust the LD_LIBRARY_PATH of the supporting libraries as:

    export delphes_install=/software/Delphes-3.4.2
    module use /software/modulefiles/
    module load gcc-8.2.0
    export LD_LIBRARY_PATH=/cvmfs/sft.cern.ch/lcg/views/LCG_92/x86_64-slc6-gcc62-opt/lib:$LD_LIBRARY_PATH
    export PATH=$delphes_install:$PATH
    
Running Delphes with HepMC input files:
 
    $delphes_install/DelphesHepMC $delphes_install/cards/delphes/card_CMS.tcl delphes_output.root <input.hepmc>

Running Delphes with STDHEP (XDR) input files:
    
    $delphes_install DelphesSTDHEP $delphes_install/cards/delphes/delphes_card_CMS.tcl delphes_output.root <input.hep>   

Running Delphes with LHEF input files:
      
     $delphes_install/DelphesLHEF $delphes_install/cards/delphes/delphes_card_CMS.tcl delphes_output.root <input.lhef>

Running Delphes with files accessible via HTTP. The following example downloads a test HepMC input file and runs the executable. 

    curl http://cp3.irmp.ucl.ac.be/~demin/test.hepmc.gz | gunzip | $delphes_install/DelphesHepMC $delphes_install/cards/delphes_card_CMS.tcl delphes_output.root

### Delphes on the OSPool

For jobs on OSG, it recommended you use the singularity container image hosted `/cvmfs/singularity.opensciencegrid.org/snowmass21software/delphes-osg:latest`. The image contains the full installation of the software which includes the examplles folder. The following example is a submission script which will request the availability of Singularity as a requirement on the remote worker node and loads the image for your job. 

    Universe = Vanilla
    Executable     = run.sh
    Requirements = HAS_SINGULARITY == TRUE
    +SingularityImage = "/cvmfs/singularity.opensciencegrid.org/snowmass21software/delphes-osg:latest"
    Error   = output.err.$(Cluster)-$(Process)
    Output  = output.out.$(Cluster)-$(Process)
    Log     = output.log.$(Cluster)
    should_transfer_files = YES
    WhenToTransferOutput = ON_EXIT
    request_cpus = 1
    request_memory = 1 GB
    +ProjectName="snowmass21.energy"
    Queue 1     

The executable script, run.sh, contains the HTTP  listed above for local jobs. 

    #!/bin/bash

    curl http://cp3.irmp.ucl.ac.be/~demin/test.hepmc.gz | gunzip | DelphesHepMC /opt/Delphes-3.4.2/cards/delphes_card_CMS.tcl ~/delphes_output.root

There is no need to source any external environment and all Delphes executables have been added to $PATH as part of the image. 

## Running Whizard

### Snowmass21 login host (local)

Whizard is installed on the Snowmass21 host in /software/ee_gen. You must set up your environment before by running the following on the submit node: 

        module use /software/modulefiles/
        module load gcc-8.2.0 
        export LD_LIBRARY_PATH=/software/ee_gen/./packages/OpenLoops/lib:$LD_LIBRARY_PATH
        export PATH=/software/ee_gen/bin:$PATH 
        
Examples are contained in this directory: /software/ee_gen/share/whizard/examples. The whizard executable will be in your $PATH. You can run an example from your home directory as: 
    
        whizard /software/ee_gen/share/whizard/examples/LEP_cc10.sin

### Whizzard on the OSPool

Whizard is also available over cvmfs in /cvmfs/snowmass21.opensciencegrid.org/ee_gg. To run on the grid, ensure that your submit script has `Requirements = (HAS_MODULES =?= TRUE)`. You must also source the setup script in /cvmfs/snowmass21.opensciencegrid.org/ee_gg/setup.sh which will set up your environment. An example of a job submission to the OSPool for whizard is inlined below:

Submit script:

        Universe = Vanilla
        Executable     = run.sh
        Requirements = (HAS_CVMFS =?= TRUE) && (OSGVO_OS_STRING == "RHEL 7) && (HAS_MODULES =?= TRUE) 
        Error   = output.err.$(Cluster)-$(Process)
        Output  = output.out.$(Cluster)-$(Process)
        Log     = output.log.$(Cluster)
        should_transfer_files = YES
        WhenToTransferOutput = ON_EXIT
        request_cpus = 1
        request_memory = 1 GB
        +ProjectName="snowmass21.energy"
        Queue 1
        
Execution script (run.sh from the submit script above):

        #!/bin/bash
        source /cvmfs/snowmass21.opensciencegrid.org/ee_gg/setup.sh
        whizard /cvmfs/snowmass21.opensciencegrid.org//ee_gen/share/whizard/examples/LEP_cc10.sin
        
