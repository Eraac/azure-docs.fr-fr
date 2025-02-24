---
title: Configurer l'intégration des rapports sur les coûts et l'utilisation AWS avec Azure Cost Management
description: Cet article vous guide tout au long de la configuration de l'intégration des rapports de coûts et d'utilisation AWS avec Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 951178a82e0975f5f2af71bd48cf0f931246ae37
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66002121"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>Configurer l'intégration des rapports sur les coûts et l'utilisation AWS

Grâce à l'intégration du rapport de coût et d'utilisation (CUR) d'Amazon Web Services (AWS), vous pouvez surveiller et contrôler vos dépenses AWS dans Azure Cost Management. L'intégration offre un emplacement unique dans le portail Azure où vous pouvez surveiller et contrôler les dépenses pour Azure et AWS. Cet article explique comment configurer l'intégration et l'utiliser pour analyser les coûts et revoir les budgets à l'aide des fonctionnalités Azure Cost Management.

Cost Management traite le rapport sur les coûts et l'utilisation AWS stocké dans un compartiment S3 en utilisant vos identifiants d'accès à AWS pour obtenir les définitions des rapports et télécharger des fichiers CSV de rapport au format GZIP.

## <a name="create-a-cost-and-usage-report-in-aws"></a>Créer un rapport sur les coûts et l’utilisation dans AWS

L'utilisation d'un rapport sur les coûts et l'utilisation est la méthode recommandée par AWS pour recueillir et traiter les coûts AWS. Pour plus d'informations, consultez la documentation [Rapport sur les coûts et l’utilisation AWS](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html).

Utilisez la page **Cost & Usage Reports** (Rapports sur les coûts et l'utilisation) de la console Facturation et gestion des coûts d’AWS pour créer un rapport sur les coûts et l'utilisation en procédant comme suit :

1. Connectez-vous à la console de gestion AWS et ouvrez la [console Billing and Cost Management](https://console.aws.amazon.com/billing) (Facturation et gestion des coûts).
2. Dans le volet de navigation, sélectionnez **Cost & Usage Reports** (Rapports sur les coûts et l'utilisation).
3. Sélectionnez **Create report** (Créer un rapport).
4. Pour **Report name** (Nom du rapport), entrez un nom pour votre rapport.
5. Pour **Additional report details** (Détails supplémentaires du rapport), spécifiez les ID de chaque ressource dans le rapport et sélectionnez **Include Resource IDs** (Inclure les ID de ressource).
6. Pour **Data refresh settings** (Paramètres d’actualisation des données), indiquez si vous voulez actualiser le rapport sur les coûts et l’utilisation AWS si AWS applique des remboursements, des crédits ou des frais de support à votre compte une fois votre facture finalisée. À chaque actualisation d’un rapport, un nouveau rapport est chargé sur Amazon S3. Il est recommandé de laisser ce réglage activé.
7. Sélectionnez **Suivant**.
8. Pour **S3 bucket** (Compartiment S3), choisissez **Configure** (Configurer).
9. Dans la boîte de dialogue Configure S3 Bucket (Configuration du compartiment S3), effectuez l'une des opérations suivantes :
    1. Sélectionnez un compartiment existant dans la liste déroulante puis choisissez **Next** (Suivant).
    2. Entrez un nom de compartiment et la région où vous voulez créer un compartiment, puis choisissez **Next** (Suivant).
10. Sélectionnez I have confirmed that this policy is correct (Je confirme que cette stratégie est correcte), puis choisissez Save (Enregistrer).
11. (Facultatif) Pour le préfixe Report path (Chemin du rapport), entrez le préfixe du chemin du rapport que vous voulez ajouter au nom de votre rapport.
Si vous ne spécifiez aucun préfixe, le préfixe par défaut correspond au nom que vous avez spécifié pour le rapport à l'étape 4 et la plage de dates du rapport, au format suivant : `/report-name/date-range/`
12. Pour **Unité de temps**, choisissez **Toutes les heures**.
13. Pour **Report versioning** (Gestion des versions du rapport), choisissez si vous voulez que chaque version du rapport écrase la version précédente ou soit fournie en complément des versions précédentes.
14. Pour **Enable data integration for** (Activer l'intégration des données pour), aucune sélection n'est nécessaire.
15. Pour **Compression**, sélectionnez **GZIP**.
16. Sélectionnez **Suivant**.
17. Après avoir vérifié les paramètres de votre rapport, sélectionnez **Review and Complete** (Vérifier et finaliser).

    Notez le nom du rapport. Vous l’utiliserez plus tard.

Il peut s'écouler jusqu'à 24 heures avant qu'AWS commence à distribuer des rapports à votre compartiment Amazon S3. Après chaque distribution, AWS met à jour au moins une fois par jour les fichiers du rapport sur les coûts et l'utilisation AWS. Vous pouvez continuer à configurer votre environnement AWS sans attendre le début de la distribution.

## <a name="create-a-role-and-policy-in-aws"></a>Créer un rôle et une stratégie dans AWS

Azure Cost Management accède plusieurs fois par jour au compartiment S3 où se trouve le rapport sur les coûts et l'utilisation. Le service a besoin d'accéder aux informations d'identification pour vérifier la présence de nouvelles données. Vous créez un rôle et une stratégie dans AWS pour permettre à Cost Management d'y accéder.

Pour activer l'accès en fonction du rôle à un compte AWS dans Cost Management, le rôle est créé dans la console AWS. Vous devez obtenir un _rôle ARN_ et un _identifiant externe_  de la console AWS. Vous utiliserez ces informations sur la page **Créer un connecteur AWS** dans Cost Management.

Utilisez l'Assistant Créer un rôle :

1. Connectez-vous à votre console AWS, puis sélectionnez **Services** (Services).
2. Dans la liste des services, sélectionnez **IAM**.
3. Sélectionnez **Rôles**, puis sélectionnez **Créer un rôle**.
4. Sur la page suivante, **Another AWS account** (Autre compte AWS).
5. Dans **ID de compte**, entrez **432263259397**.
6. Dans **Options**, sélectionnez **Require external ID (Best practice when a third party will assume this role)** (Nécessite un ID externe (il est recommandé qu’un tiers occupe ce rôle)).
7. Dans **ID externe**, entrez l’ID externe. L’ID externe est un code secret partagé entre le rôle AWS et Azure Cost Management. Le même ID externe est également utilisé sur la page **Nouveau connecteur** dans Cost Management. Par exemple, un ID externe ressemble à _Companyname1234567890123_.

    > [!NOTE]
    > Ne modifiez pas la sélection pour **Exiger une authentification multifacteur**. Elle doit rester désactivée.
8. Sélectionnez **Suivant : Autorisations**.
9. Sélectionnez **Créer une stratégie**. Un nouvel onglet de navigateur s'ouvre. C’est ici que vous créez une stratégie.
10. Sélectionnez **Choisir un service**.

Configurez les autorisations pour le rapport sur les coûts et l’utilisation :

1. Entrez **Rapport sur les coûts et l’utilisation**.
2. Sélectionnez **Niveau d’accès** > **Lecture** > **DescribeReportDefinitions**. Cette étape permet à Cost Management de lire les rapports CUR définis et de déterminer s’ils correspondent aux exigences de définition de rapport.
3. Sélectionnez **Add additional permissions** (Ajouter des autorisations supplémentaires).

Configurez les autorisations pour votre compartiment S3 et les objets :

1. Sélectionnez **Choisir un service**.
2. Entrez **S3**.
3. Sélectionnez **Niveau d’accès** > **Liste** > **ListBucket**. Cette action obtient la liste des objets dans le compartiment S3.
4. Sélectionnez **Niveau d’accès** > **Lecture** > **GetObject**. Cette action permet de télécharger des fichiers de facturation.
5. Sélectionnez **Resources** (Ressources).
6. Sélectionnez **compartiment – Ajouter ARN**.
7. Dans **Bucket name** (Nom du compartiment), entrez le compartiment utilisé pour stocker les fichiers CUR.
8. Sélectionnez **objet – Ajouter ARN**.
9. Dans **Bucket name** (Nom du compartiment), entrez le compartiment utilisé pour stocker les fichiers CUR.
10. Dans **Object name** (Nom de l’objet), sélectionnez **Any** (N’importe lequel).
11. Sélectionnez **Add additional permissions** (Ajouter des autorisations supplémentaires).

Configurez les autorisations pour Cost Explorer :

1. Sélectionnez **Choisir un service**.
2. Entrez **Service Cost Explorer**.
3. Sélectionnez **All Cost Explorer Service actions (ce:\*)** (Toutes les actions du service Cost Explorer). Cette action valide la collection.
4. Sélectionnez **Add additional permissions** (Ajouter des autorisations supplémentaires).

Ajoutez l’autorisation pour les organisations AWS :

1. Entrez **Organizations** (Organisations).
2. Sélectionnez **Niveau d’accès** > **Liste** > **ListAccounts**. Cette action obtient les noms des comptes.
3. Dans **Review Policy** (Stratégie de vérification), entrez le nom de la nouvelle stratégie. Vérifiez que vous avez entré les informations correctes, puis sélectionnez **Créer une stratégie**.
4. Revenez à l’onglet précédent et actualisez la page web de votre navigateur. Sur la barre de recherche, recherchez votre nouvelle stratégie.
5. Sélectionnez **Next:Review**.
6. Entrez un nom pour le nouveau rôle. Vérifiez que vous avez entré les informations correctes, puis sélectionnez **Créer un rôle**.

    Notez le rôle ARN et l’ID externe utilisés dans les étapes précédentes lorsque vous avez créé le rôle. Vous allez les utiliser ultérieurement pour configurer le connecteur Azure Cost Management.

L’élément JSON de la stratégie doit ressembler à l’exemple suivant. Remplacez _bucketname_ par le nom de votre compartiment S3.

```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
"organizations:ListAccounts",
            "ce:*",
            "cur:DescribeReportDefinitions"
            ],
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::bucketname",
                "arn:aws:s3:::bucketname/*"
            ]
        }
    ]
}
```

## <a name="set-up-a-new-aws-connector-in-azure"></a>Configurer un nouveau connecteur AWS dans Azure

Utilisez les informations suivantes pour créer un connecteur AWS et démarrer la surveillance de vos coûts AWS :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Accédez à **Gestion des coûts + facturation** > **Gestion des coûts**.
3. Sous **Paramètres**, sélectionnez **Connecteurs cloud (préversion)** .  
    ![Exemple montrant le paramètre Connecteurs Cloud (préversion)](./media/aws-integration-setup-configure/cloud-connectors-preview01.png).
4. Sélectionnez **+Ajouter** en haut de la page pour créer un connecteur.
5. Sur la page **Créer un connecteur AWS**, dans **Nom d’affichage**, entrez un nom pour votre connecteur.  
    ![Exemple de page pour la création d’un connecteur AWS](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. Vous pouvez également sélectionner le groupe d'administration par défaut. Il stockera tous les comptes liés découverts. Vous pouvez configurer ce paramètre ultérieurement.
7. Dans la section **Facturation**, sélectionnez **Facturer automatiquement le 1 % au niveau de la disponibilité générale** si vous souhaitez garantir un fonctionnement continu lorsque la préversion arrive à expiration. Si vous sélectionnez l’option automatique, vous devez sélectionner un abonnement de facturation.
8. Pour **Role ARN** (Rôle ARN), entrez la valeur que vous avez utilisée pour configurer le rôle dans AWS.
9. Pour **External ID** (ID externe), entrez la valeur que vous avez utilisée pour configurer le rôle dans AWS.
10. Pour **Nom du rapport**, entrez le nom que vous avez créé dans AWS.
11. Sélectionnez **Suivant**, puis **Créer**.

Il faudra peut-être quelques heures avant qu’apparaissent les nouvelles étendues AWS, le compte AWS consolidé, les comptes AWS liés et leurs données de coûts.

Après avoir créé le connecteur, nous vous recommandons de lui attribuer un contrôle d'accès. Les utilisateurs se voient attribuer des autorisations pour les nouvelles étendues découvertes : Compte AWS consolidé et comptes AWS liés. L'utilisateur qui crée le connecteur est le propriétaire du connecteur, du compte consolidé et de tous les comptes liés.

Attribuez des autorisations de connecteur aux utilisateurs si la découverte n'attribue aucune autorisation aux étendues AWS existantes. Au lieu de cela, des autorisations sont attribuées uniquement aux nouveaux comptes liés.

## <a name="take-additional-steps"></a>Effectuer des étapes supplémentaires

- Le cas échéant, [configurez des groupes d’administration](../governance/management-groups/index.md#initial-setup-of-management-groups).
- Vérifiez que les nouvelles étendues sont ajoutées à votre sélecteur d’étendue. Sélectionnez **Actualiser** pour afficher les données les plus récentes.
- Sur la page **Connecteurs cloud**, sélectionnez votre connecteur, puis choisissez **Accéder au compte de facturation** pour attribuer le compte lié à des groupes d’administration.

## <a name="manage-cloud-connectors"></a>Gérer les connecteurs cloud

Lorsque vous sélectionnez un connecteur sur la page **Connecteurs cloud**, vous pouvez :

- Sélectionner **Accéder au compte de facturation** pour afficher les informations relatives au compte AWS consolidé.
- Sélectionner **Contrôle d'accès** pour gérer l'attribution de rôle pour le connecteur.
- Sélectionner **Modifier** pour mettre à jour le connecteur. Vous ne pouvez pas modifier le numéro de compte AWS car il apparaît dans le rôle ARN. Mais vous pouvez créer un connecteur.
- Sélectionnez **Vérifier** pour réexécuter le test de vérification afin de vous assurer que Cost Management peut collecter des données en utilisant les paramètres du connecteur.

![Exemple de liste de connecteurs AWS créés](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="set-up-azure-management-groups"></a>Configurer de groupes d’administration Azure

Afin de créer un endroit unique pour afficher les informations des fournisseurs de services inter-cloud, vous devez placer vos abonnements Azure et vos comptes AWS liés dans le même groupe d'administration. Si vous n’avez pas déjà configuré votre environnement Azure avec des groupes d’administration, veuillez consulter la section [Configuration initiale des groupes d’administration](../governance/management-groups/index.md#initial-setup-of-management-groups).

Si vous voulez séparer les coûts, vous pouvez créer un groupe d’administration ne contenant que des comptes AWS liés.

## <a name="set-up-an-aws-consolidated-account"></a>Configurer un compte AWS consolidé

Le compte AWS consolidé combine la facturation et le paiement pour plusieurs comptes AWS. Il sert également de compte AWS lié.

![Exemple de détail d'un compte AWS consolidé](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

À partir de cette page, vous pouvez :

- Sélectionner **Mettre à jour** pour mettre à jour en bloc l'association de comptes AWS liés avec un groupe d'administration.
- Sélectionner **Contrôle d'accès** afin de définir l'attribution de rôle pour l’étendue.

### <a name="permissions-for-an-aws-consolidated-account"></a>Autorisations d'un compte AWS consolidé

Par défaut, les autorisations d’un compte AWS consolidé sont définies lors de la création du compte, selon les autorisations du connecteur AWS. Le créateur du connecteur est le propriétaire.

Vous gérez le niveau d'accès en utilisant la page **Niveau d'accès** du compte AWS consolidé. Cependant, les comptes AWS liés n'héritent pas des droits d'accès au compte AWS consolidé.

## <a name="set-up-an-aws-linked-account"></a>Configurer un compte AWS lié

les ressources AWS sont créées et gérées dans le compte AWS lié. Un compte lié agit également comme une limite de sécurité.

À partir de cette page, vous pouvez :

- Sélectionner **Mettre à jour** pour mettre à jour l'association d’un compte AWS lié avec un groupe d'administration.
- Sélectionner **Contrôle d'accès** afin de définir une attribution de rôle pour l’étendue.

![Exemple de page de compte AWS lié](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="permissions-for-an-aws-linked-account"></a>Autorisations d'un compte AWS lié

Par défaut, les autorisations d’un compte AWS lié sont définies lors de la sa création, selon les autorisations du connecteur AWS. Le créateur du connecteur est le propriétaire. Vous gérez le niveau d'accès en utilisant la page **Niveau d'accès** du compte AWS lié. Les comptes AWS liés n'héritent pas des droits d'accès d’un compte AWS consolidé.

Les comptes AWS liés à AWS héritent toujours des autorisations du groupe d’administration auquel ils appartiennent.

## <a name="next-steps"></a>Étapes suivantes

- Maintenant que vous avez configuré l'intégration du rapport sur les coûts et l'utilisation AWS, passez à la section [Gérer les coûts et l'utilisation AWS](aws-integration-manage.md).
- Si vous n’êtes pas familiarisé avec l’analyse des coûts, consultez le guide de démarrage rapide [Explorer et analyser les coûts avec l’analyse du coût](quick-acm-cost-analysis.md).
- Si vous n’êtes pas familiarisé avec les budgets dans Azure, consultez [Créer et gérer des budgets Azure](tutorial-acm-create-budgets.md).
