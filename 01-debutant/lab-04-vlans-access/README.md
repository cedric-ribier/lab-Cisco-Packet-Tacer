# L-04 — VLANs & ports access

> **Niveau :** 🟢 Débutant · **Durée estimée :** 20 min · **Prérequis :** [L-01](../lab-01-premier-pas/)

Créer des VLANs manuellement sur un switch, assigner les ports access et vérifier  
que les VLANs isolent correctement le trafic L2.

---

## 🎯 Objectifs

- Créer des VLANs et leur attribuer un nom
- Assigner des ports en mode `access` à un VLAN
- Vérifier la configuration avec `show vlan brief`
- Comprendre l'isolation L2 entre VLANs

---

## 🗺️ Topologie

```
PC0 ──── |            | ──── PC2
PC1 ──── | Switch2960 | ──── PC3
         |            | ──── PC4
```

| Équipement | Port switch | VLAN | Adresse IP     | Masque        |
|------------|-------------|------|----------------|---------------|
| PC0        | Fa0/1       | 10   | 172.16.10.1/24 | 255.255.255.0 |
| PC1        | Fa0/2       | 10   | 172.16.10.2/24 | 255.255.255.0 |
| PC2        | Fa0/3       | 20   | 172.16.20.1/24 | 255.255.255.0 |
| PC3        | Fa0/4       | 20   | 172.16.20.2/24 | 255.255.255.0 |
| PC4        | Fa0/5       | 30   | 172.16.30.1/24 | 255.255.255.0 |

---

## 📝 Travail demandé

1. Créer les VLANs 10 (`Compta`), 20 (`RH`), 30 (`Direction`) sur le switch
2. Assigner chaque port access au VLAN correspondant
3. Configurer les adresses IP sur chaque PC
4. Vérifier l'isolation : ping intra-VLAN doit réussir, inter-VLAN doit échouer

---

## 🔑 Commandes de vérification

```
! Vérifier les VLANs et leurs ports
show vlan brief

! Vérifier un port en particulier
show interfaces fa0/1 switchport

! Depuis les PCs
ping 172.16.10.2   (PC0 → PC1 : succès attendu)
ping 172.16.20.1   (PC0 → PC2 : échec attendu)
```

---

## ✅ Critères de réussite

- [ ] `show vlan brief` — VLANs 10, 20 et 30 présents avec leurs noms
- [ ] Ping PC0 → PC1 : **succès** (intra VLAN 10)
- [ ] Ping PC2 → PC3 : **succès** (intra VLAN 20)
- [ ] Ping PC0 → PC2 : **échec** (inter-VLAN, isolation L2)
- [ ] Ping PC0 → PC4 : **échec** (inter-VLAN, isolation L2)

---

## 💡 Point pédagogique

Sans routage, deux machines dans des VLANs différents ne peuvent pas communiquer  
même si elles sont sur le même switch physique. C'est l'objectif du VLAN : **segmenter** le réseau.  
Le routage inter-VLAN sera abordé en niveau 2 (L-07 et L-08).

---

## 📁 Fichiers

| Fichier | Description |
|---------|-------------|
| `lab-02-vlans-access.pka` | Fichier activité Packet Tracer |

---

*Lab précédent : [L-01 — Premier pas](../lab-01-premier-pas/)*  
*Lab suivant : [L-03 — VTP & trunks dot1q](../lab-03-vtp-trunks/)*
