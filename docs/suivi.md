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


---

## Semaine 9 (1er–7 juillet 2026)

### Objectifs de la période

- Fiabiliser le traitement Bronze vers Silver pour les tableaux imbriqués vides
- Corriger les métriques enregistrées dans les journaux d’exécution
- Valider une exécution complète du pipeline sur l’ensemble des objets ProjectWorks

### Travail réalisé

!!! abstract "Avancement"

    - [x] Correction de l’erreur rencontrée lors du traitement de `PW_Invoice_Payments`
    - [x] Sécurisation du traitement des tableaux imbriqués afin de gérer correctement les structures vides ou atypiques retournées par l’API
    - [x] Extension de la gestion des fichiers vides pour couvrir le cas où le fichier Bronze contient des données, mais où le tableau imbriqué est vide pour l’ensemble du lot
    - [x] Ajout du contrôle `has_any_row` afin d’éviter toute reconstruction incorrecte d’une table Silver lorsqu’aucune ligne n’est produite
    - [x] Correction de la métrique du nombre de lignes écrites dans les logs d’exécution
    - [x] Correction de la transmission de la métrique `activity_rowsWritten` vers les activités de journalisation `Success` et `Failure`
    - [x] Vérification en SQL du nombre réel de lignes écrites
    - [x] Validation de l’exécution complète des 13 objets configurés, tous terminés avec le statut `Succeeded`

### Décisions et ajustements

!!! info "Décisions"

    - Considérer un tableau imbriqué vide comme un cas normal de traitement et non comme une erreur bloquante.
    - Conserver le mode `append` lorsqu’un lot ne produit aucune ligne afin d’éviter qu’un chargement incrémental vide n’écrase une table existante.

### Difficultés rencontrées

!!! warning "Difficultés"

    - Lorsque le tableau `Payments` était vide pour toutes les factures d’un lot, Spark pouvait inférer un type `ARRAY<STRING>` plutôt qu’un `ARRAY<STRUCT>`, provoquant l’échec de l’extraction des sous-champs.
    - La gestion initiale d’un DataFrame Silver vide pouvait faire basculer le traitement vers un mode `overwrite`, avec un risque d’écrasement complet de la table.
    - La métrique des lignes écrites retournait le nombre de fichiers générés au lieu du nombre réel de lignes.

---

## Semaine 10 (8–14 juillet 2026)

### Objectifs de la période

- Fiabiliser la configuration centralisée des traitements ProjectWorks
- Rétablir l’écriture des données Silver vers la zone inbound du Warehouse
- Vérifier la cohérence de la configuration sur l’ensemble des objets

### Travail réalisé

!!! abstract "Avancement"

    - [x] Génération des scripts SQL permettant de reconstruire les tables de contrôle à partir des fichiers de configuration corrigés
    - [x] Rechargement des tables de contrôle Bronze vers Silver et Source vers Bronze
    - [x] Diagnostic des échecs rencontrés sur les traitements `Timesheets` et `Resourcing`
    - [x] Correction des paramètres `wh_inbound_schema_name` et `wh_inbound_table_name` pour les 13 objets configurés
    - [x] Vérification de l’existence des 13 tables cibles dans la zone inbound du Warehouse
    - [x] Analyse et correction du mécanisme de récupération du schéma cible avant l’écriture des données
    - [x] Mise à jour du suivi de projet couvrant les travaux réalisés depuis le début du stage

### Décisions et ajustements

!!! info "Décisions"

    - Générer automatiquement le nom de la table cible à partir du nom de la table Silver plutôt que de configurer chaque objet manuellement.
    - Conserver une règle de nommage générique afin de faciliter l’ajout futur de nouveaux objets ProjectWorks.

### Difficultés rencontrées

!!! warning "Difficultés"

    - Le rechargement complet des tables de contrôle a remplacé par `NULL` certaines colonnes présentes dans la base mais absentes des fichiers de configuration.
    - Le message d’erreur retourné suggérait initialement un problème d’accès à la source alors que l’origine réelle était un paramètre de configuration non renseigné.
    - Plusieurs notebooks semblaient fonctionner correctement uniquement parce que leurs fichiers Bronze du jour étaient vides. Les traitements `Timesheets` et `Resourcing`, qui contenaient des données, ont permis de mettre en évidence le problème de configuration global.

---

## Semaine 11 (15–21 juillet 2026)

### Objectifs de la période

- Finaliser la règle d’alerte préparée lors de la mise en place du monitoring
- Vérifier que les échecs de pipeline sont correctement enregistrés et détectés
- Tester la chaîne de supervision de bout en bout

### Travail réalisé

!!! abstract "Avancement"

    - [x] Vérification du fonctionnement de l’Eventhouse de supervision et de la table de journalisation des échecs
    - [x] Construction de la règle d’alerte `ALERT_Pipeline_Failure` à partir des requêtes KQL existantes
    - [x] Configuration d’une surveillance périodique de la table de journalisation toutes les 15 minutes
    - [x] Correction de la requête KQL, notamment pour la gestion de `ingestion_time()` et la conversion des valeurs de date
    - [x] Déclenchement volontaire d’un échec de pipeline afin de tester le mécanisme
    - [x] Validation de l’enregistrement de l’erreur dans l’Eventhouse et de sa détection par la règle d’alerte
    - [ ] Validation de la réception de la notification

### Décisions et ajustements

!!! info "Décisions"

    - Construire la règle d’alerte directement à partir du jeu de requêtes KQL déjà utilisé pour le monitoring.
    - Simuler un échec à l’aide d’un objet inexistant dans la table de contrôle afin de tester la supervision sans modifier les données existantes.

### Difficultés rencontrées

!!! warning "Difficultés"

    - L’échec du pipeline était correctement enregistré et détecté par la règle, mais aucune notification n’était reçue.
    - Les tests ont permis d’isoler le problème au niveau de l’action de notification plutôt qu’au niveau de la logique de détection.

---

## Semaine 12 (22–31 juillet 2026)

### Objectifs de la période

- Finaliser le dispositif de supervision et d’alerte
- Préparer l’exécution du pipeline ProjectWorks dans l’environnement de production
- Ajuster les méthodes de chargement des différents objets
- Préparer la construction de la zone analytique

### Travail réalisé

!!! abstract "Avancement"

    - [x] Correction de l’action de notification et validation complète de la chaîne d’alerte
    - [x] Réception des alertes contenant le nom du pipeline, l’identifiant d’exécution et l’horodatage
    - [x] Personnalisation du contenu des notifications
    - [x] Création des valeurs de production dans la bibliothèque de variables
    - [x] Configuration du nom du workspace, de la chaîne de connexion aux métadonnées et des identifiants des Lakehouses
    - [x] Construction des chemins de stockage Bronze et Silver pour l’environnement de production
    - [x] Passage des objets ProjectWorks en chargement incrémental, à l’exception de `Resourcing`
    - [x] Début de la conception du modèle dimensionnel avec les premières dimensions et tables de faits
    - [ ] Compléter les variables restantes nécessaires au déploiement
    - [ ] Effectuer les chargements initiaux manquants

### Décisions et ajustements

!!! info "Décisions"

    - Conserver `Resourcing` en chargement complet conformément à la décision prise précédemment.
    - Regrouper les chargements initiaux manquants avec les prochaines corrections de configuration afin d’éviter plusieurs rechargements complets successifs.

### Difficultés rencontrées

!!! warning "Difficultés"

    - Plusieurs objets avaient uniquement été alimentés par des chargements incrémentaux sans avoir bénéficié d’un chargement initial complet.
    - Cette situation entraînait des données incomplètes dans la zone inbound et empêchait une validation fiable des futurs traitements analytiques.

---

## Semaine 13 (1er–7 août 2026)

### Objectifs de la période

- Construire la zone analytique à partir des données disponibles dans la zone inbound
- Définir des modèles de chargement réutilisables pour les dimensions et les faits
- Corriger l’incomplétude des données identifiée lors des semaines précédentes

### Travail réalisé

!!! abstract "Avancement"

    - [x] Création de `USER_DIM` et `PROJECT_DIM` ainsi que de leurs procédures de chargement
    - [x] Définition d’un modèle standard pour les dimensions utilisant une table temporaire, une empreinte `SHA2_256` pour détecter les changements, une mise à jour de type 1 et des clés de substitution
    - [x] Ajout d’une ligne Inconnu dans les dimensions afin de gérer les références absentes
    - [x] Mise en place des contrôles de doublons et de la gestion transactionnelle des chargements
    - [x] Génération de `DATE_DIM` pour la période 2015 à 2055 selon le calendrier fiscal du 1er avril au 31 mars
    - [x] Création de `RESOURCING_FACT` avec un grain projet, date et utilisateur et agrégation des heures
    - [x] Définition d’un modèle standard de chargement des faits avec résolution des clés de substitution
    - [x] Réalisation de tests d’idempotence, de doublons et de réconciliation sur les premières tables
    - [x] Retrait du suffixe `_SRC` des tables inbound à la demande du superviseur
    - [x] Mise à jour des tables de contrôle et renommage des 13 tables physiques afin d’aligner leur nomenclature avec celle de la couche Silver
    - [x] Passage temporaire des extractions en chargement complet afin de reconstruire les jeux de données incomplets

### Décisions et ajustements

!!! info "Décisions"

    - Utiliser par défaut une historisation de type 1 pour les dimensions, sauf lorsqu’un besoin métier justifie la conservation de l’historique.
    - Ajouter une ligne Inconnu identifiée par la clé de substitution `-1` dans chaque dimension afin d’éviter le rejet des faits dont la référence n’est pas disponible.
    - Ne pas ajouter de ligne Inconnu dans `DATE_DIM`, celle-ci étant générée de manière exhaustive.
    - Nommer l’année fiscale à partir de son année de fin, sous réserve de validation avec le superviseur.

### Difficultés rencontrées

!!! warning "Difficultés"

    - Plusieurs tables contenaient uniquement une fraction des données attendues en raison de l’absence de chargement initial complet.
    - Cette situation rendait les validations de la zone analytique non représentatives.
    - La conversion et l’arrondi de certaines mesures d’heures ont nécessité des contrôles supplémentaires afin de garantir la réconciliation des totaux entre les différentes couches.

---

## Semaine 14 (8–14 août 2026)

### Objectifs de la période

- Compléter le modèle en étoile avec les dimensions et les faits demandés
- Valider la qualité et la cohérence de l’ensemble de la zone analytique
- Vérifier la réconciliation des données entre les différentes couches du projet

### Travail réalisé

!!! abstract "Avancement"

    - [x] Création de `CLIENT_DIM` et `ASSIGNMENT_FACT`
    - [x] Création de `TASK_DIM` et `MODULE_DIM`
    - [x] Création de `TIMESHEET_FACT` et `INVOICE_FACT`
    - [x] Passage de `MODULE_DIM` vers une historisation de type 2 sans recréation de la table
    - [x] Validation de l’historisation : une modification du budget génère désormais une nouvelle version au lieu d’écraser la précédente
    - [x] Ajout de la ligne Inconnu manquante dans les procédures de chargement de `USER_DIM` et `PROJECT_DIM`
    - [x] Adaptation de la grille de tests aux noms définitifs des tables
    - [x] Validation des six dimensions et quatre tables de faits : absence de doublons, absence de lignes orphelines et conformité de la réconciliation des mesures
    - [x] Développement d’un notebook permettant de comparer les données entre la couche Silver et la zone inbound
    - [x] Identification et correction des écarts de données par reconstruction complète des tables concernées
    - [x] Validation du fonctionnement de bout en bout dans l’environnement de développement

### Décisions et ajustements

!!! info "Décisions"

    - Ajouter l’identifiant de ligne à la clé de `TIMESHEET_FACT` et `INVOICE_FACT`, le grain initialement défini ne garantissant pas l’unicité des enregistrements.
    - Utiliser une historisation de type 2 pour `MODULE_DIM` afin de conserver l’évolution du budget dans le temps.
    - Utiliser temporairement uniquement la version courante de `MODULE_DIM` lors de la résolution des clés de substitution, en attendant de confirmer si les faits doivent être rattachés à la version historiquement valide à leur date.
    - Documenter et communiquer au superviseur les écarts identifiés dans le document de correspondance avant toute modification du modèle prévu.

### Difficultés rencontrées

!!! warning "Difficultés"

    - Le grain initialement prévu pour `TIMESHEET_FACT` n’était pas unique : plusieurs cas existaient où une même personne saisissait plusieurs entrées pour une même tâche à la même date.
    - Le même problème a été identifié pour `INVOICE_FACT`, certaines factures contenant plusieurs lignes associées au même module.
    - Plusieurs incohérences supplémentaires ont été relevées dans le document de correspondance, notamment une jointure utilisant une colonne inexistante, une jointure vers une dimension incorrecte et certaines règles de transformation non applicables.
    - Les tests de réconciliation ont également mis en évidence des écarts importants entre la couche Silver et la zone inbound, liés principalement à des chargements incrémentaux effectués sans chargement initial complet.

### Validation

!!! success "Démonstration et validation des écarts de grain"

    Pour `TIMESHEET_FACT` et `INVOICE_FACT`, les procédures ont d’abord été développées selon le grain défini dans le document de correspondance.

    Les contrôles d’unicité ont permis de démontrer que ce grain n’était pas suffisant pour garantir l’unicité des lignes. Les transactions ont volontairement été laissées en échec afin de conserver une preuve technique du problème.

    Après présentation des résultats au superviseur, le grain des deux tables a été ajusté en ajoutant l’identifiant de ligne à la clé. Les procédures ont ensuite été exécutées et validées avec succès.


