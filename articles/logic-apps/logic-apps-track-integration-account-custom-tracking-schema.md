---
title: Schémas de suivi personnalisés pour les messages B2B - Azure Logic Apps | Microsoft Docs
description: Créer des schémas de suivi personnalisés qui surveillent les messages B2B dans les comptes d’intégration pour Azure Logic Apps avec Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 433ae852-a833-44d3-a3c3-14cca33403a2
ms.date: 01/27/2017
ms.openlocfilehash: 76a9ece9e925543e856136a798a60038316caad9
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203038"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-apps"></a>Créer des schémas de suivi personnalisé qui surveillent des flux de travail de bout en bout dans Azure Logic Apps

Vous pouvez activer un suivi intégré pour les différentes parties de votre flux de travail d’entreprise à entreprise, notamment le suivi des messages AS2 ou X12. Lorsque vous créez des flux de travail qui incluent une application logique, BizTalk Server, SQL Server ou n’importe quelle autre couche, vous pouvez activer un suivi personnalisé qui consigne les événements du début à la fin de votre flux de travail. 

Cet article fournit un code personnalisé que vous pouvez utiliser dans les couches en dehors de votre application logique. 

## <a name="custom-tracking-schema"></a>Schéma de suivi personnalisé

```json
{
   "sourceType": "",
   "source": {
      "workflow": {
         "systemId": ""
      },
      "runInstance": {
         "runId": ""
      },
      "operation": {
         "operationName": "",
         "repeatItemScopeName": "",
         "repeatItemIndex": "",
         "trackingId": "",
         "correlationId": "",
         "clientRequestId": ""
      }
   },
   "events": [
      {
         "eventLevel": "",
         "eventTime": "",
         "recordType": "",
         "record": {                
         }
      }
   ]
}
```

| Propriété | Obligatoire | Type | Description |
| --- | --- | --- | --- |
| sourceType | OUI |   | Type de source d’exécution. Les valeurs autorisées sont **Microsoft.Logic/workflows** et **custom**. |
| source | OUI |   | Si le type de source est **Microsoft.Logic/workflows**, les informations source doivent suivre ce schéma. Si le type de source est **custom**, le schéma est un JToken. |
| systemId | OUI | Chaîne | ID système d’application logique. |
| runId | OUI | Chaîne | ID d’exécution d’application logique. |
| operationName | OUI | Chaîne | Nom de l’opération (par exemple action ou déclencheur). |
| repeatItemScopeName | OUI | Chaîne | Répéter le nom de l’élément si l’action se trouve au sein d’une boucle `foreach`/`until`. |
| repeatItemIndex | OUI | Entier | Indique si l’action se trouve au sein d’une boucle `foreach`/`until`. Indique l’index de l’élément répété. |
| trackingId | Non | Chaîne | ID de suivi permettant de corréler les messages. |
| correlationId | Non | Chaîne | ID de corrélation permettant de corréler les messages. |
| clientRequestId | Non | Chaîne | Le client peut remplir ce champ pour corréler les messages. |
| eventLevel | OUI |   | Niveau de l’événement. |
| eventTime | OUI |   | Heure de l’événement au format UTC AAAA-MM-JJTHH:MM:SS.00000Z. |
| recordType | OUI |   | Type de l’enregistrement de suivi. La valeur autorisée est **Custom**. |
| record | OUI |   | Type d'enregistrement personnalisé. Le format autorisé est JToken. |
||||

## <a name="b2b-protocol-tracking-schemas"></a>Schémas de suivi de protocole B2B

Pour plus d’informations sur les schémas de suivi de protocole B2B, consultez :

* [Schémas de suivi AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Schémas de suivi X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [la surveillance des messages B2B](logic-apps-monitor-b2b-message.md)
* En savoir plus sur le [suivi des messages B2B dans les journaux Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)
