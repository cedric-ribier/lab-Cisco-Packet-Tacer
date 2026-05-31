# L-10 — EtherChannel LACP

> **Niveau :** 🔵 Intermédiaire · **Durée estimée :** 30 min · **Prérequis :** [L-09](../lab-09-stp/)

Agréger les liens redondants entre Switch3 et Switch4 avec le protocole LACP,  
vérifier l'état du bundle et observer le comportement en cas de panne d'un lien.

---

## 🎯 Objectifs

- Comprendre l'intérêt de l'EtherChannel vs liens redondants simples gérés par STP
- Configurer un bundle LACP (active/active) entre Switch3 et Switch4
- Vérifier l'état du port-channel avec `show etherchannel summary`
- Observer que STP voit le bundle comme un seul lien logique
- Simuler la panne d'un lien membre et vérifier la continuité

---

## 🗺️ Topologie

```
                        [ Router0 ]
                             | Gi0/0 (trunk — ROAS)
                   [ Multilayer Switch0 ]
                  /          |            \
           [ Switch2 ]  [ Switch3 ]══[ Switch4 ]
                |         /  |  \      /  |  \
             Server0   PC3 PC0 PC2  PC4 PC1 PC5

         Switch3 Fa0/23 ══ Fa0/23 Switch4  ← LACP Po1
         Switch3 Fa0/24 ══ Fa0/24 Switch4  ← second membre
```

> Point de départ : solution lab-09 (STP + bridge priorities configurés).  
> Tu dois ajouter un second câble entre Switch3 et Switch4 (Fa0/24 ↔ Fa0/24)  
> puis configurer l'EtherChannel LACP sur les deux liens.

---

## 📋 EtherChannel LACP

| Switch  | Ports membres   | Port-channel | Mode        |
|---------|-----------------|--------------|-------------|
| Switch3 | Fa0/23 + Fa0/24 | Po1          | LACP active |
| Switch4 | Fa0/23 + Fa0/24 | Po1          | LACP active |

---

## 📝 Travail demandé

### Étape 1 — Ajouter le second câble dans PT

Dans Packet Tracer, ajoute un câble **Copper Cross-Over** entre :
- Switch3 **Fa0/24** → Switch4 **Fa0/24**

Ce second lien physique sera le deuxième membre du bundle Po1.

### Étape 2 — Configurer EtherChannel sur Switch3

Les ports membres doivent être configurés **avant** d'être ajoutés au bundle :

```
configure terminal
interface range fa0/23 - 24
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30
 channel-group 1 mode active
interface port-channel 1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30
end
```

### Étape 3 — Configurer EtherChannel sur Switch4

Même configuration des deux côtés :

```
configure terminal
interface range fa0/23 - 24
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30
 channel-group 1 mode active
interface port-channel 1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30
end
```

### Étape 4 — Vérifier l'état du bundle

```
show etherchannel summary
```

Résultat attendu :

```
Group  Port-channel  Protocol  Ports
------+-------------+---------+--------------------
1      Po1(SU)       LACP      Fa0/23(P)  Fa0/24(P)
```

| Code | Signification |
|------|---------------|
| `SU` | Layer2, in use — bundle opérationnel |
| `P`  | Port membre actif |
| `D`  | Port down |

### Étape 5 — Vérifier STP avec le bundle

```
show spanning-tree vlan 10
```

STP doit voir **Po1** comme une seule interface — plus Fa0/23 et Fa0/24 séparément.  
Le port-channel hérite des priorités STP configurées en L-09.

### Étape 6 — Simuler une panne

Dans PT, déconnecte le câble Fa0/23 entre Switch3 et Switch4.  
Observe que :
- Po1 reste **SU** avec un seul port **P**
- La connectivité est maintenue
- STP ne recompute pas (Po1 est toujours actif)

Reconnecte le câble — Po1 revient à 2 ports P.

### Étape 7 — Sauvegarder

```
copy running-config startup-config
```

---

## 🔑 Commandes de vérification

```
! État du bundle
show etherchannel summary
show etherchannel 1 detail

! Négociation LACP
show lacp neighbor

! STP avec EtherChannel
show spanning-tree vlan 10

! Connectivité
ping 172.16.10.3    (PC0 → PC4 : succès via Po1)
ping 172.16.30.1    (PC0 → PC1 : succès inter-VLAN via ROAS)
```

---

## ✅ Critères de réussite

- [ ] `show etherchannel summary` — Po1 en état **SU**, Fa0/23 et Fa0/24 en état **P**
- [ ] `show lacp neighbor` — négociation LACP confirmée des deux côtés
- [ ] `show spanning-tree vlan 10` — **Po1** visible comme interface trunk
- [ ] Ping PC0 → PC4 : **succès** (trafic passe par le bundle)
- [ ] Après suppression d'un câble : ping toujours **succès**

---

## 💡 Points pédagogiques

**EtherChannel vs STP**  
En L-09, STP bloquait Fa0/23 ou Fa0/24 pour éviter la boucle — un lien était  
gaspillé. Avec Po1, STP voit un seul lien logique et les deux câbles  
transportent du trafic simultanément.

**LACP active/active**  
Les deux switches initient la négociation. `active/passive` fonctionne aussi.  
`passive/passive` ne fonctionne jamais — personne ne négocie.

**Ordre de configuration**  
Toujours configurer trunk + allowed vlans sur les ports membres **avant**  
`channel-group`. Des configs différentes des deux côtés = bundle refusé.

---

## 📁 Fichiers

| Fichier | Description |
|---------|-------------|
| `lab-10-etherchannel.pka` | Fichier activité Packet Tracer |

---

*Lab précédent : [L-09 — STP & bridge priority](../lab-09-stp/)*  
*Lab suivant : [L-11 — SVIs & routage L3 switch](../lab-11-svi-l3/)*
