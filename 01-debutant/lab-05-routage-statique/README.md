# L-05 — Routage statique

> **Niveau :** 🟢 Débutant · **Durée estimée :** 30 min · **Prérequis :** [L-02](../lab-02-osi-adressage/) · [L-03](../lab-03-ios-base/)

Configurer les interfaces d'un routeur, écrire des routes statiques vers des réseaux  
distants et une route par défaut. Comprendre et lire la table de routage.

---

## 🎯 Objectifs

- Configurer les interfaces d'un routeur (ip address + no shutdown)
- Écrire des routes statiques vers des réseaux distants
- Configurer une route par défaut
- Lire et interpréter `show ip route`
- Tester la connectivité bout en bout entre deux réseaux

---

## 🗺️ Topologie

```
PC0 ──── Switch0 ──── Router0 ──────── Router1 ──── Switch1 ──── PC1
       192.168.1.0/24    10.0.0.0/30        192.168.2.0/24
```

---

## 📋 Plan d'adressage

| Équipement | Interface | Adresse IP     | Masque            | Rôle              |
|------------|-----------|----------------|-------------------|-------------------|
| PC0        | Fa0       | 192.168.1.1/24 | 255.255.255.0     | Hôte réseau 1     |
| Router0    | Gi0/0     | 192.168.1.254/24 | 255.255.255.0   | Passerelle PC0    |
| Router0    | Gi0/1     | 10.0.0.1/30    | 255.255.255.252   | Lien WAN          |
| Router1    | Gi0/0     | 10.0.0.2/30    | 255.255.255.252   | Lien WAN          |
| Router1    | Gi0/1     | 192.168.2.254/24 | 255.255.255.0   | Passerelle PC1    |
| PC1        | Fa0       | 192.168.2.1/24 | 255.255.255.0     | Hôte réseau 2     |

> **Pourquoi /30 sur le lien WAN ?**  
> Un /30 offre exactement 2 adresses hôtes — parfait pour un lien point à point entre deux routeurs.  
> Gaspiller un /24 pour 2 équipements serait inefficace.

---

## 📝 Travail demandé

### Étape 1 — Configurer les interfaces des routeurs

Sur **Router0** :
```
configure terminal
interface gi0/0
 ip address 192.168.1.254 255.255.255.0
 no shutdown
interface gi0/1
 ip address 10.0.0.1 255.255.255.252
 no shutdown
end
```

Sur **Router1** :
```
configure terminal
interface gi0/0
 ip address 10.0.0.2 255.255.255.252
 no shutdown
interface gi0/1
 ip address 192.168.2.254 255.255.255.0
 no shutdown
end
```

### Étape 2 — Écrire les routes statiques

Sur **Router0** — route vers le réseau 192.168.2.0/24 via Router1 :
```
ip route 192.168.2.0 255.255.255.0 10.0.0.2
```

Sur **Router1** — route vers le réseau 192.168.1.0/24 via Router0 :
```
ip route 192.168.1.0 255.255.255.0 10.0.0.1
```

### Étape 3 — Configurer les PCs

Sur **PC0** :
- IP : `192.168.1.1` / Masque : `255.255.255.0` / Passerelle : `192.168.1.254`

Sur **PC1** :
- IP : `192.168.2.1` / Masque : `255.255.255.0` / Passerelle : `192.168.2.254`

### Étape 4 — Sauvegarder
```
copy running-config startup-config
```

---

## 🔑 Commandes de vérification

```
! Table de routage — lire les codes C, S, S*
show ip route

! État des interfaces
show ip interface brief

! Ping entre routeurs (lien WAN)
ping 10.0.0.2        (depuis Router0)

! Ping bout en bout
ping 192.168.2.1     (depuis PC0)
```

### Lire show ip route

```
Router0# show ip route
C    10.0.0.0/30      is directly connected, GigabitEthernet0/1
C    192.168.1.0/24   is directly connected, GigabitEthernet0/0
S    192.168.2.0/24   [1/0] via 10.0.0.2
```

| Code | Signification |
|------|---------------|
| `C` | Route directement connectée |
| `S` | Route statique |
| `S*` | Route statique par défaut |
| `[1/0]` | Distance administrative / métrique |

---

## ✅ Critères de réussite

- [ ] `show ip interface brief` — toutes les interfaces **up/up**
- [ ] `show ip route` sur Router0 — 2 routes **C** + 1 route **S**
- [ ] `show ip route` sur Router1 — 2 routes **C** + 1 route **S**
- [ ] Ping Router0 → Router1 : **succès** (`ping 10.0.0.2`)
- [ ] Ping PC0 → PC1 : **succès** (`ping 192.168.2.1`)
- [ ] Ping PC1 → PC0 : **succès** (`ping 192.168.1.1`)

---

## 💡 Points pédagogiques

**Route statique vs route connectée** — une route `C` est créée automatiquement  
quand une interface est configurée et active. Une route `S` doit être écrite manuellement.

**Symétrie du routage** — si Router0 sait joindre 192.168.2.0, mais que Router1  
ne sait pas joindre 192.168.1.0, le ping aller réussit mais le retour échoue.  
Les deux sens doivent être configurés.

**Le /30** — masque 255.255.255.252, 2 hôtes utilisables.  
Convention : `.1` pour un côté, `.2` pour l'autre.

---

## 📁 Fichiers

| Fichier | Description |
|---------|-------------|
| `lab-05-routage-statique.pka` | Fichier activité Packet Tracer |

---

*Lab précédent : [L-04 — VLANs & ports access](../lab-04-vlans-access/)*  
*Lab suivant : [L-06 — DHCP sur routeur](../lab-06-dhcp/)*
