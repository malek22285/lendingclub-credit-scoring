# Scoring de risque de défaut de crédit

Projet de machine learning sur le dataset public LendingClub (prêts accordés entre 2007 et 2018Q4). L'objectif : prédire si un emprunteur va faire défaut sur son crédit, dans une logique de recouvrement (identifier tôt les clients à risque pour prioriser les relances).

## Ce que fait le projet

Le pipeline part du CSV brut LendingClub, nettoie les données, construit une cible binaire (`default_risk`), teste plusieurs modèles de classification et retient celui qui offre le meilleur compromis recall/précision sur un jeu de test.

Le repo est organisé en trois notebooks qui suivent la progression réelle du travail :

- `notebooks/01_data_cleaning.ipynb` : nettoyage du dataset brut (suppression des colonnes hors-sujet comme les colonnes hardship/settlement, création de la cible à partir de `loan_status`, traitement des valeurs manquantes, encodage des variables catégorielles).
- `notebooks/02_exploration.ipynb` : phase d'itération. Feature engineering (ratios financiers, features temporelles, agrégations), essais de plusieurs modèles (Random Forest, XGBoost, voting classifier). Ce notebook documente les tâtonnements, y compris les résultats qui n'ont pas marché.
- `notebooks/03_modeling_final.ipynb` : version aboutie. Split train/validation/test (70/15/15), entraînement de trois modèles (Random Forest, Logistic Regression, CatBoost), optimisation du seuil de décision, sélection de features (importance + RFE), tuning d'hyperparamètres avec Optuna, calibration des probabilités, évaluation finale sur le jeu de test.

## Stack technique

Python, pandas, numpy, scikit-learn, CatBoost, Optuna, matplotlib, seaborn.

## Installation

```bash
pip install -r requirements.txt
```

Le dataset (`accepted_2007_to_2018Q4.csv`) n'est pas inclus dans le repo, voir `data/README.md` pour savoir où le récupérer.

## Résultats

Le modèle final retenu est un CatBoost avec un seuil de décision fixé à 0.20 (plutôt que le seuil par défaut de 0.5, pour privilégier le recall). Évalué sur le jeu de test (303 observations) :

| Métrique | Validation | Test |
|---|---|---|
| Recall | 63.5% | 47.1% |
| Precision | 45.8% | 35.3% |
| F1-score | 0.532 | 0.403 |
| Accuracy | 80.9% | 76.6% |
| ROC-AUC | 0.820 | 0.740 |

Concrètement, sur les 51 défauts réels du jeu de test, le modèle en détecte 24 (47%), au prix de 44 fausses alertes sur les 252 bons payeurs.

## Limites connues

Le jeu de test ne compte que 303 observations, ce qui est très petit face au dataset LendingClub complet (plusieurs millions de lignes). L'écart entre les métriques de validation et de test (F1 de 0.532 à 0.403, par exemple) vient probablement en bonne partie de cette petite taille d'échantillon plutôt que d'un vrai problème de généralisation, mais on ne peut pas trancher avec certitude sans un test set plus grand.

La calibration des probabilités a été testée mais dégrade légèrement le F1-score (0.403 à 0.377), donc le modèle final reste non calibré.

Ce projet est un exercice de modélisation, pas un système prêt pour la production : pas de pipeline de déploiement, pas de monitoring de drift, pas de validation sur des données plus récentes que 2018.
