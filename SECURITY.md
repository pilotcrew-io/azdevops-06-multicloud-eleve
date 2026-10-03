# Sécurité du laboratoire

Ce dépôt est un support de formation. Il ne prétend pas constituer une architecture de production. Travaillez avec des données fictives et des ressources dédiées à votre atelier.

## Identités et fichiers

Utilisez les identités et la portée préparées au Jour 0. Un dépôt de code, même privé, n’est pas un coffre de secrets. Ne commettez ni clés, ni jetons, ni profils de publication, ni fichiers d’identifiants. Les états Terraform et plans sauvegardés peuvent contenir des valeurs sensibles ; gardez-les hors Git, avec les droits locaux appropriés. Le fichier `.terraform.lock.hcl` doit au contraire être conservé : il verrouille les versions et empreintes des fournisseurs.

Avant un commit, examinez les fichiers réellement ajoutés. Masquez les identifiants personnels dans les captures et les preuves partagées. Les journaux doivent contenir des informations opérationnelles utiles, sans corps de requête, cookie, en-tête d’autorisation ou chaîne de connexion.

## Ressources et nettoyage

Chaque participant possède son propre préfixe et son propre état. N’importez pas une ressource existante inconnue dans cet état. Lisez un plan de destruction avant de l’exécuter ; il doit viser uniquement les objets créés pour cet atelier. Ne supprimez jamais un abonnement, un compte cloud, un projet partagé, toutes les ressources d’un groupe ou un ensemble de ressources sélectionné par un nom générique.

Une limite de réplicas, un quota d’ingestion ou un budget d’alerte n’est pas une garantie de gratuité. L’arrêt d’une application ne supprime pas nécessairement les frais de son plan, de son registre ou de ses journaux. Vérifiez la disparition des ressources propres à l’exercice et la fin des opérations de suppression.

## Signalement

Si vous trouvez un secret dans l’historique, cessez de le diffuser, faites-le révoquer et suivez la procédure de l’organisation. Pour un défaut de sécurité du support, contactez directement votre formateur par le canal privé habituel ; ne publiez pas de secret dans une issue. Les commandes de déploiement restent des actions manuelles réalisées avec votre identité : aucun déploiement cloud automatique n’est déclenché par une pull request dans ce support.
