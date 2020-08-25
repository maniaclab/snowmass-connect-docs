## Software Availability on the Snowmass Connect Node


## Running Delphes on OSG

Delphes is installed in `/cvmfs/snowmass21.opensciencegrid.org/software/Delphes-3.4.2`. To run the software add the following in your submit script:

    #congifure ROOT/gcc environment
    source /cvmfs/sft.cern.ch/lcg/views/LCG_92/x86_64-slc6-gcc62-opt/setup.sh
    
Running Delphes with HepMC input files:
 
    /cvmfs/snowmass21.opensciencegrid.org/software/Delphes-3.4.2/DelphesHepMC /cvmfs/snowmass21.opensciencegrid.org/software/Delphes-3.4.2/cards/delphes/card_CMS.tcl delphes_output.root input.hep

Running Delphes with STDHEP (XDR) input files:
    
    /cvmfs/snowmass21.opensciencegrid.org/software/Delphes-3.4.2/DelphesSTDHEP /cvmfs/snowmass21.opensciencegrid.org/software/Delphes-3.4.2/cards/delphes/delphes_card_CMS.tcl delphes_output.root input.hep   

Running Delphes with LHEF input files:
      
     /cvmfs/snowmass21.opensciencegrid.org/software/Delphes-3.4.2/DelphesLHEF /cvmfs/snowmass21.opensciencegrid.org/software/Delphes-3.4.2/cards/delphes/delphes_card_CMS.tcl delphes_output.root input.lhef

Running Delphes with files accessible via HTTP:

    curl http://cp3.irmp.ucl.ac.be/~demin/test.hepmc.gz | gunzip | /cvmfs/snowmass21.opensciencegrid.org/software/Delphes-3.4.2/DelphesHepMC /cvmfs/snowmass21.opensciencegrid.org/software/Delphes-3.4.2/cards/delphes_card_CMS.tcl delphes_output.root

## Running ILC on OSG
