## Access to Software

You can access software on the Snomwass21 Connect node from a) local installations in `/collab/project/snowmass21/software` b) from CVMFS in commonly used repos in the High Energy Physics community c) the OSG module environment. For applications that need to run on OSG grid only options b) and c) are viable. We have created a new CVMFS repo in /cvmfs/snowmass21.opensciencegrid.org to create a library of applications specific to the collaboration. Software installed in that repo is made available on remote worker nodes on OSG. 

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


