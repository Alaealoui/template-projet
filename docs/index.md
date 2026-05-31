---
title: Vue d'ensemble du projet
---

<style>
    @media screen and (min-width: 76em) {
        .md-sidebar--primary {
            display: none !important;
        }
    }
</style>

# Vue d'ensemble du projet

!!! info "Informations générales"
    **Session**: Été 2026  
    **Auteur(s)**: Alaealoui 20253423 
    **Thème(s)**: Entrepôt de données, Microsoft Fabric, Intégration API 
    **Superviseur(s)**: Louis-Edouard Lafontant / Ali Kettani
    **Collaborateur(s):**   

## Description du projet

> :bulb: N'oubliez pas d'effacer ou mettre en commentaires les notes (`>`) en début de section

### Contexte

Groupe Neos est une firme de services-conseils spécialisée dans l'analytique et l'intelligence d'affaires. L'équipe accompagne ses clients dans la valorisation de leurs données, la conception de solutions BI, et la mise en œuvre d'architectures de données modernes.

Neos a récemment migré vers Projectworks, un logiciel SaaS dédié à la gestion de projets de consultation. Cette plateforme centralise les informations stratégiques utilisées au quotidien :

- Suivi des budgets clients et de leur consommation dans le temps
- Facturation des honoraires selon les feuilles de temps des conseillers
- Suivi de l'avancement des projets et gestion des dépassements budgétaires
- Mesure du taux d'utilisation des conseillers selon les mandats assignés

L'environnement technologique comprend : Microsoft Fabric, API REST Projectworks, architecture Médaillon (Bronze / Argent / Or), Power BI, SQL et Python.

### Problématique

Bien que Projectworks offre des rapports prédéfinis, ceux-ci ne peuvent pas être personnalisés et ne répondent pas aux besoins analytiques spécifiques de Groupe Neos.

Il n'existe présentement aucune infrastructure permettant d'exploiter de façon flexible les données contenues dans Projectworks. Cette situation limite la capacité de l'organisation à prendre des décisions éclairées basées sur ses propres données opérationnelles :

- Absence d'un entrepôt de données centralisé pour les données Projectworks
- Impossibilité de croiser les données opérationnelles librement
- Dépendance totale aux rapports figés fournis par la plateforme SaaS

### Proposition et objectifs

L'objectif principal est de construire un entrepôt de données alimenté par Projectworks, hébergé dans Microsoft Fabric, servant de fondation à des rapports Power BI entièrement personnalisés.

- Extraire les données des endpoints de l'API REST Projectworks vers la zone Bronze
- Transformer et standardiser les données vers la zone Argent (Delta Tables)
- Documenter les pipelines et les choix techniques
- Modélisation dimensionnelle dans la zone Or
- Développement de rapports Power BI exploitant les données modélisées

### Méthodologie

Le projet est structuré en trois grandes étapes séquentielles suivant l'**architecture Médaillon** :

1. **Bronze** : Extraction des données brutes depuis l'API REST Projectworks vers le Lakehouse Microsoft Fabric
2. **Argent** : Nettoyage, standardisation et application des règles d'affaires
3. **Or** : Modélisation dimensionnelle pour alimenter des rapports Power BI

Chaque étape est validée avant de passer à la suivante. Les validations incluent des tests manuels, la vérification des métriques d'exécution des pipelines, et des revues régulières avec mon superviseur Ali Kettani.

### Validation et Évaluation

L'évaluation se fera en vérifiant que l'entrepôt de données permet concrètement à Groupe Neos de répondre à ses besoins analytiques, notamment :

- **Scénarios d'usage** : est-ce qu'on peut calculer le taux d'utilisation des conseillers ? Suivre la consommation budgétaire d'un projet ? Identifier les dépassements ?
- **Tests de bout en bout** : validation que les données passent correctement de l'API Projectworks → Bronze → Argent sans perte ni altération
- **Indicateurs quantitatifs** : taux d'erreur des pipelines, nombre de lignes traitées, couverture des endpoints...
- **Retours du superviseur** : validation que les données transformées correspondent aux règles d'affaires de Groupe Neos et peuvent alimenter des rapports Power BI


## Équipe

- **Alae Aloui** : Stagiaire, conception et développement de l'entrepôt de données
- **Ali Kettani** : Superviseur de stage, validation des livrables et des choix techniques

## Échéancier

!!! info
    Le suivi complet est disponible dans la page [Suivi de projet](suivi.md).

| Activités                      | Début   |   Fin   | Livrable                            | Statut      |
|--------------------------------|---------|---------|-------------------------------------|-------------|
| Ouverture de projet            | 4 mai   | 15 mai  | Proposition de projet               | ✅ Terminé  |
| Études préliminaires           | 4 mai   | 22 mai  | Document d'analyse                  | 🔄 En cours |
| Présentation + Rapport         | 7 aout  | 14 aout | Présentation + Rapport              | ⏳ À venir  |
