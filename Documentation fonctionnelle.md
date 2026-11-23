# Documentation fonctionnelle – DPE Lyon

## 1. Présentation

DPE Lyon est une application web développée avec Streamlit. Elle permet à un particulier d'estimer l'étiquette DPE et la consommation annuelle d'énergie finale d'un logement situé à Lyon ou dans une partie de la métropole.

Les données proviennent de l'ADEME. Après nettoyage, l'application s'appuie sur 202 989 diagnostics.

L'application comporte quatre pages, accessibles depuis le menu latéral : Contexte, Exploration, Prédiction et Administration.

## 2. Page Contexte

Cette page présente le projet, les données disponibles et les deux objectifs :

- estimer l'étiquette DPE d'un logement ;
- estimer sa consommation annuelle d'énergie finale, en kWh par an.

Elle affiche la répartition des logements par étiquette DPE. Les classes A et B sont regroupées, car les logements classés A sont trop rares dans les données.

Les résultats fournis par les modèles sont des estimations statistiques. Ils ne remplacent pas un diagnostic réglementaire.

## 3. Page Exploration

Cette page permet d'analyser les données existantes. Elle comporte trois parties : des filtres, des indicateurs clés et des visualisations.

**Filtres.** Ils portent sur le code postal, le type de logement et la surface habitable. Une case permet d'exclure 1 % des consommations les plus faibles et les plus fortes. Les filtres mettent à jour l'ensemble de la page.

**Indicateurs clés.** Ils donnent le nombre de diagnostics, la surface médiane, les consommations médianes (totale et par mètre carré) et la part des classes F et G.

**Visualisations.** La page comporte plusieurs onglets :

- Vue d'ensemble : répartition des étiquettes DPE ;
- Consommation : distribution des surfaces ;
- Facteurs : comparaison selon la période de construction ;
- Carte : échantillon de logements, colorés selon leur étiquette ;
- Données : aperçu du tableau filtré.

Les comparaisons décrivent des associations. Elles ne démontrent pas de relation de causalité.

**Export.** La sélection complète peut être téléchargée au format CSV.

## 4. Page Prédiction

Cette page permet d'estimer l'étiquette DPE et la consommation d'un logement décrit par l'utilisateur.

Seuls le code postal, le type de logement et la surface habitable sont obligatoires. Les autres caractéristiques peuvent rester inconnues. Le formulaire est organisé par thème : chauffage, eau chaude sanitaire, isolation et climatisation.

Après validation, deux modèles estiment l'étiquette DPE et la consommation annuelle. Les champs inconnus sont signalés, et un récapitulatif permet de vérifier la saisie.

**Export.** La saisie et l'estimation peuvent être exportées au format CSV ou JSON.

## 5. Page Administration

Cette page présente l'état des données et la date d'entraînement des modèles.

Les mises à jour et le réentraînement passent par GitHub Actions, si l'accès GitHub est configuré. La page donne aussi accès à la documentation de l'API, qui permet à un autre programme d'utiliser les modèles.
