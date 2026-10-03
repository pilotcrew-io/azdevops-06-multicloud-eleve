# Dépannage — P06

| Symptôme | Cause à examiner | Vérification / résolution |
|---|---|---|
| Maven n’utilise pas Java 17 | JDK sélectionné par le terminal | Comparer les versions du runtime, du compilateur et de Maven |
| Docker affiche un client mais pas de serveur | Moteur arrêté ou contexte incorrect | Revenir au moteur de formation prévu au Jour 0 |
| Construction d’image bloquée | Proxy, registre ou téléchargement Maven | Vérifier le réseau autorisé et son certificat, sans désactiver TLS |
| Image fonctionne sur le poste mais pas Fargate | Architecture ARM/x86-64 différente | Construire la cible Linux x86-64 déclarée par la tâche |
| Conteneur démarre, aucune réponse depuis l’hôte | Écoute loopback dans le conteneur ou mauvaise publication | Vérifier adresse interne, port conteneur et port hôte séparément |
| Port 18080 déjà utilisé | Un autre projet local écoute sur ce port | Choisir un autre port hôte pour votre seul projet |
| Santé échoue alors que Java tourne | Mauvais chemin, délai ou classe de sonde absente | Exécuter la sonde fournie dans la même image et lire son code de sortie |
| Processus tué pour mémoire | Limite trop basse ou fuite | Vérifier l’état OOM et la mémoire totale, pas seulement le tas Java |
| HCL formaté mais `validate` échoue | Schéma incompatible ou provider indisponible | Distinguer erreur de configuration et problème d’exécution du plugin |
| Tests demandent des identifiants | Provider mock absent ou commande normale lancée | Relire les `.tftest.hcl` et confirmer que l’exécution est bien un test mock |
| Container Apps ne lit pas ACR | Identité, rôle ou propagation RBAC | Contrôler l’identité configurée et le droit AcrPull sur ce seul registre |
| ECS reste en attente ou arrête la tâche | Image, rôle d’exécution, réseau, architecture | Lire la raison d’arrêt et les logs ; comparer le digest et la sortie HTTPS |
| ECS inaccessible depuis le poste | IP de tâche obsolète, /32 changé, route ou port | Retrouver l’ENI courante et vérifier seulement la règle de votre groupe |
| Cloud Run refuse le déploiement | API, droits, compte de service ou `PORT` réservé | Vérifier le Jour 0 et la configuration ; ne pas redéfinir PORT |
| Cloud Run répond 403 | Absence d’identité ou de droit d’invocation | Utiliser le membre IAM prévu et un jeton d’identité approprié |
| Première réponse lente | Démarrage depuis zéro instance | Observer séparément démarrage à froid et requêtes déjà chaudes |
| Registre non vide lors du nettoyage | Image encore présente / politique de suppression | Suivre la procédure du registre dédié, sans toucher un registre partagé |
| Destruction encore en cours | Tâche, ENI, identité ou service en suppression | Attendre la fin, relire l’état et les opérations du fournisseur |

## Ne pas confondre les preuves

Un corps JSON indiquant un cloud ne prouve pas son hébergement. Une sortie Terraform mock n’est pas une URL déployée. Un `docker compose config` réussi ne lance pas le conteneur. Un état Docker « running » ne prouve pas le contrat de l’API. Chaque ligne du compte rendu doit mentionner le niveau réellement testé.

Pour l’extension AWS, l’IP publique d’une tâche peut changer quand ECS la remplace. Le support n’installe pas d’équilibreur : l’URL est une adresse éphémère de test. Ne transformez pas cette simplification en proposition d’architecture de production.
