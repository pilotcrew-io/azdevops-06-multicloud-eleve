# Atelier P06 — une API, un conteneur, trois contrats de déploiement

**Durée du tronc commun : 4 h 30, soit 270 minutes.** Le Jour 0 est terminé. Aucun compte cloud n’est demandé dans ce tronc commun. La mise en service réelle sur un cloud est une extension distincte.

## Mission

Votre équipe veut éviter que son code HTTP dépende d’un hébergeur. Vous devez construire une API Java portable, l’exécuter réellement dans Docker, puis définir et vérifier les contrats Terraform qui la relient à Azure Container Apps, AWS ECS Fargate et Google Cloud Run. Vous devez également expliquer pourquoi une image portable ne rend pas identiques l’authentification, le réseau, le stockage ni les coûts des trois plateformes.

L’objectif est **la portabilité du contrat et de la configuration**. Le projet ne demande ni routage actif entre trois clouds, ni bascule automatique, ni réplication de base de données.

## Planning

| Temps | Étape | Résultat vérifiable |
|---|---|---|
| 00–15 | Fixer le contrat et les limites | Contrat HTTP et hypothèses dans le compte rendu |
| 15–60 | Écrire/tester l’API Java | Tests HTTP et JAR autonome |
| 60–110 | Construire et exécuter le conteneur | Image locale, Compose sain, smoke réel |
| 110–120 | Pause | |
| 120–150 | Écrire le contrat Azure | Port, image, sondes, ressources et entrée cohérents |
| 150–180 | Écrire le contrat AWS | Tâche Fargate/service et séparation des rôles |
| 180–210 | Écrire le contrat Google Cloud | Cloud Run, port, identité et invocation |
| 210–245 | Valider les trois configurations | Trois validations de schéma et tests mock identifiés |
| 245–260 | Comparer IAM, réseau et coûts | Matrice argumentée et décision |
| 260–270 | Nettoyer et remettre les preuves | Arrêt du projet Compose et compte rendu |

## 1. Décrire un contrat portable — 15 minutes

Définissez deux chemins exacts : `/health` et `/api/status`. Le contrat doit fournir un statut, un nom de service, une version d’artefact et une étiquette d’environnement. Les deux endpoints répondent en JSON. Prévoyez le comportement d’un chemin inexistant et d’une méthode qui ne lit pas les données.

Choisissez un port interne commun de 8080 tout en le rendant configurable par `PORT`. La valeur d’environnement « azure », « aws » ou « gcp » est une étiquette renseignée par le déploiement : elle ne prouve pas que le processus tourne dans ce cloud. Conservez cette distinction dans vos conclusions.

**Périmètre :** pas de base de données, pas de secrets métier, pas d’appel à une API propriétaire, sortie standard pour les journaux, arrêt propre du processus. Il s’agit d’une API d’apprentissage sans authentification applicative intégrée.

## 2. Créer et vérifier le service — 45 minutes

Créez le projet Maven et son point d’entrée Java 17. Le serveur HTTP du JDK suffit. Embarquez la version dans le JAR afin qu’elle corresponde au fichier construit. Prenez le port et l’étiquette depuis l’environnement, en validant les valeurs avant le démarrage. Une écoute dans un conteneur doit accepter les connexions sur ses interfaces internes ; la publication du port sur le poste doit rester limitée à loopback.

Écrivez des tests HTTP sur un port éphémère. Vérifiez les réponses des deux chemins, leur type de contenu, les chemins exacts, les méthodes refusées et les mauvaises configurations. Ajoutez une petite sonde HTTP exécutable par la JVM : elle devra sortir avec succès uniquement si la santé répond correctement, pour fonctionner dans l’image même sans curl.

**Acceptation :** tests exécutés, JAR démarrable seul, réponse conforme, configuration invalide refusée et étiquette d’environnement présentée comme déclarative.

## 3. Démontrer le fonctionnement Docker — 50 minutes

Créez un Dockerfile en plusieurs étapes. Une étape construit le projet et exécute les tests ; une autre ne contient que le runtime et le JAR. Utilisez Java 17, un utilisateur non privilégié et une commande d’entrée qui fait de Java le processus principal. Expliquez pourquoi la dernière image n’a pas besoin de Maven.

Définissez un healthcheck qui réutilise votre sonde JVM. Écrivez un fichier Compose avec port hôte local distinct du port conteneur, variables d’environnement, mémoire bornée et nettoyage facile. Ne montez ni le socket Docker ni un dossier de secrets dans le conteneur. Évitez les privilèges inutiles.

Construisez réellement l’image. Démarrez Compose, attendez son état sain et exécutez un smoke qui vérifie statut HTTP et corps JSON, puis le comportement d’un chemin inconnu et d’une mauvaise méthode. Inspectez l’utilisateur effectif et le statut de santé. Notez la différence entre « image construite », « conteneur démarré », « conteneur sain » et « API conforme ».

**Acceptation :** les requêtes atteignent un vrai conteneur local. Un simple parsing du Dockerfile ou de Compose ne valide pas cette étape. Si le moteur est indisponible, la preuve reste manquante et doit être rattrapée.

## 4. Écrire trois contrats Terraform minimaux — 3 × 30 minutes

Créez trois racines indépendantes, avec une contrainte de version majeure par provider et un état distinct. Le tronc commun peut modéliser les dépendances d’environnement par **variables** et valeurs fictives utilisées seulement dans les tests. Il n’exige pas d’écrire le bootstrap réseau/registre complet d’AWS pendant ces 30 minutes. Le corrigé fournit également cette extension pour rendre les déploiements optionnels reproductibles.

### Azure Container Apps

Votre contrat minimum comprend l’application, un identifiant d’environnement d’exécution, la référence d’image, les variables de l’API, la cible du port d’entrée et une sonde vers `/health`. Bornez les réplicas. Définissez comment l’image privée serait lue par une identité et comment l’accès de test serait restreint à votre adresse. Les ressources de registre, de journalisation et d’identité peuvent être des entrées du contrat minimum ; leur création réelle appartient à l’extension.

**À expliquer :** la sonde parle au conteneur, l’entrée publique se termine en HTTPS sur la plateforme et le droit de lire le registre n’est pas une autorisation métier de l’API.

### AWS ECS Fargate

Votre contrat minimum comprend une définition de tâche Fargate et un service relié à un cluster et des sous-réseaux fournis par variables. Fixez l’architecture, le couple CPU/mémoire, le port interne et la sonde. Distinguez le **rôle d’exécution** qui permet au service de tirer l’image/écrire les logs, du **rôle de tâche** disponible au code applicatif. Le réseau VPC, les routes, le registre et la création de ces rôles sont une extension, pas du travail caché obligatoire au tronc commun.

**À expliquer :** le choix `awsvpc`, l’accès sortant nécessaire au démarrage et la restriction du trafic entrant. ECS Fargate est retenu car App Runner n’accepte plus de nouveaux clients depuis le 31 mars 2026, d’après [AWS](https://docs.aws.amazon.com/apprunner/latest/api/API_CreateConnection.html).

### Google Cloud Run

Votre contrat minimum comprend un service Cloud Run v2, la référence d’image, le compte de service d’exécution, un port et une sonde de démarrage. Bornez les instances. Laissez la plateforme fournir sa variable réservée `PORT`. Gardez l’invocation IAM authentifiée ; ne donnez pas l’accès à `allUsers`.

**À expliquer :** autorisation de déployer, identité d’exécution, permission d’invoquer et accès réseau sont quatre notions distinctes. Une entrée réseau ouverte ne supprime pas le contrôle IAM.

## 5. Vérifier sans utiliser de compte cloud — 35 minutes

Pour chaque racine, contrôlez le format, initialisez les providers sans backend distant et validez la configuration. Ensuite, écrivez des tests Terraform avec provider mock qui font porter les assertions sur les propriétés importantes : port, sonde, limite d’instances, identité choisie et portée des droits ou de l’entrée. Les mocks doivent être déclarés explicitement dans les fichiers de test.

Un test mock peut demander une phase nommée `apply` **dans son bloc de test**, tout en remplaçant les opérations du fournisseur par des réponses fictives. Cela n’autorise pas à lancer un `terraform apply` normal : ce dernier utiliserait le vrai provider et créerait des ressources. Relisez cette différence avant tout lancement.

Les trois validations doivent exploiter les vrais schémas des providers. Gardez les lockfiles et consignez leurs versions. Un provider qui ne démarre pas, un téléchargement bloqué ou une validation de schéma qui échoue n’est pas un résultat réussi. Corrigez ou indiquez précisément la limite. Vérifiez enfin que les valeurs fictives ne sont présentées nulle part comme des URLs ou images réellement déployées.

## 6. Prendre une décision d’architecture — 15 minutes

Complétez la matrice IAM/réseau avec vos propres mots. Pour chaque cloud, indiquez le contrôle de l’entrée, la terminaison TLS, l’identité qui lit l’image, l’identité du code, le lieu des logs, le comportement à zéro instance et les principales ressources facturables.

Choisissez une cible pour cette API d’apprentissage. Appuyez votre choix sur les contraintes données, pas sur un classement général des clouds. Expliquez deux limites de portabilité : registre/identité/réseau, données, déploiement ou supervision. L’image Java portable ne constitue pas un système de secours multicloud.

## 7. Nettoyer et conclure — 10 minutes

Arrêtez votre projet Compose nommé et vérifiez son arrêt. Conservez le code, les lockfiles et les preuves utiles ; gardez les caches, états et rapports bruts ignorés. Aucun nettoyage de compte cloud n’est nécessaire puisque le tronc commun n’y a rien créé.

Remettez le compte rendu, la matrice et les tests. Si vous choisissez ensuite une extension, utilisez uniquement votre propre état et prenez le temps de lire le plan de destruction puis de vérifier sa fin. Les étapes d’extension n’augmentent pas le barème du tronc commun.

## Extension facultative, hors 4 h 30

Choisissez **un seul** fournisseur déjà autorisé. Créez le registre et les dépendances nécessaires, publiez votre image `linux/amd64`, relevez son digest, puis déployez le service avec cette référence immuable. Vérifiez l’URL réelle et l’identité/le contrôle réseau qui l’autorise. Les trois procédures sont disponibles dans la variante corrigée. Aucune extension n’a été exécutée pour préparer ce support ; les résultats d’exécution vous appartiennent.

## Fichiers du socle et de l’extension

Pour votre implémentation du socle, créez les trois racines `infra-contracts/azure`, `infra-contracts/aws` et `infra-contracts/gcp`. Chacune contient `versions.tf` (versions/provider), `variables.tf` (entrées), `main.tf` (contrat) et `tests/contract.tftest.hcl` (provider mock et assertions). Le lockfile provient de l’initialisation.

| Racine du socle | Ressources essentielles | Dépendances fournies comme entrées |
|---|---|---|
| Azure | Application Container Apps | Groupe, environnement, image, registre, identité de lecture et /32 |
| AWS | Définition de tâche et service ECS | Cluster, sous-réseau, groupe de sécurité, deux rôles, groupe de logs et image |
| Google | Service Cloud Run v2 et droit d’invocation | Projet, image, compte de service et membre autorisé |

Les répertoires `infra/azure`, `infra/aws` et `infra/gcp` de la variante corrigée contiennent le bootstrap complet des extensions. Ils ne sont pas à reproduire pendant le socle de 270 minutes. Les mémos, cette table et les références de schémas du support élève suffisent pour construire les contrats : l’accès au corrigé privé n’est pas un prérequis.
