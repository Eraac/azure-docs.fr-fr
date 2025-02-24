---
title: 'Didacticiel : Créer une page web de recherche personnalisée - Recherche personnalisée Bing'
titleSuffix: Azure Cognitive Services
description: Décrit la configuration d’une instance de recherche personnalisée et son intégration à une page web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 3a8d6a831adf24212191a103dcf356bbe74d7962
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405219"
---
# <a name="tutorial-build-a-custom-search-web-page"></a>Didacticiel : Créer une page web Recherche personnalisée

La Recherche personnalisée Bing vous permet de créer des expériences de recherche adaptées pour des sujets qui vous intéressent. Par exemple, si vous avez un site web d’arts martiaux qui fournit une expérience de recherche, vous pouvez spécifier les domaines, sous-sites et pages web dans lesquels Bing effectue les recherches. Vos utilisateurs consultent des résultats de recherche adaptés au contenu qui les intéresse au lieu de compulser des pages de résultats de recherche générale dont le contenu peut être inapproprié. 

Ce tutoriel montre comment configurer une instance de recherche personnalisée et l’intégrer à une nouvelle page web.

Les tâches traitées sont les suivantes :

> [!div class="checklist"]
> - Création d’une instance de recherche personnalisée
> - Ajout d’entrées actives
> - Ajout d’entrées bloquées
> - Ajout d’entrées épinglées
> - Intégration de la recherche personnalisée à une page web

## <a name="prerequisites"></a>Prérequis

- Pour suivre ce tutoriel, vous avez besoin d’une clé d’abonnement pour l’API Recherche personnalisée Bing.  Pour obtenir une clé, consultez [Essayer Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
- Si vous n’avez pas encore installé Visual Studio 2017 ou version ultérieure, vous pouvez télécharger et utiliser **gratuitement** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/).

## <a name="create-a-custom-search-instance"></a>Création d’une instance de recherche personnalisée

Pour créer une instance Recherche personnalisée Bing :

1. Ouvrez un navigateur Internet.  
  
2. Accédez au [portail](https://customsearch.ai) de la Recherche personnalisée.  
  
3. Connectez-vous au portail à l’aide d’un compte Microsoft. Si vous n’en avez pas, cliquez sur **Créer un compte Microsoft**. Si c’est la première fois que vous utilisez le portail, des autorisations d’accès à vos données vous sont demandées. Cliquez sur **Oui**.  
  
4. Une fois connecté, cliquez sur **New custom search** (Nouvelle recherche personnalisée). Dans la fenêtre **Create a new custom search instance** (Créer une nouvelle instance de recherche personnalisée), entrez un nom explicite et décrivez le type de contenu que la recherche retourne. Vous pouvez modifier le nom à tout moment.  
  
   ![Capture d’écran de la boîte de dialogue de création d’une instance de recherche personnalisée](../media/newCustomSrch.png)  
  
5. Cliquez sur OK, spécifiez une URL et précisez s’il faut inclure les sous-pages de l’URL.  
  
   ![Capture d’écran de la page de définition de l’URL](../media/newCustomSrch1-a.png)  


## <a name="add-active-entries"></a>Ajout d’entrées actives

Pour inclure les résultats d’URL ou de sites web particuliers, ajoutez-les à l’onglet **Active** (Éléments actifs).

1.  Dans la page **Configuration**, cliquez sur l’onglet **Active** et entrez l’URL d’un ou plusieurs sites web à inclure dans votre recherche.

    ![Capture d’écran de l’onglet des éléments actifs dans l’éditeur de définitions](../media/customSrchEditor.png)

2.  Pour vérifier que votre instance retourne des résultats, entrez une requête dans le volet de visualisation situé à droite. Bing retourne uniquement les résultats pour les sites web publics qu’il a indexés.

## <a name="add-blocked-entries"></a>Ajout d’entrées bloquées

Pour exclure les résultats d’URL ou de sites web particuliers, ajoutez-les à l’onglet **Blocked** (Éléments bloqués).

1. Dans la page **Configuration**, cliquez sur l’onglet **Blocked** et entrez l’URL d’un ou plusieurs sites web à exclure de votre recherche.

    ![Capture d’écran de l’onglet des éléments bloqués dans l’éditeur de définitions](../media/blockedCustomSrch.png)


2. Pour vérifier que votre instance ne retourne pas de résultats provenant des sites web bloqués, entrez une requête dans le volet de visualisation situé à droite. 

## <a name="add-pinned-entries"></a>Ajout d’entrées épinglées

Pour épingler une page web spécifique au début des résultats de recherche, ajoutez la page web et le terme de requête dans l’onglet **Pinned** (Éléments épinglés). L’onglet **Pinned** (Éléments épinglés) contient une liste de paires, constituées de termes de requête associés à des pages web, qui spécifient la page web devant s’afficher en tant que meilleur résultat pour une requête particulière. La page web est épinglée uniquement si la chaîne de requête de l’utilisateur correspond à la chaîne de requête de l’épingle selon la condition de correspondance de l’épingle. Seules les pages web indexées apparaîtront dans les recherches. Consultez [Définir votre affichage personnalisé](../define-your-custom-view.md#pin-slices-to-the-top-of-search-results) pour en savoir plus.

1. Dans la page **Configuration**, cliquez sur l’onglet **Pinned** et entrez la page web et le terme de requête de la page web que vous voulez voir retourner comme premier résultat.  
  
2. Par défaut, la chaîne de requête de l’utilisateur doit correspondre exactement à la chaîne de requête de votre épingle pour que Bing retourne la page web comme premier résultat. Pour changer la condition de correspondance, modifiez l’épingle (cliquez sur l’icône du crayon), cliquez sur Exact dans la colonne **Query match condition** (Condition de correspondance des requêtes), puis sélectionnez la condition de correspondance qui convient à votre application.  
  
    ![Capture d’écran de l’onglet des éléments épinglés dans l’éditeur de définitions](../media/pinnedCustomSrch.png)
  
3. Pour vérifier que votre instance retourne la page web spécifiée comme meilleur résultat, entrez le terme de requête que vous avez épinglé dans le volet de visualisation situé à droite.

## <a name="configure-hosted-ui"></a>Configurer l’interface utilisateur hébergée

La Recherche personnalisée fournit une IU hébergée pour restituer la réponse JSON de votre instance de recherche personnalisée. Pour définir votre expérience d’interface utilisateur :

1. Cliquez sur l’onglet **Hosted UI** (IU hébergée).  
  
2. Sélectionnez une disposition.  
  
   ![Capture d’écran de l’étape de sélection de la disposition de l’interface utilisateur hébergée](./media/custom-search-hosted-ui-select-layout.png)  
  
3. Sélectionnez un thème de couleur.  
  
   ![Capture d’écran de la sélection du thème de couleur de l’interface utilisateur hébergée](./media/custom-search-hosted-ui-select-color-theme.png)  

   Si vous avez besoin de régler le thème de couleur pour mieux l’intégrer à votre application web, cliquez sur **Customize theme** (Personnaliser le thème). Certaines configurations de couleur ne s’appliquent pas à tous les thèmes de disposition. Pour changer une couleur, entrez la valeur HEX RVB de la couleur (par exemple, #366eb8) dans la zone de texte correspondante. Sinon, cliquez sur le bouton de couleur, puis sur la nuance qui vous convient. Pensez toujours à l’accessibilité quand vous sélectionnez des couleurs.
  
   ![Capture d’écran de la personnalisation du thème de couleur de l’interface utilisateur hébergée](./media/custom-search-hosted-ui-customize-color-theme.png)  

  
4. Spécifiez des options de configuration supplémentaires.  
  
   ![Capture d’écran de l’étape des configurations supplémentaires de l’interface utilisateur hébergée](./media/custom-search-hosted-ui-additional-configurations.png)  
  
   Pour obtenir des configurations avancées, cliquez sur **Show advanced configurations** (Afficher les configurations avancées). S’ajoutent alors les configurations *Link target* (Lier la cible) aux options de recherche web, *Enable filters* (Activer les filtres) aux options des images et des vidéos, et *Search box text placeholder* (Espace réservé du texte de la zone de recherche) aux options diverses.

   ![Capture d’écran de l’étape des configurations avancées de l’interface utilisateur hébergée](./media/custom-search-hosted-ui-advanced-configurations.png)  
  
5. Dans les listes déroulantes, sélectionnez vos clés d’abonnement. Vous pouvez aussi entrer la clé d’abonnement manuellement. Pour plus d’informations sur l’obtention de clés, consultez [Essayez Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search-api).  
  
   ![Capture d’écran de l’étape des configurations supplémentaires de l’interface utilisateur hébergée](./media/custom-search-hosted-ui-subscription-key.png)

[!INCLUDE [publish or revert](../includes/publish-revert.md)]

<a name="consuminghostedui"></a>
## <a name="consuming-hosted-ui"></a>Consommation de l’interface utilisateur hébergée

Il existe deux façons de consommer l’IU hébergée.  

- Option 1 : intégrer l’extrait de code JavaScript fourni dans votre application.
- Option 2 : utiliser le point de terminaison HTML fourni.

Le reste de ce tutoriel illustre l’**Option 1 : Extrait de code Javascript**.  

## <a name="set-up-your-visual-studio-solution"></a>Configurer votre solution Visual Studio

1. Ouvrez **Visual Studio** sur votre ordinateur.  
  
2. Dans le menu **Fichier**, sélectionnez **Nouveau**, puis choisissez **Projet**.  
  
3. Dans la fenêtre **Nouveau projet**, sélectionnez **Visual C# / Web / Application web ASP.NET Core**, nommez votre projet, puis cliquez sur **OK**.  
  
   ![Capture d’écran de la fenêtre Nouveau projet](./media/custom-search-new-project.png)  
  
4. Dans la fenêtre **Nouvelle application ASP.NET Core**, sélectionnez **Application web** et cliquez sur **OK**.  
  
   ![Capture d’écran de la fenêtre Nouveau projet](./media/custom-search-new-webapp.png)  

## <a name="edit-indexcshtml"></a>Éditer index.cshtml

1. Dans l’**Explorateur de solutions**, développez **Pages** et double-cliquez sur **index.cshtml** pour ouvrir le fichier.  
  
   ![Capture d’écran de l’Explorateur de solutions, où Pages est développé et index.cshtml sélectionné](./media/custom-search-visual-studio-webapp-solution-explorer-index.png)  
  
2. Dans index.cshtml, supprimez tout le contenu à partir de la ligne 7 et en dessous.  
  
   ```razor
   @page
   @model IndexModel
   @{
      ViewData["Title"] = "Home page";
   }    
   ```  
  
3. Ajoutez un élément de saut de ligne et un élément div pour servir de conteneur.  
  
   ```html
   @page
   @model IndexModel
   @{
      ViewData["Title"] = "Home page";
   }
   <br />
   <div id="customSearch"></div>
   ```  
  
4. Dans la page **Hosted UI** (IU hébergée), faites défiler jusqu’à la section intitulée **Consuming the UI** (Consommation de l’IU). Cliquez sur les *Points de terminaison* pour accéder à l’extrait de code JavaScript. Vous pouvez aussi obtenir l’extrait de code en cliquant sur **Production**, puis sur l’onglet **Hosted UI**.
  
   <!-- Get new screenshot after prod gets new bits
   ![Screenshot of the Hosted UI save button](./media/custom-search-hosted-ui-consuming-ui.png)  
   -->
  
5. Collez l’élément de script dans le conteneur que vous avez ajouté.  
  
   ``` html
   @page
   @model IndexModel
   @{
      ViewData["Title"] = "Home page";
   }
   <br />
   <div id="customSearch">
      <script type="text/javascript" 
          id="bcs_js_snippet"
          src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
      </script>
   </div>
   ```  
  
6. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **wwwroot** et cliquez sur **Afficher dans le navigateur**.  
  
   ![Capture d’écran de l’Explorateur de solutions, avec sélection de l’option Afficher dans le navigateur dans le menu contextuel de wwwroot](./media/custom-search-webapp-view-in-browser.png)  

Votre nouvelle page web de recherche personnalisée doit ressembler à ceci :

![Capture d’écran d’une page web de recherche personnalisée](./media/custom-search-webapp-browse-index.png)

L’exécution d’une recherche restitue les résultats comme suit :

![Capture d’écran des résultats de la recherche personnalisée](./media/custom-search-webapp-results.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Appeler le point de terminaison Recherche personnalisée Bing (C#)](../call-endpoint-csharp.md)
