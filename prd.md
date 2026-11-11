---
title: jira-spec-compiler
status: draft
created: 2026-11-11
updated: 2026-11-11
---

# PRD: jira-spec-compiler

## 0. Objet du document

Ce Product Requirements Document (PRD) définit le périmètre fonctionnel, les parcours utilisateurs, les exigences testables et les indicateurs de succès de `jira-spec-compiler`. Il s'adresse au Chef de Projet IA, aux Product Owners, aux Tech Leads et aux développeurs amenés à construire ou utiliser l'outil, ainsi qu'aux compétences aval de la méthode BMad (`bmad-architecture`, `bmad-create-epics-and-stories`, `bmad-agent-dev`). Le document s'appuie sur la session d'idéation initiale consignée dans [.memlog.md](../../brainstorming/brainstorm-optimisation-completion-tickets-jira-2026-11-11/.memlog.md) et sur son livrable visuel [brainstorm.html](brainstorm.html). Les hypothèses prises en mode rapide sont balisées par `[ASSUMPTION]` et récapitulées à la section 9.

## 1. Vision

`jira-spec-compiler` transforme la création et le raffinement des tickets Jira en un processus de compilation assisté par IA. Aujourd'hui, les Product Owners manquent de temps et de visibilité technique pour rédiger des tickets exhaustifs, tandis que les développeurs perdent de précieuses heures à chercher la documentation associée, à poser des questions de clarification et à recoder des fonctionnalités déjà présentes ailleurs dans le patrimoine applicatif. Cette friction bloque également le passage à l'échelle du développement assisté par agent, car un agent comme `bmad-agent-dev` ne peut produire un code fiable à partir d'un ticket ambigu ou déconnecté de l'existant.

Le produit agit comme un compilateur situé entre l'intention produit et l'exécution logicielle. Dès qu'un Product Owner esquisse un besoin dans Jira, `jira-spec-compiler` l'interroge sur les zones d'ombre métier, attache automatiquement les extraits pertinents de la documentation (Confluence, ADR, contrats d'API), identifie dans le code source les Briques Logicielles réutilisables et vérifie la complétude du ticket via une lecture à blanc par le persona `bmad-agent-dev`.

À la sortie de ce pipeline, l'équipe dispose d'un Ticket Bifrons: une vue métier concise que le Product Owner pilote sans surcharge technique, et un Manifeste d'Exécution BMad complet permettant au développeur humain de démarrer en cinq minutes ou de déléguer directement la réalisation à `bmad-agent-dev`.

## 2. Utilisateurs cibles et parcours

### 2.1 Travaux à accomplir (Jobs to be done)

1. En tant que Product Owner, je veux transformer trois puces d'intention métier en un ticket complet et bordé en moins de trois minutes afin de ne plus bloquer l'équipe de développement sans y sacrifier mes soirées.
2. En tant que Product Owner, je veux savoir dès la rédaction si mon besoin peut s'appuyer sur une Brique Logicielle existante afin d'arbitrer le périmètre en faveur d'une livraison rapide.
3. En tant que Développeur, je veux ouvrir un ticket contenant déjà les extraits documentaires exacts, les fichiers du dépôt impactés, les Briques Logicielles à importer et les cas limites tranchés afin de coder ou de lancer `bmad-agent-dev` sans aller-retour sur Slack.
4. En tant que Chef de Projet IA, je veux standardiser la qualité des tickets selon le référentiel BMad et mesurer le taux de réutilisation du code afin d'industrialiser l'usage de `bmad-agent-dev` avec un retour sur investissement démontrable.

### 2.2 Non-utilisateurs en version 1

En version 1, l'outil ne cible pas les équipes de support client de niveau 1 gérant des tickets d'incidents purement opérationnels (hors évolution de code ou correction de bug applicatif), ni les gestionnaires de portefeuille financier cherchant un outil de reporting budgétaire macroscopique.

### 2.3 Parcours utilisateurs clés

#### UJ-1. Claire (Product Owner) compile un ticket de nouvelle fonctionnalité en trois minutes
Claire, Product Owner d'une squad métier, sort d'un atelier et ouvre Jira Cloud `[ASSUMPTION: cible primaire Jira Cloud avec extension ou webhook]` pour créer une story sur l'export PDF des factures. Elle saisit un titre et trois lignes de description brute, puis clique sur l'action "Compiler le ticket". En dix secondes, `jira-spec-compiler` affiche dans le panneau d'assistance trois questions fermées sur les cas limites (gestion des factures multi-devises, comportement en cas d'échec de génération, archivage légal) et signale qu'une Brique Logicielle interne nommée `PdfExportEngine` couvre déjà 85 % du besoin si l'on conserve la mise en page standard. Claire coche l'option de réutilisation de `PdfExportEngine` et répond aux trois questions en un clic. Le ticket est mis à jour sous forme de Ticket Bifrons avec un Score BMad-Ready de 92/100. Cas d'erreur: si la page Confluence de référence sur la facturation contredit le schéma de données actuel du dépôt Git, l'interface signale le conflit à Claire et marque l'extrait documentaire comme "À vérifier avec le Tech Lead".

#### UJ-2. Maxime (Développeur Senior) prend le ticket compilé et délègue l'implémentation à `bmad-agent-dev`
Maxime ouvre au début du sprint le ticket validé par Claire. Il déplie le volet technique du Ticket Bifrons et consulte le Manifeste d'Exécution BMad: l'extrait précis de l'ADR sur la génération de documents, la fiche de confiance de la Brique Logicielle `PdfExportEngine` (couverture de tests à 94 %, trois exemples d'appels récents dans le code), la liste des quatre fichiers du dépôt Git à modifier et les scénarios Gherkin d'acceptation. Convaincu par le découpage, Maxime copie l'identifiant du ticket dans son terminal pour invoquer `bmad-agent-dev`. L'agent charge le Manifeste d'Exécution BMad, importe `PdfExportEngine`, écrit le câblage et exécute la suite de tests associée sans poser de question supplémentaire. Cas d'erreur: si `PdfExportEngine` a été modifié sur la branche `main` depuis la compilation du ticket, un indicateur d'obsolescence invite Maxime à relancer un rafraîchissement du manifeste en un clic avant d'exécuter l'agent.

#### UJ-3. Henri (Chef de Projet IA) audite la maturité du backlog et la capitalisation des briques
Henri prépare le bilan mensuel de transformation IA de ses squads. Il consulte le tableau de bord de gouvernance de `jira-spec-compiler` qui agrège les métriques des tickets passés en sprint. Il observe que le Score BMad-Ready moyen avant sprint planning est passé de 45 à 88/100, que le nombre de commentaires de clarification pendant le sprint a chuté de 60 %, et que 14 stories ont réutilisé des Briques Logicielles existantes au lieu de recoder des composants similaires. Il identifie également un module nouvellement fusionné par une squad et valide son ajout automatique dans l'Index de Briques pour les prochains sprints.

## 3. Glossaire

Chaque terme ci-dessous possède une signification unique dans l'ensemble du PRD et des spécifications aval:

1. **Ticket Bifrons**: structure de ticket Jira générée par le système, composée de deux vues synchronisées: une vue fonctionnelle concise appartenant au Product Owner et un Manifeste d'Exécution BMad destiné au développeur et à l'agent.
2. **Brique Logicielle**: composant, service, module, hook ou utilitaire existant dans les dépôts Git de l'organisation, indexé avec sa signature, sa documentation, son propriétaire et ses métriques de fiabilité.
3. **Index de Briques**: catalogue sémantique et structurel auto-maintenu qui recense l'ensemble des Briques Logicielles du patrimoine applicatif à partir des dépôts Git et des fichiers `AGENTS.md`.
4. **Devis de Réutilisation**: estimation comparative affichée au Product Owner mettant en regard la complexité d'un développement neuf et la complexité réduite obtenue en réutilisant une Brique Logicielle candidate.
5. **Dry-Run Amelia**: simulation de lecture du ticket par un agent configuré selon les exigences de `bmad-agent-dev`, destinée à détecter toute information manquante qui obligerait un développeur ou un agent à deviner une règle lors du codage.
6. **Score BMad-Ready**: note sur 100 évaluant la complétude métier, documentaire et technique d'un Ticket Bifrons ainsi que son aptitude à être exécuté sans ambiguïté par un développeur ou par `bmad-agent-dev`.
7. **Manifeste d'Exécution BMad**: section technique structurée du Ticket Bifrons contenant les extraits documentaires vérifiés, les Briques Logicielles à importer, les chemins de fichiers cibles dans Git, les scénarios Gherkin et le contexte d'invocation pour `bmad-agent-dev`.

## 4. Fonctionnalités et exigences fonctionnelles

### 4.1 Assistant maïeutique PO et linter sémantique

Cette fonctionnalité accompagne le Product Owner dès la création ou l'édition d'un ticket brouillon dans Jira. Au lieu de générer un long texte générique qui noie l'intention initiale, l'assistant identifie les formulations vagues et les angles morts métier, puis pose un nombre restreint de questions ciblées. Cette approche réalise le parcours UJ-1. `[ASSUMPTION: l'interaction peut s'effectuer soit dans un panneau Jira Forge, soit via un fil de commentaires structuré déclenché par webhook]`.

#### FR-1: Détection des termes ambigus et incomplétudes métier
Le système analyse le titre et la description brute d'un ticket Jira à la demande du Product Owner ou lors du passage au statut de raffinement, afin de repérer les adjectifs non mesurables, les acteurs non définis et les règles de gestion implicites. Réalise UJ-1.
1. Condition testable: lorsqu'un brouillon contient des termes qualitatifs sans seuil chiffré ("rapide", "simple", "optimisé", "volumineux"), le système les surligne et demande la métrique ou le comportement précis attendu.
2. Condition testable: le temps d'analyse initiale du brouillon ne dépasse pas 10 secondes pour un ticket standard.

#### FR-2: Génération du questionnaire d'arbitrage rapide
Le système génère entre 1 et 5 questions fermées ou à choix multiples permettant au Product Owner de trancher les cas limites et les règles métier manquantes sans rédiger de longs paragraphes. Réalise UJ-1.
1. Condition testable: chaque question propose au moins deux options concrètes issues du contexte métier du projet ainsi qu'une option de saisie libre.
2. Condition testable: les réponses sélectionnées par le Product Owner mettent automatiquement à jour la vue fonctionnelle du Ticket Bifrons et les critères d'acceptation Gherkin.

### 4.2 Ancrage documentaire contextuel et détection d'obsolescence

Cette fonctionnalité élimine la recherche manuelle de documentation dans Confluence et les dépôts Git. Le système interroge les bases de connaissances de l'équipe, sélectionne uniquement les extraits pertinents et vérifie leur cohérence avec l'état réel du code source. Réalise UJ-1 et UJ-2.

#### FR-3: Extraction et citation granulaire de la documentation
Le système recherche dans Confluence, les ADR et les spécifications d'API (OpenAPI/GraphQL) les passages correspondant au domaine fonctionnel du ticket et insère dans le Ticket Bifrons un résumé exécutif accompagné du lien ancré vers la section exacte. Réalise UJ-1, UJ-2.
1. Condition testable: chaque référence documentaire insérée contient l'URL exacte, la date de dernière mise à jour de la source et un extrait limité à 150 mots maximum par source.
2. Condition testable: aucune source ayant un score de pertinence sémantique inférieur au seuil configuré n'est attachée au ticket.

#### FR-4: Vérification de cohérence entre documentation et branche principale
Le système compare les affirmations de la documentation citée avec les schémas de données et contrats du code source sur la branche principale du dépôt Git. Réalise UJ-1, UJ-2.
1. Condition testable: en cas de divergence détectée entre une page Confluence et le code source actuel, le système affiche un avertissement explicite "Conflit Doc / Code" dans le Ticket Bifrons en désignant le fichier Git qui fait foi.

### 4.3 Radar de Briques Logicielles et Devis de Réutilisation

Cette fonctionnalité connecte l'expression du besoin produit au patrimoine logiciel déjà développé par l'entreprise. Elle pousse les propositions de réutilisation dès la phase de rédaction par le Product Owner afin d'éviter les développements redondants. Réalise UJ-1, UJ-2 et UJ-3.

#### FR-5: Indexation continue du patrimoine en Briques Logicielles
Le système maintient l'Index de Briques en analysant les dépôts Git configurés, les fichiers `AGENTS.md`, les manifestes de packages et les Pull Requests fusionnées. Réalise UJ-2, UJ-3.
1. Condition testable: chaque Brique Logicielle de l'Index de Briques expose son chemin dans le dépôt, son objectif fonctionnel, ses points d'entrée publics, son taux de couverture de tests et la date de sa dernière modification.
2. Condition testable: lors de la fusion d'une Pull Request créant ou modifiant un composant partagé, l'Index de Briques est mis à jour automatiquement `[ASSUMPTION: déclenchement par webhook GitHub/GitLab ou tâche planifiée CI]`.

#### FR-6: Suggestion de Briques Logicielles et Devis de Réutilisation
Lors de la compilation d'un ticket, le système identifie les Briques Logicielles candidates dans l'Index de Briques, calcule leur pourcentage de couverture du besoin et présente au Product Owner un Devis de Réutilisation. Réalise UJ-1, UJ-2.
1. Condition testable: lorsque au moins une Brique Logicielle couvre plus de 60 % du besoin détecté, le système propose au Product Owner une variante de périmètre appuyée sur cette Brique Logicielle avec l'estimation du gain d'effort relatif.
2. Condition testable: pour le développeur, le Manifeste d'Exécution BMad inclut l'exemple d'import et les trois derniers tickets ou commits ayant utilisé cette Brique Logicielle.

### 4.4 Qualification Dry-Run Amelia et compilation du Manifeste d'Exécution BMad

Cette fonctionnalité prépare le passage de relais vers le développeur humain et vers la compétence `bmad-agent-dev`. Elle garantit que le ticket contient l'ensemble des éléments requis pour coder et tester la fonctionnalité sans incertitude. Réalise UJ-2 et UJ-3.

#### FR-7: Simulation de lecture Dry-Run Amelia et calcul du Score BMad-Ready
Le système exécute une passe d'analyse critique incarnant les exigences de `bmad-agent-dev` afin de lister les hypothèses implicites restantes et de calculer le Score BMad-Ready sur 100. Réalise UJ-1, UJ-2, UJ-3.
1. Condition testable: le Score BMad-Ready décompose la note selon quatre axes: clarté des règles métier (25 pts), présence des cas limites et critères Gherkin (25 pts), ancrage documentaire et Briques Logicielles (25 pts), identification des fichiers cibles et commandes de test (25 pts).
2. Condition testable: si le Score BMad-Ready est inférieur à 80/100, le système liste explicitement les éléments bloquants à lever avant le passage du ticket au statut *Ready for Dev*.

#### FR-8: Génération du Manifeste d'Exécution BMad et détection de péremption
Le système assemble le Manifeste d'Exécution BMad dans le volet technique du Ticket Bifrons et l'associe au hash du commit de référence de la branche principale. Réalise UJ-2.
1. Condition testable: le Manifeste d'Exécution BMad contient les scénarios Gherkin (cas nominal, cas dégradés, erreurs), la liste des fichiers candidats à modifier dans le dépôt, les Briques Logicielles retenues, les contraintes architecturales issues de `AGENTS.md` et la commande de vérification des tests.
2. Condition testable: si l'un des fichiers cibles ou l'une des Briques Logicielles référencées dans le manifeste est modifié sur la branche principale avant le démarrage du ticket, le système marque le manifeste comme "À rafraîchir" et permet sa régénération en un clic.

#### FR-9: Passerelle d'invocation directe pour `bmad-agent-dev`
Le système expose le Manifeste d'Exécution BMad sous un format directement consommable par `bmad-agent-dev` (via serveur MCP Jira ou export d'un fichier de story normalisé dans l'espace de travail local du développeur). Réalise UJ-2.
1. Condition testable: un développeur exécutant `bmad-agent-dev` avec la référence du ticket compilé récupère l'intégralité du Ticket Bifrons sans copier-coller manuel des sections Confluence ou des chemins de fichiers.

### 4.5 Gouvernance et télémétrie pour le Chef de Projet IA

Cette fonctionnalité offre une visibilité factuelle sur la qualité du backlog, l'adoption des Briques Logicielles et la réduction des frictions entre Product Owners et développeurs. Réalise UJ-3.

#### FR-10: Tableau de bord de maturité et de réutilisation
Le système agrège par squad et par sprint le Score BMad-Ready moyen, le nombre d'allers-retours de commentaires après passage en sprint et le taux de réutilisation effective des Briques Logicielles recommandées. Réalise UJ-3.
1. Condition testable: lors de la clôture d'un ticket, le système vérifie dans la Pull Request associée si les Briques Logicielles suggérées dans le Manifeste d'Exécution BMad ont été effectivement importées dans le code livré.

## 5. Exigences non fonctionnelles transverses (NFR)

### Performance et temps de réponse
L'analyse interactive d'un brouillon PO (FR-1, FR-2, FR-6) doit s'exécuter en moins de 12 secondes au 95e centile afin de permettre une utilisation fluide en direct pendant la rédaction ou lors d'une séance de refinement.

### Concision et ergonomie cognitive
La vue fonctionnelle du Ticket Bifrons ne doit jamais dépasser 25 lignes de texte hors critères Gherkin dépliables. Les détails destinés au développeur et à `bmad-agent-dev` doivent être isolés dans une section repliable ou un onglet dédié afin de préserver la lisibilité pour les profils métier.

### Sécurité, confidentialité et contrôle d'accès
Le moteur RAG et l'Index de Briques respectent strictement les périmètres d'autorisation de l'organisation. Aucun secret, jeton d'API ou donnée personnelle issue d'un ticket ne doit être persisté hors de l'infrastructure autorisée, et chaque appel LLM doit pouvoir être routé vers les modèles approuvés par la politique de sécurité interne `[ASSUMPTION: compatibilité multi-fournisseurs LLM via API standardisée]`.

## 6. Non-objectifs explicites (v1)

1. `jira-spec-compiler` ne remplace pas le Product Owner dans la décision stratégique de ce qu'il faut construire ni dans la priorisation métier de la feuille de route.
2. L'outil en version 1 ne fusionne jamais de code en production de manière autonome sans revue et validation par un développeur humain.
3. L'outil ne réécrit pas automatiquement les pages Confluence obsolètes sans validation humaine, il se limite à signaler les divergences entre la documentation et le code source.

## 7. Périmètre du MVP

### 7.1 Inclus dans le MVP (v1)
1. Pipeline de compilation déclenché sur un ticket Jira (analyse sémantique, 3 questions d'arbitrage PO, génération du Ticket Bifrons).
2. Connecteur de recherche documentaire (Confluence + fichiers Markdown/ADR du dépôt Git).
3. Index de Briques initial construit par analyse statique et sémantique du dépôt Git principal, avec suggestion de Briques Logicielles et Devis de Réutilisation.
4. Simulation Dry-Run Amelia, calcul du Score BMad-Ready et génération du Manifeste d'Exécution BMad compatible avec `bmad-agent-dev`.

### 7.2 Hors périmètre du MVP (reporté en v2)
1. Transcription audio en temps réel des réunions de backlog refinement (reporté en v2 pour privilégier d'abord la fiabilité du compilateur écrit).
2. Analyse multimodale automatique des maquettes Figma par vision par ordinateur (en v1, l'outil exploite les descriptions textuelles et liens de composants associés).
3. Génération nocturne automatique de Draft Pull Requests par `bmad-build-auto` avant même l'ouverture du sprint (reporté en v2 une fois que le Score BMad-Ready aura prouvé sa calibration sur plusieurs sprints).

## 8. Métriques de succès et contre-métriques

### Métriques primaires
1. **SM-1 (Taux de tickets BMad-Ready du premier coup)**: au moins 75 % des tickets compilés atteignent un Score BMad-Ready supérieur ou égal à 85/100 avant le démarrage du sprint et sont exécutés sans question bloquante de spécification. Valide FR-1, FR-2, FR-7, FR-8.
2. **SM-2 (Taux de réutilisation des Briques Logicielles)**: augmentation de 40 % de la part des stories intégrant au moins une Brique Logicielle existante identifiée par le radar, vérifiée dans les Pull Requests fusionnées au bout de 3 mois. Valide FR-5, FR-6, FR-10.

### Métriques secondaires
1. **SM-3 (Réduction du temps de raffinement)**: diminution de 50 % de la durée moyenne passée en réunion de backlog refinement par ticket grâce au pré-arbitrage des cas limites et à l'ancrage documentaire automatique. Valide FR-3, FR-4, FR-7.
2. **SM-4 (Adoption de `bmad-agent-dev`)**: au moins 50 % des tickets compilés ayant un Score BMad-Ready supérieur à 85/100 font l'objet d'une exécution assistée par `bmad-agent-dev`. Valide FR-8, FR-9.

### Contre-métriques (à surveiller pour éviter les effets pervers)
1. **SM-C1 (Inflation de la longueur textuelle des tickets)**: ne jamais optimiser la quantité de texte généré. Si la lecture de la vue PO dépasse 90 secondes ou si les développeurs signalent du remplissage artificiel dans le Manifeste d'Exécution BMad, le gabarit de génération doit être immédiatement resserré. Contrebalance SM-1.
2. **SM-C2 (Taux de rejet des Briques Logicielles suggérées)**: si plus de 30 % des Briques Logicielles recommandées par le radar sont écartées par les développeurs comme non pertinentes, le seuil de similarité de l'Index de Briques doit être relevé pour éviter l'effet d'alerte inutile. Contrebalance SM-2.

## 9. Questions ouvertes

1. Quelle est la cible d'hébergement exacte de votre instance Jira actuelle (Jira Cloud ou Jira Data Center) et préférez-vous une intégration initiale par webhook/commentaire automatisé ou une application Jira Forge avec panneau dédié?
2. Les Briques Logicielles à réutiliser sont-elles concentrées dans un monorepo unique ou réparties sur plusieurs dépôts Git distincts (polyrepo)?
3. Souhaitez-vous que le Score BMad-Ready agisse uniquement comme un indicateur visuel consultatif lors des premiers sprints, ou qu'il bloque formellement la transition Jira vers le statut *Ready for Dev* en dessous d'un certain seuil?

## 10. Index des hypothèses (`[ASSUMPTION]`)

1. Section 2.3 (UJ-1): cible primaire Jira Cloud avec déclenchement par action ou webhook.
2. Section 4.1: interface d'arbitrage PO livrable en MVP via un fil de commentaires structuré ou un panneau léger sans nécessiter d'interface externe lourde.
3. Section 4.3 (FR-5): rafraîchissement de l'Index de Briques déclenché automatiquement par webhook Git lors de la fusion des Pull Requests.
4. Section 5: architecture agnostique du fournisseur LLM permettant d'utiliser les modèles autorisés par la gouvernance IA de l'entreprise.
