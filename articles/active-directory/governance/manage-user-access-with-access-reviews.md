---
title: Gérer l’accès utilisateur avec les révisions d’accès - Azure Active Directory | Microsoft Docs
description: Découvrez comment gérer les accès utilisateurs en tant qu’appartenance à un groupe ou en tant qu’affectation à une application avec les révisions d’accès Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d873f1dace3e3be6a8767e77d57db5ac554afd7
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66473806"
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Gérer les accès des utilisateurs avec les révisions d’accès Azure AD

Avec Azure Active Directory (Azure AD), vous pouvez facilement vous assurer que les utilisateurs ont un accès approprié. Vous pouvez demander directement aux utilisateurs ou bien à un décisionnaire de prendre part à une révision d’accès et de renouveler la certification (ou d’effectuer une « attestation ») pour l’accès utilisateur. Les réviseurs peuvent donner leur avis sur les besoins de chaque utilisateur en matière d’accès continu, en fonction des suggestions d’Azure AD. Lorsqu’une révision d’accès est terminée, vous pouvez effectuer des modifications et supprimer l’accès des utilisateurs qui n’en ont plus besoin.

> [!NOTE]
> Si vous souhaitez examiner uniquement les accès des utilisateurs invités, plutôt que tous les types d’accès utilisateurs, consultez [Gestion des accès utilisateurs invités avec les révisions d’accès](manage-guest-access-with-access-reviews.md). Si vous voulez examiner l’appartenance d’un utilisateur à un rôle d’administrateur tel que le rôle Administrateur général, consultez [Démarrer une révision d’accès dans Azure AD Privileged Identity Management](../privileged-identity-management/pim-how-to-start-security-review.md).

## <a name="prerequisites"></a>Conditions préalables

- Azure AD Premium P2

Pour plus d’informations, consultez [les utilisateurs doivent disposer de licences ?](access-reviews-overview.md#which-users-must-have-licenses).

## <a name="create-and-perform-an-access-review"></a>Créer et exécuter une révision d’accès

Vous pouvez avoir un ou plusieurs utilisateurs comme réviseurs dans une révision d’accès.  

1. Sélectionnez un groupe dans Azure AD comprenant un ou plusieurs membres. Ou bien sélectionnez une application connectée à Azure AD ayant un ou plusieurs utilisateurs attribués. 

2. Décidez si vous souhaitez que chaque utilisateur révise son propre accès, ou si vous voulez qu’un ou plusieurs utilisateurs révisent l’accès de tout le monde.

3. En tant qu’un administrateur général ou un utilisateur administrateur, accédez à la [page de gouvernance des identités](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

4. Créer la révision d’accès. Pour plus d’informations, consultez [créer une révision d’accès des groupes ou des applications](create-access-review.md).

5. Lorsque la révision d’accès démarre, demandez aux réviseurs de donner leur avis. Par défaut, ils reçoivent un message électronique à partir d’Azure AD avec un lien du volet d’accès, où ils [réviser l’accès à des groupes ou des applications](perform-access-review.md).

6. Si les réviseurs n’ont pas donné leur avis, vous pouvez demander à Azure AD de leur envoyer un rappel. Par défaut, Azure AD envoie automatiquement un rappel à mi-parcours de l’échéance fixée pour les réviseurs n’ayant pas encore répondu.

7. Une fois que les réviseurs ont fait part de leur avis, arrêtez la révision d’accès et appliquez les modifications. Pour plus d’informations, consultez [effectuer une révision d’accès des groupes ou des applications](complete-access-review.md).


## <a name="next-steps"></a>Étapes suivantes

[Créer une révision d’accès des groupes ou des applications](create-access-review.md)




