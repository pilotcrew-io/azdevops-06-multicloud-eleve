# Mémos — ce qui se transporte et ce qui change

## Contrat HTTP et processus

Un conteneur contient le code et son runtime, mais pas une machine virtuelle complète. Le contrat utile ici tient à peu de décisions : un processus qui démarre sans terminal interactif, un port configurable, une écoute appropriée, des réponses HTTP déterministes, une sortie standard exploitable et un arrêt qui ne laisse pas des tâches en arrière-plan.

`EXPOSE` documente un port de l’image ; il ne publie pas ce port sur l’hôte. La publication Docker associe un port hôte à un port conteneur. Dans Compose, une adresse hôte loopback limite l’exposition locale ; dans le conteneur, écouter seulement sur loopback empêcherait généralement la plateforme d’atteindre l’application. [Dockerfile](https://docs.docker.com/reference/dockerfile/), [Compose services](https://docs.docker.com/reference/compose-file/services/).

La variable `PORT` est un contrat de configuration, pas une constante métier. Le service doit la valider. Cloud Run l’injecte lui-même ; sa configuration de port et la sonde doivent viser la même valeur. Une variable réservée ne doit pas être redéfinie comme une variable utilisateur. [Conteneurs Cloud Run](https://docs.cloud.google.com/run/docs/configuring/services/containers).

Une réponse déclarant « deployment: aws » peut être produite sur votre ordinateur : cette valeur vient de l’environnement. La preuve d’un cloud réel associe un identifiant de ressource, une configuration effective, une URL réelle et une observation de cet environnement.

## Image, tag et digest

Un tag est un nom de référence pratique. Selon la politique du registre, il peut être repointé vers un autre contenu. Un digest identifie le contenu du manifeste d’image. Conservez le tag pour lire facilement une livraison et le digest pour déployer un contenu précis.

Un Dockerfile en plusieurs étapes sépare la construction de l’exécution. La dernière étape peut être plus petite et moins riche en outils. Cela ne constitue pas une preuve de sécurité : il faut encore maintenir les images de base, analyser les dépendances et respecter la politique de l’organisation. Le support fixe Java 17 et des images de base explicites ; les tags de base peuvent néanmoins évoluer, ce qui doit être reconnu dans les preuves de reproductibilité.

L’architecture de l’image fait partie du contrat. Le corrigé cloud vise Linux x86-64 ; une construction native ARM peut ne pas convenir à cette définition Fargate. Sur un poste ARM, la construction pour `linux/amd64` demande une capacité de construction ou d’émulation adaptée. Le succès d’un test local ARM n’atteste pas une exécution x86-64.

## Santé et ressources

Une sonde de démarrage décide quand le processus est suffisamment initialisé. Une sonde de disponibilité renseigne l’aptitude à recevoir du trafic. Une sonde de vie peut déclencher un redémarrage. Les paramètres ne s’appellent pas toujours de la même façon entre plateformes ; lire une sonde comme si tous les services partageaient exactement la sémantique Kubernetes serait une erreur.

Le healthcheck Docker est un état observé du conteneur. Compose peut attendre cet état avant de poursuivre. Dans ECS, la définition de tâche doit préciser la sonde pour qu’ECS l’utilise ; la simple présence d’un healthcheck dans l’image ne suffit pas à exprimer toute la politique du service. [Santé ECS](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/healthcheck.html).

Une limite de mémoire s’applique au processus complet, pas uniquement au tas Java. La JVM utilise aussi des threads, des buffers, des bibliothèques et des structures natives. Une seule requête passée ne démontre ni capacité de charge ni stabilité prolongée. Le laboratoire borne la mémoire et les instances pour réduire son empreinte, sans fournir de dimensionnement de production.

## Les identités ne sont pas interchangeables

L’**identité de déploiement** modifie les ressources. L’**identité qui lit l’image** permet au runtime de récupérer l’artefact. L’**identité d’exécution** autorise les appels du code à d’autres services. L’**identité du client** détermine qui peut appeler l’API. Une même technologie d’identité peut intervenir dans plusieurs rôles sans rendre leurs permissions identiques.

Azure Container Apps peut utiliser une identité managée pour lire ACR, avec un droit de lecture sur le registre concerné. AWS distingue explicitement le rôle d’exécution de la tâche et le rôle disponible au code. Cloud Run possède une identité de service et un contrôle IAM d’invocation. [Azure](https://learn.microsoft.com/en-us/azure/container-apps/managed-identity-image-pull), [AWS](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/security-ecs-iam-role-overview.html), [Google](https://docs.cloud.google.com/run/docs/securing/service-identity).

Le code de ce projet n’appelle aucune API cloud. Il n’a donc pas besoin de droits métier de stockage, de publication de messages ou d’administration. Le rôle de lecture du registre ne doit pas devenir un raccourci vers un rôle administrateur. Les droits humains nécessaires à la création de rôles font partie du Jour 0 de l’extension, pas du contrat Java.

## Réseau et authentification

Une règle réseau choisit les connexions qui peuvent atteindre un service. Une règle IAM choisit les identités qui peuvent agir. TLS protège le transport et identifie le serveur ; il ne remplace ni l’une ni l’autre. Pour comparer les solutions, décrivez les trois couches au lieu de n’écrire qu’« accès sécurisé ».

Dans le corrigé optionnel, Azure fournit une entrée HTTPS avec restriction IP. AWS utilise une IP publique temporaire sur la tâche et un groupe de sécurité limité au /32 de l’élève, sans équilibreur ni certificat TLS. Cette dernière simplification est acceptable uniquement pour cette API fictive de laboratoire ; la rendre publiquement exploitable demanderait une vraie entrée HTTPS et une politique d’accès. Cloud Run fournit une URL HTTPS et conserve l’invocation IAM authentifiée.

Le trafic sortant est également nécessaire : une tâche privée sans route ou service de sortie ne peut pas tirer une image ni envoyer ses logs. Éviter un NAT payant dans le laboratoire change le schéma réseau ; cela ne rend pas l’exemple universel. [Réseau Fargate](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/fargate-task-networking.html).

## Ce que prouve Terraform

Le formatage vérifie une syntaxe présentable. L’initialisation choisit/télécharge les providers et prépare la racine. La validation vérifie la cohérence de la configuration avec les schémas disponibles. Un plan réel interroge les APIs et propose des changements. L’application réelle exécute ces changements.

Les tests mock utilisent les vrais types déclarés et des résultats simulés, ce qui permet d’évaluer une expression, un port, une portée ou une condition sans compte cloud. Ils ne valident pas un quota, une disponibilité régionale, l’existence d’une image, les droits d’un compte réel ni l’arrivée d’un paquet réseau. Une configuration formatée mais dont le provider ne démarre pas n’a pas passé sa validation de schéma. [Validation](https://developer.hashicorp.com/terraform/cli/commands/validate), [mocks](https://developer.hashicorp.com/terraform/language/tests/mocking).

Un lockfile conserve le provider sélectionné et ses empreintes. L’état Terraform conserve les ressources gérées et parfois des valeurs sensibles. Le premier va dans Git ; le second reste dans un stockage d’état approprié, ignoré dans ce laboratoire local. Trois racines permettent trois périmètres de destruction indépendants.

## Coûts et limites de portabilité

Le passage à zéro instance peut réduire certaines dépenses de calcul ; il ne supprime pas le stockage d’images, les journaux, l’egress, les adresses publiques ou tous les coûts annexes. Un maximum d’instances borné ne constitue pas une limite de facture. Une phase de bootstrap sans service crée déjà des ressources facturables.

La portabilité du binaire est donc plus simple que celle de l’exploitation. Changer de fournisseur demande encore d’adapter le registre, les identités, l’entrée, les logs, les alertes et les procédures de retour arrière. Un système qui survit à la perte complète d’un cloud demanderait en plus des données cohérentes, une stratégie de routage et des exercices de reprise. Aucun test de ce projet ne prétend les couvrir.

## Prise en main autonome de HCL

HCL décrit un état voulu par blocs et attributs. Un bloc `resource` représente un objet à gérer ; un bloc `data` lit un objet déjà existant. Une `variable` déclare une entrée typée et un `output` rend une valeur lisible. Le bloc `provider` configure le connecteur ; le bloc `terraform` fixe les versions requises. Une référence relie un attribut d’un objet à un autre et établit sa dépendance.

Lisez un fichier de l’extérieur vers l’intérieur : type de ressource, nom local, attributs obligatoires, blocs imbriqués, puis références. Le nom local sert au code ; il n’est pas toujours le nom publié dans le cloud. Un bloc comportant plusieurs attributs se présente sur plusieurs lignes. Une liste ordonnée, un ensemble et une map n’ont pas les mêmes règles de comparaison ; vos assertions doivent comparer la propriété utile, pas une présentation de texte.

Commencez avec un seul contrat et ses entrées. Consultez la page officielle de sa ressource pour identifier les champs requis. Ajoutez une propriété à la fois, formatez, puis validez. Les variables fictives d’un test mock donnent des valeurs aux entrées sans créer leurs dépendances ; elles ne représentent pas un environnement existant.
