# P06 — Portabilité multicloud d’une API Java

**Atelier français · tronc commun de 4 h 30 · variante eleve.**

Ce dépôt élève contient uniquement les tutoriels, mémos, critères et modèles vierges. Vous écrivez votre API, votre image et vos contrats de déploiement.

Le tronc commun exige un **vrai test Docker local** et la vérification des configurations Azure Container Apps, AWS ECS Fargate et Google Cloud Run. Il ne requiert **aucun compte cloud**. Les trois déploiements réels possibles sont documentés comme des extensions séparées ; il suffit d’en choisir un si vous disposez déjà du périmètre et du budget autorisés.

## Commencer

1. Terminez les [prérequis du Jour 0](docs/PREREQUIS.md).
2. Suivez le [parcours de 270 minutes](docs/ATELIER.md).
3. Appuyez-vous sur les [mémos](docs/MEMOS.md) et le [dépannage](docs/DEPANNAGE.md).
4. Remplissez le [compte rendu](livrables/COMPTE_RENDU.md) et la [matrice IAM/réseau](livrables/MATRICE_IAM_RESEAU.md).
5. Vérifiez les [critères sur 20 points](docs/EVALUATION.md) et les [sources officielles](docs/SOURCES.md).

Les preuves sont distinguées explicitement : tests Java, JAR local, conteneur local, schéma Terraform, test mock et déploiement cloud réel. Un label dans le JSON n’est pas une preuve d’hébergement. Ce laboratoire ne démontre ni bascule entre fournisseurs ni architecture de production.

## Distribution

Dépôt cible : `pilotcrew-io/azdevops-06-multicloud-eleve`. Visibilité prévue : **public**. Aucun déploiement cloud ne se déclenche automatiquement. Consultez [SECURITY.md](SECURITY.md) avant de préparer une extension et gardez le nettoyage limité à votre propre état.

© 2026 Curtys ACCIPE · [Licence MIT](LICENSE).

## Parcours GitHub et remise

Lire [CONTRIBUTING.md](CONTRIBUTING.md) pour le fork, la branche, la pull request et les preuves attendues.
