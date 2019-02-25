# Rendu TP 2 - Routage statique et services d'infra

## I. Mise en place du lab

### 1. Création des VMs et adressage IP
* Ip static :
    * client1 sans NAT:
    ```bash
        cat /etc/sysconfig/network-scripts/ifcfg-enp0s8

        TYPE=Ethernet
        BOOTPROTO=static
        NAME=enp0s8
        DEVICE=enp0s8
        ONBOOT=yes
        IPADDR=10.2.1.10
        NETMASK=255.255.255.0
    ```

    * server1 sans NAT:
    ```bash
        cat /etc/sysconfig/network-scripts/ifcfg-enp0s8

        TYPE=Ethernet
        BOOTPROTO=static
        NAME=enp0s8
        DEVICE=enp0s8
        ONBOOT=yes
        IPADDR=10.2.2.10
        NETMASK=255.255.255.0
    ```

    * router1 avec NAT:
    ```bash
        cat /etc/sysconfig/network-scripts/ifcfg-enp0s8

        TYPE=Ethernet
        BOOTPROTO=static
        NAME=enp0s8
        DEVICE=enp0s8
        ONBOOT=yes
        IPADDR=10.2.1.254
        NETMASK=255.255.255.0

        cat /etc/sysconfig/network-scripts/ifcfg-enp0s9

        TYPE=Ethernet
        BOOTPROTO=static
        NAME=enp0s9
        DEVICE=enp0s9
        ONBOOT=yes
        IPADDR=10.2.12.2
        NETMASK=255.255.255.248
    ```

    * router2 sans NAT:
    ```bash
        cat /etc/sysconfig/network-scripts/ifcfg-enp0s8

        TYPE=Ethernet
        BOOTPROTO=static
        NAME=enp0s8
        DEVICE=enp0s8
        ONBOOT=yes
        IPADDR=10.2.2.254
        NETMASK=255.255.255.0


        cat /etc/sysconfig/network-scripts/ifcfg-enp0s9

        TYPE=Ethernet
        BOOTPROTO=static
        NAME=enp0s9
        DEVICE=enp0s9
        ONBOOT=yes
        IPADDR=10.2.12.3
        NETMASK=255.255.255.248
    ```

* Hostname :
    * client1 sans NAT:
    ```bash
        sudo hostname client1.net1.b2

        cat /etc/hosts

        127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
        ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
        10.2.1.10   client1.net1.b2

    ```

    * server1 sans NAT:
    ```bash
        sudo hostname server1.tp2.b2

        cat /etc/hosts  
        
        127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
        ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
        10.2.2.10   server1.tp2.b2
    ```

    * router1 avec NAT:
    ```bash
        sudo hostname router1.tp2.b2

        cat /etc/hosts  
        
        127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
        ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
        10.2.1.254  router1.tp2.b2
        10.2.12.2   router1.tp2.b2
    ```

    * router2 sans NAT:
    ```bash
        sudo hostname router2.tp2.b2

        cat /etc/hosts  
        
        127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
        ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
        10.2.2.254  router2.tp2.b2
        10.2.12.3   router2.tp2.b2
    ```

### 2. Routage statique
* IPv4 Forwarding sur router1 & router2
    ```bash
        sudo sysctl -w net.ipv4.conf.all.forwarding=1

        cat /etc/sysctl.conf (Permanent)
        
        net.ipv4.conf.all.forwarding = 1
    ```

* Ajouter des routes :
    * router1
    ```bash
        ip route add 10.2.2.0/24 via 10.2.12.3 dev enp0s9
        systemctl restart network

        cat /etc/sysconfig/network-scripts/route-enp0s9
        
        10.2.2.0/24 via 10.2.12.3 dev enp0s9
    ```   

    * router2
    ```bash
        ip route add 10.2.1.0/24 via 10.2.12.2 dev enp0s9
        systemctl restart network

        cat /etc/sysconfig/network-scripts/route-enp0s9
        
        10.2.1.0/24 via 10.2.12.2 dev enp0s9
    ``` 
    
    * client1
    ```bash
        ip route add 10.2.2.0/24 via 10.2.1.254 dev enp0s8
        systemctl restart network

        cat /etc/sysconfig/network-scripts/route-enp0s8
        
        10.2.2.0/24 via 10.2.1.254 dev enp0s8
    ```  

    * server1
    ```bash
        ip route add 10.2.2.0/24 via 10.2.2.254 dev enp0s8
        systemctl restart network

        cat /etc/sysconfig/network-scripts/route-enp0s8
        
        10.2.1.0/24 via 10.2.2.254 dev enp0s8
    ```

* Test de ping :
    * Depuis client1 :
        ```bash
            ping 10.2.2.10

            PING 10.2.2.10 (10.2.2.10) 56(84) bytes of data.
            ^C
            --- 10.2.2.10 ping statistics ---
            2 packets transmitted, 0 received, 100% packet loss, time 1001ms
        ```
    
    * Depuis server1 :
        ```bash
            ping 10.2.1.10

            PING 10.2.1.10 (10.2.1.10) 56(84) bytes of data.
            ^C
            --- 10.2.1.10 ping statistics ---
            2 packets transmitted, 0 received, 100% packet loss, time 1000ms
        ```

### 3. Visualisation du routage avec Wireshark


## II. NAT et services d'infra

### 1. Mise en place du NAT
### 2. Routage statique
### 3. Visualisation du routage avec Wireshark


## Auteur
Rédigé par [Lucas Consejo](https://github.com/lucasconsejo) - Etudiant Ingésup B2B [Ynov Bordeaux](https://www.ynov.com/)