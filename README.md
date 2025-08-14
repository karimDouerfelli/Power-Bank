the english version is after 

# 🔋 Powerbank 10 000 mAh – Guide Complet PCB (Version 2 × Cellules 1S)

## 1. 📖 L’histoire

On veut fabriquer une **batterie externe** qui :

* Se recharge en USB-C
* Peut charger un téléphone vite (QC/PD)
* A une capacité \~**10 000 mAh**
* Utilise **2 cellules 1S** (en parallèle = 1S2P)

---

## 2. 🧠 Comment ça marche

Imagine deux bouteilles d’eau identiques (les **cellules**).
Si on les relie en parallèle, c’est comme verser dans une grande bassine : plus de capacité mais **même tension**.
Ensuite, un **cerveau électronique** (le BMS et le chip de gestion USB) contrôle :

* **La charge** (ne pas dépasser la tension max)
* **La décharge** (ne pas descendre trop bas)
* **La température** (ne pas chauffer)

---

## 3. 📦 Les ingrédients (BOM principale)

| Nom du composant                | Rôle                                                          | Référence conseillée               |
| ------------------------------- | ------------------------------------------------------------- | ---------------------------------- |
| **Contrôleur USB-C tout-en-un** | Gérer charge/décharge USB, QC, PD                             | IP5328P                            |
| **BMS 1S**                      | Protéger la batterie (surcharge, court-circuit, sous-tension) | DW01A + FS8205A                    |
| **MOSFETs**                     | Couper ou autoriser le courant                                | FS8205A (dans le BMS)              |
| **Cellules Li-ion**             | Source d’énergie                                              | 2× Samsung INR21700-50E (5000 mAh) |
| **NTC 10 kΩ**                   | Mesurer température                                           | 103AT-2                            |
| **PTC (Polyfuse)**              | Protection contre surcourant                                  | RUE600-0.5                         |
| **Condensateurs céramiques**    | Lisser le courant                                             | 10 µF et 22 µF X5R/X7R             |
| **Résistances de détection**    | Mesurer courant/tension                                       | Shunt 10–20 mΩ                     |
| **Connecteur USB-C**            | Entrée/sortie énergie                                         | Type-C réversible                  |
| **PCB 4 couches**               | Support et connexions                                         | 100×60 mm, 1 oz cuivre             |

---

## 4. 🛠 Schéma de principe

```
[Cellule 1]---+
              |---(Pont de mise en parallèle)---[VBAT]---[BMS 1S]---[IP5328P]---USB-C
[Cellule 2]---+

[NTC] -> vers IP5328P
[PTC] -> sur ligne VBAT après les cellules
```

* **Cellule 1** et **Cellule 2** sont reliées **en parallèle** via un pont (peut être un jumper ou une résistance d’équilibrage temporaire).
* **VBAT** = point où la tension batterie arrive sur le PCB.
* **BMS 1S** placé **avant** le contrôleur USB pour protéger la batterie.

---

## 5. 🎨 Design du PCB (4 couches)

**Couche 1 (Top)**

* Composants principaux (IP5328P, BMS, USB-C)
* Pistes de signal
* Zones de cuivre pour le **+BAT** et **GND**
* Largeur piste batterie : ≥ 2 mm (pour 3–5 A)

**Couche 2 (GND)**

* Plan de masse complet (GND)
* Connexions directes aux pads GND des composants

**Couche 3 (VBAT)**

* Plan entier pour le +BAT (après BMS)
* Évite pertes et échauffement

**Couche 4 (Bottom)**

* Composants secondaires (résistances, petits condos)
* Pads pour test (VBAT\_A, VBAT\_B, VBAT\_SUM)
* Pont de mise en parallèle (empreinte de résistance/jumper)

---

## 6. 🧩 Étapes de routage

1. **Commencer par le connecteur USB-C**

   * Traces différentielles pour CC1/CC2 si besoin
   * Pistes larges pour VBUS et GND
2. **Placer l’IP5328P** proche du port USB-C

   * Condensateurs d’entrée/sortie **le plus près possible**
3. **BMS 1S** proche des pads BAT+ / BAT-

   * MOSFETs orientés pour flux direct vers IP5328P
4. **Plans de cuivre** :

   * GND sur Couche 2
   * VBAT sur Couche 3
5. **Pads séparés pour Cellule 1 et Cellule 2**

   * Permet test individuel avant soudure
6. **Pont de mise en parallèle** :

   * Footprint d’une résistance 1206 (0.5–1 Ω)
   * Remplacée par un shunt une fois équilibré
7. **NTC** :

   * Empreinte pour connecteur JST ou soudage direct
   * Placer piste fine vers IP5328P

---

## 7. 🔌 Connexions importantes

* **VBAT\_A** et **VBAT\_B** : chaque cellule séparée
* **Pont parallèle** : à fermer après équilibrage
* **PTC** : en série sur VBAT pour protection
* **NTC** : collée sur cellule et reliée au contrôleur
* **Shunt courant** : entre BMS et IP5328P

---

## 8. 📐 Règles de fabrication PCB

* **Épaisseur cuivre** : 1 oz (35 µm)
* **Largeur piste puissance** : ≥ 2 mm pour >3 A
* **Via stitching** sur zones VBAT et GND
* **Minimum clearance** : 0.2 mm
* **Sérigraphie** claire (polarités, repères tests)

---

## 9. 📋 Procédure d’assemblage

1. Souder tous les composants CMS du PCB
2. Fixer connecteur USB-C
3. Relier cellules **sans** fermer le pont parallèle
4. Mesurer tension des deux cellules

   * Si écart ≤ 0.05 V → fermer pont (shunt)
5. Fixer NTC sur cellule
6. Brancher batterie et tester charge/décharge
7. Si tout OK → fermer boîtier

---

## 10. 💰 Coût estimatif

| Élément                  | Prix (\~€) |
| ------------------------ | ---------- |
| IP5328P                  | 0.90       |
| BMS 1S                   | 0.40       |
| MOSFETs supplémentaires  | 0.20       |
| USB-C                    | 0.30       |
| Passifs (résistances, C) | 1.50       |
| PCB 4 couches            | 3.00       |
| Cellules Li-ion          | 6.90       |
| **Total**                | **\~16.8** |

---

## 11. ⚠ Conseils sécurité

* Ne jamais court-circuiter une cellule
* Toujours vérifier température lors des premiers cycles
* Utiliser uniquement cellules en bon état et identiques

---


