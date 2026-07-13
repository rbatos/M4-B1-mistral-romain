# Verdict — Recommandation Mistral Assurances

> **5 lignes maximum.** Document remis à Inès Tabet.
> Auteur : Romain — Date : 13/07/2026

**Recommandation** : déployer HistGradientBoostingRegressor comme modèle cible v2.

**Raison principale (chiffrée)** : selon la grille C4, le volume (~17k) et le signal non-linéaire (saisonnalité) orientent vers le boosting, HistGradientBoostingRegressor est donc le meilleur (R² 0.796 vs 0.39 baseline, MAE 51.89, RMSE 76.17), avec une latence de 0.0125 ms/ligne (< 10 ms) et un train de 0.745 s.

**Condition de changement d'avis** : si la contrainte métier devient l'explicabilité réglementaire forte, la latence ultra-minimale, ou la maintenance quasi nulle, alors je bascule sur le Ridge et le RandomForest reste challenger si l'arbitrage favorise davantage la robustesse que le coût de calcul.

---

*Verdict produit par Romain, 13/07/2026, dans le cadre du brief M4-B1 ATOS.*
