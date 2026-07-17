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

---

## Semaine 1 (1er–7 mai 2026)

### Objectifs de la période

- Comprendre le fonctionnement de l’API REST ProjectWorks
- Identifier les données nécessaires au projet
- Mise en place de l'architecture 
- Mettre en place une première extraction vers Microsoft Fabric
- Créer la première version de la couche Bronze

### Travail réalisé

!!! abstract "Avancement"

    - [x] Analyse de la documentation de l’API ProjectWorks
    - [x] Test et validation des endpoints dans Postman
    - [x] Analyse des réponses JSON et de la pagination
    - [x] Prise en main du workspace, du Lakehouse et des notebooks Microsoft Fabric
    - [x] Création de la structure de stockage Bronze
    - [x] Développement d’un premier notebook d’extraction
    - [x] Première extraction complète des données vers des fichiers JSON
    - [x] Validation des fichiers et des métriques d’exécution

---

## Semaine 2 (8–14 mai 2026)

### Objectifs de la période

- Rendre le notebook Bronze réutilisable
- Mettre en place les chargements complets et incrémentaux
- Centraliser la configuration des objets ProjectWorks
- Préparer l’automatisation du traitement

### Travail réalisé

!!! abstract "Avancement"

    - [x] Transformation du notebook Bronze en notebook générique
    - [x] Ajout de paramètres dynamiques
    - [x] Analyse des chargements `Full` et `Incremental`
    - [x] Test du filtre `ModifiedSinceDate` dans Postman
    - [x] Création d'un premier Warehouse 
    - [x] Création d'une premiere table de controle que pour la couche bronze
    - [x] Configuration des différents objets ProjectWorks

### Décisions et ajustements

!!! info "Décisions"

    - Conserver `Resources` en extraction complète, même si l’endpoint supporte l’incrémental, car certaines valeurs de `ResourceID` sont nulles et pourraient créer des problèmes dans la couche Silver.

---

## Semaine 3 (15–21 mai 2026)

### Objectifs de la période

- Automatiser l’extraction des données ProjectWorks
- Piloter les traitements à partir de la table de contrôle
- Mettre à jour automatiquement les watermarks
- Tester le pipeline Source vers Bronze

### Travail réalisé

!!! abstract "Avancement"

    - [x] Création d'un premier pipeline pour la couche bronze
    - [x] Ajout d’une activité `Lookup` pour lire la table de contrôle
    - [x] Ajout d’une boucle `ForEach` pour traiter les objets actifs
    - [x] Transmission dynamique des paramètres au notebook Bronze
    - [x] Mise à jour du watermark après une extraction réussie
    - [x] Test du pipeline avec les différents objets ProjectWorks

### Difficultés rencontrées

!!! warning "Difficultés"

    - Conflit SQL lors de la mise à jour simultanée des watermarks
        - Résolu en exécutant les objets de manière séquentielle
    - Présence de doublons dans la table de contrôle
        - Certains objets étaient exécutés plusieurs fois
        - Résolu par le nettoyage de la table

---

## Semaine 4 (22–28 mai 2026)

### Objectifs de la période

- Ajouter le suivi des exécutions du pipeline
- Enregistrer les succès, les erreurs et les métriques
- Stabiliser l’écriture des fichiers dans OneLake
- Finaliser la couche Bronze

### Travail réalisé

!!! abstract "Avancement"

    - [x] Création d’une table de journalisation des activités
    - [x] Ajout du logging des exécutions réussies
    - [x] Ajout du logging des exécutions en erreur
    - [x] Enregistrement des lignes lues, des lignes écrites et des fichiers générés
    - [x] Enregistrement des dates d’exécution et des messages d’erreur
    - [x] Correction de l’écriture des fichiers JSON dans OneLake
    - [x] Validation du fonctionnement complet de la couche Bronze

### Difficultés rencontrées

!!! warning "Difficultés"

    - L’écriture avec `pandas.to_json()` ne fonctionnait pas correctement dans OneLake
        - Résolu en convertissant les données en DataFrame Spark
        - Utilisation de l’écriture Spark pour générer les fichiers JSON
    - La récupération des erreurs et des métriques du notebook demandait plusieurs ajustements dans le pipeline
---

## Semaine 5 (1er–7 juin 2026)

### Objectifs de la période

- Commencer le développement de la couche Silver
- Transformer les fichiers JSON Bronze en tables structurées
- Gérer les objets simples et les tableaux imbriqués
- Tester le traitement sur plusieurs objets

### Travail réalisé

!!! abstract "Avancement"

    - [x] Développement du notebook générique Silver
    - [x] Lecture des fichiers JSON enregistrés dans la couche Bronze
    - [x] Sélection, renommage et typage des colonnes
    - [x] Création des premières tables Delta dans le Lakehouse Silver
    - [x] Mise en place des differents cas de traitements des endpoints
    - [x] Tests sur les objets 

### Difficultés rencontrées

!!! warning "Difficultés"

    - Certains objets contenaient des listes imbriquées qui devaient être séparées de la table principale.
    - Plusieurs vérifications ont été nécessaires sur les chemins, les paramètres et l’actualisation du Lakehouse.

---

## Semaine 6 (8–14 juin 2026)

### Objectifs de la période

- Stabiliser le notebook Silver
- Gérer les différentes méthodes de chargement
- Améliorer le suivi des exécutions
- Centraliser les configurations ProjectWorks

### Travail réalisé

!!! abstract "Avancement"

    - [x] Ajout des modes de chargement `overwrite`, `append` et `merge`
    - [x] Mise en place du `merge` pour mettre à jour les lignes existantes et insérer les nouvelles
    - [x] Gestion des clés simples, composites et des clés provenant des objets parents
    - [x] Ajout du traitement des fichiers vides avec un statut `Warning`
    - [x] Ajout des métriques : fichiers lus, lignes lues, lignes transformées et erreurs
    - [x] Création du notebook utilitaire de gestion des tables de paramètres
    - [x] Début de la synchronisation des tables de contrôle dans le Warehouse

### Décisions et ajustements

!!! info "Décisions"

    - Utiliser `overwrite` lorsqu’aucune clé fiable n’est disponible.
    - Utiliser une clé composite pour certains objets enfants, notamment les jours de congé.                   
    - Considérer une réponse API vide comme un avertissement et non comme une erreur technique.


---

## Semaine 7 (15–21 juin 2026)

### Objectifs de la période

- Automatiser l’exécution complète des traitements ProjectWorks
- Centraliser les paramètres dans le Warehouse
- Mettre en place un pipeline master quotidien
- Ajouter un monitoring centralisé

### Travail réalisé

!!! abstract "Avancement"

    - [x] Finalisation des notebooks de configuration Bronze et Silver
    - [x] Création d’un notebook master pour synchroniser les configurations
    - [x] Création du pipeline de la couche silver
    - [x] Orchestration des flux Bronze et Silver
    - [x] Création du pipeline master quotidien ProjectWorks
    - [x] Transmission des paramètres et des informations d’exécution entre les pipelines
    - [x] Mise en place du monitoring dans un Eventhouse
    - [x] Création des logs de statut, des logs d’erreur et d’une règle d’alerte
    - [x] Changement de l'architecture actuel de l'entreprise, en ecrivant direct les configurations dans le Warehouse
      sans cree une copie dans le lakehouse 
    
    

### Décisions et ajustements

!!! info "Décisions"

    - Abandonner le passage par un Lakehouse de paramètres et centraliser directement
      les configurations dans le Warehouse.

    - Utiliser `synapsesql()` dans le notebook pour écrire le DataFrame Spark dans
      une table de staging du Warehouse. Cette table est reconstruite en mode
      `overwrite` à chaque exécution, puisqu’elle sert uniquement de zone temporaire.

    - Utiliser une connexion JDBC vers le Warehouse pour exécuter le `MERGE T-SQL`
      entre la table de staging et la table de contrôle cible. Cette étape était
      nécessaire, car `synapsesql()` permet de lire ou d’écrire des DataFrames,
      mais pas d’exécuter directement une commande SQL.

    - Authentifier la connexion JDBC avec un jeton Microsoft Entra récupéré pendant
      l’exécution, afin d’éviter de stocker un mot de passe dans le notebook.

    - Encapsuler le `MERGE` dans une transaction avec gestion des erreurs et
      `ROLLBACK`, afin d’éviter toute mise à jour partielle de la table cible
      en cas d’échec.

    - Utiliser un pipeline master pour lancer les traitements dans le bon ordre.
    
### Difficultés rencontrées

!!! warning "Difficultés"

    - Comprendre la circulation des paramètres entre les notebooks, les sous-pipelines et le pipeline master.
    - Adapter la solution aux limites d’écriture entre les notebooks Spark et le Warehouse.
    - Vérifier que les erreurs remontaient correctement jusqu’au monitoring central.

---


## Semaine 8 (22–30 juin 2026)

### Objectifs de la période

- Adapter la méthode de chargement Silver à l’architecture utilisée dans l’entreprise
- Remplacer la logique `merge` par une approche `delete + append`
- Gérer correctement les objets enfants et les tableaux imbriqués
- Adapter le notebook au traitement de `Task_Users`

### Travail réalisé

!!! abstract "Avancement"

    - [x] Abandon de la méthode de chargement `merge`
    - [x] Mise en place de la logique `delete + append`
    - [x] Suppression des anciennes lignes associées aux objets présents dans le nouveau fichier Bronze
    - [x] Ajout des nouvelles versions des lignes dans les tables Silver
    - [x] Adaptation du traitement des objets de type `array`
    - [x] Adaptation du notebook pour traiter le tableau imbriqué `Tasks.Users`
    - [x] Conservation du `TaskID` comme clé de rattachement au parent
    - [x] Création d’une ligne par association tâche-utilisateur dans `Task_Users`
    - [x] Tests et validation de la nouvelle logique de chargement

### Décisions et ajustements

!!! info "Décisions"

    - Remplacer le `merge` par la méthode `delete + append` afin d’aligner le notebook avec l’architecture utilisée dans l’entreprise.
    - Pour les tables enfants, supprimer les anciennes lignes liées aux identifiants parents présents dans le nouveau lot, puis ajouter les nouvelles lignes.
    - Traiter `Task_Users` comme un tableau enfant de `Tasks`. Chaque tâche retournée par l’API contient un tableau `Users` imbriqué qui doit être explosé afin         de produire une ligne par association tâche-utilisateur, plutôt que d’être conservé sous forme d’un objet JSON.
    - Conserver le `TaskID` dans chaque ligne créée afin de relier l’utilisateur à sa tâche d’origine.
    - Comme la relation entre les tâches et les utilisateurs est contenue dans la structure imbriquée de l’API, le notebook doit d’abord lire les tâches                parentes, puis dérouler le tableau `Users` de chaque tâche pour alimenter la table `PW_Task_Users`.

### Difficultés rencontrées

!!! warning "Difficultés"

    - Certaines tables enfants ne possédaient pas de clé unique suffisamment fiable pour utiliser le `merge`, ce qui a motivé le passage à la méthode `delete +     append`.
    - Le tableau `Tasks.Users` contenait des identifiants utilisateurs imbriqués qui devaient être transformés en plusieurs lignes distinctes tout en conservant        leur lien avec le `TaskID` parent.
    - Une simple lecture à plat du fichier JSON ne permettait pas de construire correctement la relation entre les tâches et les utilisateurs.
    - La suppression devait cibler uniquement les lignes associées aux `TaskID` présents dans le nouveau fichier Bronze avant l’ajout des nouvelles associations,       afin d’éviter les doublons ou la suppression de données non concernées.
