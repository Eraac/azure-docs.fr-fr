---
title: Pipeline CI/CD avec Azure DevOps Projects - Azure IoT Edge | Microsoft Docs
description: Azure DevOps Projects facilite la prise en main d’Azure. Il vous aide à lancer une application Azure IoT Edge de votre choix en quelques étapes rapides.
author: shizn
manager: ''
ms.author: xshi
ms.date: 07/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 109a7e327217a342f485dd61b53115569f2346cd
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722981"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects"></a>Créer un pipeline CI/CD pour IoT Edge avec Azure DevOps Projects

Configurez l’intégration continue (CI) et la livraison continue (CD) pour votre application IoT Edge avec Azure DevOps Projects. DevOps Projects simplifie la configuration initiale d’un pipeline de build et de mise en production dans Azure Pipelines.

Si vous n’avez pas d’abonnement Azure actif, créez un [compte gratuit](https://azure.microsoft.com/free) avant de commencer.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Azure DevOps Projects crée un pipeline CI/CD dans Azure DevOps. Vous pouvez créer une organisation Azure DevOps ou utiliser une organisation existante. DevOps Projects crée également des ressources Azure dans l’abonnement Azure de votre choix.

1. Connectez-vous au [portail Microsoft Azure](https://portal.azure.com).

1. Dans le volet gauche, sélectionnez **Créer une ressource**, puis recherchez **DevOps Projects**.  

1.  Sélectionnez **Créer**.

## <a name="create-a-new-application-pipeline"></a>Créer un pipeline d'application 

1. Vos modules Azure IoT Edge peuvent être écrits en [C#](tutorial-csharp-module.md), [Node.js](tutorial-node-module.md), [Python](tutorial-python-module.md), [C](tutorial-c-module.md) et [Java](tutorial-java-module.md). Sélectionnez votre langage préféré pour démarrer une nouvelle application : **.NET**, **Node.js**, **Python**, **C**, ou **Java**. Sélectionnez **Suivant** pour continuer.

   ![Sélectionner le langage pour créer une application](./media/how-to-devops-project/select-language.png)

2. Sélectionnez **IoT simple** en tant que framework d’application, puis **Suivant**.

   ![Sélectionner le framework IoT simple](media/how-to-devops-project/select-iot.png)

3. Sélectionnez **IoT Edge** en tant que service Azure pour déployer votre application, puis **Suivant**.

   ![Sélectionner le service IoT Edge](media/how-to-devops-project/select-iot-edge.png)

4. Créez gratuitement une organisation Azure DevOps ou choisissez une organisation existante.

   1. Entrez un nom pour votre projet. 

   2. Sélectionnez votre organisation Azure DevOps. En l'absence d'organisation existante, sélectionnez **Paramètres supplémentaires** pour en créer une. 

   3. Sélectionnez votre abonnement Azure.

   4. Utilisez le nom IoT Hub généré par le nom de votre projet ou fournir votre propre nom.

   5. Sélectionnez **Paramètres supplémentaires** pour configurer les ressources Azure créées par DevOps Projects.

   6. Sélectionnez **Terminé** pour finir de créer votre projet. 

   ![Nommer et créer l’application](media/how-to-devops-project/select-devops.png)

Après quelques minutes, le tableau de bord DevOps Projects s’affiche dans le portail Azure. Sélectionnez le nom de votre projet pour afficher la progression. Il peut être nécessaire d’actualiser la page. Un exemple d’application IoT Edge est configuré dans un dépôt de votre organisation Azure DevOps, une build est exécutée et votre application est déployée sur l’appareil IoT Edge. Ce tableau de bord donne une visibilité sur votre dépôt de code, le pipeline CI/CD et votre application dans Azure.

   ![Afficher l’application dans le portail DevOps](./media/how-to-devops-project/devops-portal.png)


## <a name="commit-code-changes-and-execute-cicd"></a>Valider les modifications de code et exécuter CI/CD

DevOps Projects crée un dépôt Git pour votre projet dans Azure Repos. Dans cette section, vous affichez le dépôt et modifiez le code dans votre application.

1. Pour accéder au référentiel créé pour votre projet, sélectionnez **Référentiels** dans le menu du tableau de bord de votre projet.  

   ![Afficher le référentiel généré dans Azure Repos](./media/how-to-devops-project/view-repositories.png)

2. Les étapes suivantes expliquent comment utiliser le navigateur web pour apporter des modifications au code. Si vous préférez cloner localement votre référentiel, sélectionnez **Cloner** dans la partie supérieure droite de la fenêtre. Utilisez l’URL fournie pour cloner votre référentiel Git dans Visual Studio Code ou votre outil de développement préféré. 

3. Le référentiel contient déjà du code pour un module appelé **SampleModule** basé sur le langage de l’application que vous avez sélectionné lors du processus de création. Ouvrez le fichier **modules/SampleModule/module.json**.

   ![Ouvrir le fichier module.json dans Azure Repos](./media/how-to-devops-project/open-module-json.png)

4. Sélectionnez **Modifier**, puis modifiez `"version"` sous `"tag"`. Par exemple, vous pouvez la changer en `"version": "${BUILD_BUILDID}"` pour utiliser des [variables de build Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) dans le cadre de votre étiquette d’image de module Azure IoT Edge.

   ![Modifier la version de façon à accepter des variables de build](media/how-to-devops-project/update-module-json.png)

5. Sélectionnez **Valider**, puis enregistrez vos modifications.

6. Dans votre navigateur, revenez à votre tableau de bord DevOps Projects dans le portail Azure. Vous devez maintenant voir qu’une build est en cours. Les modifications que vous avez apportées font automatiquement l’objet d’une build et d’un déploiement via un pipeline CI/CD.

    ![Afficher l’état en cours](media/how-to-devops-project/ci-cd-in-progress.png)

## <a name="examine-the-cicd-pipeline"></a>Examiner le pipeline CI/CD

Dans les précédentes sections, Azure DevOps Projects a configuré automatiquement un pipeline CI/CD complet pour votre application IoT Edge. Vous avez ensuite testé ce pipeline de build en validant les modifications apportées à un des fichiers. À présent, explorez et personnalisez le pipeline selon vos besoins. Pour vous familiariser avec les pipelines de build et de mise en production d’Azure DevOps, effectuez les étapes suivantes.

1. Pour afficher les pipelines de build dans votre projet DevOps, sélectionnez **Générer des pipelines** dans le menu du tableau de bord de votre projet. Ce lien ouvre un onglet du navigateur et le pipeline de build Azure DevOps pour votre nouveau projet.

   ![Afficher, des pipelines de build dans Azure Pipelines](./media/how-to-devops-project/view-build-pipelines.png)

2. Sélectionnez **Modifier**.

    ![Modifier le pipeline de build](media/how-to-devops-project/click-edit-button.png)

3. Dans le panneau qui s’ouvre, vous pouvez examiner les tâches qui interviennent lors de l'exécution de votre pipeline de build. Le pipeline de build effectue différentes tâches, comme la récupération (fetch) de sources dans le dépôt Git, la génération d’images de module IoT Edge, la publication de modules IoT Edge vers un registre de conteneurs et la publication des sorties utilisées pour les déploiements. Pour plus d’informations sur les tâches Azure IoT Edge dans Azure DevOps, consultez [Configurer Azure Pipelines pour l’intégration continue](how-to-ci-cd.md#configure-continuous-integration).

4. Sélectionnez l'en-tête **Pipeline** en-tête en haut du pipeline de build pour ouvrir les détails correspondants. Remplacez le nom de votre pipeline de build par un nom plus descriptif.

   ![Modifier les détails du pipeline](./media/how-to-devops-project/edit-build-pipeline.png)

5. Sélectionnez **Enregistrer et mettre en file d’attente**, puis **Enregistrer**.

6. Dans le menu de votre pipeline de build, sélectionnez **Déclencheurs**. DevOps Projects a créé automatiquement un déclencheur CI, et chaque validation dans le dépôt lance une nouvelle build.  Vous pouvez éventuellement choisir d’inclure ou d’exclure des branches dans le processus d’intégration continue.

7. Sélectionnez **Rétention**. En fonction de votre scénario, vous pouvez spécifier des stratégies pour conserver ou supprimer un certain nombre de builds.

8. Sélectionnez **Historique**. Le volet Historique contient une piste d’audit des modifications que vous avez apportées récemment à la build. Azure Pipelines gère les modifications apportées au pipeline de build et vous permet de comparer les versions.

9. Après avoir exploré le pipeline de build, accédez au pipeline de mise en production correspondant. Sélectionnez **Mises en production** sous **Pipelines**, puis **Modifier** pour afficher les détails du pipeline.

    ![Afficher le pipeline de mise en production](media/how-to-devops-project/release-pipeline.png)

10. Sous **Artefacts**, sélectionnez **Déposer**. Le pipeline de build que vous avez examiné aux étapes précédentes correspond à la source que surveille l’artefact. 

11. En regard de l’icône **Déposer**, sélectionnez le **déclencheur de déploiement continu** qui ressemble à un éclair. Ce pipeline de mise en production a activé un déclencheur, qui effectue un déploiement chaque fois qu’un nouvel artefact de build est disponible. Si vous le souhaitez, vous pouvez désactiver le déclencheur afin que vos déploiements nécessitent une exécution manuelle.  

12. Dans le menu de votre pipeline de mise en production, sélectionnez **Tâches**, puis la phase **dev** dans la liste déroulante. DevOps Projects a mis en place une phase de mise en production qui crée un IoT hub, crée un appareil IoT Edge dans ce hub, déploie l'exemple de module provenant du pipeline de build et configure une machine virtuelle à exécuter en tant qu'appareil IoT Edge. Pour plus d’informations sur les tâches Azure IoT Edge pour le déploiement continu, consultez [Configurer Azure Pipelines pour le déploiement continu](how-to-ci-cd.md#configure-continuous-deployment).

    ![Afficher les tâches de déploiement continu](media/how-to-devops-project/dev-release.png)

13. Sur la droite, sélectionnez **Afficher les mises en production**. Cette vue affiche un historique des mises en production.

14. Sélectionnez le nom d’une mise en production pour afficher plus d’informations.


## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’en avez plus besoin, vous pouvez supprimer Azure App Service et les autres ressources associées que vous avez créées. Utilisez la fonctionnalité **Supprimer** du tableau de bord DevOps Projects.

## <a name="next-steps"></a>Étapes suivantes
* Découvrez plus d’informations sur les tâches pour Azure IoT Edge sur Azure DevOps dans [Intégration continue et déploiement continu sur Azure IoT Edge](how-to-ci-cd.md)
* Pour plus d’informations sur le déploiement IoT Edge, consultez [Comprendre les déploiements IoT Edge pour les appareils uniques ou à l’échelle](module-deployment-monitoring.md).
* Suivez les étapes pour créer, mettre à jour ou supprimer un déploiement dans [Déployer et surveiller les modules IoT Edge à grande échelle](how-to-deploy-monitor.md).
