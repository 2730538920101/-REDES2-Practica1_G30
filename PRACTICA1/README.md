<h1> <center> REDES 2 Practica 1 </center></h1>

Estudiantes:

<ul>
    <li>201700339 - Luis Fernando Sánchez Soto</li>
    <li>201902502 - Carlos Eduardo Soto Marroquin</li>
    <li>201709282 - Carlos Javier Martínez Polanco</li>
</ul>


<table>
    <thead>
        <tr>
            <td>Escenario</td>
            <td>Protocolo - Spanning-Tree</td>
            <td>Red Primaria</td>
            <td>Red Basicos</td>
            <td>Red diversificado</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>1</td>
            <td>PVST</td>
            <td>26.89s</td>
            <td>27s</td>
            <td>27s</td>
        </tr>
        <tr>
            <td>2</td>
            <td>Rapid PVST</td>
            <td>2.62s</td>
            <td>2.54s</td>
            <td>2.30s</td>
        </tr>
    </tbody>
</table>

<p align="justify">Se escogio el rapid pvst, ya que la convergencia es un 90% mas rápida</p>

### Configuraciones:

```bash
SW1_30 (vtp server):

enable
configure terminal
hostname SW1_30
vtp domain grupo30
vtp password redes2grupo30
vtp version 2
vtp mode server
vlan 13
name PRIMARIA
vlan 23
name BASICOS
vlan 33
name DIVERSIFICADO
vlan 999
name Parking_lot
no ip domain-lookup

spanning-tree mode rapid-pvst


enable secret usac
line console 0
password usac
login
line vty 0 15
password usac
login
exit
service password-encryption
banner motd $ SOLO PERSONAL AUTORIZADO! $
end
copy running-config startup-config


SW2-10_30 (vtp client):

enable
configure terminal
hostname SW12_30
vtp domain grupo30
vtp password redes2grupo30
vtp version 2
vtp mode client
no ip domain-lookup


enable secret usac
line console 0
password usac
login
line vty 0 15
password usac
login
exit
service password-encryption
banner motd $ SOLO PERSONAL AUTORIZADO! $
end
copy running-config startup-config

Troncales primer y segundo nivel:

configure terminal
interface range Fa0/1-5
switchport nonegotiate
switchport mode trunk
switchport trunk allowed vlan 13,23,33
switchport trunk native vlan 1
no shutdown
exit
interface range Fa0/6-24, g0/1-2
switchport mode access
switchport access vlan 999
shutdown
end
copy running-config startup-config


Troncales tercer nivel (SW5, SW7, SW10, SW12):

configure terminal
interface range Fa0/1-2
switchport nonegotiate
switchport mode trunk
switchport trunk allowed vlan 13,23,33
switchport trunk native vlan 1
no shutdown
exit
interface range Fa0/5-24, g0/1-2
switchport mode access
switchport access vlan 999
shutdown
end
copy running-config startup-config

Troncales tercer nivel (SW6, SW11):

configure terminal
interface range Fa0/1-2
switchport nonegotiate
switchport mode trunk
switchport trunk allowed vlan 13,23,33
switchport trunk native vlan 1
no shutdown
exit
interface range Fa0/4-24, g0/1-2
switchport mode access
switchport access vlan 999
shutdown
end
copy running-config startup-config


Modo acceso tercer nivel:

SWITCH 5 Y 12:
configure terminal
interface range Fa0/3-4
switchport mode access
switchport access vlan 13
no shutdown
end
copy running-config startup-config

SWITCH 6 y 11:
configure terminal
interface Fa0/3
switchport port-security
switchport port-security mac-address <dirección_MAC>
switchport port-security maximum 1
switchport port-security violation shutdown
switchport mode access
switchport access vlan 23
no shutdown
end
copy running-config startup-config

SWITCH 7 y 10:
configure terminal
interface range Fa0/3-4
switchport mode access
switchport access vlan 33
no shutdown
end
copy running-config startup-config


spanning-tree mode rapid-pvst
```

IP'S:

```bash
192.168.13.2
192.168.13.4
192.168.23.2
192.168.33.2
192.168.33.3
192.168.33.4
192.168.33.5
192.168.23.3
192.168.13.5
192.168.13.3
```