---
title: Désactiver la vérification par e-mail lors de l’inscription du consommateur dans Azure Active Directory B2C | Microsoft Docs
description: Rubrique expliquant comment désactiver la vérification par e-mail lors de l’inscription du consommateur à Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8b50b59b6a1f99787b842923cd6ec77ee6500f0a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66509530"
---
# <a name="disable-email-verification-during-consumer-sign-up-in-azure-active-directory-b2c"></a>Désactiver la vérification par e-mail lors de l’inscription du consommateur à Azure Active Directory B2C 
Lorsqu'il est activé, Azure Active Directory (Azure AD) B2C permet à un consommateur de s’inscrire à des applications en fournissant une adresse e-mail et en créant un compte local. Azure Active Directory B2C assure la validité des adresses e-mail en demandant aux consommateurs de les vérifier pendant le processus d’inscription. Cela empêche un processus malveillant automatisé de générer des faux comptes pour les applications.

Certains développeurs d’applications préfèrent ignorer la vérification par e-mail pendant le processus d’inscription et demander aux consommateurs de vérifier l’adresse e-mail ultérieurement. Pour ce faire, Azure Active Directory B2C peut être configuré afin de désactiver la vérification par e-mail. Cette opération crée un processus de connexion plus simple et offre aux développeurs la possibilité de différencier les consommateurs qui ont vérifié leur e-mail de ceux qui ne l'ont pas encore fait.

Par défaut, la vérification est activée pour les flux d’utilisateur d’inscription. Pour la désactiver, procédez comme suit :

1. Cliquez sur **flux d’utilisateur**.
2. Ouvrez votre flux d’utilisateur (par exemple, « B2C_1_SiUp ») en cliquant dessus. 
3. Cliquez sur **Mises en page**.
4. Cliquez sur **page d’inscription à un compte Local**.
5. Cliquez sur **Adresse e-mail** dans la colonne **Nom** sous la section **Attributs d’utilisateur**.
6. Sous **Requiert une vérification**, sélectionnez **Non**.
7. Cliquez sur **Enregistrer** dans la partie supérieure du panneau. Vous avez terminé !

> [!NOTE]
> La désactivation de la vérification par e-mail dans le processus d’inscription peut entraîner la réception de courrier indésirable. Si vous désactivez la vérification par défaut, nous vous recommandons d’ajouter votre propre système de vérification.
> 
>
