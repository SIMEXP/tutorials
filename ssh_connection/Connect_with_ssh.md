To connect remotly to a server, the standard way to do it is using the ssh protocol.
It uses public-key cryptography algorithms with the idea that a mathematical function can encode information with a form of password.
And no one can decode the information but just the one who has the password (depending on prime numbers).
This is how you can connect to a server, ther server will autehentificate yourself by sending a message,
and if you can prove that it is indeed you (by decoding a message) then you can connect to that server.
The passwords used to authentificate you are called encryption key-pair : a public one (everyone has access to it) and a 
private one (nobody but you should know it).

Many application derives from this algorithm :
- Connect to your mail acount, play your favorite game online !
- Paying online
- Send encrypted informations
- Blockchain technology (although it uses hashing algos)

### Create encription keys
1. Open a prompt (ctrl+alt+t)
2. Create your keys

``ssh-keygen -t rsa``

3. Skip all the steps (press Enter)
4. You have now your keys in `~/.ssh` and you can open them to see what they look like
(Note: **Never give your private key called id_rsa !** )

### Connect to the server
1. 

`ssh <you_username>@<server_name>`

2. Create a fingerprint
3. Provide your password

### Automatic authentification
1. Send your public key to the server

`ssh-copy-id <you_username>@<server_name>`

2. When you will log-in to the server, at this point you are not supposed to be ask for a password

`ssh <your_username>@cedar.computecanada.ca`

3. You can check the file `~/.ssh/authorized_keys`, the file should match your public key (.pub)

Current available servers are :
stark (UNF), magma (UNF), thuya (Processing room), pin (processing room), sapin (processing room)
