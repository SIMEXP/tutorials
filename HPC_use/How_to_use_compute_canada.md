[Compute-canada](https://www.computecanada.ca/home/) is a provider of HPCs that allows you to achieve massive computation speed. 
The HPCs are available accross all Canada and free for academia, so you should use it !
Most of this tutorial was highly based from the [official website](https://docs.computecanada.ca/wiki/Running_jobs).

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

Though it is not recomended for performance purpose, you can log-in with a GUI by using the x11 forwarding option `ssh -Y` to connect to nodes that enables this feature.

## Submit a simple serial job

When using HPCs, you have to tell him how to interact between you and your programm. 
Execution of one program is called a *job* and you will require to create a *job script* to interact with the HPCs.
The HPC uses a *job scheduler* called [SLURM](https://slurm.schedmd.com/) to decide when and where the job will be run using the job script.
A serial job is not parrallelized, and is the simplest type of job.

1. Create a simple job script `simple_job.sh` that will output a sentence

```
#!/bin/bash
#SBATCH --time=00:01:00
#SBATCH --account=def-pbellec
echo 'Hello HPC world !'
sleep 5s
```
`#SBATCH` specify what options you want to give to SLURM.
You can add more informations like the job name `--job-name=test`, the output `test-%J.out`...

2. Transfer this file with rsync in the server (your folder was created when you registered)
You can also use [sftp](https://docs.computecanada.ca/wiki/Transferring_data) if you want to encrypt what you are sending
```
rsync -rlt --progress simple_job.sh cedar.computecanada.ca:~/projects/def-pbellec/<user_name>/
```
A best practice is to use your home directory inside the lab group (`def-xxx`) like above. If you store data in your root directory at the location of `~`, you will run out of memory fast because [you have just 47GB in there](https://docs.computecanada.ca/wiki/Storage_and_file_management).

3. Submit the job script with SLURM
```
sbatch simple_job.sh
```

4. To check the status of the job in the queue (time remaining, finish status etc..) you can type
```
squeue -u <user_name>
```

5. When it is done, the output will be available in a file called `slurm-<id_of_job>
.out`

## Launch a parrallelized containerized app

[Docker](https://docs.docker.com/) is a common and powerfull tool to bundle or "containerize" application into a virtual environment. This will help you to deploy easilly your work, without worrying about the environment where it will be used.
You can't use docker on HPCs because you need admin rights to run it, but [singularity](http://singularity.lbl.gov/) doesn't need sudo rights.

Before continuing this tutorial, you should [install singularity on your computer](https://singularity.lbl.gov/install-linux) (>=2.5.2).

1. Create a single python script `par_job.py` inside you home `~/` that will output number from *a* to *b*, every 10s
```
import sys
import time

for i in range(int(sys.argv[1]), int(sys.argv[2])):
    print(i)
    time.sleep(10) 
```
You can test it with `python 1 10` and it should output all the numbers from 1 to 10 after 100s

2. Download a container online (on [shub](https://singularity-hub.org/) so you can use it to launch your script inside it
```
singularity pull --name anaconda3.simg shub://mjstealey/anaconda3
```

3. Test your script inside the container
```
singularity --quiet exec anaconda3.simg python par_job.py 1 10
```

By default, singularity will mount your home inside the container. You can check that `par_job.py` is indeed inside the container
```
singularity --quiet shell anaconda3.simg
ls
```

4. Transfer the singularity image `anaconda3.simg` from your computer to cedar `~/project/rrg-pbellec/<user_name>/`
and create in the home folder a file `params` that will be used later
```
1 10
11 20
21 30
31 40
41 50
51 60
61 70
71 80
81 90
91 100
```

5. We will submit a whole batch of job with just one script `simple_ar_job.sh`. This will allows us to run our application in parrall through many nodes on computecanada.
```
#!/bin/bash
#SBATCH --time=00:20:00
#SBATCH --account=rrg-pbellec
#SBATCH --array=1-10

PARAMS=$(cat params | head -n $SLURM_ARRAY_TASK_ID| tail -n 1)
echo $PARAMS

singularity --quiet exec -B ~/projects/rrg-pbellec/<user_name>/:/scripts anaconda3.simg python /scripts/par_job.py ${PARAMS[0]} ${PARAMS[1]}
```
The line `#SBATCH --array=1-10` tells you that this is a [job array](https://docs.computecanada.ca/wiki/Running_jobs#Array_job) and you specify here that you will run 10 parrallel jobs. Using `--array=1-10%2` tells you that you want no more than 2 jobs running in parrallel, `--array=1-10:2` is equivalent to `--array=1,3,5,7,9`.

`PARAMS=$(cat params | head -n $SLURM_ARRAY_TASK_ID| tail -n 1)` is used to read all the parameters that you want to pass to the python script from the file `params`.

The option `singularity --quiet exec -B ~/projects/rrg-pbellec/<user_name>/:/scripts` allows you to mount the directory on your host `~/projects/rrg-pbellec/<user_name>/` to the directory on the container `/scripts`.

6. Now you can submit the script to SLURM !

7. When your jobs is running, check the process for one job in one of the node by running
```
srun --jobid <job_id> --pty htop -u <user_name>
```
`srun` will allow you to run something (in our case `htop`) in parrallel.

8. When the jobs are finished, check the log and all the `slurm-<jobid>.out`
```
seff <job_id>
```

It is possible to allow slack to send you notifications when a job is running, finished etc.. 
First create a mail in slack in `preferences` under `messages and media` section.
Then, you can use the provided email address to let SLURM send you notifications in slack (it will be sent by the *slackbot*).
Just insert the following in your `.sh` job script
```
#SBATCH --mail-user=XXXX@simexp.slack.com 
#SBATCH --mail-type=BEGIN
#SBATCH --mail-type=END
```

<img src="slackMail.png" width="300">
