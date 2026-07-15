# Carte de décision personnelle — M4-B1

> **Ton ébauche perso** de la grille de décision C4, **avant** la
> restitution collective de mercredi. À confronter aux propositions des
> autres pour construire la grille commune.
> Auteur : Romain — Date : 13/07/2026

## Critères que je mobilise (mon ordre de priorité)

1. Précision en exploitation (MAE/RMSE/R2) sur la saisonnalité de la location de vélo.
2. Latence d'inférence compatible usage métier (objectif < 10 ms/ligne).
3. Explicabilité suffisante pour un échange actuaire (SHAP acceptable si gain fort).
4. Coût de maintenance/retraining raisonnable (pipeline trimestriel possible).

## Modèles que j'ai benchmarkés

> Liste rapide.

- Famille A (linéaire) : Ridge (avec StandardScaler dans Pipeline).
- Famille B (arbre) : RandomForestRegressor.
- Famille C (boosting) : HistGradientBoostingRegressor.

## Pour quel cas je choisirais chaque famille ?

> Si Inès me demandait *« et pour un cas X, vous prendriez quoi ? »*.

| Cas | Famille recommandée | Pourquoi |
|---|---|---|
| **Mistral Bike Sharing (saisonnalité forte)** | **Boosting (HistGradientBoostingRegressor)** | Meilleur compromis C4 observé : MAE 51.89, RMSE 76.17, R2 0.796, latence 0.0125 ms/ligne, train 0.745 s. |
| **Cas similaire mais 100 lignes seulement** | **Linéaire (Ridge)** | Petit volume = risque de surapprentissage pour RF/HGB. Ridge est le plus stable, très léger et explicable. |
| **Cas avec exigence d'explicabilité réglementaire** | **Linéaire (Ridge)** | Coefficients interprétables directement, justification plus simple en audit qu'un modèle boîte noire. |
| **Cas avec latence < 5 ms imposée** | **Linéaire (Ridge)** | Latence mesurée 1,3 ms/1k ligne (et baseline ~1), donc très proche. Les 2 autres modèle dépassent les 10ms pour 1k lignes. |

## Analyse éthique et réglementaire (C2)

> ~5 lignes. Le geste : **évaluer s'il existe réellement des risques**, puis
> **conclure**. Conclure « risque faible » est une réponse valide et
> professionnelle — pas besoin d'inventer un problème.

- Le dataset contient-il des **données personnelles** ? Non, données agrégées de location + météo/temps, sans identifiant individuel.
- Utilise-t-on un **attribut sensible** / y a-t-il un risque de **biais** envers une population ? Pas d'attribut sensible direct. Risque résiduel = biais géographique/saisonnier si le contexte d'usage change.
- Y a-t-il un enjeu **RGPD / confidentialité** ? Faible sur ce dataset.
- Quel **impact sociétal** de l'usage du modèle (destinataires directs/indirects) ? Le modèle influence l'allocation des vélos, donc la qualité de service selon zones/heures.
- **Conclusion** : risque faible dans le périmètre actuel (données agrégées, pas de PII), avec vigilance sur l'équité de service et audit périodique en cas d'extension des données.

## Ouverture — Robustesse d'une solution d'IA (hors C2, prépare M7)

> Cette activité **ne relève pas de C2**. C'est un **complément à ta décision
> C4** et une ouverture vers **M7** (menaces sur les systèmes d'IA) : un réflexe
> pro = évaluer les limites d'un modèle au-delà de ses performances. Une ligne
> suffit. Cf. mini-cours `06_Menaces_robustesse_essentiel.md`.

- **Vulnérabilité identifiée** : risque hors-distribution (valeurs météo/usage hors plage d'entraînement) avec extrapolation silencieuse du modèle.
- **Garde-fou envisagé** en conception : contrôle des bornes d'entrée + seuil d'abstention :
    - Avant `model.predict()` : contrôle de plages (hour ∈ [0,23], features normées ∈ [0,1], etc.) + alerte OOD (log + compteur)
    - Dans `preprocess.py` : validation de schéma (colonnes attendues, types, valeurs manquantes)

## Ce que je veux apporter à la grille collective

> 1-2 contributions ou questions à pousser pendant la restitution.

- Proposition d'une règle de choix en 2 temps: d'abord performance (MAE/RMSE/R2), puis filtre opérationnel (latence, explicabilité, maintenance).

---

*Carte personnelle à compléter avant la restitution collective mercredi 11h30.*
