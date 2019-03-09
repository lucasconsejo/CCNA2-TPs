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
    * On demande une nouvelle ip
        ```bash
            dhclient -v -r           
        ```
        * L'ip devient 10.2.1.50

    * On check la route par defaut
        ```bash
            ip route show

            default via 10.2.1.254 dev enp0s8 proto static metric 100
            10.2.1.0/24 dev enp0s8 proto kernel scope link src 10.2.1.50 metric 100
        ```
### 3. NTP server

* router1 && router2:
    * Installation de chrony
        ```bash
            yum install -y chrony    
        ```
    * Config chrony
        ```bash
            cat /etc/chrony.conf   

            server 0.fr.pool.ntp.org iburst
            server 1.fr.pool.ntp.org iburst
            server 2.fr.pool.ntp.org iburst
            server 3.fr.pool.ntp.org iburst
        ```
    * Port pour NTP
        ```bash
            sudo firewall-cmd --add-port=123/udp --permanent
            success

            sudo firewall-cmd --reload
            success
        ```
    * Restart chrony
        ```bash
            sudo systemctl start chronyd
        ```
    * On verifie la synchro NTP 
        ```bash
            chronyc sources

            210 Number of sources = 4
            MS Name/IP address         Stratum Poll Reach LastRx Last sample
            ===============================================================================
            ^* x.ns.gin.ntt.net              2   6    37    24   -195us[+1319us] +/-   77ms
            ^- 6mc-a7aifk.6metric.fr         2   6    37    27  +2251us[+2251us] +/-   56ms
            ^- support4.russianbridesne>     2   6    37    32  -3564us[-3564us] +/-   47ms
            ^- net1.web.yas-online.net       3   6    37    37  -8343us[-8343us] +/-   75ms
        ```
        ```bash
            chronyc tracking

            Reference ID    : 81FA23FA (x.ns.gin.ntt.net)
            Stratum         : 3
            Ref time (UTC)  : Sat Mar 09 12:43:36 2019
            System time     : 0.001167651 seconds slow of NTP time
            Last offset     : -0.000153610 seconds
            RMS offset      : 0.001712065 seconds
            Frequency       : 1.285 ppm fast
            Residual freq   : -0.063 ppm
            Skew            : 19.912 ppm
            Root delay      : 0.143136293 seconds
            Root dispersion : 0.004816985 seconds
            Update interval : 64.8 seconds
            Leap status     : Normal
        ```

* server1:

    ***Hummmmm.... Il n'y a pas d'acces à internet sur server1 !!***

    * Config firewall
        ```bash
            sudo firewall-cmd --zone=internal --add-masquerade --permanent
            success

            sudo firewall-cmd --add-service=http --permanent
            success

            sudo firewall-cmd --add-service=https --permanent
            success

            sudo firewall-cmd --reload
            success
        ```

    * On ajoute une route par default vers router2
        ```bash
            ip route show

            default via 10.2.2.254 dev enp0s8 proto static metric 100
            10.2.1.0/24 via 10.2.2.254 dev enp0s8 proto static metric 100
            10.2.2.0/24 dev enp0s8 proto kernel scope link src 10.2.2.10 metric 100
        ```

    * Installation de chrony
        ```bash
            yum install -y chrony    
        ```
    * Config chrony
        ```bash
            cat /etc/chrony.conf   

            server 0.fr.pool.ntp.org iburst
            server 1.fr.pool.ntp.org iburst
            server 2.fr.pool.ntp.org iburst
            server 3.fr.pool.ntp.org iburst
        ```
    * Port pour NTP
        ```bash
            sudo firewall-cmd --add-port=123/udp --permanent
            success

            sudo firewall-cmd --reload
            success
        ```
    * Restart chrony
        ```bash
            sudo systemctl start chronyd
        ```
    * On verifie la synchro NTP 
        ```bash
            chronyc sources

            210 Number of sources = 4
            MS Name/IP address         Stratum Poll Reach LastRx Last sample
            ===============================================================================
            ^* x.ns.gin.ntt.net              2   6    37    24   -195us[+1319us] +/-   77ms
            ^- 6mc-a7aifk.6metric.fr         2   6    37    27  +2251us[+2251us] +/-   56ms
            ^- support4.russianbridesne>     2   6    37    32  -3564us[-3564us] +/-   47ms
            ^- net1.web.yas-online.net       3   6    37    37  -8343us[-8343us] +/-   75ms
        ```
        ```bash
            chronyc tracking

            Reference ID    : 81FA23FA (x.ns.gin.ntt.net)
            Stratum         : 3
            Ref time (UTC)  : Sat Mar 09 13:24:17 2019
            System time     : 0.000068744 seconds slow of NTP time
            Last offset     : +0.000115458 seconds
            RMS offset      : 0.000115458 seconds
            Frequency       : 3.131 ppm slow
            Residual freq   : +0.824 ppm
            Skew            : 1.583 ppm
            Root delay      : 0.023592317 seconds
            Root dispersion : 0.035824973 seconds
            Update interval : 1.9 seconds
            Leap status     : Normal
        ```


### 4. Web server

* server1:

    * Activation des dépôts EPEL
        ```bash
            sudo yum install -y epel-release
        ```

    * Installation nginx
        ```bash
            sudo yum install -y nginx
        ```
    * Port 80
        ```bash
            sudo firewall-cmd --add-port=80/tcp --permanent
            success

            sudo firewall-cmd --reload
            success
        ```
    * Lancer le server
        ```bash
            sudo systemctl start nginx
        ```
    * Verifier si le server est lancé
        ```bash
            sudo systemctl status nginx

            ● nginx.service - The nginx HTTP and reverse proxy server
            Loaded: loaded (/usr/lib/systemd/system/nginx.service; disabled; vendor preset: disabled)
            Active: active (running) since sam. 2019-03-09 14:43:05 CET; 55s ago
            Process: 1539 ExecStart=/usr/sbin/nginx (code=exited, status=0/SUCCESS)
            Process: 1536 ExecStartPre=/usr/sbin/nginx -t (code=exited, status=0/SUCCESS)
            Process: 1534 ExecStartPre=/usr/bin/rm -f /run/nginx.pid (code=exited, status=0/SUCCESS)
            Main PID: 1541 (nginx)
            CGroup: /system.slice/nginx.service
                    ├─1541 nginx: master process /usr/sbin/nginx
                    ├─1542 nginx: worker process
                    └─1543 nginx: worker process

            mars 09 14:43:05 server1.tp2.b2 systemd[1]: Starting The nginx HTTP and reverse proxy se.....
            mars 09 14:43:05 server1.tp2.b2 nginx[1536]: nginx: the configuration file /etc/nginx/ng...ok
            mars 09 14:43:05 server1.tp2.b2 nginx[1536]: nginx: configuration file /etc/nginx/nginx....ul
            mars 09 14:43:05 server1.tp2.b2 systemd[1]: Started The nginx HTTP and reverse proxy server.
            Hint: Some lines were ellipsized, use -l to show in full.
        ```

        ```bash
            ss -l -t -4

            State       Recv-Q Send-Q Local Address:Port                 Peer Address:Port               
            LISTEN      0      128              *:http                           *:*
            LISTEN      0      128              *:ssh                            *:*
            LISTEN      0      100      127.0.0.1:smtp    
        ```
* client1 :

    * Curl du site de server1
        ```bash
            curl 10.2.2.10
        ```

    * Resultat
        ```html
            <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd">
            <html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en">

            <head>
                <title>Test Nginx HTTP Server by Lucas Consejo</title>
                <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
            </head>

            <body>
                <h1>Welcome to my test page</h1>

                <div class="content">
                    <p>I don't hnow what to say except that the server is running and
                       I have finished the TP !</p>

                    <p>Here is the repo git of the rendering of the TP : <a href="http://github.com/lucasconsejo/CCNA2-TPs/tree/master/tp-2">lien 
                       vers le repo git></a></p>
                </div>
            </body>
        </html>
        ```


## Auteur
Rédigé par [Lucas Consejo](https://github.com/lucasconsejo) - Etudiant Ingésup B2B [Ynov Bordeaux](https://www.ynov.com/)