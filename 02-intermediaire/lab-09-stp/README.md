# L-09 — STP & bridge priority

> **Niveau :** 🔵 Intermédiaire · **Durée estimée :** 30 min · **Prérequis :** [L-08](../../01-debutant/lab-08-roas/)

Analyser le fonctionnement de STP sur la topologie complète avec liens redondants,  
contrôler l'élection du root bridge et protéger les ports access.

---

## 🎯 Objectifs

- Comprendre le rôle de STP dans la prévention des boucles L2
- Identifier le root bridge par défaut et les rôles de ports
- Forcer l'élection du root bridge via la `bridge priority`
- Activer `PortFast` et `BPDU Guard` sur les ports access
- Observer la convergence STP après modification des priorités

---

## 🗺️ Topologie

```
                        [ Router0 ]
                             | Gi0/0 (trunk — ROAS)
                   [ Multilayer Switch0 ]
                  /          |            \
           [ Switch2 ]──[ Switch3 ]──[ Switch4 ]
                |         /  |  \      /  |  \
             Server0   PC3 PC0 PC2  PC4 PC1 PC5
```

> Le ROAS (Router0) est déjà configuré — solution lab-08.  
> Les liens redondants entre Switch2↔Switch3 (Fa0/24) et Switch3↔Switch4 (Fa0/23)  
> créent des boucles potentielles que STP doit gérer.

---

## 📋 Liens redondants gérés par STP

| Lien | Switch A | Port A | Switch B | Port B |
|------|----------|--------|----------|--------|
| Redondant 1 | Switch2 | Fa0/24 | Switch3 | Gig0/1 |
| Redondant 2 | Switch3 | Fa0/23 | Switch4 | Fa0/24 |

---

## 📋 Bridge priority cible

| Switch             | Rôle STP          | Priority |
|--------------------|-------------------|----------|
| Multilayer Switch0 | Root principal    | 4096     |
| Switch2            | Root secondaire   | 8192     |
| Switch3            | Non-root          | 32768    |
| Switch4            | Non-root          | 32768    |

---

## 📝 Travail demandé

### Étape 1 — Observer STP par défaut

Avant toute configuration, observe l'état STP sur chaque switch :

```
show spanning-tree vlan 10
```

Identifie :
- Quel switch est root bridge par défaut ?
- Quels ports sont en état **FWD** (forwarding) ?
- Quels ports sont en état **BLK** (blocking) ?

Note tes observations — tu compareras avec l'état après configuration.

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

Sur chaque switch, applique sur les ports connectés aux PCs et Server0 :

```
configure terminal
interface fa0/1
 spanning-tree portfast
 spanning-tree bpduguard enable
end
```

> **PortFast** : le port passe directement en forwarding sans attendre la convergence STP.  
> **BPDU Guard** : désactive le port si un BPDU est reçu (protection contre un switch non autorisé).  
> **Ne jamais activer PortFast sur un port connecté à un autre switch.**

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

! Vérifier PortFast
show spanning-tree interface fa0/1 portfast
```

### Lire show spanning-tree vlan 10

```
VLAN0010
  Root ID    Priority    4096
             This bridge is the root   ← ce switch EST le root

Interface    Role  Sts  Cost
Fa0/1        Desg  FWD  19
Fa0/2        Root  FWD  19
Fa0/23       Altn  BLK  19    ← port bloqué par STP
```

| Rôle | Signification |
|------|---------------|
| `Root` | Port le plus proche du root bridge |
| `Desg` | Port désigné — transmet le trafic |
| `Altn` | Port alternatif — bloqué pour éviter une boucle |

| État | Signification |
|------|---------------|
| `FWD` | Forwarding — le port transmet |
| `BLK` | Blocking — le port est bloqué par STP |

---

## ✅ Critères de réussite

- [ ] `show spanning-tree vlan 10` sur 3560 — **This bridge is the root**, priority 4096
- [ ] `show spanning-tree vlan 10` sur Switch2 — priority 8192
- [ ] Au moins un port en état **BLK** (preuve que STP bloque les boucles)
- [ ] `show spanning-tree interface fa0/1 portfast` — PortFast activé sur les ports access
- [ ] Ping PC0 → PC4 : **succès** (STP n'a pas bloqué le bon chemin)
- [ ] Ping PC0 → Server0 : **succès**

---

## 💡 Points pédagogiques

**Pourquoi STP ?**  
Sans STP, les trames en broadcast bouclent indéfiniment sur les liens redondants  
— tempête de broadcast qui paralyse tout le réseau.

**Priority = multiple de 4096**  
La valeur affichée dans `show spanning-tree` est `priority + VLAN ID`.  
Ex : priority 4096 sur VLAN 10 → affiché `4106`.

**Le ROAS reste en place**  
Le routage inter-VLAN via Router0 est toujours actif. STP n'impacte que  
la couche 2 — le routage L3 n'est pas affecté.

---

## 📁 Fichiers

| Fichier | Description |
|---------|-------------|
| `lab-09-stp.pka` | Fichier activité Packet Tracer |

---

*Lab précédent : [L-08 — Router-on-a-stick](../../01-debutant/lab-08-roas/)*  
*Lab suivant : [L-10 — EtherChannel LACP](../lab-10-etherchannel/)*
