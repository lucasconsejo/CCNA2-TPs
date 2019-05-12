# Rendu TP 4 - Le MENU

## Menu 3 : Infra small/medium office

* Topologie :
    ```       
                    server1        server2       printer1
                    +-----+        +-----+       +-----+
                    |     |        |     |       |     |
                    |     |        |     |       |     |
                    +--+--+        +--+--+       +--+--+
                       |              |             |
                       |              |             |
                       |  SWT-S6      |      SWT-S7 |
                       |    +-----+   |          +--+--+
                       +----+     +---+          |     |
                            |     |              |     |
                            +--+--+              +--+--+
                               |                    |
                               |                    |
                               |          +-----+   |
                               +----------+     +---+
                                          |     |                       NAT
                                          +--+--+                       +-----+
                                     SWT3-r1 |                          |     |
                                             |   +----------------------+     |
                                             |   |                      +-----+
                                             |   |
                                             |   |
                                        +----+---+-+
                                        |          |
                                        |          |
                        +---------------+          +-------------+
                        |               |          |             |
                        |               +----------+             |
                        |               router1                  |
                        |                                        |
                SWT1-r1 |                                SWT2-r1 |
                     +--+--+                                  +--+--+
                     |     |                                  |     |
           +---------+     +---------+                     +--+     +--+
           |         +--+--+         |                     |  +-----+  |
           |            |            |                     |           |
           |            |            |                     |           |
    SWT-S1 |     SWT-S2 |      SWT-S3|              SWT-S4 |     SWT-S5|
        +--+--+      +--+--+      +-----+               +--+--+     +-----+
        |     |      |     |      |     |               |     |     |     |
        |     |      |     |      |     |               |     |     |     |
        +--+--+      +--+--+      +--+--+               +--+--+     +--+--+
           |            |            |                     |           |
           |            |            |                     |           |
        +--+--+      +--+--+      +--+--+               +--+--+     +--+--+
        |     |      |     |      |     |               |     |     |     |
        |     |      |     |      |     |               |     |     |     |
        +-----+      +-----+      +-----+               +-----+     +-----+
        pro1         pro2         pro3                  admin1      rh1

    ```

* Maquette GNS3 : 

    ![Maquette](https://github.com/lucasconsejo/CCNA2-TPs/blob/master/tp-4/screen/maquette_gns3.png)

* Tableau d'adressage :

    | Nom              | Adresse       |
    |------------------|---------------|
    | tp4-net1         | 10.3.1.0/27   |
    | tp4-net2         | 10.3.2.0/27   |
    | tp4-net3         | 10.3.3.0/27   |
    | tp4-net4         | 10.3.4.0/27   |
    | tp4-net5         | 10.3.5.0/27   |
    | tp4-net6         | 10.3.6.0/27   |
    | tp4-net7         | 10.3.7.0/27   |
    | tp4-net8         | 10.3.8.0/29   |  
      
    | Hosts            | tp4-net1      | tp4-net2      | tp4-net3       | tp4-net4       | tp4-net5       | tp4-net6       | tp4-net7        | tp4-net8        |
    |------------------|---------------|---------------|----------------|----------------|----------------|----------------|-----------------|-----------------|
    | pro1.tp4         | 10.3.1.1/27   | x             | x              | x              | x              | x              | x               | x               |
    | pro2.tp4         | x             | 10.3.2.1/27   | x              | x              | x              | x              | x               | x               | 
    | pro3.tp4         | x             | x             | 10.3.3.1/27    | x              | x              | x              | x               | x               |
    | rh1.tp4          | x             | x             | x              | 10.3.4.1/27    | x              | x              | x               | x               | 
    | admin1.tp4       | x             | x             | x              | x              | 10.3.5.1/27    | x              | x               | x               | 
    | server1.tp4      | x             | x             | x              | x              | x              | 10.3.6.1/27    | x               | x               |
    | server2.tp4      | x             | x             | x              | x              | x              | 10.3.6.2/27    | x               | x               |
    | printer1.tp4     | x             | x             | x              | x              | x              | x              | 10.3.7.1/27     | x               |
    | router1.tp4      | 10.3.1.30/27  | 10.3.2.30/27  | 10.3.3.30/27   | 10.3.4.30/27   | 10.3.5.30/27   | 10.3.6.30/27   | 10.3.7.30/27    | 10.3.8.1/29     |


* Plan VLAN :

    | VLANs            | VLAN1   | VLAN2   | VLAN3   |
    |------------------|---------|---------|---------|
    | pro              | yes     | x       | x       |
    | admin            | x       | yes     | x       |
    | rh               | yes     | x       | x       |
    | server1          | x       |         | yes     |
    | server2          | x       | yes     | x       |
    | printer          | x       | x       | yes     |

* Liste du matériel nécessaire : 

    | Matériel          | Quantité |
    |-------------------|----------|
    | Pro (Centos 7)    | 3        |
    | RH (Centos 7)     | 1        |
    | Admin (Centos 7)  | 1        |
    | Server (Centos 7) | 2        |
    | Printer           | 1        |
    | Router Cisco      | 1        |
    | Switch            | 10       |
    | Cables            | 19       |
