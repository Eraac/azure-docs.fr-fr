---
title: Fichier Include
description: Fichier Include
services: digital-twins
author: alinamstanciu
ms.service: digital-twins
ms.topic: include
ms.date: 06/26/2019
ms.author: alinast
ms.custom: include file
ms.openlocfilehash: 9a5b3b04287a8b732d01bd8fe4610e073332da0d
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67479122"
---
1. Dans le [portail Azure](https://portal.azure.com), ouvrez **Azure Active Directory** dans le panneau de gauche, puis le volet **Propriétés**. Copiez l’**ID de répertoire** dans un fichier temporaire. Cette valeur vous permettra de configurer un exemple d’application dans la section suivante.

    ![ID d’annuaire Azure Active Directory](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png)

1. Dans le [portail Microsoft Azure](https://portal.azure.com), ouvrez **Azure Active Directory** dans le panneau de gauche, puis ouvrez le volet **Inscriptions d’applications (hérité)** . Cliquez sur le bouton **Nouvelle inscription d’application**.

1. Dans la zone **Nom**, donnez un nom convivial à l’inscription de cette application. Choisissez **Type d’application** comme **Natif** et **URI de redirection** comme `https://microsoft.com`. Sélectionnez **Créer**.

    ![Volet de création](./media/digital-twins-permissions-legacy/aad-app-reg-create.png)

1.  Ouvrez l’application inscrite, puis copiez la valeur du champ **ID d’application** dans un fichier temporaire. Cette valeur identifie votre application Azure Active Directory. L’ID d’application vous permettra de configurer votre exemple d’application dans les sections suivantes.

    ![ID d’application Azure Active Directory](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png)

1. Ouvrez votre volet des inscriptions d’applications. Sélectionnez **Paramètres** > **Autorisations requises**, puis :

   a. Sélectionnez **Ajouter** en haut à gauche pour ouvrir le volet **Ajouter un accès d’API**.

   b. Sélectionnez **Sélectionner une API** et recherchez **Azure Digital Twins**. Si votre recherche ne trouve pas cette API, sélectionnez **Azure Smart Spaces** à la place.

   c. Sélectionnez l’option **Azure Digital Twins (Azure Smart Spaces Service)** et choisissez **Sélectionner**.

   d. Choisissez **Sélectionner des autorisations**. Cochez la case des autorisations déléguées **Accès en lecture/écriture** et choisissez **Sélectionner**.

   e. Sélectionnez **Terminé** dans le volet **Ajouter un accès d’API**.

   f. Dans le volet **Autorisations requises**, sélectionnez le bouton **Accorder des autorisations**, puis acceptez l’accusé réception qui s’affiche. Si l’autorisation n’est pas accordée pour cette API, contactez l’administrateur.

      ![Volet des autorisations requises](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png)

 