---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: bbcf38ab54632144920b729f433c222f426d96dc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177291"
---
1. Connectez-vous au [portail Azure][Azure portal].
2. Dans le menu de gauche, sélectionnez **+ Créer une ressource**. Ensuite, sélectionnez **Intégration** > **Relais**. Si **Relais** ne s’affiche pas dans la liste, sélectionnez **Afficher tout** dans l’angle supérieur droit. 
3. Sous **Créer un espace de noms**, saisissez un nom d’espace de noms. Le système vérifie immédiatement si le nom est disponible.
4. Dans la case **Abonnement**, sélectionnez un abonnement Azure dans lequel créer l’espace de noms.
5. Dans la case [Groupe de ressources](../articles/azure-resource-manager/manage-resource-groups-portal.md), sélectionnez un groupe de ressources existant dans lequel placer l’espace de noms, ou créez-en un nouveau.  
6. Dans **Emplacement**, sélectionnez le pays ou la région où votre espace de noms doit être hébergé.
   
    ![Créer un espace de noms][create-namespace]
7. Sélectionnez **Créer**. Le système crée votre espace de noms et l'active. Après quelques minutes, le système approvisionne des ressources pour votre compte.

### <a name="get-management-credentials"></a>Obtenir des informations d'identification d'administration

1. Sélectionnez **Toutes les ressources**, puis sélectionnez le nom de l’espace de noms nouvellement créé.
2. Sous l’espace de noms de relais, sélectionnez **Stratégies d’accès partagées**.  
3. Dans **Stratégies d’accès partagées**, sélectionnez **RootManageSharedAccessKey**.
   
    ![informations de connexion][connection-info]
4. Sous **Stratégie : RootManageSharedAccessKey**, sélectionnez le bouton **Copie** à côté de **Clé primaire de la chaîne de connexion**. La chaîne de connexion est alors copiée dans le presse-papiers pour une utilisation ultérieure. Copiez cette valeur dans le Bloc-notes ou un autre emplacement temporaire.
   
    ![connection-string][connection-string]

5. Répétez l’étape précédente pour copier et coller la valeur de **Clé primaire** dans un emplacement temporaire pour une utilisation ultérieure.  

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
