# Data Management

This section provides important information on how the users can manage their data on the Snowmass21 node.

## Storage locations on the Snowmass21 node

This section describes the storage locations accessible by the users when connected to Snowmass login node.

1. **Home** directory. Your home directory, `/home/<user_id>`, has 50GB of storage available. It is recommended to use it for storing scripts, 
submission files and small size data. Large input files for jobs on the grid should not be stored here.
2. **Local storage** in`/local-scratch`. This is a 200TB local disk where you can create your own directory, store data and build your own software stack. It should be used as your private work area for local analysis or processing jobs on the node. 
3. **Stash storage** is accebible from the login node at `/collab`. Each user receives a 1TB quota for their personal user space. There is also a shared 50TB allocation for the project. This location is primarily intented for staging data that need to be accessible by the OSG compute sites running your job. There two subdirectories in `/collab`:  
    * For private user data: `/collab/user/<user_id>`  
    * For shared data among the members of the Snowmass21 project:`/collab/project/snowmass21/data`


## Transferring data to and from the Snowmass21 login node
You can transfer data from external institutions to Snowmass Connect storage using any of the three following methods:

1. **scp**. For example: `scp -r <file_or_directory> <user_id>@login.snowmass.io:/collab/user/<user_id>/.` will copy a file or a directory
from your local machine to your user directory on stash storage. The ssh-keys used for your profile on the Snowmass Connect portal
must stored on the local machine.

2. **rsync**. For example: `rsync -avz -e "ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null" --progress test.transfer <user_id>@login.snowmass21.io:dump/` will copy the `test.transfer` file in the `/home/<user_id>/dump/` directory. If the directory
does not exist, it will be created. As in `scp` the ssh-keys used for your profile on the Snowmass Connect portal
must stored on the source machine.

3. **Globus Connect** can be used to transfer files to stash storage only. A guide on how to gain access to the Globus door and instructions for transfering
data to the stash storage can be found here: [Uploading Data Instructions](globus.md)


## Data for OSG jobs

There are four methods for the user to make data available to remote sites running their jobs.

1. HTCondor File Transfer. This method is recommended for the majority of computational workflows running on the OSG. Users can employ this method if the total size of the input data per job does not exceed 1 GB. In addition, OSG recommends that the output data per job that need to be transfered back does not exceed 1 GB as well. To enable HTCondor File transfers for your input and output data insert the following parameters anywhere in your HTCondor submit file:

        transfer_input_files = <comma separated files or directories>
        transfer_output_files = <comma separated files or directories>

This method can leverage any storage location on the Snowmass21 Connect node. However it is recommended that you primarily use `/local-scratch` and avoid the `/home/<user_id>` directory.

2. OSG's StashCache. To use this service, data should placed either in `/collab/user/<user_id>` or 
`/collab/project/snowmass21`. This method is recommended for input files larger than 1 GB each or 10 GB total from all input data. The recommended upper limit for the output files to be transfered back from the remote node is 10 GB per job. Users can use the stashcp tool to transfer data from their `/collab` space only to the remote host. You can insert the following command in your execution script to transfer data from `/collab/user/<user_id>` to the local
directory on the remote worker node where your job is running:

        module load stashcache
        stashcp /osgconnect/collab/user/<user_id>/<input_file> .
        
To transfer data back to your collab space from the remote node run the following command in your execution script:

        stashcp <output_file> stash:///osgconnect/collab/user/<user_id>/<output_file>

3. If the filesize of each input dataset exceeds 10 GB then an alternative method for transfers is the GridFTP protocol using the gfal-copy tool. Please reach out for a consultation to discuss if your workflow can benefit from access to a GridFTP door.

4. Transfers over HTTP. Files stored in the shared namespace, `/collab/project/snowmass21` are public and also accessible via HTTP. To access datta there you can use linux tools like wget as shown in the following example:

        wget http://stash.osgconnect.net/collab/project/snowmass21/<file_name>

You can insert such command in your execution script to download datasets on the remote worker node where your job is running. Alternatively, you can declare those files inside your HTCondor submission script as follows:

        transfer_input_files = http://stash.osgconnect.net/collab/project/snowmass21/<file_name>

HTTP based transfers are best for filesizes up to 1GB.

