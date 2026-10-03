# Sources officielles — P06

**Consultées le 3 octobre 2026.** Les contraintes du corrigé retiennent AzureRM 4, AWS 6 et Google 7 ; les lockfiles enregistrent les versions effectivement téléchargées. Les extensions requièrent une nouvelle vérification des quotas, des régions et des politiques au Jour 0.

| Référence | Usage |
|---|---|
| [Java 17 HttpServer](https://docs.oracle.com/en/java/javase/17/docs/api/jdk.httpserver/com/sun/net/httpserver/HttpServer.html) | Contrat HTTP sans framework |
| [Maven lifecycle](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html) | Compilation, tests et JAR |
| [Dockerfile reference](https://docs.docker.com/reference/dockerfile/) | Multi-stage, USER, ENTRYPOINT et HEALTHCHECK |
| [Compose services](https://docs.docker.com/reference/compose-file/services/) | Publication, santé et limites locales |
| [Terraform validate](https://developer.hashicorp.com/terraform/cli/commands/validate) | Vérification de configuration |
| [Terraform mocking](https://developer.hashicorp.com/terraform/language/tests/mocking) | Tests sans appels cloud |
| [AzureRM Container App](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/container_app) | Schéma de l’application et sondes |
| [Azure image pull par identité](https://learn.microsoft.com/en-us/azure/container-apps/managed-identity-image-pull) | Lecture ACR sans mot de passe de registre |
| [Azure ingress](https://learn.microsoft.com/en-us/azure/container-apps/ingress-overview) | Entrée HTTPS et restrictions |
| [AWS ECS service](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/ecs_service) | Service Fargate et awsvpc |
| [AWS ECS task definition](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/ecs_task_definition) | CPU/mémoire, image et définition du conteneur |
| [AWS rôles ECS](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/security-ecs-iam-role-overview.html) | Rôle d’exécution et rôle de tâche |
| [AWS santé du conteneur](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/healthcheck.html) | Commande de sonde et codes de sortie |
| [AWS réseau Fargate](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/fargate-task-networking.html) | ENI, image et accès réseau |
| [AWS App Runner — annonce dans la référence API](https://docs.aws.amazon.com/apprunner/latest/api/API_CreateConnection.html) | Fermeture aux nouveaux clients depuis le 31 mars 2026 ; choix de Fargate |
| [Google Cloud Run v2](https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/cloud_run_v2_service) | Service, sonde et limite d’instances |
| [Google configuration du conteneur](https://docs.cloud.google.com/run/docs/configuring/services/containers) | PORT injecté par la plateforme |
| [Google identité du service](https://docs.cloud.google.com/run/docs/securing/service-identity) | Identité du code et du service |
| [Google invocation authentifiée](https://docs.cloud.google.com/run/docs/authenticating/developers) | Test d’un service conservant son contrôle IAM |
| [Google ingress](https://docs.cloud.google.com/run/docs/securing/ingress) | Entrée réseau distincte de l’autorisation |
| [Calculateur Azure](https://azure.microsoft.com/en-us/pricing/calculator/), [AWS](https://calculator.aws/), [Google](https://cloud.google.com/products/calculator) | Estimations à préparer ; aucun prix garanti dans le support |

Aucun benchmark cloud, prix mensuel calculé ou taux de disponibilité réel n’est affirmé par le projet. Une réussite du tronc commun prouve le contrat local et les configurations contrôlées ; les preuves d’une extension doivent venir de son exécution réelle.
