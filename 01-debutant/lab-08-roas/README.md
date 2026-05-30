# L-08 — Router-on-a-stick

> **Niveau :** 🟢 Débutant · **Durée estimée :** 30 min · **Prérequis :** [L-06](../lab-06-dhcp/) · [L-07](../lab-07-vtp-trunks/)

Activer le routage inter-VLAN via des sous-interfaces dot1q sur un routeur 2901.  
Lab de synthèse du niveau débutant : VLANs + trunks + routage + DHCP réunis.

---

## 🎯 Objectifs

- Configurer un lien trunk entre le 3560 et le routeur 2901
- Créer des sous-interfaces dot1q sur le 2901 (une par VLAN)
- Activer le routage inter-VLAN
- Vérifier la connectivité entre tous les VLANs
- Comprendre la différence entre ROAS et routage L3 switch (aperçu L-11)

---

## 🗺️ Topologie

```
                        [ Router0 ]
                             | Gi0/0 (trunk)
                   [ Multilayer Switch0 ]
                  /          |            \
           [ Switch2 ]  [ Switch3 ]  [ Switch4 ]
                |         /  |  \      /  |  \
             Server0   PC3 PC0 PC2  PC4 PC1 PC5
```

> Le câblage, VTP, trunks et ports access sont déjà configurés (solution L-07).  
> Tu dois ajouter le trunk vers Router0 et configurer les sous-interfaces.

---

## 📋 Sous-interfaces Router0

| Sous-interface | Encapsulation | Adresse IP       | VLAN |
|----------------|---------------|------------------|------|
| Gi0/0.10       | dot1q 10      | 172.16.10.254/24 | 10   |
| Gi0/0.20       | dot1q 20      | 172.16.20.254/24 | 20   |
| Gi0/0.30       | dot1q 30      | 172.16.30.254/24 | 30   |

---

## 📋 Plan d'adressage complet

| Équipement | VLAN | Adresse IP      | Passerelle     |
|------------|------|-----------------|----------------|
| PC0        | 10   | 172.16.10.1/24  | 172.16.10.254  |
| PC2        | 10   | 172.16.10.2/24  | 172.16.10.254  |
| PC4        | 10   | 172.16.10.3/24  | 172.16.10.254  |
| PC1        | 30   | 172.16.30.1/24  | 172.16.30.254  |
| PC3        | 30   | 172.16.30.2/24  | 172.16.30.254  |
| PC5        | 30   | 172.16.30.3/24  | 172.16.30.254  |
| Server0    | 20   | 172.16.20.10/24 | 172.16.20.254  |

---

## 📝 Travail demandé

### Étape 1 — Trunk 3560 → Router0

Sur **Multilayer Switch0** — port vers Router0 (vérifie le port dans ta topologie) :

```
configure terminal
interface fa0/24
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30
 no shutdown
end
```

### Étape 2 — Sous-interfaces sur Router0

```
configure terminal
interface gi0/0
 no shutdown
interface gi0/0.10
 encapsulation dot1q 10
 ip address 172.16.10.254 255.255.255.0
interface gi0/0.20
 encapsulation dot1q 20
 ip address 172.16.20.254 255.255.255.0
interface gi0/0.30
 encapsulation dot1q 30
 ip address 172.16.30.254 255.255.255.0
end
```

### Étape 3 — Vérifier les passerelles sur les PCs

Les PCs ont leurs IPs statiques depuis L-07.  
Vérifie que la **passerelle** est bien configurée sur chaque PC :
- PC0/PC2/PC4 → `172.16.10.254`
- PC1/PC3/PC5 → `172.16.30.254`
- Server0 → `172.16.20.254`

### Étape 4 — Sauvegarder

```
copy running-config startup-config
```

---

## 🔑 Commandes de vérification

```
! Sous-interfaces du routeur
show ip interface brief

! Table de routage du routeur
show ip route

! Depuis les PCs
ping 172.16.30.1    (PC0 VLAN10 → PC1 VLAN30 : succès)
ping 172.16.20.10   (PC0 VLAN10 → Server0 VLAN20 : succès)
tracert 172.16.30.1 (observe le passage par 172.16.10.254)
```

---

## ✅ Critères de réussite

- [ ] `show ip interface brief` — Gi0/0.10, .20, .30 en **up/up**
- [ ] `show ip route` — 3 routes **C** (une par sous-réseau VLAN)
- [ ] Ping PC0 → PC1 : **succès** (VLAN 10 → VLAN 30)
- [ ] Ping PC0 → Server0 : **succès** (VLAN 10 → VLAN 20)
- [ ] Traceroute PC0 → PC1 : passe par **172.16.10.254**

---

## 💡 Points pédagogiques

**Pourquoi Router-on-a-stick ?**  
Un seul lien physique entre le switch et le routeur transporte les 3 VLANs  
grâce au trunk dot1q. Le routeur crée une sous-interface logique par VLAN.

**Limite du ROAS** — tout le trafic inter-VLAN passe par le même lien physique.  
Sur un réseau chargé, ce lien devient un goulot d'étranglement.  
En L-11 on verra comment le 3560 peut router directement via les SVIs,  
sans passer par le routeur.

**`encapsulation dot1q X`** — doit correspondre exactement au numéro de VLAN.  
`no shutdown` sur l'interface physique Gi0/0 est obligatoire — les sous-interfaces  
héritent de son état.

---

## 🎓 Félicitations — Niveau débutant terminé !

Tu maîtrises maintenant :
- L'interface Packet Tracer et la mise en forme de topologie
- Le plan d'adressage IPv4 et le subnetting de base
- Les commandes IOS fondamentales et SSH
- Les VLANs, VTP et les trunks dot1q
- Le routage statique et le DHCP
- Le routage inter-VLAN avec Router-on-a-stick

**Niveau suivant :** [02-intermediaire/](../../02-intermediaire/) — STP, EtherChannel, SVIs, OSPF, ACL, NAT

---

## 📁 Fichiers

| Fichier | Description |
|---------|-------------|
| `lab-08-roas.pka` | Fichier activité Packet Tracer |

---

*Lab précédent : [L-07 — VTP & trunks dot1q](../lab-07-vtp-trunks/)*  
*Niveau suivant : [02-intermediaire/](../../02-intermediaire/)*
