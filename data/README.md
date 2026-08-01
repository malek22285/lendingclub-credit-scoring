# Données

Le dataset utilisé est "Lending Club Loan Data" (fichier `accepted_2007_to_2018Q4.csv`), disponible publiquement sur Kaggle : https://www.kaggle.com/datasets/wordsforthewise/lending-club

Il n'est pas inclus dans ce repo (plusieurs centaines de Mo). Pour reproduire le projet :

1. Télécharger `accepted_2007_to_2018Q4.csv.gz` depuis Kaggle
2. Le décompresser et le placer dans ce dossier `data/`
3. Lancer `notebooks/01_data_cleaning.ipynb`, qui génère `data_clean.csv` utilisé par les notebooks suivants
