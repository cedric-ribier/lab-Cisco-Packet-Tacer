# L-01 — Premier pas

> **Niveau :** 🟢 Débutant · **Durée estimée :** 15 min · **Prérequis :** Aucun

Premier contact avec Cisco Packet Tracer : câbler une topologie simple, naviguer dans  
l'interface IOS et tester la communication entre deux PCs.

---

## 🎯 Objectifs

- Placer et câbler des équipements dans Packet Tracer
- Naviguer entre les modes IOS (`user`, `enable`, `config`)
- Configurer des adresses IP sur des PCs
- Tester la connectivité avec `ping`

---

## 🗺️ Topologie

```
PC0 ──── Switch2960 ──── PC1
         192.168.1.0/24
```

| Équipement | Interface | Adresse IP    | Masque        |
|------------|-----------|---------------|---------------|
| PC0        | Fa0       | 192.168.1.1    | 255.255.255.0 |
| PC1        | Fa0       | 192.168.1.2    | 255.255.255.0 |
| Switch0    | —         | —             | —             |

---

## 📝 Travail demandé

1. Placer un switch **2960** et deux **PC-PT** sur le plan de travail
2. Câbler les PCs sur le switch avec des câbles **Copper Straight-Through**
3. Configurer les adresses IP sur chaque PC (`Desktop → IP Configuration`)
4. Tester la communication : `ping 192.168.1.2` depuis PC0

---

## 🔑 Commandes de vérification

```
! Depuis le CLI d'un PC
ping 192.168.1.2

! Depuis le switch (optionnel)
enable
show mac address-table
show interfaces status
```

---

## ✅ Critères de réussite

- [ ] Les deux PCs sont câblés au switch (voyants verts)
- [ ] PC0 → PC1 : ping réussi
- [ ] PC1 → PC0 : ping réussi

---

## 📁 Fichiers

| Fichier | Description |
|---------|-------------|
| `lab-01-premier-pas.pka` | Fichier activité Packet Tracer |

---

*Lab suivant : [L-02 — VLANs & ports access](../lab-02-vlans-access/)*
