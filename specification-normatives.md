

---

# **📘 Proposition de `specification-normatives.md` (version complète et professionnelle)**

```markdown
# Norme de Structure de Transport de Prompts Prédictifs (NSTPP)

Version : 0.1  
Statut : Draft  
Auteur : Projet Treeiat  

---

## 1. Objet de la norme

La Norme de Structure de Transport de Prompts Prédictifs (NSTPP) définit un format strict, déterministe et non ambigu destiné au transport, à la sérialisation et à la validation de prompts structurés pour systèmes prédictifs (IA génératives, moteurs symboliques, agents hybrides).

Cette norme vise à garantir :

- une structure stable et vérifiable,
- une absence totale d’ambiguïité syntaxique,
- une compatibilité machine‑machine optimale,
- une dérivabilité automatique vers des représentations symboliques ou binaires.

---

## 2. Principes fondamentaux

La NSTPP repose sur les principes suivants :

1. **Déterminisme total**  
   Chaque structure doit produire une représentation unique, sans variation possible.

2. **Grammaire fermée**  
   L’ensemble des constructions autorisées est fini, explicite et non extensible sans révision normative.

3. **Absence de texte libre**  
   Aucun segment textuel non typé n’est autorisé dans la structure normative.

4. **Auto‑délimitation**  
   Chaque bloc doit contenir sa propre information de taille ou de clôture.

5. **Bijection structurelle**  
   Toute structure NSTPP doit pouvoir être convertie en une représentation binaire ou symbolique sans perte d’information.

---

## 3. Modèle conceptuel

La NSTPP définit trois entités fondamentales :

### 3.1. Nœud (Node)
Un nœud est l’unité structurelle minimale.  
Il possède :

- un **type** (entier non signé),
- zéro ou plusieurs **attributs**,
- zéro ou plusieurs **enfants**.

### 3.2. Attribut (Attribute)
Un attribut est une paire clé‑valeur typée.  
Les types autorisés sont :

- entier (32 ou 64 bits),
- flottant (32 ou 64 bits),
- booléen,
- identifiant symbolique (entier),
- vecteur de valeurs typées.

### 3.3. Arbre (Tree)
Un arbre est un nœud racine et l’ensemble de ses descendants.

---

## 4. Structure normative

### 4.1. Format général

Une structure NSTPP est composée de blocs auto‑délimités :

```
<Bloc> ::= <Header> <Payload>
```

#### 4.1.1. Header
Le header contient :

- `TypeID` (uint16)
- `AttributeCount` (uint8)
- `ChildCount` (uint8)
- `PayloadLength` (uint32)

#### 4.1.2. Payload
Le payload contient :

1. la liste des attributs,
2. la liste des enfants (chacun étant un bloc complet).

---

## 5. Règles de validation

Une structure NSTPP est valide si :

1. tous les blocs respectent la grammaire,
2. les longueurs déclarées correspondent aux longueurs réelles,
3. les types d’attributs sont conformes,
4. aucun identifiant de type n’est hors du domaine autorisé,
5. l’arbre est bien formé (pas de cycles, pas de références externes).

---

## 6. Domaine des types

La norme définit un domaine fermé de types :

| TypeID | Nom | Description |
|--------|------|-------------|
| 1 | Prompt | Racine d’un prompt prédictif |
| 2 | Instruction | Directive structurée |
| 3 | Input | Entrée utilisateur typée |
| 4 | Output | Sortie attendue ou contrainte |
| 5 | Metadata | Informations auxiliaires |

Aucun autre type n’est autorisé dans la version 0.1.

---

## 7. Exemples normatifs

### 7.1. Exemple minimal

```
Prompt
 └─ Instruction(type=“summarize”)
```

### 7.2. Exemple avec attributs

```
Prompt
 ├─ Metadata(model=42)
 └─ Instruction(type=“translate”, lang=“en”)
```

---

## 8. Compatibilité et dérivabilité

La NSTPP doit pouvoir être dérivée vers :

- une représentation binaire compacte,
- une représentation symbolique (tokens),
- une représentation JSON strictement typée.

La dérivation doit être bijective.

---

## 9. Versioning

La norme utilise un versioning sémantique :

- `MAJOR` : rupture de compatibilité,
- `MINOR` : ajout de types ou règles compatibles,
- `PATCH` : corrections mineures.

---

## 10. Annexes

### 10.1. Domaines numériques

- `uint8` : 0–255  
- `uint16` : 0–65535  
- `uint32` : 0–4 294 967 295  

### 10.2. Encodage

Tous les entiers sont encodés en big‑endian.

---

```

---

