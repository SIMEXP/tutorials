Containerized technology is one of the major change in scientific world.
It allows reproducible, large-scale and burden-free environment setup for science.
In the mean-time, jupyter notebook provides a interactive coding session and easy way to work on python environment.
You will learn how to launch your favourite notebook on a remote server (from CRIUGM, or computecanada) using a specialized container technology called [singularity](https://singularity.lbl.gov/)

### Connect to the desired server

1. open a cmd prompt

2. connect on `pin` (passwordless authentification is assumed)
```
ssh pin
```

### Upload your data in the server

1. upload he notebooks
```
rsync -rlt --info=progress2 <my_local_file> pin.criugm.qc.ca:~/path/where/you/want/<my_remote_file>
```

1. upload the needed database
```
rsync -rlt --info=progress2 <my_local_database> pin.criugm.qc.ca:~/path/where/you/want/<my_remote_database>
```

### Launch the container

1. go to `/data/cisl/CONTAINERS`
```
cd /data/cisl/CONTAINERS

```
2. run the singularity image
```
singularity run -B user:/run/user tensorflow-latest-py3.simg --port=7777
```

### work on the notebook remotely !

1. open a new command prompt

2. Create a ssh tunnel so you can work on your browser locally (even if it is running remotely)
```
ssh -L 7777:localhost:7777 ltetrel@pin.criugm.qc.ca
```

3. You can now just