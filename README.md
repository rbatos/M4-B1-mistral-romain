# M4-B1 — Benchmark Mistral Assurances (Romain)

Benchmark de modèles de régression sur `bike_sharing.csv` pour recommander un modèle de tarification/prévision robuste, lisible et industrialisable.

---

## ✅ État d’avancement du projet

- [x] Reprise baseline `mistral-tarif-v1` (référence R² ≈ 0.39)
- [x] EDA saisonnalité (4 visualisations)
- [x] Feature engineering métier :
  - encodage `season` en ordinal
  - création `is_rush_hour` (pics semaine matin/soir)
- [x] Validation croisée temporelle avec `TimeSeriesSplit(n_splits=5)`
- [x] Benchmark de 3 familles :
  - Ridge (+ `StandardScaler` en pipeline)
  - RandomForestRegressor
  - HistGradientBoostingRegressor
- [x] Sauvegarde des modèles en `.joblib` avec compression (`compress=3`)
- [x] Génération de métadonnées `.json` (versions, timestamp, hash dataset, hyperparamètres, métriques, features)

---

## 📁 Structure actuelle

```text
M4-B1-mistral-romain/
├── data/
│   └── bike_sharing.csv
├── notebooks/
│   └── M4-B1_Romain.ipynb
├── src/
│   ├── preprocess.py
│   ├── train_models.py
│   └── evaluate.py
├── models/                             # artefacts générés au benchmark
│   ├── Ridge_(lineaire_+_scaling).joblib
│   ├── RandomForestRegressor.joblib
│   ├── HistGradientBoostingRegressor.joblib
│   └── *.json                          # métadonnées par modèle
├── benchmark_table.md
├── decision_card.md
├── verdict.md
├── requirements.txt
└── README.md
```

---

## 🚀 Exécution (Windows / VS Code)

1. Créer et activer l’environnement :
```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python.exe -m pip install --upgrade pip
pip install -r requirements.txt
```

2. Lancer Jupyter :
```powershell
jupyter notebook notebooks/M4-B1_Romain.ipynb
```

> Si avertissement `loky` sous Windows : définir `LOKY_MAX_CPU_COUNT` dans la première cellule avant imports sklearn/joblib.

---

## 🧠 Méthodologie retenue

- **Split temporel** pour éviter fuite temporelle (`TimeSeriesSplit`).
- **Comparaison équitable** : mêmes folds, mêmes métriques, mêmes règles d’évaluation.
- **Métriques suivies** :
  - MAE
  - RMSE
  - R²
  - écart-type du R² (stabilité CV)
  - temps d’entraînement
  - latence d’inférence (ms/ligne)

---

## 🧪 Features utilisées

- Numériques :  
  `year`, `month`, `hour`, `is_holiday`, `weekday`, `is_working_day`, `weather`,  
  `temperature_norm`, `temperature_feels_norm`, `humidity_norm`, `windspeed_norm`, `is_rush_hour`
- Catégorielle encodée :  
  `season` → `{winter:1, spring:2, summer:3, fall:4}`
- Cible :  
  `total_rentals`

---

## 📦 Artefacts produits

Pour chaque modèle benchmarké :
- `models/<nom_modele>.joblib` (compression `compress=3`)
- `models/<nom_modele>.json` avec :
  - versions (`python`, `scikit-learn`, `pandas`, `numpy`)
  - timestamp
  - hash dataset
  - hyperparamètres
  - métriques CV
  - colonnes de features
  - flag `selected_model`

---

## ⚠️ Points d’attention

- Ne pas inclure `casual_riders` / `registered_riders` (fuite de cible).
- Conserver le prétraitement dans les pipelines (anti-fuite CV).
- Relancer le kernel après modification des imports/env vars.

---

## 📌 Livrables

- `benchmark_table.md` : comparaison lisible métier
- `verdict.md` : recommandation finale (5 lignes max)
- `decision_card.md` : grille de décision C4 personnalisée
