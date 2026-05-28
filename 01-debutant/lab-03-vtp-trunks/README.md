# L-03 — VTP & trunks dot1q

> **Niveau :** 🟢 Débutant · **Durée estimée :** 40 min · **Prérequis :** [L-02](../lab-02-vlans-access/)

Propager automatiquement les VLANs via VTP sur une topologie multi-switches,  
mettre en place les liens trunk dot1q, agréger les liens redondants avec LACP  
et contrôler l'élection du root bridge STP.

---

## 🎯 Objectifs

- Configurer VTP en mode `server` sur le 3560 et `client` sur les 2960
- Propager les VLANs 10, 20 et 30 automatiquement via VTP
- Configurer les liens trunk dot1q entre tous les switches
- Créer un EtherChannel LACP (Po1) entre Switch3 et Switch4
- Définir le 3560 comme root bridge STP principal

---

## 🗺️ Topologie

```
                    [ Router0 ]
                         |
               [ Multilayer Switch0 ]  ← VTP Server / Root bridge
              /           |            \
        [ Switch2 ]  [ Switch3 ]  [ Switch4 ]
             |        /   |   \     /   |   \
          Server0  PC3  PC0  PC2  PC4  PC1  PC5

         Switch3 ══════════════ Switch4  (EtherChannel LACP Po1)
```

---

## 📋 Plan d'adressage

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

## 📋 VLANs

| VLAN | Nom    | Réseau          |
|------|--------|-----------------|
| 10   | Compta | 172.16.10.0/24  |
| 20   | Server | 172.16.20.0/24  |
| 30   | DG     | 172.16.30.0/24  |

---

## 📋 Liens trunk

| Switch A           | Port A  | Switch B | Port B  |
|--------------------|---------|----------|---------|
| Multilayer Switch0 | Fa0/1   | Switch2  | Gi0/1   |
| Multilayer Switch0 | Fa0/2   | Switch3  | Gi0/1   |
| Multilayer Switch0 | Fa0/3   | Switch4  | Gi0/1   |
| Switch3            | Fa0/23  | Switch4  | Fa0/23  |
| Switch3            | Fa0/24  | Switch4  | Fa0/24  |

---

## 📋 EtherChannel LACP

| Switch  | Ports membres    | Port-channel | Mode        |
|---------|------------------|--------------|-------------|
| Switch3 | Fa0/23 + Fa0/24  | Po1          | LACP active |
| Switch4 | Fa0/23 + Fa0/24  | Po1          | LACP active |

---

## 📋 Bridge priority STP

| Switch             | Rôle              | Priority |
|--------------------|-------------------|----------|
| Multilayer Switch0 | Root principal    | 4096     |
| Switch2            | Root secondaire   | 8192     |
| Switch3            | Non-root          | 32768    |
| Switch4            | Non-root          | 32768    |

---

## 📝 Travail demandé

### Phase 1 — VTP & ports access
1. Configurer VTP domaine `TSSR-LAB` version 2 sur tous les switches
2. Créer les VLANs 10/20/30 sur le serveur VTP (Multilayer Switch0)
3. Vérifier la propagation : `show vlan brief` sur Switch3 et Switch4
4. Assigner les ports access selon le plan d'adressage
5. Configurer les IPs statiques sur les PCs et Server0

### Phase 2 — Trunks, EtherChannel & STP
6. Configurer les trunks dot1q sur tous les liens inter-switches
7. Créer l'EtherChannel LACP Po1 entre Switch3 et Switch4
8. Configurer les bridge priorities STP
9. Vérifier la convergence STP et l'état du port-channel

---

## 🔑 Commandes de vérification

```
! VTP
show vtp status
show vlan brief

! Trunks
show interfaces trunk

! EtherChannel
show etherchannel summary
show lacp neighbor

! STP
show spanning-tree vlan 10
show spanning-tree vlan 30

! Connectivité
ping 172.16.10.3    (PC0 → PC4 : succès intra VLAN 10)
ping 172.16.30.1    (PC0 → PC1 : échec inter-VLAN)
```

---

## ✅ Critères de réussite

- [ ] `show vtp status` — domain TSSR-LAB, version 2, modes corrects
- [ ] `show vlan brief` sur Switch3 — VLANs 10/20/30 propagés
- [ ] `show interfaces trunk` — Po1 présent comme trunk
- [ ] `show etherchannel summary` — Po1 en état **SU**, 2 ports **P**
- [ ] `show spanning-tree vlan 10` sur 3560 — rôle **root**, priority 4096
- [ ] Ping PC0 → PC4 : **succès** (intra VLAN 10)
- [ ] Ping PC0 → PC1 : **échec** (inter-VLAN, pas de routage)

---

## 💡 Points pédagogiques

**VTP vs manuel** — VTP propage les VLANs automatiquement sur les clients. Sans trunk,  
la propagation ne fonctionne pas — c'est pourquoi les deux phases sont liées.

**EtherChannel** — agrège deux liens physiques en un lien logique. STP voit un seul  
port et ne bloque pas, tout en doublant la bande passante disponible.

**Bridge priority** — doit être un multiple de 4096. Plus la valeur est basse,  
plus le switch a de chances de devenir root bridge.

---

## 📁 Fichiers

| Fichier | Description |
|---------|-------------|
| `lab-03-vtp-trunks.pka` | Fichier activité Packet Tracer |

---

*Lab précédent : [L-02 — VLANs & ports access](../lab-02-vlans-access/)*  
*Lab suivant : [L-04 — STP & bridge priority](../lab-04-stp-root/)*
