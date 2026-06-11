# Labos Réseau — Packet Tracer (Phase 1)

Série de labos CCNA 200-301 réalisés sur Cisco Packet Tracer 9.0.
Objectif : maîtriser les fondamentaux réseau de façon pratique avant l'examen.

---

## Labo 01 — Communication sur un même réseau (switch)

### Objectif
Comprendre comment deux machines communiquent sur un même sous-réseau
via un switch de couche 2.

### Topologie

PC0 ---- [Switch 2960] ---- PC1

### Plan d'adressage
| Appareil | IP          | Masque        |
|----------|-------------|---------------|
| PC0      | 192.168.1.1 | 255.255.255.0 |
| PC1      | 192.168.1.2 | 255.255.255.0 |

### Test de validation
Depuis PC0 :

ping 192.168.1.2

Résultat : réponse reçue — communication établie.

### Ce que j'ai appris
- Un switch travaille en couche 2 : il transmet les trames selon les
  adresses MAC, pas les IP.
- Le switch construit une table MAC associant chaque port à un appareil.
- Deux machines dans le même sous-réseau (/24) communiquent directement,
  sans routeur.
 
---

## Labo 02 — Routage entre deux réseaux (routeur)

### Objectif
Faire communiquer deux sous-réseaux distincts à travers un routeur
qui assure le routage inter-réseaux.

### Topologie

PC0 ---\                              /--- PC2
[Switch A] --- [Routeur] --- [Switch B]
PC1 ---/                              /--- PC3

### Plan d'adressage
| Appareil      | IP          | Passerelle  |
|---------------|-------------|-------------|
| PC0           | 192.168.1.2 | 192.168.1.1 |
| PC1           | 192.168.1.3 | 192.168.1.1 |
| PC2           | 192.168.2.2 | 192.168.2.1 |
| PC3           | 192.168.2.3 | 192.168.2.1 |
| Routeur G0/0  | 192.168.1.1 | 192.168.1.2           |
| Routeur G0/1  | 192.168.2.1 | 192.168.2.1|

### Configuration du routeur (CLI)

#### Mode config
- interface gigabitEthernet 0/0
- ip address 192.168.1.1 255.255.255.0
- no shutdown
- exit

- interface gigabitEthernet 0/1
- ip address 192.168.2.1 255.255.255.0
- no shutdown
- exit
 #### Mode privilégier

 - show ip int brief (pour voir les interfaces du routeur qui ont été configurées)

 #### test de connectivité inter-réseau

 - faire le ping d'un pc du réseau 1 vers un du réseau 2

 #### Éléments que j'ai compris
 - les interfaces du routeur son désactivées par défaut et le `no shut` les active
 - le `show ip int brief` permet de vérifier les config des interfaces et doit être un réflexe automatique
 #### Erreur rencontrée
 - faute de frappe dans l'ip de pc0 m'empêchait de communiquer avec pc2. J'ai du faire un ping vers la passerelle par défaut pour voir qu'il n'est surement pas dans le même rx, car j'avais déjats vérifier l'adresse de la passerelle via `show ip int brief` 
---

  ## Labo 03 routage interVlan

  ### Objectif

  Segmentation de couche 2 et routage Inter-Vlan

  ### Topologie

  PC0 ---\    /--- PC2
        Switch0
  PC1 ---/    /--- PC3

  ### Adressage

  | PC0(vlan10) | 192.168.10.2 |
  | PC1(vlan10) | 192.168.10.3 |
  | PC2(vlan20) | 192.168.20.2 |
  | PC3(vlan20) | 192.168.20.3 |

  ### Config Vlans
  - vlan10 
    - vlan 10; name USERS_A
  - vlan20
    - vlan 20; name USERS_B
  - interface sw PC0
    - switchport mode access
    - switchport access vlan 10
  - interface sw PC1
    - switchport mode access
    - switchport access vlan 10
  - interface sw PC2
    - switchport mode access
    - switchport access vlan 20
  - interface sw PC3
    - switchport mode access
    - switchport access vlan 20
  - interface sw relier au routeur
    * switchport mode trunk
    * switchport allowed vlan 10,20
  ### routage 
  #### Router on a stick
  - Segmentation de g0/0(no shut)
    - g0/0.10 
        * encapsulation dot1q 10
        * int g0/0.10 192.168.10.1 255.255.255.0
    - g0/0.20
      * encapsulation dot1q 20
      * int g0/0.10 192.168.20.1 255.255.255.0
  #### Routage via SVI
  - interface vlan 10; ip add 192.168.10.1 255.255.255.0; no shut
  - interface vlan 20; ip add 192.168.20.1 255.255.255.0; no shut
  - Activation du routage(ip routing)

  #### Vérification
  - show ip interface brief(loin en bas)
  - show ip route (les deux réseaux ont la mention "C")
  
---

 ## Labo 04 routage statique

 ### Objectif

 faire communiquer des pc n'étant pas sur le même réseau éthernet.

 ### Topologie

 PC0 ---- Switch0 ----- Routeur0 ------ Routeur1 ------- Switch1 -------- PC1

 ### Plan d'adressage

 | PC0            | 192.168.1.2/24 |
 | Routeur0(g0/0) | 192.168.1.1/24 |
 | Routeur0(g0/1) | 10.0.0.1/30    |
 | Routeur1(g0/0) | 10.0.0.2/30    |
 | Routeur1(g0/1)  | 192.168.2.1/24 |
 | PC1            | 192.168.2.2/24 |

 ### Config des routes

 - Routeur0: 
   - g0/0(ip add 192.168.1.1 255.255.255.0)
   - route statique (ip route 192.168.2.0 255.255.255.0 g0/1 10.0.0.2)

 - Routeur0: 
   - g0/1(ip add 192.168.2.1 255.255.255.0)
   - route statique (ip route 192.168.2.0 255.255.255.0 g0/0 10.0.0.1)

 #### Élements compris
 - il y a trois type de commande pour la config de route statique
   - celle où l'on spécifie uniquement l'adresse de saut saut suivant (interface du prochain routeur)
   - celle où l'on spécifie uniquement l'interface de sortie du paquet sur le routeur où l'on configure la route(bien dans le cas des connexion point à point).
   - celle où l'on spécifie l'interface de sortie et l'adresse de saut suivant (la plus complète résoud le problème de surménage dû au requette ARP aveugle de la configuration avec uniquement l'interface de sortie)