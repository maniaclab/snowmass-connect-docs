## Software Availability on the Snowmass Connect Node

Local installations of software are available on `/collab/project/snowmass21/software`. To access software for jobs on OSG Grid we will be making the locally installed software available over CVMFS from a repository hosted on an Origin at UChicago. Note that additional CVMFS repositories, hosted elsewhere might be needed for jobs to successfully execute.

## Running Delphes on OSG

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

## Running ILC on OSG

