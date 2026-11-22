# Documentation fonctionnelle - DPE Lyon

**Projet :** m2_enedis · **Équipe :** Xilian Jing, Quentin Bétemps, Soraya Saad

## 1. Finalité et accès

DPE Lyon aide un particulier à explorer les diagnostics énergétiques du territoire étudié et à estimer les caractéristiques énergétiques d’un logement. L’application est accessible à l’adresse [ml-dpe-ademe.onrender.com](https://ml-dpe-ademe.onrender.com). Aucune installation n’est nécessaire pour l’utilisation en ligne. Une première ouverture après une période d’inactivité peut demander un temps de démarrage.

Le périmètre couvre Lyon et une partie de sa métropole, soit 17 codes postaux. Les données sont des diagnostics ADEME de logements existants. Les estimations s’appliquent à ce contexte de données, elles ne remplacent pas un diagnostic réalisé par un professionnel.

## 2. Page Contexte

La page présente le projet, le périmètre des données et les deux objectifs de prédiction. Elle affiche les effectifs des diagnostics, un graphique de répartition des logements par étiquette et les performances des modèles. Une échelle DPE illustre les classes de A à G, dans le modèle de classification, A et B sont regroupées.

## 3. Page Exploration

Les **filtres** s’appliquent à toute la page : codes postaux (tous si aucun n’est choisi), type de logement, plage de surface habitable et exclusion des 1 % de consommations les plus faibles et les plus fortes, cochée par défaut. Si aucun diagnostic ne correspond, la page invite à élargir la sélection.

Cinq **indicateurs** résument la sélection : nombre de diagnostics, surface médiane, consommation médiane en kWh/an et en kWh/m²/an, part des classes F et G. Les graphiques sont répartis en onglets :

| Onglet | Contenu |
|---|---|
| Vue d’ensemble | Répartition des étiquettes DPE |
| Consommation | Surfaces / consommation par étiquette / nuage surface et consommation |
| Facteurs | Consommation médiane au m² et étiquettes selon une variable choisie (modalités de 30 logements ou plus) |
| Carte | Logements colorés par étiquette, du vert (A-B) au rouge (G) |
| Données | Aperçu des 100 premières lignes de la sélection |

Carte, nuage de points et boîtes à moustaches portent sur un échantillon aléatoire de 3 000 logements. Le bouton **Télécharger les données filtrées (CSV)** exporte toute la sélection. Chaque graphique s’enregistre en PNG par l’icône de téléchargement qui apparaît en haut à droite au survol.

## 4. Page Prédiction

L’utilisateur renseigne les caractéristiques du logement, puis valide le formulaire. Trois informations sont obligatoires : **code postal**, **type de logement** (appartement ou maison) et **surface habitable en m²**. Les autres champs peuvent être laissés inconnus : bâtiment (période de construction, nombre de niveaux), chauffage (énergie, générateur, installation), eau chaude sanitaire (énergie, installation), enveloppe et confort (toiture, menuiseries, logement traversant, climatisation). Pour une maison, les types d’installation du chauffage et de l’eau chaude sont sans objet dans le modèle et sont ignorés. Un encadré identifie les renseignements non fournis.

La page retourne une **classe estimée parmi A-B, C, D, E, F et G** et une **consommation annuelle d’énergie finale en kWh/an**. La classe est mise en évidence sur l’échelle DPE, une estimation A-B met en évidence les deux classes. La consommation est un total annuel, pas une valeur par m² ni une facture en euros. L’utilisateur peut exporter les informations et l’estimation en CSV ou JSON.

## 5. Page Administration

La page est ouverte à tous, les identifiants `admin` / `admin` sont préremplis. Elle affiche le résultat de la dernière mise à jour des données et un lien vers la documentation de l’API. Le lancement propose deux options : **reconstruction complète** (environ 12 minutes, seule à retirer les diagnostics supprimés par l’ADEME) au lieu des seuls diagnostics modifiés, et **réentraînement des modèles**. L’avancement est suivi automatiquement, puis l’application est redéployée si des fichiers ont changé.

## 6. Utilisation de l’API

L’API rend les estimations de la page Prédiction accessibles à un autre programme. La page [m2-enedis-api.onrender.com/docs](https://m2-enedis-api.onrender.com/docs) décrit chaque champ et permet d’essayer une requête depuis le navigateur. `POST /predict` reçoit en JSON les champs du formulaire, dont seuls `code_postal_ban`, `type_batiment` et `surface_habitable_logement` sont obligatoires, par exemple `{"code_postal_ban": "69003", "type_batiment": "appartement", "surface_habitable_logement": 65}`. Elle renvoie `etiquette_dpe`, `conso_5_usages_ef` (kWh/an) et la liste `champs_non_renseignes`. `GET /info` donne les valeurs acceptées pour chaque champ et les performances. Une saisie invalide reçoit le code 422.

## 7. Interprétation et situations particulières

Les modèles produisent une estimation statistique à partir des informations renseignées. Un renseignement inconnu est traité comme tel par le modèle, il n’est pas assimilé à une absence d’équipement. Deux logements ayant les mêmes réponses au formulaire peuvent avoir des diagnostics différents, car le formulaire ne décrit pas toutes les propriétés thermiques du bâtiment.

La distinction exacte entre classes reste limitée. Pour les modèles évalués le 13 novembre 2026, 45,0 % des classes sont prédites exactement et 78,8 % à un écart d’au plus une catégorie dans l’ordre A-B, C, D, E, F, G. L’erreur absolue moyenne sur la consommation est de 2 190 kWh/an. Cette moyenne ne garantit pas un niveau d’erreur pour un logement particulier.

| Situation | Comportement |
|---|---|
| Champ obligatoire absent, valeur non reconnue, surface nulle | Message sous le formulaire / aucune estimation n’est calculée. |
| Erreur lors de la prédiction | Un message neutre est affiché / le détail technique est conservé dans les journaux du serveur. |
| Service en veille après inactivité | Le premier chargement de l’application ou de l’API peut prendre environ une minute. |
