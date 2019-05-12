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

* Nos avis 

    Nous avons choisi de prendre le menu 3 (infra small/medium office) car nous n'avions pas envie de faire de la sécu (menu 1) et parmi les deux projets d'infra, on a préféré voir de plus près, comment est organisée l'infrastructure dans une petite entreprise.

    Nous avons tous deux trouvé le Tp intéressant car c'était une réelle mise en situation et cela nous a permis de mieux comprendre la configuration et l'utilité de router, switch ainsi que les VLAN, au sein d'une entreprise. On a aussi pu mieux se familiariser avec GNS3 malgré le fait qu'il nous a laissé pas mal de fil à retordre.

    Voici donc nos bilans individuels:  
    * FERREIRA Théo : le système de menus était super ! GNS3 super pratique quand ça fonctionne, une bonne manière pour faire un bilan de nos capacités.
    * CONSEJO Lucas : le Tp était vraiment intéressant. Je ne suis pas trop centré sur le réseau, mais cela dit, j'essaie de m'intéresser à tout dans le domaine de l'informatique. Ce Tp m'a plu car j'ai découvert comment est organisé une infra small office.

## Auteur
Rédigé par [Lucas Consejo](https://github.com/lucasconsejo) & [Théo Ferreira](https://github.com/misfitonie) - Etudiant Ingésup B2B [Ynov Bordeaux](https://www.ynov.com/)