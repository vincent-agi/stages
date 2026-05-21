# FICHE DE PROJET DE STAGE : Monitoring des serveur & alerting

## Informations Générales
* **Intitulé du sujet :** Conception d'un système de monitoring léger et d'alerting précoce en Python pour infrastructures SaaS.
* **Niveau ciblé :** Bac Professionnel CIEL (Cybersécurité, Informatique et réseaux, Électronique) – Première Année.
* **Environnement technique :** Python 3, Linux (Debian/Ubuntu), Docker, API REST, Webhooks / Protocoles de notification, Planificateur de tâches (Cron).

---

## Contexte et Problématique
Dans le cadre du maintien en condition opérationnelle (MCO) de nos applications web distribuées en mode SaaS, la réactivité face aux incidents est un enjeu critique. Nos infrastructures hébergent des architectures modernes combinant des services système traditionnels, des bases de données relationnelles et des conteneurs Docker.

L'objectif de ce stage est de concevoir un système de supervision automatisé, **léger, non intrusif et hautement réactif**. Plutôt que de déployer des solutions lourdes du marché, le choix est fait de développer des scripts de diagnostic ciblés en **Python**. Ces outils devront analyser en temps réel l'état de santé du matériel, des services et de la couche applicative afin de détecter toute dégradation et de déclencher une alerte au plus tôt (approche *fail-fast*).

---

## Objectifs de la Mission
Le stagiaire aura pour mission de concevoir un outil modulaire capable de couvrir trois niveaux d'analyse indispensables :

### 1. Supervision Infrastructure & Système
* Évaluer les performances physiques et logiques du système (charge processeur globale, saturation de la mémoire vive).
* Surveiller l'état des espaces de stockage, en portant une attention particulière aux partitions accueillant les volumes de données et l'écosystème Docker.

### 2. Supervision de la Couche Conteneurisée et des Services
* S'assurer de la persistance et de la stabilité des processus essentiels (serveurs web, moteurs de bases de données).
* Interroger l'état de santé des conteneurs Docker pour identifier immédiatement les arrêts inopinés, les cycles de redémarrage infinis (*bootloops*) ou les surconsommations de ressources.

### 3. Supervision Applicative (API Health Check)
* Consommer et analyser de manière automatisée l'API de diagnostic interne déjà intégrée à nos applications SaaS.
* Interpréter les réponses de cette API afin de valider non seulement que le serveur répond, mais que l'application est fonctionnellement pleinement opérationnelle.

### 4. Notification et Alerting Instantané
* Développer le canal de communication permettant de formater et d'expédier les alertes vers nos outils de messagerie d'équipe dès le franchissement de seuils critiques.

---

## Organisation et Chronogramme du Stage

### Semaine 1 : Immersion, Architecture et Maquettage
* Prise en main de l'environnement de développement et appréhension de la topologie des serveurs de l'entreprise.
* Déploiement d'une maquette locale (Machine Virtuelle sous Linux) émulant l'architecture cible.
* Étude de la structure des réponses de l'API de diagnostic interne existante.

### Semaine 2 : Développement des Modules de Collecte en Python
* Conception de la brique de récupération des données système et des indicateurs de charge.
* Développement du module d'interaction avec l'API Docker pour auditer l'état des conteneurs actifs.
* Intégration du composant client HTTP en Python pour interroger l'API de *Health Check* applicative de nos SaaS.

### Semaine 3 : Moteur d'Alerte et Automatisation
* Création de la logique conditionnelle déterminant les niveaux de criticité (Alerte / Critique).
* Développement du module d'envoi vers l'API de notification (Webhook de messagerie).
* Configuration de l'exécution industrielle et cyclique des scripts via le planificateur de tâches Linux (Cron).

### Semaine 4 : Recette, Simulation de Pannes et Documentation
* Mise en œuvre de scénarios de tests de robustesse (arrêts volontaires de services, coupures réseau, saturation simulée).
* Vérification de la réactivité et de la bonne réception des alertes au plus tôt.
* Rédaction de la documentation technique d'exploitation et préparation du support pour la soutenance orale de fin d'année.

---

## Compétences du Référentiel Métier Développées
* **Administration Système Linux :** Gestion des processus, analyse de journaux d'événements, planification de tâches, gestion des environnements d'exécution.
* **Réseaux et Protocoles :** Compréhension des requêtes et codes de statut HTTP/HTTPS, requêtes REST, adressage et ports de services.
* **Virtualisation et Conteneurs :** Manipulation des concepts Docker (états de cycle de vie des conteneurs, isolation des ressources).
* **Développement Algorithmique :** Structuration de code propre en Python, manipulation de structures de données (JSON), gestion des exceptions et des erreurs de communication.

---

## Livrables Attendus
1. **Dépôt de Code Source :** Ensemble des modules Python documentés, commentés et structurés de façon claire.
2. **Guide d'Exploitation :** Manuel technique explicitant la procédure d'installation, la modification des seuils d'alerte et la méthode pour intégrer un nouveau point de contrôle.
3. **Cahier de Recette :** Tableau récapitulatif des cas d'usage testés, détaillant la panne provoquée, l'heure du test, le délai de détection et la conformité du message d'alerte reçu.
