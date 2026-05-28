# 🔬 lab-Cisco-Packet-Tracer

Série de labs progressifs pour apprendre la configuration réseau Cisco avec **Packet Tracer**.  
Chaque lab est un fichier `.pka` autonome avec énoncé intégré, scoring automatique et corrigé.

---

## 🎯 Objectif

Fournir une progression pédagogique complète, du premier câblage jusqu'à l'architecture d'entreprise,  
utilisable en autonomie ou en accompagnement de formation TSSR / CCNA.

---

## 📋 Prérequis

- [Cisco Packet Tracer](https://www.netacad.com/courses/packet-tracer) 8.x ou supérieur
- Compte Cisco NetAcad (gratuit) pour télécharger PT

---

## 🗂️ Structure du dépôt

```
lab-Cisco-Packet-Tracer/
├── 01-debutant/          # Commutation L2, VLANs, STP
├── 02-intermediaire/     # EtherChannel, routage, DHCP
├── 03-avance/            # OSPF, ACL, NAT
└── 04-expert/            # HSRP, BGP, VPN, sécurité
```

---

## 📚 Liste des labs

### 🟢 Niveau 1 — Débutant

| Lab | Titre | Thèmes | Durée |
|-----|-------|--------|-------|
| [L-01](01-debutant/lab-01-premier-pas/) | Premier pas | Câblage, IOS, SSH | 15 min |
| [L-02](01-debutant/lab-02-vlans-access/) | VLANs & ports access | VLAN, access, isolation L2 | 20 min |
| [L-03](01-debutant/lab-03-vtp-trunks/) | VTP & trunks dot1q | VTP server/client, trunk, dot1q | 40 min |
| [L-04](01-debutant/lab-04-stp-root/) | STP & bridge priority | STP, root bridge, PortFast | 30 min |

### 🔵 Niveau 2 — Intermédiaire

| Lab | Titre | Thèmes | Durée |
|-----|-------|--------|-------|
| [L-05](02-intermediaire/lab-05-etherchannel-lacp/) | EtherChannel LACP | LACP, Port-channel, redondance | 30 min |
| [L-06](02-intermediaire/lab-06-routage-statique/) | Routage statique | ip route, default route | 30 min |
| [L-07](02-intermediaire/lab-07-roas/) | Router-on-a-stick | Sous-interfaces, inter-VLAN | 30 min |
| [L-08](02-intermediaire/lab-08-svi-l3/) | SVIs & routage L3 | ip routing, SVI, L3 switch | 30 min |
| [L-09](02-intermediaire/lab-09-dhcp-helper/) | DHCP centralisé | DHCP server, ip helper-address | 30 min |

### 🟠 Niveau 3 — Avancé

| Lab | Titre | Thèmes | Durée |
|-----|-------|--------|-------|
| [L-10](03-avance/lab-10-ospf-mono-area/) | OSPF mono-area | OSPF area 0, DR/BDR | 45 min |
| [L-11](03-avance/lab-11-ospf-multi-area/) | OSPF multi-area | ABR, summarization | 45 min |
| [L-12](03-avance/lab-12-acl-standard/) | ACL standard | permit, deny, show access-lists | 30 min |
| [L-13](03-avance/lab-13-acl-etendues/) | ACL étendues nommées | DMZ, named ACL, filtrage L4 | 45 min |
| [L-14](03-avance/lab-14-nat-pat/) | NAT & PAT | NAT statique, PAT overload | 30 min |

### 🟣 Niveau 4 — Expert

| Lab | Titre | Thèmes | Durée |
|-----|-------|--------|-------|
| [L-15](04-expert/lab-15-hsrp/) | HSRP & redondance | HSRP, failover, preempt | 45 min |
| [L-16](04-expert/lab-16-bgp-bases/) | BGP bases | eBGP, AS-PATH, préfixes | 60 min |
| [L-17](04-expert/lab-17-vpn-ipsec/) | VPN site-à-site IPsec | IKEv1, crypto map, tunnel | 60 min |
| [L-18](04-expert/lab-18-securite-switch/) | Sécurité switch | Port-security, DHCP snooping, DAI | 45 min |
| [L-19](04-expert/lab-19-qos-bases/) | QoS bases | DSCP, MQC, priority queue | 45 min |
| [L-20](04-expert/lab-20-entreprise-complet/) | Réseau d'entreprise complet | Synthèse 3-tier, full stack | 120 min |

---

## 📁 Contenu de chaque lab

```
lab-XX-nom/
├── README.md              # Objectifs, prérequis, commandes de vérification
├── instructions.html      # Énoncé à coller dans le Plain Editor du Activity Wizard
└── lab-XX-nom.pka         # Fichier Packet Tracer Activity (apprenant)
```

---

## 🚀 Comment utiliser un lab

1. Télécharge le fichier `.pka` du lab souhaité
2. Ouvre-le dans Packet Tracer 8.x
3. Lis l'énoncé dans le panneau **Instructions** (coche **Dock** pour l'ancrer)
4. Configure les équipements selon les consignes
5. Clique **Check Results** pour voir ta progression

---

## 🗺️ Graphe de dépendances

```
L-01 → L-02 → L-03 → L-04 → L-05
                              ↓
              L-06 → L-07 → L-08 → L-09 → L-18
                              ↓
              L-10 → L-11 → L-16
               ↓
              L-12 → L-13 → L-17
               ↓
              L-14 → L-15 → L-20
               ↓
              L-19
```

---

## 🤝 Contribuer

Les fichiers `.pka` ne sont pas versionnables ligne à ligne (format binaire).  
Les contributions sont les bienvenues sur les fichiers texte : `README.md`, `instructions.html`.

1. Fork le dépôt
2. Crée une branche `feat/lab-XX-nom`
3. Propose une Pull Request

---

## 📄 Licence

Ce dépôt est partagé à des fins pédagogiques.  
Cisco, Packet Tracer et IOS sont des marques déposées de Cisco Systems, Inc.

---

*Maintenu par [@cedric-ribier](https://github.com/cedric-ribier) — Formation TSSR · Normandie*
