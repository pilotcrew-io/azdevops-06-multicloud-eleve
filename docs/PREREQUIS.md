# Prérequis — P06 multicloud

## Jour 0 hors chronomètre

Le tronc commun de **4 h 30** se déroule sur votre poste. Il ne nécessite **aucun compte Azure, AWS ou Google Cloud** et ne déploie aucune ressource cloud. Il nécessite Internet pour télécharger les dépendances et les schémas des providers, ou un cache préparé par le formateur. La construction et les tests Docker sont bien des exécutions de conteneurs locaux.

Préparez Git, Bash, un JDK **17**, Maven **3.9.x**, Python **3.10 ou supérieur**, Docker Engine ou Docker Desktop avec **Compose 2**, et Terraform **1.7 à moins de 2**. Les définitions du corrigé ciblent **AzureRM 4**, **AWS 6** et **Google 7** ; leurs versions exactes figurent dans chaque lockfile. Un poste avec environ 8 Go de RAM disponibles et plusieurs Go de disque libre facilite les téléchargements et les builds. Linux, macOS et WSL2 conviennent avec un moteur Docker fonctionnel.

Vérifications génériques avant le départ :

```bash
java -version
javac -version
mvn --version
python3 --version
terraform version
docker version
docker compose version
```

`docker version` doit joindre le **serveur**, pas seulement afficher un client installé. Préchargez les images de base Maven/Temurin, les dépendances Maven et les providers avec le formateur. Si le poste interdit les sockets de plugins Terraform ou si Docker n’a pas de moteur, résolvez le problème avant la séance. Une analyse YAML ne remplace pas un test Docker, et un fichier HCL formaté ne remplace pas sa validation par les schémas.

Connaissances attendues : petites classes Java, tests automatisés, HTTP/JSON, variables d’environnement, images/conteneurs, bases Git et Bash. La syntaxe HCL et les identités applicatives sont introduites dans le mémo autonome du support ; aucune expérience préalable du cloud n’est exigée. Vous n’avez pas besoin d’un dépôt précédent. Vous construisez une API autonome pendant l’atelier.

## Extensions cloud, après le tronc commun

Une extension facultative prend **45 à 90 minutes pour un seul fournisseur**, après préparation des droits. Elle n’est pas comprise dans les 270 minutes et n’est pas nécessaire pour obtenir 20/20. Choisissez uniquement le cloud dont vous possédez déjà un environnement pédagogique autorisé ; ne créez pas trois comptes payants pour ce projet.

| Extension | Préparation requise |
|---|---|
| Azure | Azure CLI, abonnement de formation, groupe dédié existant, fournisseurs App/ContainerRegistry/ManagedIdentity/OperationalInsights enregistrés ; droits sur le groupe et attribution de `AcrPull` autorisée sur le registre du laboratoire |
| AWS | AWS CLI v2, identité de formation, région Fargate, quotas, permissions ECR/ECS/VPC/CloudWatch/IAM limitées aux ressources de l’exercice et `iam:PassRole` sur ses deux rôles |
| Google Cloud | gcloud, projet de formation facturable déjà créé, APIs Cloud Run/Artifact Registry/IAM déjà activées, droits de création du service et du registre, usage du compte de service et attribution d’invocation autorisés |

Azure Container Apps et ECS utilisent le /32 public du poste pour l’accès de test. Cloud Run reste authentifié par IAM. Aucun identifiant réel n’est nécessaire aux tests mock. Les adresses et empreintes de test dans les fichiers `.tftest.hcl` sont fictives et ne doivent pas être réutilisées pour un déploiement.

Préparez le budget, le périmètre des ressources et le créneau de destruction avant de lancer l’extension. Même une phase de registre sans service déployé peut coûter de l’argent. Les tarifs varient par région et usage ; utilisez les [calculateurs officiels](SOURCES.md).

## Fichiers du socle et de l’extension

Pour votre implémentation du socle, créez les trois racines `infra-contracts/azure`, `infra-contracts/aws` et `infra-contracts/gcp`. Chacune contient `versions.tf` (versions/provider), `variables.tf` (entrées), `main.tf` (contrat) et `tests/contract.tftest.hcl` (provider mock et assertions). Le lockfile provient de l’initialisation.

| Racine du socle | Ressources essentielles | Dépendances fournies comme entrées |
|---|---|---|
| Azure | Application Container Apps | Groupe, environnement, image, registre, identité de lecture et /32 |
| AWS | Définition de tâche et service ECS | Cluster, sous-réseau, groupe de sécurité, deux rôles, groupe de logs et image |
| Google | Service Cloud Run v2 et droit d’invocation | Projet, image, compte de service et membre autorisé |

Les répertoires `infra/azure`, `infra/aws` et `infra/gcp` de la variante corrigée contiennent le bootstrap complet des extensions. Ils ne sont pas à reproduire pendant le socle de 270 minutes. Les mémos, cette table et les références de schémas du support élève suffisent pour construire les contrats : l’accès au corrigé privé n’est pas un prérequis.
