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