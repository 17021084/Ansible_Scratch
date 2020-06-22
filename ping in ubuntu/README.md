# Ping  with SSH
How to ping with ssh from virtual Ubutu master to Client
## Prerequired
* Docker
* Ubuntu image (if havent, ``` docker pull ubuntu```)

## Let do it

Let get 2 Ubuntu virtual machine
1. ansible_master 
    ```bash
    docker run -it --name ansible_master ubuntu
    ```
2. normalos
    ```bash
    docker run -it --name normalos ubuntu
    ```

_if you want accecss bash in running container_ 
``` docker attach < container name > ``` or ``` docker exec -it < containerid > bash ```

### Packages 
first of all: Let update ubuntu ``` apt update```

1. ansible_master
    1. python (v2)
    2. ansible
    3. openssh-client
    4. vim
    5. iputils-ping
command : ``` apt install python ansible openssh-client vim iputils-ping -y ``` 

<br>

2. normalos
    1. ssh
    2. vim
    
command : ``` apt install vim ssh -y ``` 
<br>_option ```-y``` mean "always say yes"_

### Get Ipv4
By default,when run a images, it will belong of brigde network 
``` docker network inspect bridge ```
<br>
Other way: ``` docker inspect < container id>```

### Configuration
1. ansible_master
* Genarate key pair
``` ssh-keygen```
* Send public key for remote (normalos) 
``` ssh-copy-id  <username>@ipv4 ```

* config hosts ( other name inventory ) in ``` /etc/ansible/hosts``` add in end of file 
``` bash
[machine]
<ipv4 of normalos>
#example 172.17.0.3
```

2. normalos
* Set password for virtual machine  ``` passwd root ```
* Turn on ```PermitRootLogin``` in ```/etc/ssh/sshd_config```.  
open this file by ```vim``` and set ``` PermitRootLogin yes``` save and quit ```:wq```

* start ssh ``` service ssh start```

# Main concept
1. Test connection via ```ping```.
 from  ansible_master ping to normarlos ``` ping <ipv4 of normal os> ```

2. Remote normalos via ```ssh```. <br>
Make sure in remoted machine. ```ssh``` service is opening.
```ssh  <username>@<ipv4 of normalos>```
ex: ipv4 of normalos is ```172.17.0.3``` :  ```ssh  root@172.17.0.3``` <br> 
_logout remote :```logout```_

3. Ping via ansible :
``` ansible -m ping <ipv4 normalos>```
ex: ipv4 is 172.17.0.3 then : ``` ansible -m ping 172.17.0.3 ```<br>
If results is 
``` bash
172.17.0.3 | SUCCESS =>{
    "changed": false,
    "ping":"pong"
}
``` 
this means success

