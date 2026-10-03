# Évaluation du tronc commun — 20 points

Les 20 points sont obtenables **sans compte cloud**. Les déploiements réels sont des extensions hors barème. Une validation mock doit être identifiée comme telle ; elle ne remplace pas la preuve d’exécution du conteneur local.

| Compétence | Points | Preuve attendue |
|---|---:|---|
| API portable et tests | 4 | JAR autonome, port/configuration validés, contrat HTTP et tests exécutés |
| Image et exécution Docker/Compose | 5 | Build réel, utilisateur non privilégié, santé, smoke HTTP et nettoyage ciblé |
| Trois contrats Terraform | 5 | Azure/AWS/GCP, ports et sondes cohérents, contraintes majeures et validations de schéma |
| Tests mock et portée des preuves | 3 | Assertions pertinentes, sans identifiants, distinction configuration/simulation/cloud réel |
| Comparaison et décision | 2 | IAM/réseau/coûts comparés, limites et choix argumenté |
| Traçabilité et remise | 1 | Versions, preuves, nettoyage et étapes non réalisées explicités |
| **Total** | **20** | |

Pour les trois contrats, les dépendances réseau/registre/identité peuvent être des variables dans le tronc commun. La création complète d’un VPC AWS ou d’un registre n’est pas une exigence cachée du barème. Les options du corrigé rendent ces extensions possibles pour ceux qui en ont l’autorisation.

## Questions de soutenance

1. Comment prouvez-vous que le test a atteint un conteneur réel ?
2. Qu’est-ce qui pourrait réussir en mock mais échouer au premier déploiement ?
3. Pourquoi séparer identité d’exécution et droit d’invoquer le service ?
4. Quelle différence faites-vous entre port hôte, port conteneur et entrée HTTPS ?
5. Quels coûts peuvent rester quand le nombre d’instances est nul ?
6. Pourquoi ce projet n’est-il pas une démonstration de bascule automatique entre clouds ?
