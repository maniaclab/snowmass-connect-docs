# Getting Started

## Obtain an account

In order to access the Snowmass21 Connect node you will need to sign up for an account.
To sign up, visit [https://connect.snowmass21.io](https://connect.snowmass21.io). You will need to authenticate via CILogon to your institutional sign in page 
before you proceed. One your membership request is approved you will be notified by email. 

Important steps:

1. You need to upload your ssh-keys following the instructions on the portal. The process will 
create your home directory on the Snowmass submit node and grant you access via passwordless ssh.
2. You must use an insitutional email when you sign up. Applications using personal email addresses will be rejected. 

In order to submit jobs to the OSG you should also follow up - once your account request is approved - with a request to be added to a particular 
subgroup: snowmass21.energy or snowmass21.cosmic. An email back will confirm your addition to your requested subgroup project. To do that navigate to the groups 
section in the portal here: [https://snowmass21.ci-connect.net/groups](https://snowmass21.ci-connect.net/groups) and select a particular subgroup to join.

## Login to the Submit Node

Once you upload your ssh-keys in the Snowmass21 Connect portal and your account request has been processed and approved, it takes a few minutes for your home directory 
to be created. You can then connect to the login node via passwordless ssh as:

`ssh <user_id>@login.snowmass21.io` 

You can find your `<user_id>` from your profile on the Snowmass21 Connect portal. The login node is also a submission node for 
jobs to the Open Science Grid. Upon login you will land in your home directory `/home/<user_id>`. Your home 
directory has 50GB of quota. Use your home directory to store job submission files, scripts and code. Do **not** store large files 
(larger than 300 MB) in your home directory for the purpose of submissions to the OSG.

If you need an interactive environment to support export of graphics back to your machine, you would need to use the -X or -Y flag in your ssh command:

`ssh -X <user_id>@login.snowmass21.io` 

You would also need to make your terminal X11 compatible. For machines running MacOS a useful tool can be found [here](https://www.xquartz.org/)

## Manage your account

You can come back to the portal if you need to change your institutional affiliation, email, join another subgroup or upload additional ssh-keys to your account. Account management is 
controlled by an automated process which will only make ssh-keys persistent in your user account if they are uploaded through the portal. 
