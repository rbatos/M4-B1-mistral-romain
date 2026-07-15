# Tableau comparatif — Benchmark Mistral Assurances

> Document à remettre à **Inès Tabet** (responsable actuariat). Doit être
> lisible par un actuaire, pas par un data scientist.
> Auteur : Romain — Date : 13/07/2026

## Méthodologie

- **Split** : TimeSeriesSplit (n_splits=5)
- **Métriques** : MAE, RMSE, R² (moyennes CrossValidation), + stabilité (Std R²)
- **Mêmes folds, mêmes features** pour tous les modèles (règle d'or *comparabilité*)
- **Hyperparamètres** : paramètres de base, RandomForest avec n_estimators=300
- **Référence** : baseline `mistral-tarif-v1` (LinearRegression 2024)

## Tableau (à compléter)

| Modèle | MAE | RMSE | R² | Std R² (CV) | Temps train (s) | Latence inférence (ms/ligne) | Explicabilité (1-3) | Mémoire (Mo) |
|---|---|---|---|---|---|---|---|---|
| **mistral-tarif-v1** (baseline) | 105.0 | 139.4 | 0.39 | n.d. | ~0.1 | ~0.0010 | 3 (très explicable) | 0.001 |
| **HistGradientBoostingRegressor** | 51.89 | 76.17 | 0.796 | 0.124 | 0.745 | 0.0125 | 1 (faible) | 0.15 |
| **RandomForestRegressor** | 53.78 | 81.03 | 0.772 | 0.129 | 4.983 | 0.1466 | 2 (moyenne) | 77.03 |
| **Ridge (linéaire + scaling)** | 105.39 | 138.58 | 0.392 | 0.067 | 0.008 | 0.0013 | 3 (forte) | 0.002 |

## Interprétation pour Inès

> 1-2 paragraphes en langage actuaire (pas data scientist).

Le modèle HistGradientBoostingRegressor domine clairement sur la qualité de prédiction : il réduit de moitié les erreurs par rapport au modèle historique, et augmente donc la prédiction de 40% à 80%. Le RandomForest est également performant, mais légèrement moins précis et surtout plus coûteux en calcul (temps d'entraînement et temps de réponse plus élevés).

Le modèle Ridge reste intéressant en lecture métier (explicable, très rapide), mais il n'apporte pas de gain de performance par rapport à la baseline. Pour un usage tarifaire opérationnel, HistGradientBoosting offre le meilleur compromis précision/coût de service, avec une latence encore compatible pour de l'inférence en ligne.

## Recommandation

Voir `verdict.md` (5 lignes max).

Recommandation chiffrée : retenir HistGradientBoostingRegressor comme modèle cible v2.
Garder Ridge comme baseline de contrôle (drift et monitoring), et ne conserver RandomForest qu'en challenger secondaire.

---

*Document remis à Inès Tabet — 13/07/2026.*
