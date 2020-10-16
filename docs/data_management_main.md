## Data Management


This section describes recommendations and options for transferring data to the from remote woker nodes a s part of a job submission to the OSG.

### Transferring data to and from the Snowmass21 login node
You can transfer data from external institutions to Snowmass Connect storage using any of the three following methods:

1. **scp**. For example: `scp -r <file_or_directory> <user_id>@login.snowmass.io:/collab/user/<user_id>/.` will copy a file or a directory
from your local machine to your user directory on the OSG storage. The ssh-keys used for your profile on the Snowmass Connect portal
must stored on the local machine.

2. **rsync**. For example: `rsync -avz -e "ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null" --progress test.transfer <user_id>@login.snowmass21.io:dump/` will copy the `test.transfer` file in the `/home/<user_id>/dump/` directory. If the directory
does not exist, it will be created. As in `scp` the ssh-keys used for your profile on the Snowmass Connect portal
must stored on the source machine.

3. **Globus Connect** to transfer files to OSG storage only. A guide on how to gain access to the Globus door and instructions for transfering
data to the OSG storage can be found here: [Uploading Data Instructions](globus.md)


### Data Management for OSG jobs

As disussed above, you should place their input data for processing on the Open Science Grid in `/collab/user/<user_id>` or 
`/collab/project/snowmass21`. Data can be transferred to the grid as part of an OSG job using four different methods depending on the file size.

1. HTCondor File Transfer. This method is recommended for the majority of computational workflows running on the OSG. Users can employ this method if the total size of the input data per job does not exceed 1 GB. In addition, OSG recommends that the output data per job that need to be transfered back does not exceed 1 GB as well. To enable HTCondor File transfers for your input and output data insert the following parameters anywhere in your HTCondor submit file:

        transfer_input_files = <comma separated files or directories>
        transfer_output_files = <comma separated files or directories>

2. OSG's StashCache. This method is recommended for input files larger than 1 GB each or 10 GB total from all input data. The recommended upper limit for the output files to be transfered back from the remote node is 10 GB per job. Users can use the stashcp tool to transfer data from their `/collab` space only to the remote host. You can insert the following command in your execution script to transfer data from `/collab/user/<user_id>` to the local
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

## Storage Access

This section describes the storage locations accessible by the users when connected to Snowmass login node. It also describes the means by which
users can move data in from their home institutions.

1. Home directory. As mentioned above, your home directory will have 50GB of storage  available and can
be used for scripts, submission files and small size data. Large input files for jobs on the grid should not be stored here.
2. OSG storage accebible from the login node at `/collab`. You will receive a 1TB quota for their personal user space, and there is a shared 50TB quota for the project space. Larger space allocations can be accommodated on a case-by-case basis. You should store their data in either of two subdirectories:  
    * For private user data: `/collab/user/<user_id>`  
    * For shared data among the members of the Snowmass21 collaboration:`/collab/project/snowmass21`
3. Local storage in`/scratch`. You can access that storage location for storing workflows, data and job submission scripts.

For details on Data Management on the Snowmass21 login refer to [this](data_management.md) section.
