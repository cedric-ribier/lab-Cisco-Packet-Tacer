# 🔵 Niveau 2 — Intermédiaire

Bienvenue dans le niveau intermédiaire. Ces 8 labs approfondissent la commutation L2,
introduisent le routage dynamique et couvrent les services réseau essentiels en entreprise.
Ils s'appuient sur les bases du niveau débutant et doivent être réalisés **dans l'ordre**.

---

## 📋 Prérequis

- Niveau débutant complété (L-01 → L-08)
- Maîtrise des VLANs, trunks, routage statique et DHCP

---

## 📚 Labs de ce niveau

| Lab | Titre | Thèmes principaux | Durée | Prérequis |
|-----|-------|-------------------|-------|-----------|
| [L-09](lab-09-stp/) | STP & bridge priority | STP, root bridge, PortFast, BPDU | 30 min | L-07 |
| [L-10](lab-10-etherchannel/) | EtherChannel LACP | LACP, Port-channel, redondance L2 | 30 min | L-09 |
| [L-11](lab-11-svi-l3/) | SVIs & routage L3 switch | ip routing, SVI, comparaison ROAS | 30 min | L-08 · L-10 |
| [L-12](lab-12-dhcp-helper/) | DHCP centralisé & ip helper | DHCP server dédié, helper-address | 30 min | L-11 |
| [L-13](lab-13-ospf-mono/) | OSPF mono-area | OSPF area 0, DR/BDR, convergence | 45 min | L-05 |
| [L-14](lab-14-acl-standard/) | ACL standard | permit, deny, filtrage par source | 30 min | L-05 |
| [L-15](lab-15-nat-pat/) | NAT & PAT | NAT statique, PAT overload | 30 min | L-13 |
| [L-16](lab-16-acl-etendues/) | ACL étendues & DMZ | Named ACL, filtrage L4, DMZ | 45 min | L-14 · L-15 |

---

## 🧠 Ce que tu vas apprendre

### L-09 — STP & bridge priority
- Comprendre le rôle de STP dans la prévention des boucles L2
- Analyser la topologie STP par défaut et identifier le root bridge
- Forcer l'élection du root bridge via la `bridge priority`
- Observer les rôles de ports : Root, Designated, Alternate
- Activer `PortFast` et `BPDU Guard` sur les ports access

### L-10 — EtherChannel LACP
- Agréger des liens redondants entre switches L2 avec LACP
- Configurer les membres du bundle avant le port-channel
- Vérifier avec `show etherchannel summary` (état SU, ports P)
- Simuler la panne d'un lien et observer le basculement

### L-11 — SVIs & routage L3 switch
- Activer `ip routing` sur le 3560
- Créer les SVIs (interfaces VLAN) comme passerelles
- Comparer ROAS (L-08) vs SVI : chemin, latence, cas d'usage
- Identifier quand choisir l'un ou l'autre en entreprise

### L-12 — DHCP centralisé & ip helper
- Configurer un serveur DHCP dédié (Server-PT)
- Créer un pool par VLAN avec ses options
- Configurer `ip helper-address` sur les SVIs du 3560
- Comprendre le relay DHCP et le flux des broadcasts

### L-13 — OSPF mono-area
- Configurer OSPF area 0 sur plusieurs routeurs
- Analyser la LSDB et les voisinages (`show ip ospf neighbor`)
- Comprendre l'élection DR/BDR sur un lien multi-accès
- Observer la reconvergence après panne d'un lien

### L-14 — ACL standard
- Créer des ACL numérotées et nommées
- Filtrer le trafic par adresse source
- Placer les ACL sur la bonne interface dans le bon sens
- Vérifier avec `show access-lists` et les compteurs de hits

### L-15 — NAT & PAT
- Configurer le NAT statique pour un serveur DMZ
- Configurer le PAT overload pour les postes clients
- Vérifier avec `show ip nat translations`
- Simuler l'accès Internet depuis les postes internes

### L-16 — ACL étendues & DMZ
- Filtrer par protocole, port source et destination
- Placer les ACL au plus près de la source
- Mettre en place une DMZ avec un serveur web
- Combiner NAT et ACL pour sécuriser le périmètre

---

## 🔑 Commandes clés du niveau

```
! STP
spanning-tree vlan 10 priority 4096
spanning-tree portfast / bpduguard enable
show spanning-tree vlan 10

! EtherChannel
channel-group 1 mode active
interface port-channel 1
show etherchannel summary
show lacp neighbor

! SVIs
ip routing
interface vlan 10 / ip address 172.16.10.254 255.255.255.0
show ip route

! DHCP helper
ip helper-address 172.16.20.10
show ip dhcp binding

! OSPF
router ospf 1 / network 192.168.1.0 0.0.0.255 area 0
show ip ospf neighbor / show ip route ospf

! ACL standard
ip access-list standard BLOCK_PC
 deny 192.168.1.0 0.0.0.255
 permit any
interface gi0/1 / ip access-group BLOCK_PC in
show access-lists

! NAT/PAT
ip nat inside source static 192.168.1.10 203.0.113.10
ip nat inside source list 1 interface gi0/0 overload
show ip nat translations

! ACL étendue
ip access-list extended DMZ_IN
 permit tcp any host 192.168.2.10 eq 80
 deny ip any any
```

---

## ✅ Critères de réussite du niveau

À l'issue des 8 labs, tu dois être capable de :

- [ ] Contrôler la topologie STP et l'élection du root bridge
- [ ] Agréger des liens avec EtherChannel LACP
- [ ] Choisir entre ROAS et SVI selon le contexte
- [ ] Centraliser le DHCP avec ip helper-address
- [ ] Configurer OSPF sur un réseau multi-routeurs
- [ ] Écrire et placer des ACL standard et étendues
- [ ] Mettre en place NAT statique et PAT overload
- [ ] Sécuriser un périmètre réseau avec DMZ + ACL + NAT

---

*Niveau précédent : [01-debutant/](../01-debutant/)*  
*Niveau suivant : [03-avance/](../03-avance/) — OSPF multi-area, HSRP, sécurité switch, VPN, BGP*
