# 🎯 Kit du Parieur Pro

Outil de calcul **hors-ligne** pour parieurs sportifs sérieux. Un seul fichier HTML, aucune dépendance,
aucune donnée envoyée sur internet : tout tourne dans ton navigateur et tes paris sont stockés en local.

```
paris-pro-toolkit/
└── index.html     ← l'application complète (ouvre-la simplement dans un navigateur)
```

---

## Utilisation rapide

**Option 1 — double-clic** : ouvre `index.html` dans n'importe quel navigateur (Chrome, Firefox, Safari, Edge).
Fonctionne aussi sur mobile, hors connexion.

**Option 2 — serveur local** :

```bash
cd paris-pro-toolkit
python3 -m http.server 8080 --bind 0.0.0.0
# puis ouvre http://localhost:8080
```

---

## Les 6 modules

### ① Value Bet
Le cœur de l'outil. Tu entres **la cote proposée** et **ta propre probabilité estimée**.
L'outil calcule :

| Sortie | Formule |
|---|---|
| Probabilité implicite | `1 ÷ cote` |
| Implicite sans marge | `(1/cote) ÷ (1/cote + 1/cote_opposée)` |
| Marge bookmaker | `(1/cote + 1/cote_opposée) − 1` |
| **Edge** | `ta probabilité − probabilité implicite` |
| **Espérance (EV)** | `(ta probabilité × cote) − 1` |
| Cote minimale acceptable | `1 ÷ ta probabilité` |
| Kelly complet | `((cote−1)×p − (1−p)) ÷ (cote−1)` |

Verdict en trois niveaux : ✅ **VALEUR** (edge ≥ 4 %) · ⚠️ **MARGE TROP MINCE** · ❌ **PIÈGE** (EV négative).

> Renseigne la **cote du résultat opposé** pour mesurer la marge réelle du bookmaker sur ce marché.
> C'est l'information la plus sous-utilisée par les parieurs : une marge de 3 % et une marge de 9 %
> ne se jouent pas de la même façon.

Inclut aussi une **table des seuils** : pour 16 cotes de référence, le taux de réussite minimum
pour ne pas perdre, et l'EV si ton taux réel dépasse ce seuil de 5 points.

### ② Mise & Kelly
Bankroll + probabilité + cote + fraction de Kelly → mise recommandée, gain/perte potentiels, EV du pari.

Un **comparatif de 6 stratégies** (plate 1 %, plate 2 %, ⅒ / ¼ / ½ / Kelly complet) affiche pour chacune
la mise, l'EV par pari, le drawdown typique et un verdict.

Par défaut : **¼ Kelly**. Le Kelly complet suppose que ta probabilité est exacte — elle ne l'est jamais.
Une surestimation de 5 points en Kelly complet peut transformer un edge positif en faillite.

Un bloc « taille d'échantillon » indique combien de paris il faut avant que ton bilan soit statistiquement lisible.

### ③ Comparateur / Surebet
Trois modes :

- **2 issues** — Over/Under, Draw No Bet, handicap asiatique
- **3 issues** — 1X2 complet
- **Dutching** — répartition sur N sélections

Entre les **meilleures cotes** trouvées chez tes différents bookmakers. L'outil calcule la somme des
probabilités implicites :

- **< 100 %** → surebet : profit garanti quelle que soit l'issue, avec la répartition exacte des mises
- **> 100 %** → pas d'arbitrage, avec **la cote minimale qu'il te faudrait** sur la jambe la plus proche du seuil

Inclut un **convertisseur de cotes** décimal ↔ américain ↔ fractionnel ↔ probabilité implicite.

> ⚠️ En mode Dutching, le profit n'est garanti **que si tes sélections couvrent toutes les issues possibles**.
> L'outil le rappelle explicitement : une issue non couverte = perte totale.

### ④ Tracker & CLV
Le module qui fait la différence entre un joueur et un parieur.

Enregistre pour chaque pari : date, ligue, match, marché, sélection, bookmaker, **cote prise**,
**cote de clôture**, mise, statut (gagné / perdu / remboursé / demi-gagné / demi-perdu),
ta probabilité estimée, et une note.

Le tableau de bord calcule en direct :

- Total misé, **P&L**, **ROI (yield)**
- Taux de réussite
- **CLV moyenne** = `cote prise ÷ cote de clôture − 1`
- Edge moyen déclaré
- Bankroll actuelle

Plus : **courbe de profit cumulé** (SVG), filtres par ligue et par statut, et deux tableaux croisés
**performance par ligue** et **performance par marché** — c'est là que tu découvres où tu es vraiment bon
et où tu perds sans le savoir.

La cote de clôture se remplit **après** le match, directement dans le tableau.

**Export / import** : CSV (ouvrable dans Excel, séparateur `;`, BOM UTF-8) et sauvegarde JSON complète.
Les données vivent dans le `localStorage` du navigateur → **exporte une sauvegarde JSON régulièrement**.

### ⑤ Guide Ligues
Base de **31 ligues et catégories** classées en 4 tiers, avec pour chacune :
buts par match, Over 1,5, Over 2,5, intégrité, liquidité, « mollesse » de la cote, marchés conseillés
et commentaire détaillé.

| Tier | Signification |
|---|---|
| **A** | Top niveau : propre et liquide, mais marché très efficient — difficile à battre |
| **B** | ⭐ *Sweet spot* : propre, stable, cotes encore battables |
| **C** | Soft : avantage réel mais limites basses et/ou intégrité à surveiller |
| **D** | 🚫 À éviter absolument |

Comprend aussi :

- le **paradoxe fondamental** (prévisible = bien coté = difficile à battre) et les 4 vraies sources d'avantage
- la **check-list anti-manipulation** en 9 signaux concrets
- le **classement par variance** : les ligues les plus prolifiques ont les marchés de totaux les plus stables

### ⑥ Méthode
Les 8 règles non négociables, la table des **16 marchés à haut taux de réussite** avec leur EV réelle
et un commentaire d'expert, et les ordres de grandeur honnêtes (ROI d'un bon parieur, marge moyenne
des bookmakers, nombre de paris nécessaire pour conclure).

---

## Notes techniques

- **Zéro dépendance.** HTML + CSS + JavaScript vanilla, un seul fichier de ~87 Ko.
- **Mobile-first.** Interface utilisable au téléphone, ce qui est l'usage réel avant un match.
- **Saisie tolérante.** Le point et la virgule décimale sont acceptés (`1.85` ou `1,85`).
- **Affichage français.** Séparateur décimal à virgule, espaces comme séparateurs de milliers.
- **6 devises** : FCFA (XOF, par défaut), EUR, USD, GBP, MAD, NGN.
- **Aucun réseau.** Aucune requête externe, aucun CDN, aucune police tierce, aucun traqueur.
- **Persistance** : `localStorage` (`pp_bets`, `pp_bank`, `pp_cur`).

## Testé

Suite de **81 assertions** exécutées sous jsdom couvrant : rendu initial, exactitude des calculs
(value, EV, Kelly, arbitrage, dutching, conversion de cotes), ajout/modification/suppression de paris,
filtres, agrégats, exports CSV/JSON, persistance, changement de devise, filtres de ligues,
navigation par onglets, et absence d'erreur d'exécution.

---

## Avertissement

Outil **pédagogique**. Il ne prédit aucun résultat et ne fournit aucun conseil financier.
Il sert à une seule chose : t'empêcher de placer des paris à espérance négative sans le savoir,
et à mesurer objectivement si tu bats le marché.

Les paris sportifs restent à espérance négative pour l'immense majorité des joueurs.
Joue uniquement auprès d'un opérateur légalement agréé dans ton pays, fixe tes limites de dépôt
**à l'avance**, et considère cet argent comme un budget loisir déjà dépensé.
Jouer comporte des risques : endettement, dépendance, isolement.
