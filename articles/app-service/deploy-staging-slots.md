---
title: Configurer des environnements intermédiaires pour les applications web dans Azure App Service | Microsoft Docs
description: Découvrez comment utiliser la publication intermédiaire pour les applications web dans Azure App Service.
services: app-service
documentationcenter: ''
author: cephalin
writer: cephalin
manager: jpconnoc
editor: mollybos
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2019
ms.author: cephalin
ms.openlocfilehash: fd488d475e24bc1aeebfa49b9d81b04ebae449ff
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445594"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Configurer des environnements intermédiaires dans Azure App Service
<a name="Overview"></a>

Lorsque vous déployez votre application web, votre application web Linux, votre backend mobile ou votre API dans [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714), vous pouvez utiliser un autre emplacement de déploiement que l’emplacement de production par défaut lorsque vous exécutez le niveau de plan **Standard**, **Premium** ou **Isolé** d’App Service. Les emplacements de déploiement sont des applications en production pourvues de leur propre nom d’hôte. Les éléments de contenu et de configuration des applications peuvent être échangés entre deux emplacements de déploiement, y compris l’emplacement de production. 

Le déploiement de votre application sur un emplacement hors production présente les avantages suivants :

* Vous pouvez valider les modifications d’une application dans un emplacement de déploiement intermédiaire avant de l’échanger avec l’emplacement de production.
* Déployer d’abord une application dans un emplacement et la basculer ensuite en production permet de vous assurer que toutes les instances de l’emplacement sont initialisées avant d’être basculées en production. Cela permet d’éliminer les temps d’arrêt lors du déploiement de l’application. La redirection du trafic est transparente et aucune requête n’est abandonnée du fait d’opérations de permutation. Vous pouvez automatiser l’intégralité de ce workflow en configurant l’[échange automatique](#Auto-Swap) lorsqu’aucune validation n’est nécessaire avant l’échange.
* Après basculement, la précédente application de production se retrouve dans l’emplacement de l’application précédemment intermédiaire. Si les modifications permutées en production ne vous conviennent pas, vous pouvez effectuer la même permutation afin de récupérer immédiatement le contenu du précédent site qui vous plaisait.

Chaque niveau de plan App Service prend en charge un nombre différent d’emplacements de déploiement. L’utilisation de ces emplacements de déploiement n’entraîne aucun coût supplémentaire. Pour connaître le nombre d’emplacements pris en charge par le plan de votre application, consultez [Limites App Service](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits). 

Pour mettre votre application à l’échelle vers un autre niveau, vérifiez que le niveau cible peut prendre en charge le nombre d’emplacements déjà utilisés par votre application. Par exemple, si votre application comprend plus de cinq emplacements, vous ne pouvez pas effectuer de scale-down vers le niveau **Standard**, car le niveau **Standard** ne prend en charge que cinq emplacements de déploiement. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Ajouter un emplacement
Pour que vous puissiez activer plusieurs emplacements de déploiement, l’application doit s’exécuter au niveau **Standard**, **Premium** ou **Isolé**.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la [page de ressources](../azure-resource-manager/manage-resources-portal.md#manage-resources) de votre application.

2. Dans le volet gauche, sélectionnez **Emplacements de déploiement** > **Ajouter un emplacement**.
   
    ![Add a new deployment slot](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Si l’application ne s’exécute pas au niveau **Standard**, **Premium** ou **Isolé**, vous recevez un message précisant les niveaux pris en charge pour la publication intermédiaire. À ce stade, vous pouvez sélectionner **Mettre à niveau** et accéder à l’onglet **Mettre à l’échelle** de votre application avant de continuer.
   > 

3. Dans la boîte de dialogue **Ajouter un emplacement**, nommez l’emplacement, puis indiquez si vous souhaitez cloner une configuration d’application à partir d’un autre emplacement de déploiement. Sélectionnez **Ajouter** pour continuer.
   
    ![Source de configuration](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    Vous pouvez cloner une configuration à partir d’un emplacement existant. Parmi les paramètres pouvant être clonés figurent des paramètres de l’application, des chaînes de connexion, des versions du framework de langage, des sockets web, la version HTTP et le nombre de bits de la plateforme.

4. Une fois l’emplacement ajouté, sélectionnez **Fermer** pour fermer la boîte de dialogue. Le nouvel emplacement est désormais affiché dans la page **Emplacements de déploiement**. Par défaut, l’option **% de trafic** est définie sur 0 pour le nouvel emplacement, avec tout le trafic client acheminé vers l’emplacement de production.

5. Sélectionnez le nouvel emplacement de déploiement pour ouvrir la page des ressources de cet emplacement.
   
    ![Titre de l’emplacement de déploiement](./media/web-sites-staged-publishing/StagingTitle.png)

    L’emplacement intermédiaire dispose d’une page de gestion, comme n’importe quelle application App Service. Vous pouvez modifier la configuration de l’emplacement. Le nom de l’emplacement est indiqué en haut de la page, pour vous rappeler que c’est l’emplacement de déploiement qui est affiché.

6. Sélectionnez l’URL de l’application dans la page des ressources de l’emplacement. L’emplacement de déploiement est une application en production et il a son propre nom d’hôte. Pour limiter l’accès public à l’emplacement de déploiement, consultez [Restrictions d’adresse IP avec Azure App Service](app-service-ip-restrictions.md).

Le nouvel emplacement de déploiement n’a aucun contenu, même si vous clonez les paramètres à partir d’un autre emplacement. Par exemple, vous pouvez [publier sur cet emplacement avec Git](app-service-deploy-local-git.md). Vous pouvez effectuer un déploiement sur l’emplacement à partir d’une autre branche du dépôt, ou d’un dépôt différent. 

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>Que se passe-t-il pendant un échange ?

### <a name="swap-operation-steps"></a>Étapes qui composent une opération d’échange

Lorsque vous échangez deux emplacements (en général, vous passez d’un emplacement de préproduction à un emplacement de production), App Service effectue ce qui suit pour éviter que l’emplacement cible ne subisse un temps d’arrêt :

1. Applique les paramètres suivants de l’emplacement cible (par exemple, l’emplacement de production) à toutes les instances de l’emplacement source : 
    - Les paramètres d’application et les chaînes de connexion [propres aux emplacements](#which-settings-are-swapped) (si nécessaire)
    - Les paramètres de [déploiement continu](deploy-continuous-deployment.md) (si activés)
    - Les paramètres relatifs à l’[authentification App Service](overview-authentication-authorization.md) (si activés)
    
    Quel que soit le cas, cela déclenche le redémarrage de toutes les instances dans l’emplacement source. Au cours d’un [échange avec aperçu](#Multi-Phase), cela marque la fin de la première phase. L’opération d’échange est suspendue, ce qui vous permet de vérifier que l’emplacement source fonctionne correctement avec les paramètres de l’emplacement cible.

1. Attendez que chaque instance de l’emplacement source ait terminé son redémarrage. Si l’une des instances ne parvient pas à redémarrer, l’opération d’échange rétablit toutes les modifications apportées à l’emplacement source, puis cesse d’être exécutée.

1. Si le [cache local](overview-local-cache.md) est activé, déclenchez son initialisation en envoyant une requête HTTP à la racine de l’application (« / »), sur chaque instance de l’emplacement source. Attendez que chaque instance retourne une réponse HTTP. L’initialisation du cache local provoque un autre redémarrage sur chaque instance.

1. Si l’[échange automatique](#Auto-Swap) est activé avec l’[initialisation personnalisée](#Warm-up), déclenchez l’[initialisation de l’application](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) en envoyant une requête HTTP à la racine de l’application (« / ») sur chaque instance de l’emplacement source.

    Si `applicationInitialization` n’est pas spécifié, déclenchez une requête HTTP à la racine de l’application de l’emplacement source sur chaque instance. 
    
    Si une instance retourne une réponse HTTP, elle est considérée comme initialisée.

1. Si toutes les instances de l’emplacement source sont correctement initialisées, échangez les deux emplacements en échangeant leurs règles de routage. Après cette étape, l’emplacement cible (par exemple, l’emplacement de production) dispose de l’application déjà initialisée dans l’emplacement source.

1. Maintenant que l’emplacement source dispose de l’application de l’emplacement cible avant échange, effectuez la même opération en appliquant tous les paramètres et en redémarrant les instances.

Lors d’une opération d’échange, l’intégralité du processus d’initialisation des applications échangées se déroule dans l’emplacement source. L’emplacement cible reste en ligne pendant la préparation et l’initialisation de l’emplacement source, que l’échange ait réussi ou échoué. Pour échanger un emplacement de préproduction avec un emplacement de production, vérifiez que l’emplacement de production est toujours l’emplacement cible. De cette façon, l’opération d’échange n’affectera pas votre application de production.

### <a name="which-settings-are-swapped"></a>Quels sont les paramètres échangés ?
Lorsque vous clonez la configuration depuis un autre emplacement de déploiement, celle-ci est modifiable. Au cours d’un échange, certains éléments de configuration suivent le contenu (éléments non propres à un emplacement) tandis que d’autres restent dans le même emplacement après l’échange (éléments propres à un emplacement). Les listes suivantes représentent les paramètres qui évoluent lorsque vous échangez les emplacements.

**Paramètres échangés**:

* Paramètres généraux, par exemple versions du framework, 32/64 bits, sockets web
* Paramètres d’application (peuvent être configurés pour respecter un emplacement)
* Chaînes de connexion (peuvent être configurées pour respecter un emplacement)
* Mappages de gestionnaires
* Paramètres de surveillance et de diagnostics
* Certificats publics
* Contenu WebJobs
* Connexions hybrides*
* Intégration du réseau virtuel*
* Points de terminaison de service*
* Azure Content Delivery Network*

Prochainement, les fonctionnalités marquées d’un astérisque (*) ne pourront plus être échangées. 

**Paramètres non échangés** :

* Points de terminaison de publication
* Noms de domaine personnalisés
* Certificats privés et liaisons SSL
* Paramètres de mise à l'échelle
* Planificateurs WebJobs
* Restrictions d’adresse IP
* Always On
* Paramètres de protocole (HTTPS, version TLS, certificats clients)
* Paramètres des journaux de diagnostic
* Partage des ressources cross-origin (CORS)

<!-- VNET and hybrid connections not yet sticky to slot -->

Si vous souhaitez configurer un paramètre d’application ou une chaîne de connexion pour qu’ils restent dans leur emplacement d’origine (et ne puissent donc pas être échangés), accédez à la page **Configuration** de l’emplacement en question. Ajoutez ou modifiez un paramètre, puis sélectionnez **Paramètre d’emplacement de déploiement**. Le fait de cocher cette case indique à App Service que le paramètre n’est pas échangeable. 

![Paramètre de l’emplacement](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Permuter deux emplacements 
Vous pouvez échanger des emplacements de déploiement dans la page **Emplacements de déploiement** et la page **Vue d’ensemble** de votre application. Pour obtenir des détails techniques sur l’échange des emplacements, consultez [Que se passe-t-il pendant un échange ?](#AboutConfiguration).

> [!IMPORTANT]
> Avant de basculer une application de l’emplacement de déploiement vers l’emplacement de production, vérifiez que l’emplacement de production est bien votre emplacement cible, et que tous les paramètres de l’emplacement source sont configurés comme vous le souhaitez dans l’emplacement de production.
> 
> 

Pour échanger des emplacements de déploiement :

1. Accédez à la page **Emplacements de déploiement** de votre application, puis sélectionnez **Échanger**.
   
    ![Bouton Échanger](./media/web-sites-staged-publishing/SwapButtonBar.png)

    La boîte de dialogue **Échanger** regroupe les paramètres des emplacements source et cible sélectionnés qui vont être échangés.

2. Sélectionnez les emplacements **Source** et **Cible** souhaités. En général, la cible correspond à l’emplacement de production. Sélectionnez également les onglets **Modifications sources** et **Modifications cibles** pour vérifier que les changements de configuration à opérer correspondent à ce que vous attendez. Lorsque vous avez terminé, vous pouvez échanger les emplacements immédiatement en sélectionnant **Échanger**.

    ![Échange effectué](./media/web-sites-staged-publishing/SwapImmediately.png)

    Pour voir comment votre emplacement cible s’exécuterait avec les nouveaux paramètres avant que l’échange ne soit réellement effectué, ne sélectionnez pas **Échanger**, mais suivez les instructions fournies dans [Échange avec aperçu](#Multi-Phase).

3. Lorsque vous avez terminé, fermez la boîte de dialogue en sélectionnant **Fermer**.

Si vous rencontrez des problèmes, consultez [Résoudre les problèmes liés aux échanges](#troubleshoot-swaps).

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Échange avec aperçu (échange multiphase)

> [!NOTE]
> L’échange avec aperçu n’est pas pris en charge dans les applications web sur Linux.

Avant de passer à l’emplacement de production (emplacement cible), contrôlez l’exécution de l’application avec les paramètres échangés. L’emplacement source est également initialisé avant la fin de l’échange, ce qui est souhaitable pour les applications stratégiques.

Lorsque vous effectuez un échange avec aperçu, App Service effectue la même [opération d’échange](#AboutConfiguration), mais il fait une pause après la première étape. Vous pouvez donc vérifier le résultat sur l’emplacement de préproduction avant la fin de l’échange. 

Si vous annulez l’échange, App Service réapplique les éléments de configuration à l’emplacement source.

Pour effectuer un échange avec aperçu :

1. Procédez comme indiqué dans [Échanger des emplacements de déploiement](#Swap), mais sélectionnez **Effectuer l’échange avec aperçu**.

    ![Échange avec aperçu](./media/web-sites-staged-publishing/SwapWithPreview.png)

    La boîte de dialogue montre comment la configuration de l’emplacement source est modifiée dans la phase 1, et comment les emplacements source et cible sont modifiés dans la phase 2.

2. Lorsque vous êtes prêt à démarrer l’échange, sélectionnez **Démarrer l’échange**.

    Lorsque la phase 1 est terminée, vous en êtes averti dans la boîte de dialogue. Affichez l’aperçu de l’échange dans l’emplacement source en accédant à `https://<app_name>-<source-slot-name>.azurewebsites.net`. 

3. Lorsque vous êtes prêt à effectuer l’échange en attente, sélectionnez **Terminer l’échange** dans **Action d’échange**, puis sélectionnez **Terminer l’échange**.

    Pour annuler un échange en attente, sélectionnez plutôt **Annuler l’échange**.

4. Lorsque vous avez terminé, fermez la boîte de dialogue en sélectionnant **Fermer**.

Si vous rencontrez des problèmes, consultez [Résoudre les problèmes liés aux échanges](#troubleshoot-swaps).

Pour automatiser un échange multiphase, consultez [Automatiser avec PowerShell](#automate-with-powershell).

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Restaurer un échange
Si des erreurs se produisent dans l’emplacement cible (par exemple, l’emplacement de production) après une permutation d’emplacements, rétablissez ces deux emplacements comme ils étaient avant l’opération, en les intervertissant immédiatement.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Configurer l’échange automatique

> [!NOTE]
> L’échange automatique n’est pas pris en charge dans les applications web sur Linux.

L’échange automatique simplifie les scénarios Azure DevOps impliquant un déploiement de l’application en continu, sans démarrage à froid ni temps d’arrêt pour les utilisateurs de l’application. Lorsque vous activez l’échange automatique d’un emplacement vers l’emplacement de production, chaque fois que vous envoyez (push) des modifications de votre code à cet emplacement, App Service [fait basculer automatiquement l’application vers la production](#swap-operation-steps) après son initialisation dans l’emplacement source.

   > [!NOTE]
   > Avant de configurer l’échange automatique pour l’emplacement de production, il est conseillé de tester l’échange automatique sur un emplacement cible hors production.
   > 

Pour configurer l’échange automatique :

1. Accédez à la page des ressources de votre application. Sélectionnez **Emplacements de déploiement** >  *\<emplacement source souhaité >*  > **Configuration** > **Paramètres généraux**.
   
2. Pour **Échange automatique activé**, sélectionnez **Activé**. Ensuite, sélectionnez l’emplacement cible souhaité pour **Échanger automatiquement l’emplacement de déploiement**, puis sélectionnez **Enregistrer** dans la barre de commandes. 
   
    ![Sélections pour la configuration de l’échange automatique](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Exécutez un push de code sur l’emplacement source. L’échange automatique se produit peu après, et la mise à jour est appliquée dans l’URL de votre emplacement cible.

Si vous rencontrez des problèmes, consultez [Résoudre les problèmes liés aux échanges](#troubleshoot-swaps).

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Spécifier l’initialisation personnalisée
Quand vous utilisez [Échange automatique](#Auto-Swap), certaines applications peuvent nécessiter quelques actions préparatoires personnalisées avant l’échange. L’élément de configuration `applicationInitialization` du fichier web.config vous permet de spécifier les actions d’initialisation personnalisées à exécuter. L’[opération d’échange](#AboutConfiguration) attend la fin de l’initialisation personnalisée pour procéder à l’échange avec l’emplacement cible. Voici un exemple de fragment web.config.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

Pour plus d’informations sur la personnalisation de l’élément `applicationInitialization`, consultez [Most common deployment slot swap failures and how to fix them](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

Vous pouvez également personnaliser le comportement d’initialisation en utilisant l’un des deux [paramètres d’application](configure-common.md) suivants (ou les deux) :

- `WEBSITE_SWAP_WARMUP_PING_PATH`: chemin permettant d’effectuer un test ping afin d’initialiser votre site. Ajoutez ce paramètre d’application en spécifiant un chemin d’accès personnalisé qui commence par une barre oblique comme valeur. Par exemple `/statuscheck`. La valeur par défaut est `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Codes de réponse HTTP valides pour l’opération d'initialisation. Ajoutez ce paramètre d’application avec une liste séparée par des virgules de codes HTTP. Par exemple `200,202`. Si le code d’état retourné ne figure pas dans la liste, les opérations d’initialisation et d’échange sont arrêtées. Par défaut, tous les codes de réponse sont valides.

Si vous rencontrez des problèmes, consultez [Résoudre les problèmes liés aux échanges](#troubleshoot-swaps).

## <a name="monitor-a-swap"></a>Superviser un échange

Si l’exécution de l’[opération d’échange](#AboutConfiguration) prend beaucoup de temps, vous pouvez obtenir des informations au sujet de cette opération dans le [journal d’activité](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

Sur le portail, dans la page des ressources de votre application, sélectionnez **Journal d’activité** dans le volet de gauche.

Une opération d’échange s’affiche dans la requête de journal en tant que `Swap Web App Slots`. Vous pouvez la développer et sélectionner l’une des sous-opérations ou erreurs afin d’afficher le contenu en détail.

## <a name="route-traffic"></a>Acheminer le trafic

Par défaut, toutes les requêtes clientes vers les URL de production de l’application (`http://<app_name>.azurewebsites.net`) sont acheminées vers l’emplacement de production. Vous pouvez acheminer une partie du trafic vers un autre emplacement. Cette fonctionnalité est utile si vous avez besoin d’un retour d’expérience utilisateur pour une nouvelle mise à jour, mais que vous n’êtes pas prêt à la publier en production.

### <a name="route-production-traffic-automatically"></a>Acheminer le trafic de production automatiquement

Pour router le trafic de production automatiquement :

1. Accédez à la page des ressources de votre application et sélectionnez **Emplacements de déploiement**.

2. Dans la colonne **% de trafic** de l’emplacement vers lequel vous souhaitez acheminer le trafic, spécifiez un pourcentage (compris entre 0 et 100) pour représenter la quantité totale de trafic à diriger. Sélectionnez **Enregistrer**.

    ![Définition d’un pourcentage de trafic](./media/web-sites-staged-publishing/RouteTraffic.png)

Une fois le paramètre enregistré, le pourcentage de clients spécifié est routé de manière aléatoire vers l’emplacement hors production. 

Lorsqu’un client est automatiquement routé vers un emplacement particulier, il est « épinglé » à cet emplacement pendant toute la durée de cette session cliente. Dans le navigateur client, vous pouvez voir à quel emplacement votre session est épinglée en examinant le cookie `x-ms-routing-name` dans les en-têtes HTTP. Une requête qui est acheminée vers l’emplacement « intermédiaire » contient le cookie `x-ms-routing-name=staging`. Une requête qui est acheminée vers l’emplacement de production a le cookie `x-ms-routing-name=self`.

### <a name="route-production-traffic-manually"></a>Acheminer le trafic de production manuellement

Parallèlement au routage automatique du trafic, App Service peut acheminer les requêtes vers un emplacement particulier. Cela s’avère utile si vous souhaitez que vos utilisateurs puissent choisir d’accepter ou de refuser votre application bêta. Pour router le trafic de production manuellement, vous utilisez le paramètre de requête `x-ms-routing-name`.

Par exemple, pour permettre aux utilisateurs de refuser votre application bêta, vous pouvez placer ce lien dans votre page web :

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

La chaîne `x-ms-routing-name=self` spécifie l’emplacement de production. Lorsque le navigateur client accède au lien, il est redirigé vers l’emplacement de production. Chaque requête ultérieure comprendra le cookie `x-ms-routing-name=self` qui épinglera la session à l’emplacement de production.

Pour permettre aux utilisateurs d’accepter votre application bêta, définissez le même paramètre de requête pour le nom de l’emplacement hors production. Voici un exemple :

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Par défaut, les nouveaux emplacements se voient attribuer une règle de routage de `0%` (indiquée en gris). Lorsque vous définissez cette valeur explicitement sur `0%` (indiquée en noir), vos utilisateurs peuvent accéder à l’emplacement de préproduction manuellement, à l’aide du paramètre de requête `x-ms-routing-name`. Toutefois, ils ne seront pas routés vers l’emplacement automatiquement, car le pourcentage de routage est défini sur 0. Il s’agit d’un scénario avancé où vous pouvez « cacher » votre emplacement de préproduction du public, tout en permettant aux équipes internes de tester les modifications sur l’emplacement.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Supprimer un emplacement

Accédez à la page des ressources de votre application. Sélectionnez **Emplacements de déploiement** >  *\<emplacement à supprimer>*  > **Vue d’ensemble**. Sélectionnez **Supprimer** dans la barre de commandes.  

![Supprimer un emplacement de déploiement](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatiser avec PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell est un module qui fournit des applets de commande pour gérer Azure via Windows PowerShell, notamment la prise en charge de la gestion des emplacements de déploiement des applications dans Azure App Service.

Pour plus d’informations sur l’installation et la configuration d’Azure PowerShell et sur l’authentification d’Azure PowerShell avec votre abonnement Azure, consultez la page [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).  

---
### <a name="create-a-web-app"></a>Créer une application web
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-a-slot"></a>Créer un emplacement
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Démarrer un échange avec aperçu (échange multiphase) et appliquer la configuration de l’emplacement de destination à l’emplacement source
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Annuler un échange en attente (échange avec aperçu) et restaurer la configuration de l’emplacement source
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Échanger des emplacements de déploiement
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Superviser les événements d’échange dans le journal d’activité
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Supprimer un emplacement
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

---
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automatiser avec l’interface CLI

Pour connaître les commandes de l’interface [Azure CLI](https://github.com/Azure/azure-cli) pour les emplacements de déploiement, consultez [az webapp deployment slot](/cli/azure/webapp/deployment/slot).

## <a name="troubleshoot-swaps"></a>Résoudre les problèmes liés aux échanges

Si une erreur se produit pendant un [échange d’emplacement](#AboutConfiguration), celle-ci est journalisée dans *D:\home\LogFiles\eventlog.xml*. Elle est également journalisée dans le journal des erreurs propres à l’application.

Voici quelques erreurs courantes liées aux échanges :

- Une requête HTTP envoyée à la racine de l’application a expiré. L’opération d’échange attend 90 secondes pour chaque requête HTTP, et retente jusqu’à 5 fois. Si toutes les nouvelles tentatives expirent elles aussi, l’opération d’échange est arrêtée.

- L’initialisation du cache local peut échouer si le contenu de l’application dépasse le quota de disque local spécifié. Pour plus d’informations, consultez [Vue d’ensemble du cache local](overview-local-cache.md).

- Pendant l’[initialisation personnalisée](#Warm-up), les requêtes HTTP sont effectuées en interne (sans passer par l’URL externe). Elles peuvent échouer avec certaines règles de réécriture d’URL dans *Web.config*. Par exemple, les règles de redirection des noms de domaine ou d’application du protocole HTTPS peuvent empêcher les requêtes d’initialisation d’atteindre le code de l’application. Pour contourner ce problème, modifiez vos règles de réécriture en ajoutant les deux conditions suivantes :

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Sans une initialisation personnalisée, les règles de réécriture d’URL peuvent encore bloquer les requêtes HTTP. Pour contourner ce problème, modifiez vos règles de réécriture en ajoutant la condition suivante :

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Certaines [règles de restriction IP](app-service-ip-restrictions.md) peuvent empêcher l’opération d’échange d’envoyer des requêtes HTTP à votre application. Les plages d’adresses IPv4 qui commencent par `10.` et `100.` sont internes à votre déploiement. Vous devez les autoriser à se connecter à votre application.

## <a name="next-steps"></a>Étapes suivantes
[Bloquer l’accès à des emplacements hors production](app-service-ip-restrictions.md)
