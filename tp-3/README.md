# Rendu TP 3 - Utilisation de matériel Cisco

## I. Manipulation de switches et de VLAN

### 1. Mise en place du lab

* Sur les 3 clients

    * ajouter une ip
        ```bash
            cat /etc/sysconfig/network-scripts/ifcfg-enp0s8

            TYPE=Ethernet
            BOOTPROTO=static
            NAME=enp0s8
            DEVICE=enp0s8
            ONBOOT=yes
            IPADDR=10.1.2.1
            NETMASK=255.255.255.0

            sudo ifdown enp0s8
            sudo ifup enp0s8
        ```
    * Changer l'hostname
        ```bash
            sudo hostname client1.lab1.tp3
        ```

    * Test de ping client1->client3
        ```bash
            ping 10.1.1.3

            PING 10.1.1.3 (10.1.1.3) 56(84) bytes of data.
            64 bytes from 10.1.1.3: icmp_seq=1 ttl=64 time=3.72 ms
            64 bytes from 10.1.1.3: icmp_seq=2 ttl=64 time=1.28 ms
            ^C
            --- 10.1.1.3 ping statistics ---
            2 packets transmitted, 2 received, 0% packet loss, time 2009ms
        ```
    
### 2. Configuration des VLANs

* Mise en place des ports en mode access

    * Sur le switch 1
        * Création de VLAN 10 
            ```bash
                conf t
                vlan 10
                name VLAN 10
                exit
            ```
        * Assigner une interface pour donner accès au VLAN
            ```bash
                interface Ethernet 0/0
                switchport mode access
                switchport access vlan 10
            ```

        * Création de VLAN 20
            ```bash
                conf t
                vlan 20
                name VLAN 20
                exit
            ```
        * Assigner une interface pour donner accès au VLAN
            ```bash
                interface Ethernet 0/1
                switchport mode access
                switchport access vlan 20
            ```

        * Configurer une interface entre les 2 Switch (mode Trunk)
            ```bash
                interface Ethernet 0/2
                switchport trunk encapsulation dot1q
                switchport mode trunk
            ```

    * Sur le switch 2
        * Création de VLAN 10 
            ```bash
                conf t
                vlan 10
                name VLAN 10
                exit
            ```
        * Assigner une interface pour donner accès au VLAN
            ```bash
                interface Ethernet 0/0
                switchport mode access
                switchport access vlan 10
            ```

        * Configurer une interface entre les 2 Switch (mode Trunk)
            ```bash
                interface Ethernet 0/1
                switchport trunk encapsulation dot1q
                switchport mode trunk
            ```
    * Verification :
        * Ping client1->client3
            ```bash
                ping 10.1.1.3
                
                PING 10.1.1.3 (10.1.1.3) 56(84) bytes of data.
                64 bytes from 10.1.1.3: icmp_seq=1 ttl=64 time=2.32 ms
                64 bytes from 10.1.1.3: icmp_seq=2 ttl=64 time=1.27 ms
                ^C
                --- 10.1.1.3 ping statistics ---
                2 packets transmitted, 2 received, 0% packet loss, time 2001ms
            ```
        * Traceroute client1->client3
            ```bash
                traceroute 10.1.1.3
                
                traceroute to 10.1.1.3 (10.1.1.3), 30 hops max, 60 byte packets
                1  10.1.1.3 (10.1.1.3)  2.256 ms !X  2.124 ms !X  4.359 ms !X
            ```
        * Ping client1->client2
            ```bash
                ping 10.1.1.2
                
                PING 10.1.1.2 (10.1.1.2) 56(84) bytes of data.
                From 10.1.1.1 icmp_seq=1 Destination Host Unreachable
                From 10.1.1.1 icmp_seq=2 Destination Host Unreachable
                From 10.1.1.1 icmp_seq=3 Destination Host Unreachable
                ^C
                --- 10.1.1.2 ping statistics ---
                4 packets transmitted, 0 received, +3 errors, 100% packet loss, time 4006ms
            ```

## II. Manipulation simple de routeurs

### 1. Mise en place du lab

### 2. Configuration du routage statique

## III. Mise en place d'OSPF

### 1. Mise en place du lab

## IV. Lab Final

### 1. Mise en place du lab
### 2. Configuration de OSPF

## Annexe 1 : NAT dans GNS3




## Auteur
Rédigé par [Lucas Consejo](https://github.com/lucasconsejo) - Etudiant Ingésup B2B [Ynov Bordeaux](https://www.ynov.com/)