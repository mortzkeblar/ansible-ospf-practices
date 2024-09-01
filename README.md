

![[.imgs/1.png]]

Configuraciones iniciales para el Mikrotik que actua como gateway hacia internet . 

``` bash

ip firewall/ nat/ add chain=dstnat action=dst-nat dst-address= 
192.168.0.107 dst-port=2222 protocol=tcp to-addresses=10.10.100.20 to-ports=22

# configuración inicial de interfaces y direcciones IP, 
ip address/ add interface=ether1 address=10.10.100.1/24
ip address/ add interface=ether2 address=10.75.246.121/30
ip address/ add interface=ether3 address=10.204.40.1/30
ip address/ add interface=ether4 address=10.103.162.105/30

## eth1 que apunta a la "WAN" se le asigno una IP por DHCP 

# verificamos las direcciones asignadas 
[admin@MikroTik] > ip address/ print 
Flags: D - DYNAMIC
Columns: ADDRESS, NETWORK, INTERFACE
#   ADDRESS            NETWORK         INTERFACE
0 D 192.168.0.107/24   192.168.0.0     ether1   
1   10.10.100.1/24     10.10.100.0     ether5   
2   10.103.162.105/30  10.103.162.104  ether4   
3   10.75.246.121/30   10.75.246.120   ether2   
4   10.204.40.1/30     10.204.40.0     ether3   
```

Configuración inicial de los router Cisco adyacentes al Mikrotik. Para poder trabajar con ansible necesitamos tener acceso SSH a cada uno de los equipos y una IP asignada, así como una ruta estatica para que ansible puede llegar a los routers desde el servidor docker. 

``` bash 

### routers phase 1 ###

# RA-1 
ena 
conf t
int g0/0
ip address 10.103.162.106 255.255.255.252 
no sh 
ip route 10.10.100.0 255.255.255.0 10.103.162.105 

hostname RA-1 
ip domain-name cisco.com
## enable ssh credentials
crypto key generate rsa modulus 1024
username ccna secret 1234
line vty 0 4
transport input ssh
login local 
exit
ip ssh version 2
ip ssh time-out 15
ip ssh authentication-retries 3
## enable local password 
enable password 1234
exit 
copy running-config star

# RB-1 
ena 
conf t
int g0/0
ip address 10.75.246.122 255.255.255.252 
no sh 
ip route 10.10.100.0 255.255.255.0 10.75.246.121 

hostname RB-1 
ip domain-name cisco.com
crypto key generate rsa modulus 1024
username ccna secret 1234
line vty 0 4
transport input ssh
login local 
exit
ip ssh version 2
ip ssh time-out 15
ip ssh authentication-retries 3
enable password 1234
exit 
copy running-config star

# RD-1 
ena 
conf t
int g0/0
ip address 10.204.40.2 255.255.255.252 
no sh 
ip route 10.10.100.0 255.255.255.0 10.204.40.1 

hostname RD-1 
ip domain-name cisco.com
crypto key generate rsa modulus 1024
username ccna secret 1234
line vty 0 4
transport input ssh
login local 
exit
ip ssh version 2
ip ssh time-out 15
ip ssh authentication-retries 3
enable password 1234
exit 
copy running-config star

# RC-1

ena 
conf t
int g0/1
ip address 10.105.29.90 255.255.255.252 
no sh 
ip route 10.10.100.0 255.255.255.0 10.105.29.89

hostname RC-1 
ip domain-name cisco.com
crypto key generate rsa modulus 1024
username ccna secret 1234
line vty 0 4
transport input ssh
login local 
exit
ip ssh version 2
ip ssh time-out 15
ip ssh authentication-retries 3
enable password 1234
exit 
copy running-config star


# SA-1

ena 
conf t
int g0/0
no switchport
ip address 10.170.131.194 255.255.255.252 
no sh 
ip route 10.10.100.0 255.255.255.0 10.170.131.193

hostname SA-1
ip domain-name cisco.com
crypto key generate rsa modulus 1024
username ccna secret 1234
line vty 0 4
transport input ssh
login local 
exit
ip ssh version 2
ip ssh time-out 15
ip ssh authentication-retries 3
enable password 1234
exit 
copy running-config star

### routers phase 2 ###

# RC-2

ena 
conf t
int g0/0
ip address 10.252.147.206 255.255.255.252 
no sh 
ip route 10.10.100.0 255.255.255.0 10.252.147.205 

hostname RC-2
ip domain-name cisco.com
crypto key generate rsa modulus 1024
username ccna secret 1234
line vty 0 4
transport input ssh
login local 
exit
ip ssh version 2
ip ssh time-out 15
ip ssh authentication-retries 3
enable password 1234
exit 
copy running-config star

# RF-1

ena 
conf t
int g0/0
ip address 10.20.20.2 255.255.255.252 
no sh 
ip route 10.10.100.0 255.255.255.0 10.20.20.1

hostname RF-1
ip domain-name cisco.com
crypto key generate rsa modulus 1024
username ccna secret 1234
line vty 0 4
transport input ssh
login local 
exit
ip ssh version 2
ip ssh time-out 15
ip ssh authentication-retries 3
enable password 1234
exit 
copy running-config star

# RD-2

ena 
conf t
int g0/0
ip address 10.238.58.126 255.255.255.252 
no sh 
ip route 10.10.100.0 255.255.255.0 10.238.58.125

hostname RD-2
ip domain-name cisco.com
crypto key generate rsa modulus 1024
username ccna secret 1234
line vty 0 4
transport input ssh
login local 
exit
ip ssh version 2
ip ssh time-out 15
ip ssh authentication-retries 3
enable password 1234
exit 
copy running-config star

# RA-1-SWC-1

ena 
conf t
int g0/0
no switchport
ip address 10.104.10.2 255.255.255.252 
no sh 
ip route 10.10.100.0 255.255.255.0 10.104.10.1

hostname RA-1-SWC-1 
ip domain-name cisco.com
crypto key generate rsa modulus 1024
username ccna secret 1234
line vty 0 4
transport input ssh
login local 
exit
ip ssh version 2
ip ssh time-out 15
ip ssh authentication-retries 3
enable password 1234
exit 
copy running-config star

# RA-3

ena 
conf t
int g0/1
ip address 10.20.10.2 255.255.255.252 
no sh 
ip route 10.10.100.0 255.255.255.0 10.20.10.1

hostname RA-3
ip domain-name cisco.com
crypto key generate rsa modulus 1024
username ccna secret 1234
line vty 0 4
transport input ssh
login local 
exit
ip ssh version 2
ip ssh time-out 15
ip ssh authentication-retries 3
enable password 1234
exit 
copy running-config star

```

> El usuario y contraseña en todos los casos son `ccna` y `1234` , para subir de privilegios es simplemente `1234` en todos los casos. 


### OSPF 
![[.imgs/2.png]]

Como podemos ver en la imagen, no tenemos una ruta directa entre RC-1 y Mikrotik, vamos a configurar OSPF en el Mikrotik (Area 0) para que poder tomar las rutas que OSPF ya esta expandiendo en los routers adyacentes. 

``` bash
interface bridge add name=lo1
ip address add interface=lo1 address=10.255.255.2/32

routing ospf instance add name=ospf-inst-1 router-id=10.255.255.2 
routing ospf area add name=backbone instance=ospf-inst-1

routing ospf interface-template add networks=10.255.255.2/32 area=backbone
routing ospf interface-template add networks=10.103.162.104/30 area=backbone
routing ospf interface-template add networks=10.75.246.120/30 area=backbone
routing ospf interface-template add networks=10.204.40.0/30 area=backbone
routing ospf interface-template add networks=10.10.10.100.0/24 area=backbone passive 

interface bridge add name=lo1
ip address add interface=lo1 address=10.255.255.3/32

routing ospf instance add name=ospf-inst-1 router-id=10.255.255.3
routing ospf area add name=backbone instance=ospf-inst-1

routing ospf interface-template add networks=10.255.255.2/32 area=backbone


```


### Phase 1 & 2 and execution
Para ejecutar el playbook, se debe dividir en fases. En base al alcanze que se tiene en la red a un determinado dispositivo, por ejemplo no podemos llegar a `SA-1` si no tenemos configurados los routers en el medio que permiten conectarnos con ese switch. Se debe usar esa dinamica para cada salto que se deba hacer, en este caso lo dividimos en 2 fases: los que estan conectados directamente al router mikrotik (y RC-1) y luego los que estan conectados a los routers de la fase 1.  

```
ansible-playbook -i hosts.yml config.yaml
```
### credentials server development 
user ssh: marco 
pass ssh: marco 
local user: root 
local root password: pnet
### SSH problems 
Se agrego el archivo `~/.ssh/config` con el siguiente contenido.
``` bash
Host *
	KexAlgorithms +diffie-hellman-group1-sha1,diffie-hellman-group14-sha1
	HostKeyAlgorithms +ssh-rsa
```

Y para ingresar a algún dispositivo se usa el comando: 
``` bash
ssh -v <user>@<IP>
```
