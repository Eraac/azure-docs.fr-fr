---
title: Se connecter à la Recherche Bing – Azure Logic Apps
description: Rechercher des actualités avec les API REST de la recherche Bing et Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: 7146e59eabf9e30fa263f957f1c546414ad0fe26
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60952606"
---
# <a name="find-news-with-bing-search-and-azure-logic-apps"></a>Rechercher des actualités avec la recherche Bing et Azure Logic Apps

Cet article explique comment rechercher des actualités, des vidéos et d’autres éléments par le biais de la recherche Bing, à partir d’une application logique à l’aide du connecteur de recherche Bing. De cette façon, vous pouvez créer des applications logiques qui automatisent les tâches et les flux de travail pour le traitement des résultats de la recherche, et rendre ces éléments disponibles pour d’autres actions. 

Par exemple, vous pouvez rechercher des éléments d’actualités en fonction de critères de recherche et les publier comme tweets dans votre flux Twitter.

Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>. Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md) et [Démarrage rapide : Créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Pour obtenir des informations techniques spécifiques aux connecteurs, consultez la <a href="https://docs.microsoft.com/connectors/bingsearch/" target="blank">référence du connecteur de recherche Bing</a>.

## <a name="prerequisites"></a>Prérequis

* Un [compte Cognitive Services](../cognitive-services/cognitive-services-apis-create-account.md)

* Une [clé API Recherche Bing](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api), qui fournit l’accès depuis votre application logique aux API Recherche Bing

* L’application logique à partir de laquelle vous souhaitez accéder à votre hub d’événements. Pour démarrer votre application logique avec un déclencheur de recherche Bing, vous avez besoin d’une [application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-a-bing-search-trigger"></a>Ajouter un déclencheur de recherche Bing

Dans Azure Logic Apps, chaque application logique doit démarrer avec un [déclencheur](../logic-apps/logic-apps-overview.md#logic-app-concepts), qui s’active lorsqu’un événement spécifique se produit ou lorsqu’une condition particulière est remplie. Chaque fois que le déclencheur s’active, le moteur Logic Apps crée une instance d’application logique et lance l’exécution du flux de travail de votre application.

1. Dans le portail Azure ou Visual Studio, créez une application logique vide, qui ouvre le Concepteur d’applications logiques. Cet exemple utilise le portail Azure.

2. Dans la zone de recherche, entrez « recherche Bing » comme filtre. Dans la liste des déclencheurs, sélectionnez le déclencheur souhaité.

   Cet exemple utilise ce déclencheur : **Bing Search – On new news article**.

   ![Rechercher le déclencheur de recherche Bing](./media/connectors-create-api-bing-search/add-trigger.png)

3. Si vous êtes invité à entrer les informations de connexion, [créez votre connexion de recherche Bing maintenant](#create-connection).
Ou bien, si votre connexion existe déjà, fournissez les informations nécessaires pour le déclencheur.

   Pour cet exemple, fournissez les critères permettant de retourner des articles d’actualités correspondants à partir de la recherche Bing.

   | Propriété | Obligatoire | Value | Description |
   |----------|----------|-------|-------------|
   | Search Query | OUI | <*search-words*> | Entrez les mots clés de recherche à utiliser. |
   | Market | OUI | <*locale*> | Les paramètres régionaux de recherche. La valeur par défaut est « en-US », mais vous pouvez sélectionner une autre valeur. |
   | Safe Search | OUI | <*search-level*> | Le niveau de filtre d’exclusion du contenu pour adultes. La valeur par défaut est « Modéré », mais vous pouvez sélectionner un autre niveau. |
   | Count | Non | <*results-count*> | Retourne le nombre spécifié de résultats. La valeur par défaut est 20, mais vous pouvez spécifier une autre valeur. Le nombre réel de résultats retournés peut être inférieur au nombre spécifié. |
   | Offset | Non | <*skip-value*> | Le nombre de résultats à ignorer avant de retourner les résultats |
   |||||

   Par exemple :

   ![Configurer le déclencheur](./media/connectors-create-api-bing-search/bing-search-trigger.png)

4. Sélectionnez l’intervalle et la fréquence à laquelle vous souhaitez que le déclencheur recherche des résultats.

5. Une fois terminé, dans la barre d’outils du concepteur, choisissez **Enregistrer**.

6. Continuez maintenant à ajouter une ou plusieurs actions à votre application logique pour les tâches à effectuer avec les résultats du déclencheur.

<a name="add-action"></a>

## <a name="add-a-bing-search-action"></a>Ajouter une action de recherche Bing

Dans Azure Logic Apps, une [action](../logic-apps/logic-apps-overview.md#logic-app-concepts) est une étape de votre flux de travail qui suit un déclencheur ou une autre action. Pour cet exemple, l’application logique démarre avec un déclencheur de recherche Bing qui retourne des articles d’actualités correspondant au critère spécifié.

1. Dans le portail Azure ou Visual Studio, ouvrez votre application logique dans le Concepteur d’applications logiques. Cet exemple utilise le portail Azure.

2. Sous le déclencheur ou l’action, choisissez **Nouvelle étape** > **Ajouter une action**.

   Cet exemple utilise ce déclencheur :

   **Bing Search – On new news article**.

   ![Ajouter une action](./media/connectors-create-api-bing-search/add-action.png)

   Pour ajouter une action entre des étapes, déplacez votre souris sur la flèche de connexion. 
   Cliquez sur le signe plus ( **+** ) qui s’affiche, puis choisissez **Ajouter une action**.

3. Dans la zone de recherche, entrez « recherche Bing » comme filtre.
Dans la liste des actions, sélectionnez l’action souhaitée.

   Cet exemple utilise cette action :

   **Bing Search – List news by query**.

   ![Rechercher l’action de recherche Bing](./media/connectors-create-api-bing-search/bing-search-select-action.png)

4. Si vous êtes invité à entrer les informations de connexion, [créez votre connexion de recherche Bing maintenant](#create-connection). Ou bien, si votre connexion existe déjà, fournissez les informations nécessaires pour l’action.

   Pour cet exemple, fournissez les critères permettant de retourner un sous-ensemble des résultats du déclencheur.

   | Propriété | Obligatoire | Value | Description |
   |----------|----------|-------|-------------|
   | Search Query | OUI | <*search-expression*> | Entrez une expression pour interroger les résultats du déclencheur. Vous pouvez sélectionner à partir des champs dans la liste de contenu dynamique, ou créer une expression avec le Générateur d’expressions. |
   | Market | OUI | <*locale*> | Les paramètres régionaux de recherche. La valeur par défaut est « en-US », mais vous pouvez sélectionner une autre valeur. |
   | Safe Search | OUI | <*search-level*> | Le niveau de filtre d’exclusion du contenu pour adultes. La valeur par défaut est « Modéré », mais vous pouvez sélectionner un autre niveau. |
   | Count | Non | <*results-count*> | Retourne le nombre spécifié de résultats. La valeur par défaut est 20, mais vous pouvez spécifier une autre valeur. Le nombre réel de résultats retournés peut être inférieur au nombre spécifié. |
   | Offset | Non | <*skip-value*> | Le nombre de résultats à ignorer avant de retourner les résultats |
   |||||

   Par exemple, supposez que vous souhaitez obtenir les résultats dont le nom de la catégorie inclut le mot « tech » (technique).

   1. Cliquez dans la zone **Requête de recherche** pour afficher la liste de contenu dynamique. 
   Dans cette liste, choisissez **Expression** pour que le Générateur d’expressions apparaisse. 

      ![Déclencheur de recherche Bing](./media/connectors-create-api-bing-search/bing-search-action.png)

      Vous pouvez à présent démarrer la création de votre expression.

   2. Dans la liste de fonctions, sélectionnez la fonction **contains()** , qui apparaît dans la zone d’expression. Cliquez sur **Contenu dynamique** afin que la liste de champs s’affiche de nouveau, mais assurez-vous que le curseur reste à l’intérieur des parenthèses.

      ![Sélectionner une fonction](./media/connectors-create-api-bing-search/expression-select-function.png)

   3. Dans la liste de champs, sélectionnez **Catégorie**, qui se convertit en paramètre. 
   Ajoutez une virgule après le premier paramètre, et après la virgule, ajoutez ce mot : `'tech'` 

      ![Sélectionner un champ](./media/connectors-create-api-bing-search/expression-select-field.png)

   4. Une fois que vous avez terminé, sélectionnez **OK**.

      À présent, l’expression apparaît dans la zone **Requête de recherche** au format suivant :

      ![Expression terminée](./media/connectors-create-api-bing-search/resolved-expression.png)

      En mode Code, cette expression s’affiche au format suivant :

      `"@{contains(triggerBody()?['category'],'tech')}"`

5. Une fois terminé, dans la barre d’outils du concepteur, choisissez **Enregistrer**.

<a name="create-connection"></a>

## <a name="connect-to-bing-search"></a>Se connecter à la recherche Bing

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Lorsque vous êtes invité à entrer des informations de connexion, fournissez les précisions suivantes :

   | Propriété | Obligatoire | Value | Description |
   |----------|----------|-------|-------------|
   | Nom de connexion | OUI | <*connection-name*> | Nom à créer pour votre connexion |
   | Version de l'API | OUI | <*API-version*> | Par défaut, la version de l’API Recherche Bing est définie sur la version actuelle. Vous pouvez sélectionner une version antérieure si nécessaire. |
   | Clé de l’API | OUI | <*API-key*> | Clé API Recherche Bing obtenue précédemment. Si vous n’avez pas de clé, procurez-vous votre [clé API maintenant](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api). |  
   |||||  

   Par exemple :

   ![Créer une connexion](./media/connectors-create-api-bing-search/bing-search-create-connection.png)

2. Lorsque vous êtes prêt, choisissez **Créer**.

## <a name="connector-reference"></a>Référence de connecteur

Pour plus d’informations techniques, notamment sur les déclencheurs, les actions et les limites, comme le décrit le fichier OpenAPI (anciennement Swagger) du connecteur, voir la [page de référence du connecteur](/connectors/bingsearch/).

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)
