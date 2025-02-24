---
title: Comprendre les stations de travail sécurisées gérées par Azure - Azure Active Directory
description: Apprenez-en plus sur les stations de travail sécurisées gérées par Azure et découvrez pourquoi elles sont importantes.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02a6ddef294c4872f2d7e50e8940ecbb4b4b7bc4
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491590"
---
# <a name="understand-secure-azure-managed-workstations"></a>Comprendre les stations de travail sécurisées gérées par Azure

Les stations de travail sécurisées et isolées sont extrêmement importantes pour la sécurité des rôles sensibles comme les administrateurs, développeurs et opérateurs de service critique. Si la sécurité des stations de travail clientes est compromise, un grand nombre de contrôles de sécurité et d’assurances peuvent ne pas fonctionner ou s’avérer inefficaces.

Ce document explique ce dont vous avez besoin pour créer une station de travail sécurisée, souvent appelée station de travail avec accès privilégié (PAW). Cet article contient également des instructions détaillées concernant la configuration des contrôles de sécurité initiaux. Ce guide décrit comment la technologie cloud permet de gérer le service. Cette technologie s’appuie sur les fonctionnalités de sécurité introduites dans Windows 10RS5, Microsoft Defender Advanced Threat Protection (ATP), Azure Active Directory et Intune.

> [!NOTE]
> Cet article explique le concept de station de travail sécurisée et son importance. Si vous êtes déjà familiarisé avec le concept et que vous souhaitez passer directement au déploiement, consultez [Déployer une station de travail sécurisée](https://docs.microsoft.com/azure/active-directory/devices/howto-azure-managed-workstation).

## <a name="why-secure-workstation-access-is-important"></a>Pourquoi un accès sécurisé aux stations de travail est important

L’adoption rapide des services cloud et la possibilité de travailler à partir de n’importe quel endroit a engendré une nouvelle méthode d’exploitation. En exploitant les contrôles de sécurité faibles sur les appareils sur lesquels les administrateurs travaillent, les pirates peuvent accéder à des ressources privilégiées.

L’utilisation abusive des privilèges et les attaques de chaîne d’approvisionnement font partie des cinq méthodes les plus utilisées par les pirates pour violer la sécurité des organisations. Elles occupent également la deuxième place des tactiques les plus fréquemment détectées dans les incidents rapportés en 2018, selon le [rapport de Verizon sur les menaces](https://enterprise.verizon.com/resources/reports/dbir/)et le [rapport de renseignement sur la sécurité](https://aka.ms/sir).

La plupart des pirates suivent ces étapes :

1. Reconnaissance pour trouver une faille, souvent propre à un secteur d’activité.
1. L’analyse visant à collecter des informations et identifier la meilleure façon d’infiltrer une station de travail est perçue comme faible.
1. Persistance à rechercher un moyen de se déplacer [latéralement](https://en.wikipedia.org/wiki/Network_Lateral_Movement).
1. Exfiltration des données confidentielles et sensibles.

Pendant la reconnaissance, les pirates infiltrent fréquemment des appareils qui semblent présenter un risque faible ou dont la valeur est sous-estimée. Ils utilisent ces appareils vulnérables pour identifier une opportunité de mouvement latéral, et pour trouver des utilisateurs et appareils administratifs. Après avoir réussi à accéder à des rôles d’utilisateur privilégié, les pirates identifient les données de valeur élevée et les exfiltrent.

![Modèle de compromission classique](./media/concept-azure-managed-workstation/typical-timeline.png)

Ce document décrit une solution qui peut vous aider à protéger vos appareils informatiques contre ces attaques latérales. La solution isole l’administration et les services des appareils de productivité moins intéressants, en brisant la chaîne avant que l’appareil ayant accès aux ressources de cloud sensibles puisse être infiltré. La solution utilise des services Azure natifs qui font partie de la pile Microsoft 365 Entreprise :

* Intune pour la gestion des appareils et une liste verte d’applications et d’URL
* AutoPilot pour la configuration, le déploiement et l’actualisation des appareils
* Azure AD pour la gestion des utilisateurs, l’accès conditionnel et l’authentification multifacteur
* Windows 10 (version actuelle) pour l’attestation de l’intégrité des appareils et l’expérience utilisateur
* Defender ATP pour la protection des points de terminaison gérés dans le cloud, la détection et la réponse
* Azure AD PIM pour la gestion des autorisations et des accès privilégiés juste-à-temps aux ressources

## <a name="who-benefits-from-a-secure-workstation"></a>À qui profitent les stations de travail sécurisées ?

Tous les utilisateurs et opérateurs peuvent tirer profit de l’utilisation d’une station de travail sécurisée. Un pirate qui compromet un PC ou un appareil peut emprunter l’identité de tous les comptes mis en cache. Lorsqu’il est connecté à l’appareil, il peut également utiliser ses informations d’identification et ses jetons. Ce risque appelle donc à sécuriser les appareils utilisés pour les rôles avec privilèges, notamment ceux disposant de droits d’administration. Les appareils utilisant des comptes privilégiés sont des cibles pour les attaques par mouvement latéral et réaffectation des privilèges. Ces comptes peuvent être utilisés pour un large éventail de ressources telles que :

* Administrateur de systèmes cloud ou locaux
* Station de travail de développeur pour systèmes critiques
* Administrateur de comptes sociaux avec niveau d’exposition élevé
* Station de travail hautement sensible, telle qu’un terminal de paiement SWIFT
* Station de travail contenant des secrets industriels

Pour réduire les risques, vous devriez implémenter des contrôles de sécurité plus élevés sur les stations de travail privilégiées qui utilisent ces comptes. Pour plus d’informations, consultez le [Guide de déploiement des fonctionnalités d’Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2), la [feuille de route d’Office 365](https://aka.ms/o365secroadmap) et la [feuille de route de sécurisation de l’accès privilégié](https://aka.ms/sparoadmap)).

## <a name="why-use-dedicated-workstations"></a>Pourquoi utiliser des stations de travail dédiées ?

Bien qu’il soit possible d’ajouter des dispositifs de sécurité à un appareil existant, il est préférable de commencer avec une base sécurisée. Pour que votre organisation soit dans la meilleure position possible pour maintenir un niveau de sécurité élevé, commencez avec un appareil que vous savez sécurisé et implémentez un ensemble de contrôles de sécurité connus.

Avec le nombre croissant de vecteurs d’attaque utilisant les messageries électroniques et la navigation sur le web, il est de plus en plus difficile d’être sûr qu’un appareil est digne de confiance. Ce guide part du principe qu’une station de travail dédiée est isolée des tâches de productivité, de navigation et de messagerie standard. La suppression des applications de productivité, de navigation sur le web et de messagerie d’un appareil peut nuire à la productivité. Toutefois, cette stratégie de protection est généralement acceptable dans les scénarios où les tâches à accomplir ne nécessitent pas explicitement ces applications et où le risque d’un incident de sécurité est élevé.

> [!NOTE]
> La navigation sur le web fait ici référence à l’accès général à des sites web arbitraires, qui peut être une activité à haut risque. Ce type de navigation se distingue de l’utilisation d’un navigateur web pour accéder à un petit nombre de sites web d’administration connus pour des services comme Azure, Office 365, ceux d’autres fournisseurs de cloud et les applications SaaS.

Les stratégies d’imbrication renforcent la sécurité en augmentant le nombre et les types de contrôles qui dissuadent les pirates d’accéder à des ressources sensibles. Le modèle décrit dans cet article utilise une conception à plusieurs niveaux de privilège et restreint les privilèges d’administration à des appareils spécifiques.

## <a name="supply-chain-management"></a>Gestion de la chaîne d’approvisionnement

Essentielle pour une station de travail sécurisée : une solution de chaîne d’approvisionnement dans laquelle vous utilisez une station de travail approuvée appelée « racine de confiance ». Pour cette solution, la racine de confiance utilise la technologie [Microsoft Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot). Pour sécuriser une station de travail, Autopilot vous permet d’utiliser des appareils Windows 10 Microsoft OEM optimisés. Ces appareils sont fournis par le fabricant dans un état correct connu. Au lieu de réimager l’image d’un appareil potentiellement non sécurisé, Autopilot peut faire passer un appareil Windows à l’état « prêt à l’emploi ». Il applique des paramètres et des stratégies, installe des applications et modifie même l’édition de Windows 10. Par exemple, Autopilot peut changer l’installation de Windows d’un appareil de Windows 10 Professionnel vers Windows 10 Entreprise pour qu’il puisse utiliser les fonctionnalités avancées.

![Niveaux de station de travail sécurisée](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>Rôles et profils des appareils

Ce guide décrit plusieurs profils et rôles de sécurité qui peuvent vous aider à créer des solutions plus sécurisées pour les utilisateurs, les développeurs et le personnel informatique. Ces profils équilibrent facilité d’utilisation et risques pour les utilisateurs courants qui peuvent profiter d’une station de travail sécurisée ou améliorée. Les configurations de paramètres fournies ici sont basées sur les normes du secteur. Ce guide explique comment renforcer Windows 10 et réduire les risques associés aux appareils ou utilisateurs compromis. Il fait appel aux stratégies et à la technologie pour gérer les risques et les fonctionnalités de sécurité.
![Niveaux de station de travail sécurisée](./media/concept-azure-managed-workstation/seccon-levels.png)

* **Faible sécurité** : une station de travail standard gérée constitue un bon point de départ pour la plupart des utilisations à domicile et dans les petites entreprises. Ces appareils sont inscrits dans Azure AD et gérés avec Intune. Ce profil permet aux utilisateurs d’exécuter toutes les applications et de naviguer sur tous les sites web. Une solution anti-programme malveillant comme [Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security) doit être activée.

* **Sécurité renforcée** :cette solution protégée d’entrée de gamme convient aux particuliers, aux utilisateurs des petites entreprises et aux développeurs généraux.

   La station de travail renforcée est basée sur des stratégies et permet d’améliorer la sécurité du profil de sécurité faible. Elle constitue un moyen sécurisé de travailler avec des données client en utilisant également des outils de productivité comme la messagerie et la navigation web. Vous pouvez utiliser des stratégies d’audit et Intune pour surveiller une station de travail renforcée en termes de comportement des utilisateurs et d’utilisation des profils. Déployez le profil de station de travail renforcée avec le script Windows10 (1809), qui lui permet de tirer parti de la protection avancée contre les programmes malveillants avec [Advanced Threat Protection (ATP)](https://docs.microsoft.com/office365/securitycompliance/office-365-atp).

* **Haute sécurité** : le moyen le plus efficace de réduire la surface d’attaque d’une station de travail consiste à supprimer la possibilité d’administrer soi-même la station de travail. La suppression des droits d’administrateur local améliore la sécurité, mais cela peut avoir un impact sur la productivité si cette approche implémentée de manière incorrecte. Le profil haute sécurité s’appuie sur le profil de sécurité renforcée à une différence notable : la suppression de l’administrateur local. Ce profil est conçu pour les utilisateurs importants : cadres, utilisateurs responsables de la paie et des données sensibles, approbateurs de services et processus.

   Les utilisateurs haute sécurité ont besoin d’un environnement plus contrôlé leur permettant toutefois d’effectuer des activités en rapport avec la messagerie ou la navigation web en toute simplicité. Les utilisateurs veulent des fonctionnalités telles que les cookies, les favoris et autres raccourcis qui fonctionnent. Toutefois, ces utilisateurs ne peuvent pas s’attendre à pouvoir modifier ou déboguer leur appareil. Ils ne peuvent pas non plus installer des pilotes. Le profil haute sécurité est déployé à l’aide du script Haute sécurité - Windows10 (1809).

* **Spécialisée** : les pirates ciblent les développeurs et les administrateurs informatiques, car ces derniers peuvent modifier les systèmes présentant un intérêt pour eux. La station de travail spécialisée s’appuie sur les stratégies de la station de travail haute sécurité et y ajoute la gestion des applications locales et la limitation des sites web accessibles. Elle restreint également les fonctionnalités de productivité à haut risque, comme ActiveX, Java, les plug-ins de navigateur et autres contrôles Windows. Déployez ce profil avec le script DeviceConfiguration_NCSC - Windows10 (1803) SecurityBaseline.

* **Sécurisée** : un pirate qui compromet un compte d’administrateur peut causer des dégâts significatifs pour l’entreprise par vol de données, altération de données ou interruption de service. Dans cet état de sécurité renforcé, la station de travail active tous les contrôles et stratégies de sécurité qui limitent le contrôle direct de la gestion locale des applications. Une station de travail sécurisée ne possède aucun outil de productivité. Par conséquent, l’appareil est plus difficile à compromettre. Elle bloque le vecteur le plus courant d’attaques par hameçonnage : la messagerie et les médias sociaux.  La station de travail sécurisée peut être déployée à l’aide du script Station de travail sécurisée - Windows 10 (1809) SecurityBaseline.

   ![Station de travail sécurisée](./media/concept-azure-managed-workstation/secure-workstation.png)

   Une station de travail sécurisée offre à un administrateur une station de travail renforcée disposant de protections et de contrôles clairs des applications. La station de travail utilise les fonctions Credential Guard, Device Guard et Exploit Guard pour protéger l’hôte contre les comportements malveillants. Tous les disques locaux sont également chiffrés avec BitLocker.

* **Isolé** : ce scénario personnalisé hors connexion représente l’extrémité du spectre. Aucun script d’installation n’est fourni dans ce cas. Vous devrez peut-être gérer une fonction critique pour l’entreprise qui requiert un système d’exploitation hérité non pris en charge ou non corrigé. Par exemple, une ligne de production de valeur élevée ou un système vital. Étant donné que la sécurité est essentielle et que les services cloud ne sont pas disponibles, vous pouvez gérer et mettre à jour ces ordinateurs manuellement ou à l’aide d’une architecture de forêt Active Directory isolée telle que l’environnement d’administration de sécurité renforcée (Enhanced Security Admin Environment, ESAE). Dans ce cas, envisagez de supprimer tous les accès à l’exception des contrôles d’intégrité de base d’Intune et d’ATP.

  * [Communications réseau requises pour Intune](https://docs.microsoft.com/intune/network-bandwidth-use)
  * [Communications réseau requises pour ATP](https://docs.microsoft.com/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>Étapes suivantes

[Déployer une station de travail sécurisée gérée par Azure](howto-azure-managed-workstation.md).
