---
title: Configurer et consulter les journaux d’audit pour Azure Database for MariaDB dans le Portail Azure
description: Cet article décrit comment configurer et consulter les journaux d’audit dans Azure Database for MariaDB à partir du Portail Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: 8fadcc8cb418063662df45d4134bef0578ffccda
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444814"
---
# <a name="configure-and-access-audit-logs-in-the-azure-portal"></a>Configurer et consulter les journaux d’audit sur le Portail Azure

Vous pouvez configurer les [journaux d’audit Azure Database for MariaDB](concepts-audit-logs.md) et les paramètres de diagnostic à partir du Portail Azure.

> [!IMPORTANT]
> Pour l’instant, la fonctionnalité Journal d’audit n’existe qu’en préversion.

## <a name="prerequisites"></a>Prérequis

Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :

- [Serveur Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>Configurer l’enregistrement d’audit

Activez et configurez l’enregistrement d’audit.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. Sélectionnez votre serveur Azure Database for MariaDB.

1. Dans la section **Paramètres** dans l’encadré, sélectionnez **Paramètres du serveur**.
    ![Paramètres du serveur](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. Mettez à jour le paramètre **audit_log_enabled** sur ON.
    ![Activer les journaux d’audit](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. Sélectionnez les [types d’événements](concepts-audit-logs.md#configure-audit-logging) à journaliser en mettant à jour le paramètre **audit_log_events**.
    ![Événements du journal d’audit](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. Ajoutez des utilisateurs MariaDB à exclure de la journalisation en mettant à jour le paramètre **audit_log_exclude_users**. Spécifiez les utilisateurs en fournissant leur nom d’utilisateur MariaDB.
    ![Utilisateurs à exclure du journal d’audit](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. Une fois que vous avez modifié les paramètres, vous pouvez cliquer sur **Enregistrer**. Vous pouvez également **Abandonner** vos modifications.
    ![Save](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>Configurer les journaux de diagnostic

1. Dans la section **Supervision** dans la barre latérale, sélectionnez **Paramètres de diagnostic**.

1. Cliquez sur « + Ajouter un paramètre de diagnostic » ![Ajouter un paramètre de diagnostic](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. Saisissez un nom de paramètre de diagnostic.

1. Spécifiez les récepteurs de données auxquels envoyer les journaux d’audit (compte de stockage, hub d’événements et/ou espace de travail Log Analytics).

1. Sélectionnez « MySqlAuditLogs » comme type de journal.
![Configurer un paramètre de diagnostic](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. Une fois que vous avez configuré les récepteurs de données auxquels envoyer les journaux d’audit, vous pouvez cliquer sur **Enregistrer**.
![Enregistrer un paramètre de diagnostic](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. Accédez aux journaux d’audit en les explorant dans les récepteurs de données que vous avez configurés. L’affichage des journaux peut prendre jusqu’à 10 minutes.

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur les [journaux d’audit](concepts-audit-logs.md) dans Azure Database for MariaDB.