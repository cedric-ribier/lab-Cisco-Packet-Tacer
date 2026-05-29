# L-02 — Modèle OSI & plan d'adressage

> **Niveau :** 🟢 Débutant · **Durée estimée :** 20 min · **Prérequis :** [L-01](../lab-01-premier-pas/)

---

## 📖 Théorie — Modèle OSI

Le modèle OSI (**Open Systems Interconnection**) découpe la communication réseau en **7 couches**.  
Chaque couche a un rôle précis et communique uniquement avec la couche adjacente.

```
┌─────────────────────────────────────┐
│  7 — Application   HTTP, FTP, DNS   │  ← ce que voit l'utilisateur
│  6 — Présentation  SSL, encodage    │  ← format des données
│  5 — Session       NetBIOS, RPC     │  ← gestion des sessions
│  4 — Transport     TCP, UDP         │  ← port source/destination
│  3 — Réseau        IP, ICMP         │  ← adresse IP, routage
│  2 — Liaison       Ethernet, VLAN   │  ← adresse MAC, switch
│  1 — Physique      câbles, signaux  │  ← bits sur le câble
└─────────────────────────────────────┘
```

### Quoi retenir pour la pratique Cisco

| Couche | Équipement Cisco | PDU | Adresse |
|--------|-----------------|-----|---------|
| 3 — Réseau | Routeur | Paquet | IP |
| 2 — Liaison | Switch | Trame | MAC |
| 1 — Physique | Hub, câble | Bit | — |

> **Astuce** : un switch opère en couche 2, un routeur en couche 3.  
> Un switch L3 (comme le 3560) opère aux deux.

---

## 📖 Théorie — Adressage IPv4

### Structure d'une adresse IP

Une adresse IPv4 = **32 bits** exprimés en 4 octets décimaux séparés par des points.

```
192  .  168  .   1   .   1
11000000.10101000.00000001.00000001
```

### Masque de sous-réseau

Le masque définit la **partie réseau** (bits à 1) et la **partie hôte** (bits à 0).

| Notation CIDR | Masque décimal  | Nb hôtes utilisables |
|---------------|-----------------|----------------------|
| /24           | 255.255.255.0   | 254                  |
| /25           | 255.255.255.128 | 126                  |
| /26           | 255.255.255.192 | 62                   |
| /27           | 255.255.255.224 | 30                   |
| /28           | 255.255.255.240 | 14                   |
| /30           | 255.255.255.252 | 2 (liens point à point) |

> **Formule** : nb hôtes = 2ⁿ - 2 (n = bits hôte, -2 pour adresse réseau et broadcast)

### Adresses réservées dans un sous-réseau

Pour le réseau **192.168.1.0/24** :

| Adresse | Rôle |
|---------|------|
| 192.168.1.0 | Adresse réseau (non assignable) |
| 192.168.1.1 → .254 | Adresses hôtes utilisables |
| 192.168.1.255 | Broadcast (non assignable) |

### Conventions de nommage

| Adresse | Usage courant |
|---------|---------------|
| .1 ou .254 | Passerelle par défaut (routeur) |
| .10 → .20 | Serveurs |
| .100 → .200 | Postes clients (plage DHCP) |

---

## 📋 Plan d'adressage du lab

```
PC0 ──── Switch0 ──── Router0 ──── Switch1 ──── PC1
         192.168.1.0/24     192.168.2.0/24
```

| Équipement | Interface | Adresse IP       | Masque          | Passerelle     |
|------------|-----------|------------------|-----------------|----------------|
| PC0        | Fa0       | 192.168.1.1/24   | 255.255.255.0   | 192.168.1.254  |
| Router0    | Gi0/0     | 192.168.1.254/24 | 255.255.255.0   | —              |
| Router0    | Gi0/1     | 192.168.2.254/24 | 255.255.255.0   | —              |
| PC1        | Fa0       | 192.168.2.1/24   | 255.255.255.0   | 192.168.2.254  |

---

## 🔑 Commandes de vérification

```
! Depuis un PC
ping 192.168.2.1       ← ping inter-réseau via le routeur

! Depuis le routeur
show ip interface brief
show ip route
```

---

## ✅ Critères de réussite

- [ ] Plan d'adressage saisi correctement sur tous les équipements
- [ ] Ping PC0 → PC1 : **succès** (inter-réseau via Router0)
- [ ] Mode simulation : paquet ICMP visible couche par couche
- [ ] `show ip route` sur Router0 : 2 routes **C** (connected)

---

## 📁 Fichiers

| Fichier | Description |
|---------|-------------|
| `README.md` | Ce document — théorie OSI + subnetting (à garder comme référence) |
| `lab-02-osi-adressage.pka` | Fichier activité Packet Tracer |

---

*Lab précédent : [L-01 — Premier pas](../lab-01-premier-pas/)*  
*Lab suivant : [L-03 — Commandes IOS de base](../lab-03-ios-base/)*
