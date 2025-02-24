---
title: Guide pour un exemple d’application mutualisée SQL Database - SaaS Wingtip | Microsoft Docs
description: Fournit des instructions et des étapes pour installer et exécuter l’exemple d’application multilocataire qui utilise Azure SQL Database, l’exemple Wingtip Tickets SaaS.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 758cb47760f4a15e262a4d682089ac7d9fee64e8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60326276"
---
# <a name="general-guidance-for-working-with-wingtip-tickets-sample-saas-apps"></a>Conseils généraux pour utiliser des exemples d'applications Wingtip Tickets SaaS

Cet article contient des conseils généraux sur l'exécution d'exemples d'applications Wingtip Tickets SaaS utilisant Azure SQL Database. 

## <a name="download-and-unblock-the-wingtip-tickets-saas-scripts"></a>Télécharger et débloquer les scripts Wingtip Tickets SaaS

Le contenu exécutable (scripts, DLL) peut être bloqué par Windows lorsque des fichiers zip sont téléchargés à partir d’une source externe puis extraits. Lorsque vous extrayez les scripts d’un fichier zip, **suivez les étapes ci-dessous pour débloquer le fichier .zip avant l’extraction**. Cela garantit que les scripts sont autorisés à s’exécuter.

1. Accédez au référentiel GitHub Wingtip Tickets SaaS pour connaître le modèle de client de base de données à explorer : 
    - [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
    - [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)
    - [WingtipTicketsSaaS-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. Cliquez sur **Cloner ou télécharger**.
3. Cliquez sur **Télécharger ZIP** et enregistrez le fichier.
4. Cliquez avec le bouton droit sur le fichier zip et sélectionnez **Propriétés**. Le nom du fichier zip représentera le nom du référentiel. (ex. _WingtipTicketsSaaS-DbPerTenant-master.zip_)
5. Sous l’onglet **Général**, sélectionnez **Débloquer**.
6. Cliquez sur **OK**.
7. Procédez à l’extraction des fichiers.

Les scripts se trouvent dans le dossier *..\\Learning Modules*.


## <a name="working-with-the-wingtip-tickets-powershell-scripts"></a>Utilisation des scripts PowerShell de Wingtip Tickets

Pour tirer le meilleur parti de l’exemple, vous devez approfondir les scripts fournis. Utilisez des points d’arrêt et parcourez les scripts pour examiner les détails de l’implémentation des différents motifs SaaS. Pour parcourir facilement les scripts et les modules fournis afin d’acquérir une compréhension optimale de ceux-ci, nous vous recommandons d’utiliser [PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>Mettre à jour le fichier de configuration pour votre déploiement

Modifiez le fichier **UserConfig.psm1** avec le groupe de ressources et la valeur utilisateur que vous avez définis durant le déploiement :

1. Ouvrez le *PowerShell ISE* puis chargez...\\Learning Modules\\*UserConfig.psm1*. 
2. Mettez à jour *ResourceGroupName* et *Name* avec les valeurs spécifique de votre déploiement (sur les lignes 10 et 11 uniquement).
3. N’oubliez pas d’enregistrer les modifications.

La définition de ces valeurs ici vous évite simplement d’avoir à mettre à jour ces valeurs spécifiques du déploiement dans chaque script.

### <a name="execute-the-scripts-by-pressing-f5"></a>Exécuter les scripts en appuyant sur F5

Plusieurs scripts utilisent *$PSScriptRoot* pour parcourir les dossiers, et *$PSScriptRoot* est évalué uniquement lors de l’exécution de scripts en appuyant sur **F5**.  La mise en surbrillance et l’exécution d’une sélection (**F8**) pouvant entraîner des erreurs, nous vous conseillons d’appuyer sur **F5** lors de l’exécution des scripts.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Parcourir les scripts pour examiner l’implémentation

La meilleure façon de comprendre les scripts consiste à les parcourir pour voir ce qu’ils font. Extrayez les scripts **Demo-** inclus, qui présentent un flux de travail de haut niveau facile pour suivre. Les scripts **Demo-** présentent les étapes nécessaires à l’accomplissement de chaque tâche. Par conséquent, définissez des points d’arrêt, puis approfondissez les appels individuels pour voir les détails d’implémentation des différents modèles SaaS.

Conseils pour l’exploration et le parcours des scripts PowerShell :

- Ouvrez les scripts **Demo-** dans le PowerShell ISE.
- Exécutez ou continuez à exécuter le script à l’aide de la touche **F5** (l’utilisation de la touche **F8** n’est pas conseillée, car *$PSScriptRoot* n’est pas évalué lors de l’exécution des sélections d’un script).
- Placez des points d’arrêt en cliquant ou en sélectionnant une ligne et en appuyant sur **F9**.
- Survolez l’appel d’une fonction ou d’un script à l’aide de la touche **F10**.
- Parcourez l’appel d’une fonction ou d’un script à l’aide de la touche **F11**.
- Sortez de l’appel actuel d’une fonction ou d’un script en appuyant sur **MAJ + F11**.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Explorez le schéma de base de données et exécutez des requêtes SQL à l’aide de SSMS

Utilisez [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) pour vous connecter et parcourir les bases de données et les serveurs de l’application.

Au départ, le déploiement a des locataires et des serveurs SQL Database de catalogue auxquels se connecter. Les noms de serveurs dépendent du modèle de client de base de données (voir ci-dessous pour plus les détails). 

   - **Application autonome :** serveurs pour chaque client (ex. serveur *contosoconcerthall-&lt;Utilisateur&gt;* ) et *catalog-sa-&lt;Utilisateur&gt;*
   - **Base de données par client :** serveurs *tenants1-dpt-&lt;Utilisateur&gt;* et *catalog-dpt-&lt;Utilisateur&gt;*
   - **Base de données mutualisée :** serveurs *tenants1-mt-&lt;Utilisateur&gt;* et *catalog-mt-&lt;Utilisateur&gt;*

Pour garantir une connexion de démonstration réussie, tous serveurs ont une [règle de pare-feu](sql-database-firewall-configure.md) autorisant toutes les adresses IP.


1. Ouvrez *SSMS* et connectez-vous aux clients. Le nom du serveur dépend du modèle de client de base de données que vous avez sélectionné (voir ci-dessous pour plus les détails) :
    - **Application autonome :** serveurs avec des clients individuels (ex. *contosoconcerthall-&lt;Utilisateur&gt;.database.windows.net*) 
    - **Base de données par client :** *tenants1-dpt-&lt;Utilisateur&gt;.database.windows.net*
    - **Base de données mutualisée :** *tenants1-mt-&lt;Utilisateur&gt;.database.windows.net* 
2. Cliquez sur **Connexion** > **Moteur de base de données...**  :

   ![catalog server](media/saas-tenancy-wingtip-app-guidance-tips/connect.png)

3. Les informations d’identification de démonstration sont : Connexion = *developer*, Mot de passe = *P\@ssword1*

    L'image ci-dessous montre l'ouverture d'une session pour le modèle *Base de données par client*. 
    ![Connexion](media/saas-tenancy-wingtip-app-guidance-tips/tenants1-connect.png)
    
   

4. Répétez les étapes 2 et 3 et connectez-vous au serveur de catalogue (voir ci-dessous pour connaître le nom des serveurs spécifiques en fonction du modèle de client de base de données sélectionné)
    - **Application autonome :** *catalog-sa-&lt;Utilisateur&gt;.database.windows.net*
    - **Base de données par client :** *catalog-dpt-&lt;Utilisateur&gt;.database.windows.net*
    - **Base de données mutualisée :** *catalog-mt-&lt;Utilisateur&gt;.database.windows.net*


Une fois la connexion établie, vous devez voir tous les serveurs. Votre liste des bases de données peut être différente, selon les clients que vous avez approvisionnés.

L'image ci-dessous montre l'ouverture d'une session pour le modèle *Base de données par client*.

![object explorer](media/saas-tenancy-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>Étapes suivantes
- [Déployer l’exemple d’application autonome SaaS Wingtip Tickets](saas-standaloneapp-get-started-deploy.md)
- [Déployer l’application de base de données Wingtip Tickets SaaS par client](saas-dbpertenant-get-started-deploy.md)
- [Déployer l’application de base de données multilocataire Wingtip Tickets SaaS](saas-multitenantdb-get-started-deploy.md)

