# Additional Examples

## Running Delphes

### On the login host

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

### On the OSPool

For OSPool jobs, it recommended you use the singularity container image hosted `/cvmfs/singularity.opensciencegrid.org/snowmass21software/delphes-osg:latest`. The image contains the full installation of the software which includes the examplles folder. The following example is a submission script which will request the availability of Singularity as a requirement on the remote worker node and loads the image for your job. 

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

### On the login host

Whizard is installed alled on the Snowmass21 host in /software/ee_gen. You must set up your environment before by running the following on the submit node: 

        module use /software/modulefiles/
        module load gcc-8.2.0 
        export LD_LIBRARY_PATH=/software/ee_gen/./packages/OpenLoops/lib:$LD_LIBRARY_PATH
        export PATH=/software/ee_gen/bin:$PATH 
        
Examples are contained in this directory: /software/ee_gen/share/whizard/examples. The whizard executable will be in your $PATH. You can run an example from your home directory as: 
    
        whizard /software/ee_gen/share/whizard/examples/LEP_cc10.sin

### On the OSPool

Whizard is also available over cvmfs in /cvmfs/snowmass21.opensciencegrid.org/ee_gg. To submit and run an HTCondor job on the OSPool ensure that your submit script has `Requirements = (HAS_MODULES =?= TRUE)`. You must also source the setup script in /cvmfs/snowmass21.opensciencegrid.org/ee_gg/setup.sh which will set up your environment. An example of a job submission to the OSPool for whizard is inlined below:

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
        
