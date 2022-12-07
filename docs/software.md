# Software Access

You can access software on the Snomwass21 host from:

1. Local installations in `/software`. This option is only available for local jobs.
The following contains a list of installed packages:

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
    rivet

2. Installations in stash in `/collab/project/snowmass21/software/`. The software is also available via CVMFS in ` /cvmfs/stash.osgstorage.org/osgconnect/collab/project/snowmass21/software`
The following contains a list of installed packages:

        cepcenv    
        Delphes-3.4.2
        julia
        LCIO
        miniDST

4. Commonly used repos in CVMFS for the High Energy Physics community

## The OSG Module Environment

The OSG Module Environment is being deprecated but still available if needed. Users should keep in mind that the versions of the available packages are old and there will be no effort to update them in the future. Here are some tips in using modules:

Use the `module avail` command on the Snowmass21 Connect host to see what software and libraries are available. Use the load command to load a specific module: `module load <name_of_module`. 

There are two things required in order to use modules in your HTCondor job.

    1. Create an execution script for the job. The script should load the needed modules before running the main application.  
    
    2. Include the following requirements in the HTCondor submission script:
    Requirements = (HAS_MODULES =?= TRUE)
    or 
    Requirements = [Other requirements ] && (HAS_MODULES =?= TRUE)


## Running Delphes

### Snowmass21 login host

For local calculations on the Snowmass Connect node, Delphes is installed in `/software/Delphes-3.4.2`. You must first execute the following setup script: `source /cvmfs/sft.cern.ch/lcg/views/LCG_92/x86_64-slc6-gcc62-opt/setup.sh` before using the executables. 

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

### Snowmass21 login node (local)

Whizard is installed on the Snowmass21 host in /software/ee_gen. You must set up your environment before by running the following on the submit node: 

        module use /software/modulefiles/
        module load gcc-8.2.0 
        export LD_LIBRARY_PATH=/software/ee_gen/./packages/OpenLoops/lib:$LD_LIBRARY_PATH
        export PATH=/software/ee_gen/bin:$PATH 
        
Examples are contained in this directory: /software/ee_gen/share/whizard/examples. The whizard executable will be in your $PATH. You can run an example from your home directory as: 
    
        whizard /software/ee_gen/share/whizard/examples/LEP_cc10.sin

### Whizzard on the OSG

Whizard is also available over cvmfs in /cvmfs/snowmass21.opensciencegrid.org/ee_gg. To run on the grid, ensure that your submit script has `Requirements = (HAS_MODULES =?= TRUE)`. You must also source the setup script in /cvmfs/snowmass21.opensciencegrid.org/ee_gg/setup.sh which will set up your environment. An example of an OSG job submission for whizard is inlined below:

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
        
