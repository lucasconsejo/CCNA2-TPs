# Rendu Tp-1

## Questions / Reponses

### I. Exploration du réseau d'une machine CentOS

* **Combien y a-t-il d'adresses disponibles dans un /24 ?**
    - Il y a 254 adresses disponibles dans un /24

* **Combien y a-t-il d'adresses disponibles dans un /30 ?**
    - Il y a 2 adresses disponibles dans un /30

* **Quelle est l'utilité d'un /30 ?**
    - I don't know :grey_question: 
    - (Moins d'adresse sur le réseau, donc il y a un meilleur contrôle :eyes:)

* **Expliquer le retour que vous obtenez (code retour HTTP 301)**
    - L'erreur HTTP 301 signifie que l'url recherché a été bloqué de façon permanente.
    - HTTp 301 - Moved Permanently

* **Expliquer chacune des lignes :**
    #### Routes
    ```bash
        10.1.1.0/24 dev enp0s8 proto kernel scope link src 10.1.1.2 metric 100
	    10.1.2.0/30 dev enp0s9 proto kernel scope link src 10.1.2.2 metric 100
     ```
    - 10.1.1.0/24 => Adresse réseau
    - dev enp0s8 => Nom du device - enp0s8
    - proto kernel => la route a été installé par le kernel pendant autoconfiguration
    - src 10.1.1.2 => l'adresse source

    #### Table ARP
    ```bash
        10.1.1.1 dev enp0s8 lladdr 0a:00:27:00:00:48 REACHABLE
	    10.1.2.1 dev enp0s9 lladdr 0a:00:27:00:00:4c REACHABLE
     ```
    - 10.1.1.1 => Autre ip disponible sur le meme réseau
    - dev enp0s8 => Nom du device - enp0s8
    - lladdr 0a:00:27:00:00:48 => Adresse MAC
    - REACHABLE => l'adresse est accessible

    #### Après avoir vidé la Table ARP
    ```bash
        10.1.2.1 dev enp0s9 lladdr 0a:00:27:00:00:4c STALE
     ```
    - 10.1.2.1 => Autre ip disponible sur le meme réseau
    - dev enp0s9 => Nom du device - enp0s8
    - lladdr 0a:00:27:00:00:4c => Adresse MAC
    - STALE => l'adresse vient d'être rajouté

## Auteur
Rédigé par [Lucas Consejo](https://github.com/lucasconsejo) - Etudiant Ingésup B2B [Ynov Bordeaux](https://www.ynov.com/)