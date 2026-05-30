# L-07 — VTP & trunks dot1q

> **Niveau :** 🟢 Débutant · **Durée estimée :** 40 min · **Prérequis :** [L-04](../lab-04-vlans-access/)

Propager automatiquement les VLANs via VTP sur une topologie multi-switches  
et mettre en place les liens trunk dot1q entre tous les switches.

---

## 🎯 Objectifs

- Configurer VTP en mode `server` sur le 3560 et `client` sur les 2960
- Propager les VLANs 10, 20 et 30 automatiquement via VTP
- Configurer les liens trunk dot1q entre tous les switches
- Vérifier avec `show vtp status` et `show interfaces trunk`
- Comprendre la différence VTP vs configuration manuelle

---

## 🗺️ Topologie

```
                   [ Multilayer Switch0 ]  ← VTP Server
                  /          |             \
           [ Switch2 ]  [ Switch3 ]   [ Switch4 ]
                |         /  |  \       /  |  \
             Server0   PC3 PC0 PC2   PC4 PC1 PC5
```

---

## 📋 Plan d'adressage

| Équipement | VLAN | Adresse IP      | Masque        | Passerelle    |
|------------|------|-----------------|---------------|---------------|
| PC0        | 10   | 172.16.10.1/24  | 255.255.255.0 | 172.16.10.254 |
| PC2        | 10   | 172.16.10.2/24  | 255.255.255.0 | 172.16.10.254 |
| PC4        | 10   | 172.16.10.3/24  | 255.255.255.0 | 172.16.10.254 |
| PC1        | 30   | 172.16.30.1/24  | 255.255.255.0 | 172.16.30.254 |
| PC3        | 30   | 172.16.30.2/24  | 255.255.255.0 | 172.16.30.254 |
| PC5        | 30   | 172.16.30.3/24  | 255.255.255.0 | 172.16.30.254 |
| Server0    | 20   | 172.16.20.10/24 | 255.255.255.0 | 172.16.20.254 |

---

## 📋 VLANs

| VLAN | Nom    | Réseau         |
|------|--------|----------------|
| 10   | Compta | 172.16.10.0/24 |
| 20   | Server | 172.16.20.0/24 |
| 30   | DG     | 172.16.30.0/24 |

---

## 📋 Ports access

| Switch  | Port  | VLAN | Équipement |
|---------|-------|------|------------|
| Switch2 | Fa0/7 | 20   | Server0    |
| Switch3 | Fa0/1 | 30   | PC3        |
| Switch3 | Fa0/4 | 10   | PC0        |
| Switch3 | Fa0/5 | 10   | PC2        |
| Switch4 | Fa0/2 | 10   | PC4        |
| Switch4 | Fa0/3 | 30   | PC5        |
| Switch4 | Fa0/6 | 30   | PC1        |

---

## 📋 Liens trunk

| Switch A           | Port A | Switch B | Port B |
|--------------------|--------|----------|--------|
| Multilayer Switch0 | Fa0/1  | Switch2  | Gi0/1  |
| Multilayer Switch0 | Fa0/2  | Switch3  | Gi0/1  |
| Multilayer Switch0 | Fa0/3  | Switch4  | Gi0/1  |

---

## 📝 Travail demandé

### Phase 1 — VTP & ports access

1. Configurer VTP domaine `TSSR-LAB` version 2 sur tous les switches
2. Créer les VLANs 10/20/30 sur le serveur VTP (Multilayer Switch0)
3. Assigner les ports access selon le tableau ci-dessus
4. Configurer les IPs statiques sur les PCs et Server0

### Phase 2 — Trunks dot1q

5. Configurer les trunks dot1q sur tous les liens inter-switches
6. Vérifier la propagation VTP : `show vlan brief` sur Switch3 et Switch4
7. Vérifier les trunks : `show interfaces trunk`

---

## 🔑 Commandes de vérification

```
! VTP
show vtp status
show vlan brief

! Trunks
show interfaces trunk

! Connectivité
ping 172.16.10.3    (PC0 → PC4 : succès intra VLAN 10)
ping 172.16.30.1    (PC0 → PC1 : échec inter-VLAN)
```

---

## ✅ Critères de réussite

- [ ] `show vtp status` — domain `TSSR-LAB`, version 2, modes corrects
- [ ] `show vlan brief` sur Switch3 — VLANs 10/20/30 propagés
- [ ] `show interfaces trunk` — ports trunk actifs, VLANs 10/20/30 présents
- [ ] Ping PC0 → PC4 : **succès** (intra VLAN 10)
- [ ] Ping PC0 → PC1 : **échec** (inter-VLAN, pas de routage)

---

## 💡 Points pédagogiques

**VTP vs configuration manuelle** — VTP propage les VLANs automatiquement  
sur les switches clients. Sans trunks, la propagation ne fonctionne pas —  
c'est pourquoi les phases 1 et 2 sont liées.

**`switchport trunk encapsulation dot1q`** — obligatoire sur le 3560 avant  
`switchport mode trunk`. Les 2960 nécessitent pas cette commande, car ils ne supportent  
que dot1q par défaut.

**Isolation inter-VLAN** — même avec les trunks, les VLANs restent isolés  
sans routage. Le routage inter-VLAN sera abordé en L-08.

---

## 📁 Fichiers

| Fichier | Description |
|---------|-------------|
| `lab-07-vtp-trunks.pka` | Fichier activité Packet Tracer |

---

> 📖 Référence complète : [00-documentation/Switch-configuration-base/](../../../00-documentation/Switch-configuration-base/CONFIGURATION-BASE.md)

---

*Lab précédent : [L-06 — DHCP sur routeur](../lab-06-dhcp/)*  
*Lab suivant : [L-08 — Router-on-a-stick](../lab-08-roas/)*
