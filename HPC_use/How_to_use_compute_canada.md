[Compute-canada](https://www.computecanada.ca/home/) is a provider of HPCs that allows you to achieve massive computation speed. 
The HPCs are available accross all Canada and free for academia, so you should use it !

## Connecting to their server
The first thing to do is to connect to their server. 
For that, you will need a compute-canada account : 
1. Go to the login page : https://ccdb.computecanada.ca/security/login
2. Click on 'register' and enter your personnal information. For the lab, the insitution is udem, the department is Psychology and  you should ask for the reference number for your sponsor (Pierre B.).
3. Once your registering is validated, you can start using their server !
```
ssh <your_username>@cedar.computecanada.ca
```

4. If you automatically want to log-in, follow [this](https://github.com/SIMEXP/tutorials/blob/master/ssh_connection/Connect_with_ssh.md)

If you can't connect at this point, you would want to check the [server status](https://status.computecanada.ca/).

## Submit a simple job

When using HPCs, you have to tell him how to interact between you and your programm. 
Execution of one program is called a *job* and you will require to create a *job script* to interact with the HPCs.
The HPC uses a *job scheduler* to decide when and where the job will be run using the job script.

1. Create a simple job script `simple_job.sh` that will output a sentence

```
#!/bin/bash
#SBATCH --time=00:01:00
#SBATCH --account=def-pbellec
echo 'Hello HPC world !'
sleep 5s
```

2. Transfer this file with rsync in the server (your folder was created when you registered)
You can also use [sftp](https://docs.computecanada.ca/wiki/Transferring_data) if you want to encrypt what you are sending
```
rsync -rltv simple_job.sh cedar.computecanada.ca:/home/<user_name>/projects/def-pbellec/<user_name>/
```

3. Submit the job script with [SLURM](https://slurm.schedmd.com/sbatch.html)
```
sbatch simple_job.sh
```

4. To check the status of the job in the queue (time remaining, finish status etc..) you can type
```
squeue -u <user_name>
```
You can also check the running process in the node with
```
srun --jobid <job_id> --pty htop -u <user_name>
```
`srun` will allow you to run something (in our case `htop`) in parrallel.

5. When it is done, the output will be available in a file called `slurm-<id_of_job>
.out`

6. Information on finished jobs can be found by typing
```
seff <job_id>
```

You can add more informations like the job name `--job-name=test`, the output `test-%J.out`
