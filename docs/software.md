## Access to Software

You can access software on the Snomwass21 Connect node from a) local installations in `/collab/project/snowmass21/software` b) from CVMFS in commonly used repos for the High Energy Physics community c) the OSG module environment. For applications that need to run on OSG grid, only options b) and c) are viable. We have created a new CVMFS repo in /cvmfs/snowmass21.opensciencegrid.org to create a library of applications specific to the collaboration. Software installed in that repo is made available on remote worker nodes on OSG. 

## The OSG Module Environment

Many commonly used software packages and libraries are provided on the OSG through the module command.  OSG modules are made available through the OSG Application Software Installation Service (OASIS).

On the Snowmass21 Connect node use the module avail command to see what software and libraries are available: `module avail`. Use the load command to load a specific module: `module load <name_of_module`. For example, in ... we discussed loading the stashcache module `module load stashcache` which allows you to use the stashcp command to transfer data to the grid. 

There are two things required in order to use modules in your HTCondor job.

    Create an execution script for the job. The script should load the needed modules before running the main application.  
    
    Include the following requirements in the HTCondor submission script:

    Requirements = (HAS_MODULES =?= TRUE)
    or 

    Requirements = [Other requirements ] && (HAS_MODULES =?= TRUE)


## Running Delphes

For local calculations on the Snowmass Connect node, Delphes is installed in `/collab/project/snowmass21/software/Delphes-3.4.2`. You must first execute the following setup script: `source /cvmfs/sft.cern.ch/lcg/views/LCG_92/x86_64-slc6-gcc62-opt/setup.sh` before using the executables. For jobs on OSG, Delphes is installed in `/cvmfs/snowmass21.opensciencegrid.org/software/Delphes-3.4.2`. To run the software as part of job submitted to OSG
add the following in your execution script:

    #congifure ROOT/gcc environment
    source /cvmfs/sft.cern.ch/lcg/views/LCG_92/x86_64-slc6-gcc62-opt/setup.sh
    export delphes_install=/cvmfs/snowmass21.opensciencegrid.org/software/Delphes-3.4.2
    
Running Delphes with HepMC input files:
 
    $delphes_install/DelphesHepMC $delphes_install/cards/delphes/card_CMS.tcl delphes_output.root input.hep

Running Delphes with STDHEP (XDR) input files:
    
    $delphes_install DelphesSTDHEP $delphes_install/cards/delphes/delphes_card_CMS.tcl delphes_output.root input.hep   

Running Delphes with LHEF input files:
      
     $delphes_install/DelphesLHEF $delphes_install/cards/delphes/delphes_card_CMS.tcl delphes_output.root input.lhef

Running Delphes with files accessible via HTTP:

    curl http://cp3.irmp.ucl.ac.be/~demin/test.hepmc.gz | gunzip | $delphes_install/DelphesHepMC $delphes_install/cards/delphes_card_CMS.tcl delphes_output.root


