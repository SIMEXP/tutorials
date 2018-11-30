This document is intended to guide you through all the process of getting used to work with compute-canada.

Compute-canada is a provider of HPCs (High performance computer) that allows you to achieve massive computation speed. 
The HPCs are available accross all Canada and free for academia, so you should use it !

The first thing to do is to connect to their server. 
For that, you will need a compute-canada account : 
(1) Go to the login page : https://ccdb.computecanada.ca/security/login
(2) Click on 'register' and enter your personnal information. 
(for simexp, institution is Udem, department is Psychology and you should ask for the reference number for your sponsor)
(3) Once your registering is validated, you can start using their server !

Now if you want to log-in to their server:
(1) Open a prompt (ctrl+alt+t)
(2) Type : `ssh <your_username>@cedar.computecanada.ca`
(3) At this point, you will be asked to provide your password. If you don't want to always provide a password, you can use ssh keys to identify yourself :
	(3a) Generate a public/private key-pair in ~/.ssh `ssh-keygen -t rsa` (NOTE: you should never give your private key, if anyone has it he can act as yourself)
	(3b) Press enter until it finish to ask you questions
	(3c) Copy your public key to their server `ssh-copy-id <you_username>@cedar.computecanada.ca`

When using HPCs, you have to tell him how to interact between you and your programm. 
Execution of one program is called a job and you will require to create a job script to interact with the HPCs.
One example of such a script is 
