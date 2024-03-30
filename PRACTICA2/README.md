# Práctica 2 :triangular_flag_on_post:
### Integrantes
Carnet | Nombre |
|-----|-----|
|201700339 | Luis Fernando Sánchez Soto |
|201902502 | Carlos Eduardo Soto Marroquin |
|201709282 | Carlos Javier Martínez Polanco |


## Topologia :computer:
![topologia](images/topologia.png "Topologia")

## Configuraciones de VLAN e interfaces de VLAN
##### VLANs a crear: 
  - CORPORATIVO - 63
  - VENTAS - 13
  - DISTRIBUCIÓN - 23

##### Las interfaces de VLAN por configurar, son las siguientes:
1. CORPORATIVO
   
Sector  | Dirección de Red |
|-----|-----|
|RRHH      | 192.168.73.1 con máscara 255.255.255.0 |
|SOPORTE   | 192.168.83.1 con máscara 255.255.255.0 |
|IT        | 192.168.93.1 con máscara 255.255.255.0 |

2. VENTAS
   
Sector  | Dirección de Red |
|-----|-----|
|VENTAS    | 1.0.0.0 con máscara 255.0.0.0 |

3. DISTRIBUCIÓN
   
Sector  | Dirección de Red |
|-----|-----|
|DISTRIBUCION  | 2.2.2.0 con máscara 255.0.0.0 |


#### Configuraciones de IP
![ip_conf](images/ipconfig.png "IP Config")


#### Configuraciones de Switch
##### SW1
```
enable
configure terminal
no ip domain lookup
hostname SW1

vlan 63
name CORPORATIVO
exit
vlan 13
name VENTAS
exit
vlan 23
name DISTRIBUCION
exit

interface range FastEthernet0/3 - 5
switchport mode access
switchport access vlan 63
no shutdown
exit

interface range FastEthernet0/1 - 2
no shutdown
channel-group 1 mode passive
exit

interface Port-channel1
switchport mode trunk
switchport trunk allowed vlan 13,63,23
no shutdown
exit

do write
```

##### MSW1
```
no ip domain lookup
hostname MSW1

vlan 63
name CORPORATIVO
exit
vlan 13
name VENTAS
exit
vlan 23
name DISTRIBUCION
exit

interface range FastEthernet0/1 - 2
no shutdown
channel-group 1 mode active
exit

interface Port-channel1
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan 13,63,23
no shutdown
exit

interface FastEthernet0/3
no shutdown
no switchport
ip address 1.0.0.2 255.0.0.0
exit

interface vlan 63
ip add 192.168.73.1 255.255.255.0
no shutdown
exit

interface vlan 13
ip add 1.0.0.2 255.0.0.0
no shutdown
exit

ip routing
router eigrp 100
network 2.0.0.0 0.255.255.255
network 192.168.73.0 0.0.0.255
network 1.0.0.0 0.255.255.255

exit

```



##### MSW2
```
no ip domain lookup
hostname MSW2

vlan 63
name CORPORATIVO
exit
vlan 13
name VENTAS
exit
vlan 23
name DISTRIBUCION
exit

interface range FastEthernet0/1 - 2
no shutdown
channel-group 1 mode active
exit

interface Port-channel1
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan 63,23
no shutdown
exit

interface FastEthernet0/3
switchport mode access
switchport access vlan 23
no shutdown
exit


interface vlan 63
ip add 192.168.83.1 255.255.255.0
no shutdown
exit

interface vlan 23
ip add 2.0.0.2 255.0.0.0
no shutdown
exit

ip routing
router ospf 84
network 1.0.0.0 0.255.255.255 area 0
network 192.168.83.0 0.0.0.255 area 0
network 2.0.0.0 0.255.255.255 area 0

exit
```


##### MSW3
```
no ip domain lookup

vlan 63
name CORPORATIVO
exit
vlan 13
name VENTAS
exit
vlan 23
name DISTRIBUCION
exit

hostname MSW3

interface range FastEthernet0/1-2
no shutdown
channel-group 2 mode active
exit

interface Port-channel2
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan 13,63,23
no shutdown
exit

interface FastEthernet0/3
no shutdown
switchport mode access
switchport access vlan 13
exit

interface FastEthernet0/4
no shutdown
switchport mode access
switchport access vlan 23
exit

interface vlan 63
ip address 192.168.53.1 255.255.255.0
no shutdown
exit

interface vlan 13
ip add 1.0.0.1 255.0.0.0
no shutdown
exit

interface vlan 23
ip add 2.0.0.1 255.0.0.0
no shutdown
exit

ip routing

router eigrp 100
network 2.0.0.0 0.255.255.255
network 192.168.53.0 0.0.0.255
network 1.0.0.0 0.255.255.255
exit

router ospf 64
  network 1.0.0.0 0.255.255.255 area 0
  network 192.168.53.0 0.0.0.255 area 0
  network 2.0.0.0 0.255.255.255 area 0
```


##### SW3
```
no ip domain lookup
hostname SW3

vlan 63
name CORPORATIVO
exit
vlan 13
name VENTAS
exit
vlan 23
name DISTRIBUCION
exit

interface FastEthernet0/3
no shutdown
switchport mode access
switchport access vlan 63
exit

interface range FastEthernet0/1-2
no shutdown
channel-group 2 mode passive
exit

interface Port-channel2
switchport mode trunk
switchport trunk allowed vlan 13,63,23
no shutdown
exit

```


##### SW2
```
enable
configure terminal
no ip domain lookup
hostname SW2

vlan 63
name CORPORATIVO
exit
vlan 13
name VENTAS
exit
vlan 23
name DISTRIBUCION
exit

interface range FastEthernet0/3 - 4
switchport mode access
switchport access vlan 63
no shutdown
exit

interface range FastEthernet0/1 - 2
no shutdown
channel-group 1 mode passive
exit

interface Port-channel1
switchport mode trunk
switchport trunk allowed vlan 63,23
no shutdown
exit

do write

```


## Protocolos para las 3 sucursales
##### Las sucursales por configurar, son las siguientes:
1. Retalhuleu
2. Huehuetenango
3. Sololá
   
Punto inicial  | Punto final | Protocolo |
|-----|-----|-----|
|Retalhuleu      | Huehuetenango | EIGRP |
|Huehuetenango   | Sololá        | OSPF |


#### EIGRP - MSW1, MSW2:

```
enable 
conf ter

ip routing
router eigrp 1
network 1.0.0.0
network 192.168.73.0
network 192.168.83.0
no auto-summary

exit
exit
wr
```


#### OSPF - MSW3, MSW2:
```
enable 
conf ter

ip routing
router ospf 2
network 2.0.0.0 0.0.0.255 area 2
network 192.168.83.0 0.0.0.255 area 2
network 192.168.93.0 0.0.0.255 area 2

exit
exit
wr
```