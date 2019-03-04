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
            64 bytes from 10.2.2.10: icmp_seq=1 ttl=62 time=1.35 ms
            64 bytes from 10.2.2.10: icmp_seq=2 ttl=62 time=1.32 ms
            ^C
            --- 10.2.2.10 ping statistics ---
            2 packets transmitted, 2 received, 0% packet loss, time 1001ms
            rtt min/avg/max/mdev = 1.322/1.340/1.358/0.018 ms
        ```
    
    * Depuis server1 :
        ```bash
            ping 10.2.1.10

            PING 10.2.1.10 (10.2.1.10) 56(84) bytes of data.
            64 bytes from 10.2.1.10: icmp_seq=1 ttl=62 time=1.13 ms
            64 bytes from 10.2.1.10: icmp_seq=2 ttl=62 time=1.21 ms
            ^C
            --- 10.2.1.10 ping statistics ---
            2 packets transmitted, 2 received, 0% packet loss, time 1002ms
            rtt min/avg/max/mdev = 1.132/1.172/1.213/0.053 ms
        ```

    * **Pourquoi router1 ne peut pas ping server1 ?**

        * Le router1 ne peut pas ping server1 car 
    

### 3. Visualisation du routage avec Wireshark

* Capture de net2
    * [Voir capture.pcap](https://github.com/lucasconsejo/CCNA2-TPs/tree/master/tp-2/pcap/capture.pcap)

      ![Wireshark](https://github.com/lucasconsejo/CCNA2-TPs/blob/master/tp-2/screen/capture.png)

* Capture de net12
    * [Voir capture2.pcap](https://github.com/lucasconsejo/CCNA2-TPs/tree/master/tp-2/pcap/capture2.pcap)

      ![Wireshark](https://github.com/lucasconsejo/CCNA2-TPs/blob/master/tp-2/screen/capture2.png)





## II. NAT et services d'infra

### 1. Mise en place du NAT

* Router1 peut joindre internet - Test
    ```bash
        curl 216.58.201.227

        <HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
        <TITLE>301 Moved</TITLE></HEAD><BODY>
        <H1>301 Moved</H1>
        The document has moved
        <A HREF="http://www.google.com/">here</A>.
        </BODY></HTML>
    ```
* Mise en place des interfaces : 
    
    * NAT
    ```bash
        cat /etc/sysconfig/network-scripts/ifcfg-enp0s3

        ...
        ZONE=public
    ```

    * net1
    ```bash
        cat /etc/sysconfig/network-scripts/ifcfg-enp0s8

        ...
        ZONE=public
    ```

    * net12
    ```bash
        cat /etc/sysconfig/network-scripts/ifcfg-enp0s9

        ...
        ZONE=internal
    ```

    * Activer le NAT

    ```bash
        sudo firewall-cmd --add-masquerade --zone=public --permanent
        success

        sudo firewall-cmd --reload
        success
    ```
    * Ajouter une route aux autres machines
        * router2 / client1
        ```bash
            cat /etc/sysconfig/network-scripts/ifcfg-enp0s9

            ...
            GATEWAY=10.2.12.2

            sudo ifdown enps09
            sudo ifup enp0s9
        ```

        ```bash
            cat /etc/resolv.conf

            # Generated by NetworkManager
            search tp2.b2
            nameserver 8.8.8.8
        ```
### 2. DHCP server

* client1

    * Rename 
        ```bash
            sudo hostname dhcp-server.net1.b2

            cat /etc/hosts

            127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
            ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
            10.2.1.10   localhost dhcp-server.net1.b2
        ```

    * installation dhcp 

        ```bash
            sudo yum install -y dhcp
        ```

    * Mettre la conf dans dhcpd.conf

    * Démarrage du server dhcp

        ```bash
            sudo systemctl start dhcpd
        ```

**Clone de la vm**
* client2
    * Rename 
        ```bash
            sudo hostname client2.net2.b
        ```

    * Config interface 
        ```bash
            cat /etc/sysconfig/network-scripts/ifcfg-enp0s8
            
            TYPE=Ethernet
            BOOTPROTO=dhcp
            NAME=enp0s8
            DEVICE=enp0s8
            ONBOOT=yes

            sudo ifdown enp0s8
            sudo ifdup enp0s8
        ```





### 2. Routage statique
### 3. Visualisation du routage avec Wireshark  


## Auteur
Rédigé par [Lucas Consejo](https://github.com/lucasconsejo) - Etudiant Ingésup B2B [Ynov Bordeaux](https://www.ynov.com/)