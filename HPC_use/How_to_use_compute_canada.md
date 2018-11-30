[Compute-canada](https://www.computecanada.ca/home/) is a provider of HPCs that allows you to achieve massive computation speed. 
The HPCs are available accross all Canada and free for academia, so you should use it !

# Connecting to their server
The first thing to do is to connect to their server. 
For that, you will need a compute-canada account : 
1. Go to the login page : https://ccdb.computecanada.ca/security/login
2. Click on 'register' and enter your personnal information. For the lab, the insitution is udem, the department is Psychology and  you should ask for the reference number for your sponsor (Pierre B.).
3. Once your registering is validated, you can start using their server !

`ssh <your_username>@cedar.computecanada.ca`

4. If you automatically want to log-in, follow [this](https://github.com/SIMEXP/tutorials/blob/master/ssh_connection/Connect_with_ssh.md)



When using HPCs, you have to tell him how to interact between you and your programm. 
Execution of one program is called a job and you will require to create a job script to interact with the HPCs.
One example of such a script is 
