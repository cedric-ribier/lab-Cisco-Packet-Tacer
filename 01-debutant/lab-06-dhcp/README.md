# L-06 — DHCP sur routeur

> **Niveau :** 🟢 Débutant · **Durée estimée :** 20 min · **Prérequis :** [L-05](../lab-05-routage-statique/)

Configurer un serveur DHCP directement sur un routeur Cisco, définir les pools  
par réseau, et vérifier l'attribution automatique des adresses IP sur les PCs.

---

## 🎯 Objectifs

- Créer des pools DHCP sur un routeur
- Définir les exclusions, la passerelle et le DNS
- Passer les PCs en DHCP et vérifier les baux
- Utiliser `show ip dhcp binding` et `show ip dhcp pool`

---

## 🗺️ Topologie

```
PC0 ──── Switch0 ──── Router0 ──────── Router1 ──── Switch1 ──── PC1
       192.168.1.0/24    10.0.0.0/30        192.168.2.0/24
         DHCP pool 1                          DHCP pool 2
```

> Le routage statique entre Router0 et Router1 est déjà configuré (solution L-05).  
> Tu n'as qu'à ajouter les pools DHCP et basculer les PCs en DHCP.

---

## 📋 Plan d'adressage

| Équipement | Interface | Adresse IP       | Masque          |
|------------|-----------|------------------|-----------------|
| Router0    | Gi0/0     | 192.168.1.254/24 | 255.255.255.0   |
| Router0    | Gi0/1     | 10.0.0.1/30      | 255.255.255.252 |
| Router1    | Gi0/0     | 10.0.0.2/30      | 255.255.255.252 |
| Router1    | Gi0/1     | 192.168.2.254/24 | 255.255.255.0   |
| PC0        | Fa0       | via DHCP         | —               |
| PC1        | Fa0       | via DHCP         | —               |

---

## 📋 Pools DHCP

| Pool       | Réseau           | Exclusions          | Passerelle       | DNS          |
|------------|------------------|---------------------|------------------|--------------|
| LAN1       | 192.168.1.0/24   | .1 → .10 / .254     | 192.168.1.254    | 8.8.8.8      |
| LAN2       | 192.168.2.0/24   | .1 → .10 / .254     | 192.168.2.254    | 8.8.8.8      |

> Les exclusions réservent les adresses des équipements réseau (.254) et une plage  
> serveurs (.1 → .10). Les PCs recevront des IPs à partir de .11.

---

## 📝 Travail demandé

### Étape 1 — Pool DHCP sur Router0 (réseau 192.168.1.0/24)

```
configure terminal
ip dhcp excluded-address 192.168.1.1 192.168.1.10
ip dhcp excluded-address 192.168.1.254
ip dhcp pool LAN1
 network 192.168.1.0 255.255.255.0
 default-router 192.168.1.254
 dns-server 8.8.8.8
 lease 7
end
```

### Étape 2 — Pool DHCP sur Router1 (réseau 192.168.2.0/24)

```
configure terminal
ip dhcp excluded-address 192.168.2.1 192.168.2.10
ip dhcp excluded-address 192.168.2.254
ip dhcp pool LAN2
 network 192.168.2.0 255.255.255.0
 default-router 192.168.2.254
 dns-server 8.8.8.8
 lease 7
end
```

### Étape 3 — Basculer les PCs en DHCP

Sur chaque PC : `Desktop → IP Configuration → DHCP`  
Attendre quelques secondes — l'IP s'attribue automatiquement.

### Étape 4 — Sauvegarder

```
copy running-config startup-config
```

---

## 🔑 Commandes de vérification

```
! Voir les baux actifs
show ip dhcp binding

! Voir les pools configurés
show ip dhcp pool

! Voir les adresses exclues et conflits
show ip dhcp conflict

! Depuis un PC (après attribution DHCP)
ipconfig
ping 192.168.2.x    (IP attribuée à PC1)
```

---

## ✅ Critères de réussite

- [ ] PC0 reçoit une IP en `192.168.1.x` (x ≥ 11)
- [ ] PC1 reçoit une IP en `192.168.2.x` (x ≥ 11)
- [ ] `show ip dhcp binding` — 2 baux actifs
- [ ] Passerelle correcte sur chaque PC
- [ ] Ping PC0 → PC1 : **succès**

---

## 💡 Points pédagogiques

**Pourquoi exclure des adresses ?**  
Les équipements réseau (routeurs, serveurs, imprimantes) ont besoin d'IPs fixes.  
Si le DHCP les distribue, un conflit d'adresse peut survenir au démarrage.

**`lease 7`** — bail de 7 jours. En production on ajuste selon les besoins  
(ex. `lease 0 1` = 1 heure pour une salle de formation).

**Chaque routeur gère son propre pool** — en L-08 et L-12, on verra comment  
centraliser le DHCP sur un seul serveur pour plusieurs VLANs via `ip helper-address`.

---

## 📁 Fichiers

| Fichier | Description |
|---------|-------------|
| `lab-06-dhcp.pka` | Fichier activité Packet Tracer |

---

*Lab précédent : [L-05 — Routage statique](../lab-05-routage-statique/)*  
*Lab suivant : [L-07 — VTP & trunks dot1q](../lab-07-vtp-trunks/)*
