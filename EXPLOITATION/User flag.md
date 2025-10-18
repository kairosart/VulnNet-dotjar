#Attacking_machine 
As there's a 22 port open, with the new credentials you got with John the Ripper, connect via SSH to the MACHINE. 

```
ssh jdk-admin@<MACHINE IP>
```

![[Screenshot_2025-10-17_06-06-16.png]]

## Gettin user's flag

#SSH_conection 
1. List the files.

```
ls
```

![[Screenshot_2025-10-17_06-21-29.png]]

2. Get the flass from the file `user.txt`. 
```
cat user.txt
```

> [!Question] What is the user flag? (user.txt)
> THM{1ae87fa6ec2cd9f840c68cbad78e9351}

**Next step:** [[User's privileges]]




