# Rendu TP 1 - Remise dans le bain

## I. Exploration du réseau d'une machine CentOS

* **Combien y a-t-il d'adresses disponibles dans un /24 ?**
    - Il y a 254 adresses disponibles dans un /24

* **Combien y a-t-il d'adresses disponibles dans un /30 ?**
    - Il y a 2 adresses disponibles dans un /30

* **Quelle est l'utilité d'un /30 ?**
    - I don't know :grey_question: 
    - (Moins d'adresse sur le réseau, donc il y a un meilleur contrôle :eyes:)


### Allumage et configuration de la VM
#### 1. Mise en place

* Ip statique :
    ```bash
        cat /etc/sysconfig/network-scripts/ifcfg-enp0s8

	    TYPE=Ethernet
        BOOTPROTO=static
        NAME=enp0s8
        DEVICE=enp0s8
        ONBOOT=yes
        IPADDR=10.1.1.2
        NETMASK=255.255.255.0
    ```
    ```bash
        cat /etc/sysconfig/network-scripts/ifcfg-enp0s9

        TYPE=Ethernet
        BOOTPROTO=static
        NAME=enp0s9
        DEVICE=enp0s9
        ONBOOT=yes
        IPADDR=10.1.2.2
        NETMASK=255.255.255.252
    ```

* Nom de domaine :

    ```bash
        sudo hostname client1.tp1.b2
    ```

* le fichier hosts de la VM :

    ```bash
        cat /etc/hosts

        127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
        ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
        10.1.1.2    localhost client1.tp1.b2
        10.1.2.2    localhost client1.tp1.b2
    ```

* Test des cartes 

    * **Expliquer le retour que vous obtenez (code retour HTTP 301)**
        - L'erreur HTTP 301 signifie que l'url recherché a été bloqué de façon permanente.
        - HTTp 301 - Moved Permanently$
        
        ```bash
            ping 10.1.1.1
    
            PING 10.1.1.1 (10.1.1.1) 56(84) bytes of data.
            64 bytes from 10.1.1.1: icmp_seq=1 ttl=128 time=0.337 ms
            64 bytes from 10.1.1.1: icmp_seq=2 ttl=128 time=0.457 ms
            --- 10.1.1.1 ping statistics ---
            2 packets transmitted, 2 received, 0% packet loss, time 1000ms
            rtt min/avg/max/mdev = 0.356/0.371/0.386/0.015 ms
        ```

        ```bash
            ping 10.1.2.1
    
            PING 10.1.1.1 (10.1.1.1) 56(84) bytes of data.
            64 bytes from 10.1.1.1: icmp_seq=1 ttl=128 time=0.337 ms
            64 bytes from 10.1.1.1: icmp_seq=2 ttl=128 time=0.457 ms
            --- 10.1.2.1 ping statistics ---
            2 packets transmitted, 2 received, 0% packet loss, time 1005ms
            rtt min/avg/max/mdev = 0.349/0.452/0.556/0.105 ms
        ```
#### 2. Basics

* **Expliquer chacune des lignes :**
    #### Routes

    ```bash
        ip route show

        10.1.1.0/24 dev enp0s8 proto kernel scope link src 10.1.1.2 metric 100
        10.1.2.0/30 dev enp0s9 proto kernel scope link src 10.1.2.2 metric 100
    ```
    - 10.1.1.0/24 => Adresse réseau
    - dev enp0s8 => Nom du device - enp0s8
    - proto kernel => la route a été installé par le kernel pendant autoconfiguration
    - src 10.1.1.2 => l'adresse source

        #### Supprimer une route

        ```bash
            sudo ip route del 10.1.2.0/30
        ```

        #### Rajouter la route

        ```bash
            sudo ip route add 10.1.2.0/30 dev enp0s9 proto kernel scope link src 10.1.2.2 metric 100
        ```

    #### Table ARP

    ```bash
        10.1.1.1 dev enp0s8 lladdr 0a:00:27:00:00:48 REACHABLE
        10.1.2.1 dev enp0s9 lladdr 0a:00:27:00:00:4c REACHABLE
    ```
    - 10.1.1.1 => Autre ip disponible sur le meme réseau
    - dev enp0s8 => Nom du device - enp0s8
    - lladdr 0a:00:27:00:00:48 => Adresse MAC
    - REACHABLE => l'adresse est accessible

        #### Vider la table ARP

        ```bash
            sudo ip neigh flush all
        ```

        #### Après avoir vidé la Table ARP
        ```bash
            10.1.2.1 dev enp0s9 lladdr 0a:00:27:00:00:4c STALE
        ```
        - 10.1.2.1 => Autre ip disponible sur le meme réseau
        - dev enp0s9 => Nom du device - enp0s9
        - lladdr 0a:00:27:00:00:4c => Adresse MAC
        - STALE => l'adresse vient d'être rajouté

    #### Capture réseau

    * SSH 1 :
    ```bash
        sudo ip neigh flush all
        sudo tcpdump -i enp0s9 -w ping.pcap

        tcpdump: listening on enp0s9, link-type EN10MB (Ethernet), capture size 262144 bytes
    ```

    * SSH 2 :
    ```bash
        ping -c 4 10.1.2.1

        PING 10.1.2.1 (10.1.2.1) 56(84) bytes of data.
        64 bytes from 10.1.2.1: icmp_seq=1 ttl=128 time=0.575 ms
        64 bytes from 10.1.2.1: icmp_seq=2 ttl=128 time=0.381 ms
        64 bytes from 10.1.2.1: icmp_seq=3 ttl=128 time=0.343 ms
        64 bytes from 10.1.2.1: icmp_seq=4 ttl=128 time=0.378 ms

        --- 10.1.2.1 ping statistics ---
        4 packets transmitted, 4 received, 0% packet loss, time 3001ms
        rtt min/avg/max/mdev = 0.343/0.419/0.575/0.092 ms
    ```

    * SSH 1 :
    ```bash
        ^C10 packets captured
        10 packets received by filter
        0 packets dropped by kernel
    ```

    [Voir ping.pcap](https://github.com/lucasconsejo/CCNA2-TPs/tree/master/tp-1/pcap/ping.pcap)

    ![Wireshark](https://github.com/lucasconsejo/CCNA2-TPs/blob/master/tp-1/screen/ping.png)

### II. Communication simple entre deux machines
#### 1. Mise en place

* Nouveau clone VM
    * Ip statique :

    ```bash
        cat /etc/sysconfig/network-scripts/ifcfg-enp0s8

	    TYPE=Ethernet
        BOOTPROTO=static
        NAME=enp0s8
        DEVICE=enp0s8
        ONBOOT=yes
        IPADDR=10.1.1.3
        NETMASK=255.255.255.0
    ```

    * Nom de domaine :

    ```bash
        sudo hostname client2.tp1.b2
    ```

    * le fichier hosts de la VM :

    ```bash
        cat /etc/hosts

        127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
        ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
        10.1.1.3    localhost client2.tp1.b2
    ```
#### 2. Basics
* **ping et ARP**

    * Client1 & Client2:
        ```bash
            sudo ip neigh flush all
        ```

    * SSH client1 1: 
        ```bash
            sudo tcpdump -i enp0s8 -w ping-2.pcap

            tcpdump: listening on enp0s8, link-type EN10MB (Ethernet), capture size 262144 bytes
        ```

    * SSH client1 2: 
        ```bash
             ping -c 4 10.1.1.3
        ```

    * SSH client1 1: 
        ```bash
            ^C10 packets captured
            10 packets received by filter
            0 packets dropped by kernel
        ```
    
    [Voir ping-2.pcap](https://github.com/lucasconsejo/CCNA2-TPs/tree/master/tp-1/pcap/ping-2.pcap)

    ![Wireshark](https://github.com/lucasconsejo/CCNA2-TPs/blob/master/tp-1/screen/ping-2.png)

* **netcat/UDP**
    
    * Sur client1 :
        ```bash
            sudo firewall-cmd --add-port=8888/udp --permanent
            success

            sudo firewall-cmd --reload
            success

            nc -u -l 8888
        ```

    * Sur client2 :
        ```bash
            nc -u 10.1.1.2 8888
        ```

    * Sur client1 2ème shell:
        ```bash
            ss -unp

            Recv-Q Send-Q      Local Address:Port                     Peer Address:Port
            0      0                10.1.1.2:8888                         10.1.1.3:50281               users:(("nc",pid=1433,fd=4))
        ```

    * Sur client2 2ème shell:
        ```bash
            ss -unp

            Recv-Q Send-Q      Local Address:Port                     Peer Address:Port
            0      0                10.1.1.3:50281                        10.1.1.2:8888                users:(("nc",pid=2035,fd=3))
        ```

    * Sur client1 3ème shell:
        ```bash
            sudo tcpdump -i enp0s8 -w nc-udp.pcap

            tcpdump: listening on enp0s8, link-type EN10MB (Ethernet), capture size 262144 bytes
            
            ^C15 packets captured
            15 packets received by filter
            0 packets dropped by kernel
        ```
    

    [Voir nc-udp.pcap](https://github.com/lucasconsejo/CCNA2-TPs/tree/master/tp-1/pcap/nc-udp.pcap)

    ![Wireshark](https://github.com/lucasconsejo/CCNA2-TPs/blob/master/tp-1/screen/nc-udp.png)

* **netcat/TCP**

    * Sur client1 :
        ```bash
            sudo firewall-cmd --add-port=8888/tcp --permanent
            success

            sudo firewall-cmd --reload
            success

            nc -l 8888
        ```

    * Sur client2 :
        ```bash
            nc 10.1.1.2 8888
        ```

    * Sur client1 2ème shell:
        ```bash
            ss -tnp

            State       Recv-Q Send-Q Local Address:Port                Peer Address:Port
            ESTAB       0      0           10.1.1.2:8888                    10.1.1.3:45716               users:(("nc",pid=1473,fd=5))
            ESTAB       0      0           10.1.2.2:22                      10.1.2.1:53344               users:(("sshd",pid=1436,fd=3))
            ESTAB       0      0           10.1.1.2:22                      10.1.1.1:53154               users:(("sshd",pid=1358,fd=3))
            ESTAB       0      0           10.1.2.2:22                      10.1.2.1:53069               users:(("sshd",pid=1284,fd=3))
        ```

    * Sur client2 2ème shell:
        ```bash
            ss -tnp

            State       Recv-Q Send-Q Local Address:Port                Peer Address:Port
            ESTAB       0      0           10.1.1.3:22                      10.1.1.1:53142               users:(("sshd",pid=1792,fd=3))
            ESTAB       0      0           10.1.1.3:45716                   10.1.1.2:8888                users:(("nc",pid=2138,fd=3))
        ```

    * Sur client1 3ème shell:
        ```bash
            sudo tcpdump -i enp0s8 -w nc-tcp.pcap
            
            tcpdump: listening on enp0s8, link-type EN10MB (Ethernet), capture size 262144 bytes
            
            ^C15 packets captured
            15 packets received by filter
            0 packets dropped by kernel
        ```
        
    
    [Voir nc-tcp.pcap](https://github.com/lucasconsejo/CCNA2-TPs/tree/master/tp-1/pcap/nc-tcp.pcap)

    ![Wireshark](https://github.com/lucasconsejo/CCNA2-TPs/blob/master/tp-1/screen/nc-tcp.png)
    
* **netcat/Firewall**

    * Sur client1 :
        ```bash
            sudo firewall-cmd --remove-port=8888/udp --permanent
            sudo firewall-cmd --remove-port=8888/tcp --permanent
            success

            sudo firewall-cmd --reload
            success

            nc -l 8888
        ```

    * Sur client2 :
        ```bash
            nc 10.1.1.2 8888
        ```

    * Sur client1 2ème shell:
        ```bash
            sudo tcpdump -i enp0s8 -w firewall.pcap

            tcpdump: listening on enp0s8, link-type EN10MB (Ethernet), capture size 262144 bytes

            ^C6 packets captured
            6 packets received by filter
            0 packets dropped by kernel
        ```
    

    [Voir firewall.pcap](https://github.com/lucasconsejo/CCNA2-TPs/tree/master/tp-1/pcap/firewall.pcap)

    ![Wireshark](https://github.com/lucasconsejo/CCNA2-TPs/blob/master/tp-1/screen/firewall.png)

### III. Routage statique simple
* Sur client1 :
    ```bash
        sysctl -w net.ipv4.ip_forward=1

        net.ipv4.ip_forward = 1
    ```

* Sur client2 :
    ```bash
        sudo ip route add 10.1.2.0/30 via 10.1.1.2 dev enp0s8

        ping 10.1.2.1

        traceroute 10.1.2.1

        traceroute to 10.1.2.1 (10.1.2.1), 30 hops max, 60 byte packets
        1  10.1.1.2 (10.1.1.2)  0.301 ms  0.174 ms  0.068 ms
        2  10.1.1.2 (10.1.1.2)  0.348 ms !X  0.212 ms !X  0.228 ms !X
    ```

## Auteur
Rédigé par [Lucas Consejo](https://github.com/lucasconsejo) - Etudiant Ingésup B2B [Ynov Bordeaux](https://www.ynov.com/) 
