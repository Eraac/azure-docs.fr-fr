---
title: Gérer les états des alertes et des groupes intelligents
description: Gestion des états des instances d’alerte et de groupe intelligent
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: 2aa521b0552b60e5a875a5f46ab9887c6e5b6e3e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60919664"
---
# <a name="manage-alert-and-smart-group-states"></a>Gérer les états des alertes et des groupes intelligents
Les alertes dans Azure Monitor disposent maintenant d’un [état d’alerte et d’une condition de surveillance](https://aka.ms/azure-alerts-overview), même chose pour les groupes intelligents qui ont un [état de groupe intelligent](https://aka.ms/smart-groups). Les changements d’état sont désormais inscrits dans l’historique associé à l’alerte ou au groupe intelligent correspondant. Cet article va vous expliquer comment changer l’état d’une alerte ou d’un groupe intelligent.

## <a name="change-the-state-of-an-alert"></a>Changer l’état d’une alerte
1. Vous pouvez modifier l’état d’une alerte de plusieurs manières différentes : 
    * Dans la page Toutes les alertes, cochez la case en regard des alertes dont vous souhaitez modifier l’état, puis cliquez sur Modifier l'état.   
    ![Surveillance](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * Dans la page Détails de l'alerte d’une instance d’alerte, vous pouvez cliquer sur Modifier l’état   
    ![Surveillance](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * Dans la page Détails de l'alerte d’une instance d’alerte, dans le volet Groupe intelligent, vous pouvez cocher la case en regard des alertes qui vous intéressent.    
    ![Surveillance](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * Dans la page Détails du groupe intelligent, dans la liste des alertes de membre, vous pouvez cocher la case en regard des alertes dont vous souhaitez modifier l’état, puis cliquer sur Modifier l'état.   
    ![Surveillance](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. Lorsque vous cliquez sur Modifier l’état, une fenêtre contextuelle s’ouvre et vous permet de sélectionner l’état (Nouveau/Reconnu/Fermé) ainsi que de saisir un commentaire, si nécessaire.   
![Surveillance](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. Une fois l’opération terminée, le changement d’état est enregistré dans l’historique de l’alerte correspondante. Vous pouvez accéder à cette information en ouvrant la page Détails correspondante et en recherchant dans l’historique.    
![Surveillance](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>Modifier l’état d’un groupe intelligent
1. Vous pouvez modifier l’état d’un groupe intelligent de plusieurs manières différentes :
    1. Dans la page de la liste Groupe intelligent, cochez la case en regard des groupes intelligents dont vous souhaitez modifier l’état, puis cliquez sur Modifier l'état.  
    ![Surveillance](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. Dans la page Détails du groupe intelligent, vous pouvez cliquer sur Modifier l’état.        
    ![Surveillance](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. Lorsque vous cliquez sur Modifier l’état, une fenêtre contextuelle s’ouvre et vous permet de sélectionner l’état (Nouveau/Reconnu/Fermé) ainsi que de saisir un commentaire, si nécessaire. 
![Surveillance](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  La modification de l'état d'un groupe intelligent ne modifie pas l'état des alertes de membre individuelles.

1. Une fois l’opération terminée, le changement d’état est enregistré dans l’historique du groupe intelligent correspondant. Vous pouvez accéder à cette information en ouvrant la page Détails correspondante et en recherchant dans l’historique.     
![Surveillance](./media/alerts-managing-alert-states/state-sg-history.jpg)

