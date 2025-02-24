---
title: Utilisation des méthodes d’authentification et de création de rapport des informations (préversion) - Azure Active Directory
description: Création de rapports sur la réinitialisation du mot de passe en libre-service d’Azure AD et l’utilisation de la méthode d’authentification par authentification multi-facteurs
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0f6a74308f1bc4a7b77576fb9f39f965de0a4f8
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561026"
---
# <a name="authentication-methods-usage--insights-preview"></a>Utilisation de méthodes d’authentification et informations (préversion)

L’utilisation et les informations vous permet de comprendre comment les méthodes d’authentification pour des fonctionnalités comme l’authentification multi-facteurs Azure et la réinitialisation des mots de passe en libre-service fonctionnent dans votre organisation. Cette fonctionnalité de création de rapports permet à votre organisation de comprendre quelles méthodes sont enregistrées et comment elles sont utilisées.

## <a name="permissions-and-licenses"></a>Autorisations et licences

Les rôles suivants peuvent accéder à l’utilisation et aux informations :

- Administrateur général
- Lecteur de sécurité
- Security Administrator
- Lecteur de rapports

Aucune licence supplémentaire n’est nécessaire pour l’utilisation de l’utilisation et des informations. Les informations de licence Azure Multi-Factor Authentication et de réinitialisation de mot de passe en libre-service sont disponibles sur le [Site de tarification d’Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Fonctionnement

Pour accéder aux informations et à l’utilisation de la méthode d’authentification :

1. Accédez au [portail Azure](https://portal.azure.com).
1. Accédez à **Azure Active Directory** > **Réinitialisation du mot de passe** > **Utilisation et informations**.
1. À partir de la vue d’ensemble **Inscription** ou **Utilisation**, vous pouvez choisir d’ouvrir des rapports préfiltrés ou les filtrer vous-même selon vos besoins.

![Vue d’ensemble de l’utilisation et des informations](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

Pour accéder directement à l’utilisation et aux informations, accédez à [https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade). Ce lien vous mène à la vue d’ensemble de l’inscription.

Les vignettes Utilisateurs inscrits, Utilisateurs activés et utilisateurs compatibles affichent les données d’inscription suivantes pour vos utilisateurs :

- Inscrit : Un utilisateur est considéré comme inscrit s’il (ou un administrateur) a enregistré suffisamment de méthodes d’authentification pour respecter la politique d’authentification SSPR ou d’authentification multi-facteurs de votre organisation.
- Activé : Un utilisateur est considéré comme activé s’il est dans le champ d’application de la politique SSPR. Si le SSPR est activé pour un groupe, alors l’utilisateur est considéré comme activé s’il est dans ce groupe. Si le SSPR est activé pour tous les utilisateurs, alors tous les utilisateurs du locataire (à l’exclusion des invités) sont considérés comme activés.
- Compatible : Un utilisateur est considéré comme compatible s’il est à la fois enregistré et activé. Cet état signifie qu’il peut exécuter le SSPR à tout moment si nécessaire.

En cliquant sur l’une de ces vignettes ou sur les informations qu’elles contiennent, vous obtiendrez une liste pré-filtrée de détails sur les inscriptions.

Le graphique **Inscriptions** de l’onglet **Inscriptions** indique le nombre d’inscriptions de méthodes d’authentification réussis ou échoués par méthode d’authentification. Le graphique **Réinitialisations** de l’onglet **Utilisation** indique le nombre d’authentifications réussies et échouées pendant le flux de réinitialisation du mot de passe par méthode d’authentification.

En cliquant sur l’un ou l’autre des graphiques, vous obtiendrez une liste pré-filtrée d’événements d’inscription ou de réinitialisation.

A l’aide de la commande située dans le coin supérieur droit, vous pouvez remplacer la valeur de la plage de dates pour les données d’audit affichées dans les graphiques Inscriptions et Réinitialisations par 24 heures, 7 jours ou 30 jours.

Données d’inscription à partir des 

### <a name="registration-details"></a>détails de l’inscription

En cliquant sur les vignettes ou les informations **Utilisateurs inscrits**, **Utilisateurs activés**, ou **Utilisateurs compatibles**, vous accédez aux détails de l’inscription.

Le rapport détaillé de l’inscription affiche les informations suivantes pour chaque utilisateur :

- Nom
- Nom d'utilisateur
- État de l’inscription (Tous, inscrits, Pas inscrits)
- État de l’activation (Tous, Activé, Pas activé)
- État de compatibilité (Tous, Compatible, Pas compatible)
- Méthodes (Notification de l’application, Code de l’application, Appel téléphonique, SMS, Email, Questions de sécurité)

En utilisant les contrôles en haut de la liste, vous pouvez rechercher un utilisateur et filtrer la liste des utilisateurs en fonction des colonnes affichées.

### <a name="reset-details"></a>Détails de réinitialisation

En cliquant sur les graphiques Inscriptions ou Réinitialisations, vous accédez aux détails de la réinitialisation.

Le rapport Détails de réinitialisation montre les événements d’inscription et de réinitialisation des 30 derniers jours, y compris :

- Nom
- Nom d'utilisateur
- Fonctionnalité (Toutes, Inscription, Réinitialisation)
- Méthodes d’authentification (Notification de l’application, Code de l’application, Appel téléphonique, Appel au bureau, SMS, Email, Questions de sécurité)
- État (Tous, Réussite, Échec)

En utilisant les contrôles en haut de la liste, vous pouvez rechercher un utilisateur et filtrer la liste des utilisateurs en fonction des colonnes affichées.

## <a name="limitations"></a>Limites

Les données présentées dans ces rapports seront différées jusqu’à 60 minutes. Le Portail Microsoft Azure contient un champ « Dernière actualisation ». Il permet d’identifier à quel point vos données sont récentes.

Les données d’utilisation et les informations ne remplacent pas les rapports d’activité Azure Multi-Factor Authentication ni les informations contenues dans le rapport d’ouverture de session d’Azure AD.

## <a name="next-steps"></a>Étapes suivantes

- [Utilisation de l’API de rapport d’utilisation des méthodes d’authentification](https://docs.microsoft.com/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Choix des méthodes d’authentification pour votre organisation](concept-authentication-methods.md)
- [Expérience d’inscription combinée](concept-registration-mfa-sspr-combined.md)
