# Mesures et colonnes DAX

Table de faits : `diabetic_data`. Les mesures sont rangées dans la table `_Mesures`.

## Mesures de base

```dax
Hospitalisations = COUNTROWS(diabetic_data)
```
Nombre de séjours (une ligne = un séjour).

```dax
Patients uniques = DISTINCTCOUNT(diabetic_data[patient_nbr])
```
Nombre de patients distincts (un patient peut avoir plusieurs séjours).

```dax
Durée moyenne séjour = AVERAGE(diabetic_data[time_in_hospital])
```
Durée moyenne de séjour, en jours.

## Réadmissions

```dax
Réadmissions < 30 j =
CALCULATE([Hospitalisations], diabetic_data[readmitted] = "<30")
```
```dax
Réadmissions > 30 j =
CALCULATE([Hospitalisations], diabetic_data[readmitted] = ">30")
```
```dax
Sans réadmission =
CALCULATE([Hospitalisations], diabetic_data[readmitted] = "NO")
```

## Taux

```dax
Taux réadmission < 30 j =
DIVIDE([Réadmissions < 30 j], [Hospitalisations])
```
```dax
Taux réadmission > 30 j =
DIVIDE([Réadmissions > 30 j], [Hospitalisations])
```
```dax
Taux sans réadmission =
DIVIDE([Sans réadmission], [Hospitalisations])
```
`DIVIDE` évite l'erreur de division par zéro quand un filtre ne laisse aucune ligne.

## Comparaison à la moyenne

```dax
Taux global < 30 j =
CALCULATE([Taux réadmission < 30 j], REMOVEFILTERS(diabetic_data))
```
Taux de tous les patients, en ignorant les filtres.

```dax
Écart vs moyenne globale =
[Taux réadmission < 30 j] - [Taux global < 30 j]
```

## Top 10 des spécialités

```dax
Réadm. < 30 j (spécialité renseignée) =
IF(
    SELECTEDVALUE(diabetic_data[medical_specialty]) = "Non renseigné",
    BLANK(),
    [Réadmissions < 30 j]
)
```
Exclut « Non renseigné » du classement, car la spécialité est absente pour une grande partie des séjours.

## Colonnes calculées

```dax
Statut réadmission =
SWITCH(
    diabetic_data[readmitted],
    "NO", "Pas de réadmission",
    ">30", "Après 30 jours",
    "<30", "Avant 30 jours"
)
```
```dax
Tranche médicaments =
SWITCH(
    TRUE(),
    diabetic_data[num_medications] <= 10, "1-10",
    diabetic_data[num_medications] <= 20, "11-20",
    diabetic_data[num_medications] <= 30, "21-30",
    diabetic_data[num_medications] <= 40, "31-40",
    "41+"
)
```
```dax
Ordre médicaments =
SWITCH(
    TRUE(),
    diabetic_data[num_medications] <= 10, 1,
    diabetic_data[num_medications] <= 20, 2,
    diabetic_data[num_medications] <= 30, 3,
    diabetic_data[num_medications] <= 40, 4,
    5
)
```
`Tranche médicaments` est triée par `Ordre médicaments`.

```dax
Traitement insuline =
IF(diabetic_data[insulin] = "No", "Sans insuline", "Avec insuline")
```
```dax
Résultat A1C =
SWITCH(
    diabetic_data[A1Cresult],
    "None", "Non mesuré",
    "Norm", "Normal",
    ">7", "> 7",
    ">8", "> 8"
)
```
```dax
Ordre A1C =
SWITCH(
    diabetic_data[A1Cresult],
    "None", 1,
    "Norm", 2,
    ">7", 3,
    ">8", 4
)
```
`Résultat A1C` est triée par `Ordre A1C`.

```dax
Hospitalisations antérieures (groupe) =
IF(
    diabetic_data[number_inpatient] >= 5,
    "5+",
    FORMAT(diabetic_data[number_inpatient], "0")
)
```
Regroupe les hospitalisations de l'année précédente (0 à 4, puis 5 et plus).
