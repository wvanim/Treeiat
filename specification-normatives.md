---

# 🌐 **Treeia‑Token v1.0 — Spécification normatives**  
### *Format symbolique IA‑natif dérivé automatiquement du binaire STS*

---

# **0. Objectif**

Treeia‑Token est la **représentation symbolique** de Treeia‑STS.  
Il ne contient **aucune sémantique métier** et ne définit **aucune structure** par lui‑même.

Il est entièrement dérivé de :

- la librairie **STRUCTS** du fichier STS  
- les tables **TOKEN_STRUCTS**, **TOKEN_PARAMS**, **TOKEN_TYPES**, **TOKEN_CONST** ajoutées au binaire  

Treeia‑Token est donc :

- **bijectif** avec STS  
- **déterministe**  
- **IA‑natif**  
- **sans texte humain**  
- **sans ponctuation**  
- **sans ambiguïté**  

---

# **1. Principes fondamentaux**

### ✔ 1.1. Format 100 % symbolique  
Aucun mot, aucune chaîne UTF‑8, aucune ponctuation.  
Uniquement des **tokens PUA** (Private Use Area Unicode).
Toutes les valeurs multi‑octets (uint16, uint32, float32, etc.) sont encodées en little‑endian, conformément à STS.

### ✔ 1.2. Dérivation automatique depuis STS  
Tous les tokens proviennent de tables STS :

- `TOKEN_STRUCTS`  
- `TOKEN_PARAMS`  
- `TOKEN_TYPES`  
- `TOKEN_CONST`  

Aucun token n’est défini dans la spec elle‑même.

### ✔ 1.3. Structure auto‑délimitée  
Les blocs utilisent :

- `BEGIN_BLOCK` (PUA fixe)  
- `END_BLOCK` (PUA fixe)  

Pas de tailles explicites.

### ✔ 1.4. Déterminisme absolu  
Le parsing est **sans heuristique** :  
l’ordre des paramètres est dicté par STRUCTS.

### ✔ 1.5. IA‑friendly  
- tokens courts  
- grammaire fermée  
- pas de choix syntaxique  
- pas de ponctuation  
- pas de texte libre  

---

# **2. Tables nécessaires dans STS**

Pour permettre la génération automatique du Token, STS doit contenir 4 tables :

---

## **2.1. TOKEN_STRUCTS (Library)**  
Associe un token PUA à chaque `struct_id`.

```
struct_id   : uint16
token_code  : uint32   // PUA
flags       : uint16   // 0
```

---

## **2.2. TOKEN_PARAMS (Library)**  
Associe un token PUA à chaque paramètre d’une struct.

```
struct_id   : uint16
param_index : uint16
token_code  : uint32   // PUA
flags       : uint16
```

---

## **2.3. TOKEN_TYPES (Library)**  
Associe un token PUA à chaque opcode primitif STS.

```
opcode      : uint8    // ex : 0x06 = float
token_code  : uint32   // PUA
flags       : uint16
```

---

## **2.4. TOKEN_CONST (Library)**  
Associe un token PUA à chaque constante prédéfinie STS.

```
const_id    : uint16
token_code  : uint32   // PUA
flags       : uint16
```

---

# **3. Tokens réservés (fixes)**

Ces tokens ne dépendent pas de STS :

| Rôle | Token PUA |
|------|-----------|
| BEGIN_BLOCK | U+F400 |
| END_BLOCK | U+F401 |
| SYMBOL_DEF | U+F402 |
| SYMBOL_REF | U+F403 |

---

# **4. Grammaire Treeia‑Token**

### **4.1. Structure générale**

```
<file> ::= <symbol-def>* <script>
```

---

### **4.2. Définition de symbole**

```
<symbol-def> ::= SYMBOL_DEF <symbol-id:uint16> <block>
```

---

### **4.3. Bloc**

```
<block> ::= BEGIN_BLOCK <instruction>* END_BLOCK
```

---

### **4.4. Instruction**

Une instruction peut être :

```
<instruction> ::= <struct-instance>
                | <symbol-ref>
                | <primitive-value>
```

---

### **4.5. Référence à un symbole**

```
<symbol-ref> ::= SYMBOL_REF <symbol-id:uint16>
```

---

### **4.6. Instanciation de structure**

```
<struct-instance> ::= <token-struct> <field>*
```

Où `<token-struct>` vient de `TOKEN_STRUCTS`.

---

### **4.7. Champ**

```
<field> ::= <token-param> <value>
```

Où `<token-param>` vient de `TOKEN_PARAMS`.

---

### **4.8. Valeur**

```
<value> ::= <token-type> <raw-bytes>
          | <token-const>
          | <struct-instance>
          | <block>
```

---

# **5. Exemple complet (générique, non métier)**

Supposons que STS contient :

```
struct_id = 1  name="Coord"
  param0: float
  param1: float
```

Et que les tables STS donnent :

```
TOKEN_STRUCTS:
  struct 1 → U+E100

TOKEN_PARAMS:
  (1,0) → U+E200
  (1,1) → U+E201

TOKEN_TYPES:
  float (0x06) → U+E300
```

Alors Treeia‑Token pour :

```
Coord(10.0, 20.0)
```

devient :

```
⟦E100⟧        // struct Coord
  ⟦E200⟧ ⟦E300⟧ 00 00 20 41   // param0 = float 10.0
  ⟦E201⟧ ⟦E300⟧ 00 00 A0 41   // param1 = float 20.0
```

Aucune sémantique.  
Aucun mot.  
Aucun concept métier.  
100 % dérivable du binaire.

---

# **6. Propriétés garanties**

### ✔ Bijectif  
Token ↔ STS ↔ Token

### ✔ Déterministe  
Aucun choix syntaxique.

### ✔ IA‑natif  
Tokens courts, grammaire fermée.

### ✔ Neutre  
Aucune connaissance métier.

### ✔ Extensible  
Ajout de nouvelles structs → nouveaux tokens dérivés automatiquement.

---

La présente section constitue la définition normative du langage Treeia‑Token.
Toute implémentation conforme doit être capable de parser et de générer un flux respectant strictement cette grammaire.

# **7. Grammaire formelle (BNF)


# 🌐 **BNF officielle — Treeia‑Token v1.0 (complète)**

```bnf
; ============================
;  Treeia‑Token v1.0 — BNF
; ============================

<file> ::= <symbol-def>* <script>

<script> ::= <instruction>*

; ----------------------------
;  Symboles
; ----------------------------

<symbol-def> ::= SYMBOL_DEF <symbol-id> <block>

<symbol-id> ::= uint16

<symbol-ref> ::= SYMBOL_REF <symbol-id>

; ----------------------------
;  Blocs
; ----------------------------

<block> ::= BEGIN_BLOCK <instruction>* END_BLOCK

; ----------------------------
;  Instructions
; ----------------------------

<instruction> ::= <struct-instance>
                | <symbol-ref>
                | <primitive-value>
                | <block>

; ----------------------------
;  Structures dérivées de STS
; ----------------------------

<struct-instance> ::= <token-struct> <field>*

<token-struct> ::= PUA_STRUCT   ; dérivé de TOKEN_STRUCTS

<field> ::= <token-param> <value>

<token-param> ::= PUA_PARAM     ; dérivé de TOKEN_PARAMS

; ----------------------------
;  Valeurs
; ----------------------------

<value> ::= <primitive-value>
          | <token-const>
          | <struct-instance>
          | <block>

; ----------------------------
;  Primitifs dérivés de STS
; ----------------------------

<primitive-value> ::= <token-type> <raw-bytes>

<token-type> ::= PUA_TYPE       ; dérivé de TOKEN_TYPES

<raw-bytes> ::= byte*           ; longueur dictée par le type

; ----------------------------
;  Constantes prédéfinies
; ----------------------------

<token-const> ::= PUA_CONST     ; dérivé de TOKEN_CONST

; ----------------------------
;  Tokens réservés
; ----------------------------

BEGIN_BLOCK ::= U+F400
END_BLOCK   ::= U+F401
SYMBOL_DEF  ::= U+F402
SYMBOL_REF  ::= U+F403

; ----------------------------
;  Tokens dérivés de STS
; ----------------------------

PUA_STRUCT ::= U+E000–U+E3FF    ; struct_id → token
PUA_PARAM  ::= U+E400–U+E7FF    ; (struct_id,param_index) → token
PUA_TYPE   ::= U+E800–U+EBFF    ; opcode primitif → token
PUA_CONST  ::= U+EC00–U+EFFF    ; const_predef → token
```

