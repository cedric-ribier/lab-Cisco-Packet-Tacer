# L-09 — STP & bridge priority

> **Niveau :** 🔵 Intermédiaire · **Durée estimée :** 30 min · **Prérequis :** [L-07](../lab-07-vtp-trunks/)

Analyser le fonctionnement de STP sur une topologie avec liens redondants,  
contrôler l'élection du root bridge et protéger les ports access.

---

## 🎯 Objectifs

- Comprendre le rôle de STP dans la prévention des boucles L2
- Identifier le root bridge par défaut et les rôles de ports
- Forcer l'élection du root bridge via la `bridge priority`
- Activer `PortFast` et `BPDU Guard` sur les ports access
- Observer la convergence STP après une panne de lien

---

## 🗺️ Topologie

```
                   [ Multilayer Switch0 ]
                  /          |            \
           [ Switch2 ]───────────────[ Switch3 ]
                |                         |
             Server0                    PC0, PC1
```

> Les liens redondants entre Switch2, Switch3 et le 3560 créent des boucles potentielles.  
> STP bloque automatiquement les ports redondants pour éviter les tempêtes de broadcast.

---

## 📋 Bridge priority cible

| Switch             | Rôle STP          | Priority |
|--------------------|-------------------|----------|
| Multilayer Switch0 | Root principal    | 4096     |
| Switch2            | Root secondaire   | 8192     |
| Switch3            | Non-root          | 32768    |

---

## 📝 Travail demandé

### Étape 1 — Observer STP par défaut

Avant toute configuration, observe l'état STP :

```
show spanning-tree vlan 10
```

Identifie :
- Quel switch est root bridge par défaut ?
- Quels ports sont en état **FWD** (forwarding) ?
- Quels ports sont en état **BLK** (blocking) ?

### Étape 2 — Configurer les bridge priorities

Sur **Multilayer Switch0** (root principal) :

```
configure terminal
spanning-tree vlan 10 priority 4096
spanning-tree vlan 20 priority 4096
spanning-tree vlan 30 priority 4096
end
```

Sur **Switch2** (root secondaire) :

```
configure terminal
spanning-tree vlan 10 priority 8192
spanning-tree vlan 20 priority 8192
spanning-tree vlan 30 priority 8192
end
```

### Étape 3 — PortFast & BPDU Guard sur les ports access

Sur chaque switch, applique sur les ports connectés aux PCs et serveurs :

```
configure terminal
interface fa0/1
 spanning-tree portfast
 spanning-tree bpduguard enable
end
```

> **PortFast** : le port passe directement en forwarding sans attendre la convergence STP.  
> **BPDU Guard** : désactive le port si un BPDU est reçu (protection contre un switch non autorisé).

### Étape 4 — Sauvegarder

```
copy running-config startup-config
```

---

## 🔑 Commandes de vérification

```
! Vue globale STP par VLAN
show spanning-tree vlan 10

! Résumé de tous les VLANs
show spanning-tree summary

! Détail d'un port
show spanning-tree interface fa0/1 detail

! Vérifier PortFast et BPDU Guard
show spanning-tree interface fa0/1 portfast
```

### Lire show spanning-tree vlan 10

```
Switch# show spanning-tree vlan 10

VLAN0010
  Spanning tree enabled protocol ieee
  Root ID    Priority    4096          ← priority + vlan ID
             Address     xxxx.xxxx.xxxx
             This bridge is the root   ← ce switch EST le root
  Bridge ID  Priority    4096
             Address     xxxx.xxxx.xxxx

Interface    Role  Sts  Cost    Prio.Nbr  Type
Fa0/1        Desg  FWD  19      128.1     P2p
Fa0/2        Root  FWD  19      128.2     P2p
Fa0/3        Altn  BLK  19      128.3     P2p  ← port bloqué
```

| Rôle | Signification |
|------|---------------|
| `Root` | Port le plus proche du root bridge |
| `Desg` | Port désigné — forward le trafic |
| `Altn` | Port alternatif — bloqué pour éviter une boucle |

| État | Signification |
|------|---------------|
| `FWD` | Forwarding — le port transmet le trafic |
| `BLK` | Blocking — le port est bloqué par STP |
| `LIS` | Listening — phase de convergence |
| `LRN` | Learning — apprentissage des MACs |

---

## ✅ Critères de réussite

- [ ] `show spanning-tree vlan 10` sur 3560 — **This bridge is the root**, priority 4096
- [ ] `show spanning-tree vlan 10` sur Switch2 — priority 8192
- [ ] Au moins un port en état **BLK** sur la topologie (preuve que STP fonctionne)
- [ ] Ping PC0 → Server0 : **succès** (STP n'a pas bloqué le bon chemin)
- [ ] `show spanning-tree interface fa0/1 portfast` — PortFast activé sur les ports access

---

## 💡 Points pédagogiques

**Pourquoi STP ?**  
Sans STP, les trames Ethernet en broadcast bouclent indéfiniment sur les liens  
redondants — c'est une tempête de broadcast qui paralyse tout le réseau.

**Priority = multiple de 4096**  
La valeur réelle affichée dans `show spanning-tree` est `priority + VLAN ID`.  
Ex : priority 4096 sur VLAN 10 → affiché `4106`.

**PortFast uniquement sur les ports access**  
Ne jamais activer PortFast sur un port connecté à un autre switch — cela  
contourne STP et peut créer une boucle.

---

## 📁 Fichiers

| Fichier | Description |
|---------|-------------|
| `lab-09-stp.pka` | Fichier activité Packet Tracer |

---

*Lab précédent : [L-08 — Router-on-a-stick](../../01-debutant/lab-08-roas/)*  
*Lab suivant : [L-10 — EtherChannel LACP](../lab-10-etherchannel/)*
