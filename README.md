## HTCF cluster access
First thing first is to connect to **WUSM-secure** wifi. 

If you want to work through network/campus access, you need to use VPN. Download the [Cisco AnyConnect](https://software.cisco.com/download/home/283000185) app and install. In the app, type in `msvpn.wusm.wustl.edu` for connecting address, and use `accounts\<your_wustl_id>` and `<your_wustl_password>` for authentication.

Let's log in through your terminal. Type in
```
ssh <your_wustl_id>@htcf.wustl.edu
```
And type in `<your_wustl_password>` for the password prompt.

## Cluster usage
Now you in the HTCF login shell. **Do NOT execute any heavy computation in the login shell.** Instead, there are two ways to execute your computation jobs.

1. **Interactive session**: Get yourself a computation node with 8G of memory by the following command. You may change the memory quota for heavy computation.
```
srun --mem=8G --cpus-per-task=1 -J interactive -p interactive --pty /bin/bash -l
```

2. **SBATCH job**: Submit a SBATCH job to the (SLURM system)[https://htcf.wustl.edu/docs/queue/]. The SBATCH will be running in the background. It is specially useful when your program needs distributed computing or runs overnight. Here's an example of how you write a job script `sbatch_example.sbatch`. 
```
#!/bin/bash
#SBATCH --mem=1G
#SBATCH -J <job_name>
#SBATCH -o <job_name>_%A.out
#SBATCH -e <job_name>_%A.err
#SBATCH --mail-type=END,FAIL
#SBATCH --mail-user=<your_email>

echo Hello World > sbatch_example.txt
```
Then submit your job script.
```
sbatch sbatch_example.sbatch
```
After submission, check your the job progress.
```
squeue -u <your_account_ID>
```
For more sophisticated parallelization, check out the instruction on how to run (job array)[https://htcf.wustl.edu/docs/runningjobs/].

## File system
There are **3 spaces** in the HTCF cluster: 
1. **Home directory** `$HOME` or `/home/<your_wustl_id>`: This would be the place to install local libraries and packages, e.g. R Bioconductor and Python packages.
2. **Scratch space** `/scratch/mblab/<your_wustl_id>`: To create your directory, if not exists, do the following to create one for yourself! Your scratch directory will be your daily working space. You have 2TB storage capacity to dump all your project code and data. 
```
mkdir -p /scratch/mblab/<your_wustl_id>
```
Check your scratch quota:
```
beegfs-ctl --getquota --uid <your_account_id>
```
3. **Long term storage** `/lts/mblab/Crypto`: This is the data backup for all Brent lab users. Also create your own directory if not exists. Back up frequently with `rsync`:
```
rsync -aHv --progress <source_directory> <target_directory>
```

#### Notes on the file system:
1. Use **scratch** as your primary storage instead of home directory. 
2. Scratch space has a **clean-up policy**. Make sure to read the [cleaning policy](https://htcf.wustl.edu/docs/policies/#scratch-data-cleaning). Frequently back up your data to the long term storage, or use `touch` command to update date of your files (so that they don't get purged).

## Package management
Most bioinformatics tools are pre-installed as **modules**. Use `module avail <tool>` to check the availabilities, and use `module load <tool/version>` to load the module. If unavailable, install to your local, e.g. `pip install --user <tool>`.

## Additional storage by WUSTL Research Infrastructure Services (RIS)
The WashU IT RIS Storage offers some 5TB storage space for the group. Follow the steps to access the storage:
1. Mount to RIS by authenticating with your WUSTL password
```
wustl-ris mount ~/ris_mount brent
```
2. Transfer files on HTCF from/to RIS space
```
rsync -aHv ~/ris_mount/brent/Active/<src_file> <dest_dir>/
rsync -aHv <src_file> ~/ris_mount/brent/Active/<dest_dir>/
```
3. When completed, un-mount RIS
```
wustl-ris unmount ~/ris_mount
```
