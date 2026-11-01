# Diabetes Hospital Readmission Dashboard (Power BI)

Dashboard Power BI interactif sur plus de 100 000 hospitalisations  de patients diabétiques, pour identifier qui est le plus à risque d'être réadmis dans les 30 jours après sa sortie.

## Aperçu

| Vue d'ensemble | Profil à risque | Détail |


## Question métier

> Quels patients diabétiques sont les plus à risque d'être réadmis dans les 30 jours, et pourquoi ?

Une réadmission précoce peut signaler une prise en charge insuffisante et représente un coût important pour le système de santé.

## Données

- **Jeu de données** : *Diabetes 130-US Hospitals for Years 1999-2008*, disponible sur Kaggle et sur l'UCI Machine Learning Repository .
- **Contenu** : dix ans de soins cliniques (1999-2008) dans 130 hôpitaux américains, environ 101 766 séjours de 1 à 14 jours avec examens de laboratoire.
- **Fichiers** (dossier `data/brut/`) :
  - `diabetic_data.csv` : les séjours.
  - `IDS_mapping.csv` : les libellés des types d'admission, des destinations de sortie et des sources d'admission.
- **Colonne cible** : `readmitted` (`NO`, `>30`, `<30`).

Source : https://archive.ics.uci.edu/dataset/296/diabetes-130-us-hospitals-for-years-1999-2008

## Préparation des données (Power Query)

- Remplacement des valeurs manquantes `?` par des valeurs vides.
- Suppression de la colonne `weight` (presque entièrement vide).
- Libellé « Non renseigné » pour les valeurs manquantes de `race` et `medical_specialty`.
- Libellés de tranches d'âge simplifiés (`[70-80)` devient `70-80`).
- Séparation de `IDS_mapping.csv` (trois tables collées) en trois tables de description : `Dim_Admission`, `Dim_Sortie`, `Dim_Source`.

## Modèle de données

Modèle en étoile : la table de faits `diabetic_data` au centre, reliée par des relations plusieurs-à-un aux trois tables de description (`Dim_Admission`, `Dim_Sortie`, `Dim_Source`).

## Pages du dashboard

1. **Vue d'ensemble** : nombre d'hospitalisations, patients uniques, durée moyenne de séjour, taux de réadmission < 30 jours, répartition des réadmissions, hospitalisations par âge, par durée de séjour et par type d'admission.
2. **Profil à risque** : taux de réadmission par tranche d'âge et par durée de séjour, matrice âge × nombre de médicaments avec mise en forme conditionnelle, taux selon le résultat A1C et le traitement à l'insuline.
3. **Détail** : top 10 des spécialités médicales, effet des hospitalisations antérieures, tableau détaillé des séjours accessible par drill-through.

Fonctionnalités Power BI utilisées : segments synchronisés entre les pages, drill-through, mise en forme conditionnelle, info-bulles, navigation par boutons, thème de couleurs personnalisé.

## Mesures DAX principales

Le détail se trouve dans [`docs/dax-measures.md`](docs/dax-measures.md).

```dax
Taux réadmission < 30 j =
DIVIDE([Réadmissions < 30 j], [Hospitalisations])
```

## Principaux enseignements

> À compléter après lecture du dashboard : 3 à 5 constats chiffrés, par exemple quelles tranches d'âge ou quelles durées de séjour ressortent, et ce que montre la matrice âge × médicaments.

## Limites

- Les données couvrent 1999-2008 et des hôpitaux américains : elles ne reflètent pas forcément les pratiques actuelles ni d'autres pays.
- Un même patient peut apparaître dans plusieurs séjours : le nombre de séjours est supérieur au nombre de patients uniques.
- Un taux calculé sur un petit effectif (par exemple les tranches d'âge extrêmes) est peu fiable : les info-bulles affichent le nombre de séjours.
- L'analyse est descriptive : elle montre des associations, pas des causes.

## Structure du dépôt

```
├── data/
│   └── brut/          données originales, non modifiées
├── powerbi/
│   └── diabetes_readmission.pbix
├── docs/
│   ├── dax-measures.md
│   └── screenshots/
└── README.md
```

## Ouvrir le projet

Le fichier `.pbix` s'ouvre avec **Power BI Desktop** (gratuit, Windows). Si Power BI demande l'emplacement des CSV, pointe vers le dossier `data/brut/`.

## Citation

Strack, B., DeShazo, J. P., Gennings, C., Olmo, J. L., Ventura, S., Cios, K. J., & Clore, J. N. (2014). *Impact of HbA1c Measurement on Hospital Readmission Rates: Analysis of 70,000 Clinical Database Patient Records.* BioMed Research International, 2014, Article ID 781670.



## Auteur

PAPA FODE SOUMARE 
Linkedin : linkedin.com/in/papa-fode-soumare-18619531
email : PAPEFODESOUMARE@gmail.com 