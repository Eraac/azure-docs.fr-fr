---
title: Créer un projet Azure Notebooks avec un environnement personnalisé
description: Créez un projet dans Azure Notebooks configuré avec un ensemble spécifique de packages installés et de scripts de démarrage.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: e049f591-27a7-440f-a1a3-c5bef25e8a28
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 954bbc22e42865991ca7f38fbf3f6f0e8e78a437
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754030"
---
# <a name="quickstart-create-a-project-with-a-custom-environment"></a>Démarrage rapide : Créer un projet avec un environnement personnalisé

Dans Azure Notebooks, un projet est une collection de fichiers : notebooks, fichiers de données, documentation, images et ainsi de suite, dans un environnement qui peut être configuré avec des commandes de configuration spécifiques. En définissant l’environnement avec le projet, tout utilisateur clonant le projet dans son propre compte Azure Notebooks dispose de toutes les informations dont il a besoin pour recréer l’environnement nécessaire.

## <a name="create-a-project"></a>Création d’un projet

1. Accédez à [Azure Notebooks](https://notebooks.azure.com) et connectez-vous. (Pour plus d’informations, consultez la rubrique [Démarrage rapide sur la connexion à Azure Notebooks](quickstart-sign-in-azure-notebooks.md).)

1. En haut de votre page de profil public, sélectionnez **Mes projets** :

    ![Lien Mes projets en haut de la fenêtre du navigateur](media/quickstarts/my-projects-link.png)

1. Dans la page **Mes projets**, sélectionnez **+ Nouveau projet** (raccourci clavier : n). Le bouton peut apparaître uniquement sous la forme **+** si la fenêtre du navigateur est étroite :

    ![Commande Nouveau projet dans la page Mes projets](media/quickstarts/new-project-command.png)

1. Dans la fenêtre contextuelle **Créer un projet** qui s’affiche, entrez ou définissez les détails suivants, puis sélectionnez **Créer** :

    - **Nom du projet** : Projet avec un environnement personnalisé
    - **ID de projet** : project-custom-environment
    - **Projet public** : (désactivé)
    - **Créer un fichier README.md** : (désactivé)

1. Après quelques instants, Azure Notebooks accède au nouveau projet. Pour ajouter un notebook au projet, sélectionnez la liste déroulante **+ Nouveau** (qui peut apparaître seulement sous la forme **+** ), puis **Notebook**.

1. Donnez au notebook un nom comme *Custom environment.ipynb*, sélectionnez **Python 3.6** comme langage, puis sélectionnez **Nouveau**.

## <a name="add-a-custom-setup-step"></a>Ajouter une étape d’installation personnalisée

1. Dans la page de projet, sélectionnez **Paramètres du projet**.

    ![Commande des paramètres du projet](media/quickstarts/project-settings-command.png)

1. Dans la fenêtre contextuelle **Paramètres du projet**, sélectionnez l’onglet **Environnement**, puis sous **Environment Setup Steps** (Étapes de configuration de l’environnement), sélectionnez **+ Ajouter** :

    ![Commande d’ajout d’une nouvelle étape de configuration de l’environnement](media/quickstarts/environment-add-command.png)

1. La commande **+ Ajouter** permet de créer une étape définie par une opération et un fichier cible sélectionné parmi les fichiers de votre projet. Les opérations suivantes sont prises en charge :

    | Opération | Description |
    | --- | --- |
    | Requirements.txt | Les projets Python définissent leurs dépendances dans un fichier requirements.txt. Avec cette option, sélectionnez le fichier approprié dans la liste des fichiers du projet, et choisissez également la version de Python dans la liste déroulante qui s’affiche. Si nécessaire, sélectionnez **Annuler** pour revenir au projet, charger ou créer le fichier, puis revenez à l’onglet **Paramètres du projet** > **Environnement** et créez une nouvelle étape. Avec cette étape, l’exécution d’un notebook dans le projet exécute automatiquement `pip install -r <file>` |
    | Script shell | Permet d’indiquer un script d’interpréteur de commandes bash (généralement un fichier avec l’extension *.sh*) qui contient les commandes que vous souhaitez exécuter pour initialiser l’environnement. |
    | Environment.yml | Un projet Python qui utilise conda pour la gestion d’un environnement utilise un fichier *environments.yml* pour décrire les dépendances. Avec cette option, sélectionnez le fichier approprié dans la liste des fichiers du projet. |

1. Pour supprimer une étape d’installation, sélectionnez la croix **X** à droite de l’étape.

1. Lorsque toutes les étapes d’installation sont en place, sélectionnez **Enregistrer**. (Sélectionnez **Annuler** pour ignorer les modifications).

1. Pour tester votre environnement, créez et exécutez un nouveau notebook, puis créez une cellule de code avec des instructions dépendant d’un package dans l’environnement, comme l’utilisation d’une instruction `import` Python. Si l’instruction réussit, le package nécessaire a été installé avec succès dans l’environnement.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Gérer et configurer des projets dans Azure Notebooks](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [Didacticiel : Créer et exécuter un notebook Jupyter pour effectuer une régression linéaire](tutorial-create-run-jupyter-notebook.md)
