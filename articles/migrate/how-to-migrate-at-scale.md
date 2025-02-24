---
title: Automatiser la migration d’un grand nombre de machines virtuelles vers Azure | Microsoft Docs
description: Explique comment utiliser des scripts pour migrer un grand nombre de machines virtuelles à l’aide d’Azure Site Recovery
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 04/01/2019
ms.author: snehaa
ms.openlocfilehash: f90140e9464ee72e9ceae8ca140bd060c51aade8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60597112"
---
# <a name="scale-migration-of-vms-using-azure-site-recovery"></a>Mettre à l’échelle la migration de machines virtuelles à l’aide d’Azure Site Recovery

Cet article vous aide à comprendre le processus d’utilisation de scripts pour migrer un grand nombre de machines virtuelles à l’aide d’Azure Site Recovery. Ces scripts sont disponibles pour votre téléchargement dans le dépôt [Exemples Azure PowerShell](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) sur GitHub. Vous pouvez utiliser des scripts pour migrer de VMware, AWS, GCP VM, mais aussi de serveurs physiques, vers Azure. Ces scripts peuvent aussi prendre en charge la migration et les disques gérés. Vous pouvez également utiliser ces scripts pour migrer des machines virtuelles Hyper-V si vous migrez les machines virtuelles en tant que serveurs physiques. Les scripts tirent parti d’Azure Site Recovery PowerShell qui est documenté [ici](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell).

## <a name="current-limitations"></a>Limitations actuelles :
- Prendre en charge la spécification de l’adresse IP statique uniquement pour la carte réseau principale de la machine virtuelle cible
- Les scripts ne prennent pas les entrées associées à Azure Hybrid Benefit. Vous devez mettre à jour manuellement les propriétés de la machine virtuelle répliquée dans le portail

## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?

### <a name="prerequisites"></a>Prérequis
Avant de commencer, vous devez effectuer les étapes suivantes :
- Vérifiez que le coffre Site Recovery est créé dans votre abonnement Azure
- Vérifier que le serveur de configuration et le serveur de processus sont installés dans l’environnement source et que le coffre est en mesure de détecter l’environnement
- Vérifier qu’une stratégie de réplication est créée et associée au serveur de configuration
- Vérifier que vous avez ajouté le compte d’administrateur de machine virtuelle au serveur de configuration (qui sera utilisé pour répliquer les machines virtuelles locales)
- Vérifier que les artefacts cibles dans Azure sont créés
    - Groupe de ressources cible
    - Compte de stockage cible (et son groupe de ressources) : créez un compte de stockage premium si vous prévoyez de migrer vers des disques gérés premium
    - Compte de stockage de cache (et son groupe de ressources) : créez un compte de stockage standard dans la même région que le coffre
    - Réseau virtuel cible pour le basculement (et son groupe de ressources)
    - Sous-réseau cible
    - Réseau virtuel cible pour le test de basculement (et son groupe de ressources)
    - Groupe à haute disponibilité (si nécessaire)
    - Groupe de sécurité réseau cible et son groupe de ressources
- Vérifiez que vous avez choisi les propriétés de la machine virtuelle cible
    - Nom de la machine virtuelle cible
    - Taille de la machine virtuelle cible dans Azure (peut être définie à l’aide d’une évaluation Azure Migrate)
    - Adresse IP privée de la carte réseau principale dans la machine virtuelle
- Téléchargez les scripts à partir du dépôt [Exemples Azure PowerShell](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) sur GitHub

### <a name="csv-input-file"></a>Fichier d’entrée CSV
Une fois tous les prérequis satisfaits, vous devez créer un fichier CSV qui comporte des données pour chaque machine source que vous voulez migrer. L’entrée CSV doit avoir une ligne d’en-tête contenant les détails de l’entrée et une ligne contenant les détails de chaque machine devant être migrée. Tous les scripts sont conçus pour fonctionner sur le même fichier CSV. À titre de référence, un exemple de modèle CSV est disponible dans le dossier des scripts.

### <a name="script-execution"></a>Exécution des scripts
Une fois que le fichier CSV prêt, vous pouvez exécuter les étapes suivantes pour effectuer la migration des machines virtuelles locales :

**N° de l’étape** | **Nom du script** | **Description**
--- | --- | ---
1 | asr_startmigration.ps1 | Activer la réplication pour toutes les machines virtuelles listées dans le fichier csv. Le script crée une sortie CSV avec les détails du travail pour chaque machine virtuelle
2 | asr_replicationstatus.ps1 | Vérifier l’état de la réplication. Le script crée un fichier csv avec l’état pour chaque machine virtuelle
3 | asr_updateproperties.ps1 | Une fois les machines virtuelles répliquées/protégées, utilisez ce script pour mettre à jour les propriétés cibles de la machine virtuelle (propriétés réseau et de calcul)
4 | asr_propertiescheck.ps1 | Vérifier si les propriétés sont correctement mises à jour
5\. | asr_testmigration.ps1 |  Démarrer le test de basculement des machines virtuelles listées dans le fichier csv. Le script crée une sortie CSV avec les détails du travail pour chaque machine virtuelle
6\. | asr_cleanuptestmigration.ps1 | Après avoir validé manuellement les machines virtuelles dont le test de basculement a échoué, vous pouvez utiliser ce script pour nettoyer les machines virtuelles du test de basculement
7 | asr_migration.ps1 | Effectuer un basculement non planifié pour les machines virtuelles listées dans le fichier csv. Le script crée une sortie CSV avec les détails du travail pour chaque machine virtuelle. Le script n’arrête pas les machines virtuelles locales avant de déclencher le basculement. Pour la cohérence des applications, il est recommandé d’arrêter manuellement les machines virtuelles avant l’exécution du script.
8 | asr_completemigration.ps1 | Effectuer l’opération de validation sur les machines virtuelles et supprimer les entités Azure Site Recovery
9 | asr_postmigration.ps1 | Si vous envisagez d’attribuer des groupes de sécurité réseau aux cartes réseau après le basculement, vous pouvez, pour cela, utiliser ce script. Cela attribue un groupe de sécurité réseau à n’importe quelle carte réseau de la machine virtuelle cible.

## <a name="how-to-migrate-to-managed-disks"></a>Comment migrer vers des disques gérés ?
Par défaut, le script migre les machines virtuelles vers des disques gérés dans Azure. Si le compte de stockage cible fourni est un compte de stockage premium, des disques gérés premium sont créés après la migration. Le compte de stockage de cache peut être un compte standard. Si le compte de stockage cible est un compte de stockage standard, des disques standards sont créés après la migration. 

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) sur la migration de serveurs vers Azure à l’aide d’Azure Site Recovery
