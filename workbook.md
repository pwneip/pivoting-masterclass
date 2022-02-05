# Pivoting Workshop
## Lab Hosts
167.172.227.234
68.183.145.71 
167.71.88.196 
165.22.40.199 
68.183.148.43 
157.245.12.197 
157.245.15.122 
167.71.254.22 
157.245.4.40 
134.209.33.126 


## workshop progress tracker
https://workshop.threatsims.com


## network map

![Network Diagram](images/PivotingWorkshopLab.png)

## Let's Tunnel

### bastion host - flag 0
SSH to the bastion host on a non-standard port.  Your bastion host is the one assigned to you from the above list of lab hosts.  Throughout this workshop using 'man ssh' and google are very useful.

User: bastion

Password: bastion

SSH Port: 2222

<details>
  <summary>hint</summary>
use -p <port> to specify a non-standard port
</details>
<details>
  <summary>Tip</summary>
use -o StrictHostKeyChecking=no to streamline logging in, but this is bad opsec for known good hosts
</details>
<details>
  <summary>Solution</summary>
ssh -p 2222 bastion@<host> -o StrictHostKeyChecking=no
</details>

### Web - flag 1
Since we cannot browse directly to this website as it is on a private IPv4 address we must create a forward tunnel to it.

Browse to http://10.199.2.120/

<details>
  <summary>hint</summary>
use -L <port>:<Destination IP>:<port> for a forward tunnel
</details>
<details>
  <summary>Tip</summary>
use -D <port> to create a dynamic SOCKS5 proxy
</details>
<details>
  <summary>Solution</summary>
ssh -p 2222 bastion@<host> -o StrictHostKeyChecking=no -L8081:10.199.2.120:80

curl 127.0.0.1:8081

OR

ssh -p 2222 bastion@<host> -o StrictHostKeyChecking=no -D9050

curl -x socks5h://localhost:9050 http://10.199.2.120

</details>


### Pivot-1 - flag 2
Just like with the web challenge create a forward tunnel to the pivot server

IP: 10.212.243.13

User: tyler

Pass: fightclub

<details>
  <summary>hint</summary>
use -L <port>:<Destination IP>:<port> for a forward tunnel
</details>
<details>
  <summary>Bonus Tip</summary>
use -J <user>@<host>:<port> to specify a Jump Host that you will SSH through, a forward tunnel is not needed if using the -J option
</details>
<details>
  <summary>Solution</summary>
ssh -p 2222 bastion@<host> -o StrictHostKeyChecking=no -L2223:10.212.243.13:22

ssh -p 2223 tyler@127.0.0.1 -o StrictHostKeyChecking=no

OR

ssh -J bastion@<host>:2222 tyler@10.212.243.13 

</details>




### beaconing - flag 3
Something is Beaconing to the pivot on port 58671-58680 to ip 10.112.3.199, can you tunnel it back?

This will be your first reverse tunnel and you will need something to catch the beacon, may I suggest nc or ncat.

NOTE: IT IS THE SAME ON EACH PORT ONLY USE ONE PORT AND REMOVE YOUR TUNNEL WHEN YOU ARE DONE

<details>
  <summary>hint</summary>
use -R <Remote Host IP>:<port>:<Local Destination IP>:<port> for a reverse tunnel
</details>
<details>
  <summary>Bonus Tip</summary>
use -J <user>@<host>:<port> to specify a Jump Host that you will SSH through, a forward tunnel is not needed if using the -J option
</details>
<details>
  <summary>Solution</summary>
ssh -p 2222 bastion@<host> -o StrictHostKeyChecking=no -L2223:10.212.243.13:22

ssh -p 2223 tyler@127.0.0.1 -o StrictHostKeyChecking=no -R10.112.3.199:58671:127.0.0.1:58671

nc -klvp 58671

OR

ssh -J bastion@<host>:2222 tyler@10.212.243.13 -R10.112.3.199:58671:127.0.0.1:58671

nc -klvp 58671
</details>


### random beacon - flag 4
This is just like the last one except that you have to be a lot quicker.  I would recommend setting up your local 

Connect to ip: 10.112.3.88 port: 7000, a beacon awaits you, but you have to be quick


<details>
  <summary>hint</summary>
use -R <Remote Host IP>:<port>:<Local Destination IP>:<port> for a reverse tunnel
</details>
<details>
  <summary>Bonus Tip</summary>
You can use -D <port> again for dynamic
</details>
<details>
  <summary>Solution</summary>
Forward Tunnels

ssh -p 2222 bastion@<host> -o StrictHostKeyChecking=no -L2223:10.212.243.13:22

ssh -p 2223 tyler@127.0.0.1 -o StrictHostKeyChecking=no -D9050 -L7000:10.112.3.88:7000

nc 127.0.0.1 7000

Dynamic tunnels

ssh -p 2223 tyler@127.0.0.1 -o StrictHostKeyChecking=no -D9050

ncat --proxy 127.0.0.1:9050 --proxy-type socks5 10.112.3.88 7000


On pivot 1

~C
-R10.112.3.199:XXX:127.0.0.1:8000


Locally

nc -klvp 8000

</details>


