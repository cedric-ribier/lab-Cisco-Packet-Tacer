# 🟢 Niveau 1 — Débutant

Bienvenue dans le premier niveau. Ces 8 labs couvrent les vraies fondations du réseau Cisco :  
interface Packet Tracer, plan d'adressage, commandes IOS, VLANs, routage statique, DHCP,  
trunks VTP et routage inter-VLAN. Ils sont conçus pour être réalisés **dans l'ordre**.

---

## 📋 Prérequis

- Packet Tracer 8.x installé
- Aucune connaissance préalable en réseau Cisco requise

---

## 📚 Labs de ce niveau

| Lab | Titre | Thèmes principaux | Durée | Prérequis |
|-----|-------|-------------------|-------|-----------|
| [L-01](lab-01-premier-pas/) | Premier pas | Interface CPT, câblage, ping | 15 min | Aucun |
| [L-02](lab-02-osi-adressage/) | Modèle OSI & plan d'adressage | OSI, IPv4, subnetting | 20 min | L-01 |
| [L-03](lab-03-ios-base/) | Commandes IOS de base | IOS, SSH, show, sauvegarde | 20 min | L-01 |
| [L-04](lab-04-vlans-access/) | VLANs & ports access | VLAN, access, isolation L2 | 20 min | L-03 |
| [L-05](lab-05-routage-statique/) | Routage statique | ip route, default route | 30 min | L-02 · L-03 |
| [L-06](lab-06-dhcp/) | DHCP sur routeur | DHCP pool, options, baux | 20 min | L-05 |
| [L-07](lab-07-vtp-trunks/) | VTP & trunks dot1q | VTP server/client, trunk, dot1q | 40 min | L-04 |
| [L-08](lab-08-roas/) | Router-on-a-stick | Sous-interfaces, inter-VLAN | 30 min | L-06 · L-07 |

---

## 🧠 Ce que tu vas apprendre

### L-01 — Premier pas
- Découvrir l'interface Packet Tracer : placement, câblage, annotations, couleurs
- Tester la connectivité avec `ping`

### L-02 — Modèle OSI & plan d'adressage
- Comprendre les 7 couches OSI via le mode simulation de PT
- Construire un plan d'adressage IPv4 : classes, masques, sous-réseaux

### L-03 — Commandes IOS de base
- Naviguer entre les modes IOS (`user`, `enable`, `config`)
- Configurer hostname, enable secret, bannière, console, SSH
- Sauvegarder : `copy running-config startup-config`
- Diagnostiquer avec les commandes `show`

### L-04 — VLANs & ports access
- Créer des VLANs et leur attribuer un nom
- Assigner les ports en mode `access`
- Vérifier l'isolation L2 avec `show vlan brief`

### L-05 — Routage statique
- Configurer les interfaces d'un routeur
- Écrire des routes statiques et une route par défaut
- Lire et interpréter `show ip route`

### L-06 — DHCP sur routeur
- Créer un pool DHCP avec exclusions, passerelle et DNS
- Passer les PCs en DHCP et vérifier les baux
- Utiliser `show ip dhcp binding`

### L-07 — VTP & trunks dot1q
- Configurer VTP `server` et `client` sur plusieurs switches
- Propager les VLANs automatiquement
- Mettre en place les trunks dot1q avec `allowed vlan`
- Vérifier avec `show interfaces trunk` et `show vtp status`

### L-08 — Router-on-a-stick
- Créer des sous-interfaces dot1q sur un routeur 2901
- Activer le routage inter-VLAN
- Combiner VLANs + routage + DHCP dans une topologie complète

---

## 🔑 Commandes clés du niveau

```
! IOS navigation
enable / configure terminal / exit / end
copy running-config startup-config

! Diagnostic
show version / show ip interface brief
show running-config / show startup-config

! VLANs
vlan 10 / name Compta
switchport mode access / switchport access vlan 10
show vlan brief

! Routage
ip address 172.16.1.1 255.255.255.0 / no shutdown
ip route 0.0.0.0 0.0.0.0 [next-hop]
show ip route

! DHCP
ip dhcp pool LAN / network / default-router / dns-server
ip dhcp excluded-address
show ip dhcp binding

! VTP & trunks
vtp mode server / client / vtp domain TSSR-LAB
switchport trunk encapsulation dot1q  (3560 uniquement)
switchport mode trunk / switchport trunk allowed vlan 10,20,30
show interfaces trunk / show vtp status

! Router-on-a-stick
interface gi0/0.10 / encapsulation dot1q 10
ip address 172.16.10.254 255.255.255.0
```

---

## ✅ Critères de réussite du niveau

À l'issue des 8 labs, tu dois être capable de :

- [ ] Naviguer dans l'interface PT et mettre en forme une topologie
- [ ] Lire et construire un plan d'adressage IPv4
- [ ] Configurer un équipement Cisco depuis zéro (IOS)
- [ ] Créer des VLANs et les propager via VTP
- [ ] Mettre en place un routage statique fonctionnel
- [ ] Configurer un serveur DHCP sur routeur
- [ ] Configurer des trunks dot1q entre switches
- [ ] Activer le routage inter-VLAN avec Router-on-a-stick

---

*Niveau suivant : [02-intermediaire/](../02-intermediaire/) — STP, EtherChannel, SVIs, OSPF, ACL, NAT*
