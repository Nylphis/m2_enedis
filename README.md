# DPE Lyon

Application Streamlit qui estime l'**étiquette DPE** (A-B à G) et la **consommation annuelle d'énergie finale** (kWh/an) d'un logement de Lyon et d'une partie de la métropole. Les modèles sont entraînés sur les DPE de l'ADEME (logements existants, DPE version 2.1 et suivantes).

En ligne : https://ml-dpe-ademe.onrender.com (appli) et https://m2-enedis-api.onrender.com/docs (API)

## Documentation

Dans le dossier `rapports/` :

| Document | Contenu |
|---|---|
| [Documentation fonctionnelle](rapports/documentation_fonctionnelle.md) | Utilisation de l'appli, page par page, et de l'API |
| [Documentation technique](rapports/documentation_technique.md) | Architecture dont schéma .drawio, installation avec ou sans Docker, mise à jour des données, API |
| [Rapport de machine learning](rapports/rapport.md) | Données, prétraitement, comparaison des modèles, résultats et limites |

## Pages

| Page | Contenu |
|---|---|
| Contexte | Le projet, les données utilisées, les deux objectifs (classification, régression), les performances des modèles, l'échelle DPE |
| Exploration | Filtres, indicateurs, graphiques, carte et export des données filtrées |
| Prédiction | Formulaire du logement (3 champs obligatoires), étiquette et consommation estimées, export CSV et JSON |
| Administration | Mise à jour des données depuis l'API ADEME et réentraînement des modèles (via GitHub Actions) |

## API des modèles

Les mêmes modèles, sans interface : `POST /predict` (caractéristiques du logement en JSON, mêmes champs obligatoires que la page Prédiction) renvoie l'étiquette et la consommation estimées ; `GET /info` donne les valeurs acceptées et les performances. Documentation interactive sur `/docs`.

```bash
python -m uvicorn api.main:app --port 8000      # puis http://localhost:8000/docs
```

## Structure

```
app.py                      # configuration et menu de l'appli
vues/                       # une page par fichier, éléments communs dans commun.py
api/                        # API des modèles (FastAPI)
dpe/                        # pipeline : config, fetch (API ADEME), clean, train, features
run.py                      # lance le pipeline : python run.py [fetch] [clean] [train]
compare_models.py           # comparaison des modèles candidats -> rapports/
interpret_models.py         # matrice de confusion et importance des variables -> rapports/
data/processed/             # données nettoyées et statistiques
models/                     # modèles entraînés (.joblib) et metadata.json
rapports/                   # rapport ML, documentations, résultats et figures
.github/workflows/          # mise à jour mensuelle des données (GitHub Actions)
Dockerfile                  # image de l'appli
requirements.txt            # dépendances (Python 3.12)
```
