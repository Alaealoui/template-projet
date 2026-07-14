---
title: Suivi du projet
---

<style>
    @media screen and (min-width: 76em) {
        .md-sidebar--primary {
            display: none !important;
        }
    }
</style>

# Suivi de projet

> :bulb: Cette page documente l’évolution du projet dans le temps.
> Elle sert à rendre visibles les décisions, ajustements et apprentissages.
> Les entrées peuvent être hebdomadaires ou bi-hebdomadaires.  
> N'oubliez pas d’effacer ou de mettre en commentaires les notes (`>`) avant la remise finale.

---

## Semaine 1 (1er–7 mai 2026)

### Objectifs de la période

- Comprendre le fonctionnement général de l’API REST ProjectWorks
- Identifier les premières données nécessaires au projet
- Vérifier que les données ProjectWorks étaient accessibles
- Comprendre la structure des réponses retournées par l’API
- Mettre en place une première zone de stockage dans Microsoft Fabric
- Développer une première extraction automatisée des données
- Conserver les données brutes dans une couche Bronze

### Travail réalisé
!!! abstract "Avancement"
    - [x] Compréhension de l’architecture générale du projet

      Le projet consiste à transférer les données de ProjectWorks vers Microsoft Fabric
      afin qu’elles puissent ensuite être nettoyées, structurées et utilisées pour
      produire des analyses et des rapports.

      Le flux initial mis en place pour la couche Bronze, qui consiste à l’extraction
      des données brutes, est le suivant :

      API REST ProjectWorks -> Notebook Python dans Microsoft Fabric -> Lakehouse Microsoft Fabric -> Couche Bronze contenant les fichiers JSON bruts

    - [x] Prise en main de Microsoft Fabric

      Microsoft Fabric est la plateforme utilisée pour centraliser les données,
      exécuter les traitements et préparer les futures analyses.

      Le développement a d’abord été effectué dans mon propre workspace, avant le
      déploiement dans l’environnement de développement.

    Un workspace est un espace de travail qui regroupe les différents éléments
    du projet, par exemple :

    - les notebooks 
    - les pipelines 
    - les Lakehouses 
    - les Warehouses 
    - les rapports Power BI

    - [x] Compréhension du rôle du Lakehouse

    Le Lakehouse est l’espace dans lequel les données extraites de ProjectWorks
    sont enregistrées.

    Il combine deux façons de stocker les données :

    - une zone de fichiers, semblable à un espace de stockage de fichiers ;
    - une zone de tables structurées, qui pourra être utilisée dans les étapes suivantes.

    Pour la couche Bronze, les données ont été enregistrées dans la zone Files
    du Lakehouse.

    La zone Files permet de conserver les données sous leur format d’origine,
    sans les transformer immédiatement en tables.

    À cette étape, les données étaient donc conservées sous forme de fichiers JSON.

    - [x] Définition du rôle de la couche Bronze

    La couche Bronze correspond à la première couche de l’architecture de données.

    Son rôle est de conserver une copie des données reçues depuis la source,
    dans le format exact de la réponse originale de l’API.

    Dans cette couche :

    - les colonnes ne sont pas encore renommées 
    - les types de données ne sont pas encore corrigés 
    - les valeurs nulles ne sont pas nettoyées 
    - les objets imbriqués ne sont pas encore séparés 
    - aucune règle métier n’est appliquée

    Cette approche permet de conserver une copie fidèle des données ProjectWorks
    avant de commencer les transformations.

    En cas d’erreur dans les étapes suivantes, les fichiers Bronze peuvent être
    relus sans devoir rappeler immédiatement l’API.

    - [x] Analyse de l’API REST ProjectWorks

    Une analyse des endpoints a été réalisée dans Postman, en s’appuyant sur la
    documentation officielle de ProjectWorks, afin de déterminer de quels endpoints
    nous aurions besoin pour l’extraction.

    Il fallait donc lire attentivement la documentation, bien la comprendre, savoir
    ce que chaque endpoint retourne, et comprendre la signification de chaque attribut
    présent dans les réponses.

    - [x] Compréhension de l’authentification

    L’API ProjectWorks utilise une authentification de type Basic Auth.

    Deux informations sont nécessaires pour accéder aux données :

    - le consumer key 
    - le consumer secret

    - [x] Test manuel de l’API avec Postman

    Avant de développer le notebook dans Microsoft Fabric, les appels API ont été
    testés manuellement avec Postman.

    Postman est un outil qui permet d’envoyer des requêtes à une API et de visualiser
    directement les réponses retournées.

    Ces tests ont permis de :

    - confirmer que les informations d’authentification étaient valides 
    - vérifier les URLs exactes des endpoints 
    - confirmer que l’API était accessible 
    - observer la structure des données retournées 
    - identifier le format JSON des réponses 
    - vérifier la présence de plusieurs pages de données 
    - comprendre le fonctionnement de la pagination.

    - [x] Validation des endpoints ProjectWorks

    Après l’analyse de la documentation, chaque endpoint retenu a été validé
    individuellement dans Postman avant d’être intégré au notebook.

    Pour chaque endpoint, les vérifications suivantes ont été effectuées :

    - l’endpoint répond avec un statut HTTP `200` 
    - la réponse est bien au format JSON 
    - les champs attendus sont présents dans la réponse 
    - les données retournées correspondent à l’objet ciblé 
    - la pagination se comporte comme prévu lorsqu’il y a beaucoup d’enregistrements

    Cette validation a permis de figer la liste des endpoints à extraire et de
    s’assurer qu’aucun d’entre eux ne renvoyait d’erreur avant l’automatisation.

    - [x] Analyse des réponses JSON

    Les réponses retournées par l’API ont été analysées afin de comprendre leur
    structure avant de développer le notebook.

    Cette analyse a permis d’identifier :

    - les champs principaux de chaque objet 
    - le type de chaque champ (texte, nombre, date, booléen, etc) 
    - la présence d’objets imbriqués et de listes 
    - les champs pouvant contenir des valeurs nulles
    - le champ utilisé pour identifier chaque enregistrement de manière unique

    Cette analyse était nécessaire pour comprendre comment le notebook devait
    lire et enregistrer les données.

    - [x] Analyse du mécanisme de pagination

    L’API ne retourne pas nécessairement tous les enregistrements dans une seule réponse.

    Les données peuvent être divisées en plusieurs pages.

    Le notebook devait donc :

    1. appeler la première page 
    2. récupérer les premiers enregistrements 
    3. vérifier si une page suivante existait 
    4. appeler les pages suivantes 
    5. regrouper l’ensemble des résultats

    Une limite de 100 enregistrements par page a été utilisée.

    Cette gestion était particulièrement importante pour les endpoints contenant
    beaucoup de données.

    - [x] Création de la structure de stockage Bronze

    Un dossier distinct a été créé pour chaque objet ProjectWorks.

    Les fichiers ont également été organisés par année et par mois.

    Cette structure permet de :

    - retrouver facilement les données d’un objet 
    - identifier la période d’extraction 
    - conserver un historique des fichiers 
    - faciliter les futurs traitements 
    - éviter de mélanger les données de plusieurs endpoints

    - [x] Développement du premier notebook Bronze

    Un premier notebook dédié uniquement à l’extraction des données de l’API
    a été développé dans Microsoft Fabric.

    Un notebook est un programme composé de plusieurs cellules de code.

    Il permet d’exécuter du code Python ou Spark directement dans Microsoft Fabric.

    Le notebook développé était composé de quatre parties principales.

    - [x] Création de la cellule de configuration

    La première cellule permettait de :

    - importer les librairies Python nécessaires 
    - définir l’URL de l’API 
    - configurer l’authentification 
    - définir le Lakehouse Bronze cible 
    - définir le chemin de destination des fichiers 
    - préparer la liste des endpoints à appeler

    - [x] Création de la fonction d’extraction

    Une fonction générique a été développée pour appeler les endpoints.

    Cette fonction devait :

    - construire l’URL de l’endpoint 
    - envoyer la requête HTTP 
    - transmettre les informations d’authentification 
    - vérifier le statut de la réponse 
    - lire le contenu JSON 
    - gérer les différentes pages 
    - regrouper les enregistrements

    La même fonction pouvait être utilisée pour plusieurs endpoints.

    - [x] Création de la fonction de sauvegarde

    Une deuxième fonction a été développée pour enregistrer les résultats
    dans le Lakehouse.

    Cette fonction devait :

    - recevoir les données extraites 
    - identifier l’objet en cours 
    - construire le chemin Bronze 
    - ajouter l’année et le mois 
    - générer le nom du fichier 
    - ajouter la date d’exécution 
    - sauvegarder les données au format JSON

    - [x] Création de la boucle principale

    Une boucle principale parcourait la liste de tous les endpoints.

    - Pour chaque endpoint, le notebook commence par sélectionner l’endpoint à traiter, puis appelle l’API ProjectWorks.
     Il gère ensuite la pagination afin de récupérer l’ensemble des enregistrements, avant de récupérer les données au format JSON.
     Une fois les données obtenues, il construit le chemin de destination dans la couche Bronze, écrit le fichier dans le Lakehouse,
     et termine en calculant les métriques d’exécution.

    - [x] Exécution de la première extraction complète

    Une première extraction complète a été lancée pour l’ensemble des endpoints validés.

    Lors de cette exécution :

    - chaque endpoint a été appelé automatiquement par la boucle principale 
    - la pagination a été gérée jusqu’à la récupération de tous les enregistrements 
    - les données ont été écrites dans la couche Bronze au format JSON 
    - les fichiers ont été rangés dans les dossiers par objet, année et mois 
    - les métriques d’exécution qui sont les nombre de lignes lues et écrites ont été calculées

    - [x] Validation des résultats

    Après l’exécution, les résultats ont été vérifiés afin de confirmer que :

    - les enregistrements ont bien été lus depuis l’API 
    - les enregistrements ont bien été écrits dans le Lakehouse 
    - les fichiers JSON ont été générés 
    - le nombre de lignes écrites correspondait au nombre de lignes lues 
    - aucune erreur n’a été détectée pendant l’exécution




