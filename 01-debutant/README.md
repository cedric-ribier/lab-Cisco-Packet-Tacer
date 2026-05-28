# 🟢 Niveau 1 — Débutant

Bienvenue dans le premier niveau. Ces 4 labs couvrent les bases de la commutation L2 :  
câblage, VLANs, VTP, trunks et STP. Ils sont conçus pour être réalisés **dans l'ordre**.

---

## 📋 Prérequis

- Packet Tracer 8.x installé
- Aucune connaissance préalable en réseau Cisco requise

---

## 📚 Labs de ce niveau

| Lab | Titre | Thèmes principaux | Durée | Prérequis |
|-----|-------|-------------------|-------|-----------|
| [L-01](lab-01-premier-pas/) | Premier pas | Câblage, modes IOS, ping | 15 min | Aucun |
| [L-02](lab-02-vlans-access/) | VLANs & ports access | VLAN, access, isolation L2 | 20 min | L-01 |
| [L-03](lab-03-vtp-trunks/) | VTP & trunks dot1q | VTP server/client, trunk, dot1q | 40 min | L-02 |
| [L-04](lab-04-stp-root/) | STP & bridge priority | STP, root bridge, PortFast | 30 min | L-03 |

---

## 🧠 Ce que tu vas apprendre

### L-01 — Premier pas
- Naviguer dans les modes IOS (`user`, `enable`, `config`)
- Câbler des équipements et tester la connectivité avec `ping`
- Comprendre la différence entre switch et routeur

### L-02 — VLANs & ports access
- Créer des VLANs et leur donner un nom
- Assigner un port en mode `access` à un VLAN
- Vérifier l'isolation entre VLANs avec `show vlan brief`

### L-03 — VTP & trunks dot1q
- Configurer VTP en mode `server` et `client`
- Propager automatiquement les VLANs sur plusieurs switches
- Mettre en place des liens trunk `dot1q` et contrôler les VLANs autorisés
- Vérifier avec `show interfaces trunk` et `show vtp status`

### L-04 — STP & bridge priority
- Comprendre le rôle de STP dans la prévention des boucles L2
- Identifier le root bridge par défaut
- Forcer l'élection du root bridge via la `bridge priority`
- Observer les rôles de ports (Root, Designated, Alternate)

---

## 🔑 Commandes clés du niveau

```
! Navigation IOS
enable / configure terminal / exit / end

! VLANs
vlan 10 / name Compta
switchport mode access / switchport access vlan 10
show vlan brief

! VTP
vtp mode server / vtp mode client
vtp domain TSSR-LAB / vtp version 2
show vtp status

! Trunks
switchport trunk encapsulation dot1q   (3560 uniquement)
switchport mode trunk
switchport trunk allowed vlan 10,20,30
show interfaces trunk

! STP
spanning-tree vlan 10 priority 4096
spanning-tree portfast
show spanning-tree vlan 10
```

---

## ✅ Critères de réussite du niveau

À l'issue des 4 labs, tu dois être capable de :

- [ ] Câbler une topologie multi-switches sans aide
- [ ] Créer et propager des VLANs via VTP
- [ ] Configurer des liens trunk entre switches
- [ ] Contrôler l'élection du root bridge STP
- [ ] Diagnostiquer un problème L2 avec `show` et `ping`

---

*Niveau suivant : [02-intermediaire/](../02-intermediaire/) — EtherChannel, routage inter-VLAN & DHCP*
